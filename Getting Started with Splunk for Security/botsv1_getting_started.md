# Splunk BOTS v1 – Getting Started Walkthrough

This write-up documents my hands-on investigation using the **BOTS v1 (Boss of the SOC)** dataset in Splunk Enterprise.

The objective was to practice log analysis, identify malicious behavior, and understand how an attack unfolds using real event data.

The environment simulates a compromised web server:
**imreallynotbatman.com**

---

# 1. Understanding Splunk

Splunk Enterprise collects and indexes machine data such as:

- Web logs
- IDS alerts
- Windows event logs
- Network traffic

Using Splunk Search Processing Language (SPL), we can filter, extract, and analyze this data to detect suspicious activity.

---

# 2. Checkpoint 1 – Search Basics

## Identifying Suspicious Source IP

```sh
index="botsv1" imreallynotbatman.com sourcetype="stream:http"
| stats count by src_ip
```

Result:
`40.80.148.42`

This IP generated the highest number of HTTP requests, making it the primary suspect.

---

## Checking IDS Alerts

```sh
index="botsv1" imreallynotbatman.com sourcetype="suricata"
```

Result:
`192.168.250.70`

This is the internal server being targeted.

---

## Reviewing Web Logs

```sh
index="botsv1" imreallynotbatman.com sourcetype="iis"
```

Observed a suspicious user-agent string imitating a Windows Chrome browser. Attackers often automate requests while pretending to be legitimate browsers.

---

# 3. Transformational Searches

Transformational commands summarize data instead of listing raw events.

## Most Targeted URLs

```sh
index="botsv1" imreallynotbatman.com src_ip="40.80.148.42"
| top limit=10 url
```

Most accessed paths:

- `/joomla/administrator/index.php`
- `/windows/win.ini`
- `/boot.ini`

This indicates reconnaissance and attempts to access sensitive files.

---

## Using stats Instead of top

```sh
index="botsv1" imreallynotbatman.com src_ip="40.80.148.42"
| stats count by url
| sort 10 - count
```

The `stats` command provides more flexibility for analysis.

---

# 4. Detecting Brute Force Attempts

## Filtering POST Requests

```sh
index="botsv1" dest="192.168.250.70"
sourcetype="stream:http" http_method="POST"
```

Suspicious source:
`40.80.148.42`

---

## Searching for Login Attempts

```sh
index="botsv1" dest="192.168.250.70"
sourcetype="stream:http" http_method="POST"
form_data=*username*passwd*
```

Example:

```
username=admin&passwd=batman
```

This confirms repeated login attempts targeting the admin account.

---

## Counting Attempts by Source

```sh
| stats count by src
```

Result:

- `23.22.63.114` → 412 attempts
- `40.80.148.42` → 1 attempt

This clearly shows automated brute-force activity.

---

# 5. Field Extraction with rex

To extract only the password value:

```sh
| rex field=form_data "passwd=(?<userpassword>\w+)"
| table userpassword
```

This creates a new field called `userpassword` for analysis.

---

# 6. Evaluating Password Strength

## Calculating Password Length

```sh
| eval lenpassword=len(userpassword)
| table userpassword lenpassword
```

Examples:

- rock → 4
- august → 6
- williams → 8

Most passwords are short and common.

---

## Average Password Length

```sh
| stats avg(lenpassword) AS avg_length
| eval avg_length=round(avg_length,2)
```

This provides insight into attacker strategy.
Short average length suggests dictionary-based attacks.

---

# 7. Using Lookup for Wordlist Comparison

Using a lookup file (`coldplay.csv`):

```sh
| lookup coldplay.csv song AS password OUTPUTNEW song
| search song=*
| table song password
```

Match found:
`yellow`

This confirms use of common or predictable passwords.

---

# 8. Time-Based Analysis

```sh
| timechart span=1s fixedrange=false count by dest
```

This visualization shows spikes in login attempts.

High-frequency spikes indicate automation rather than manual login attempts.

---

# 9. Transactions

```sh
| transaction userpassword
| eval duration=round(duration,2)
| table userpassword duration
```

Transactions group related events and show how long a password attempt lasted.

Very short durations confirm scripted behavior.

---

# 10. Geolocation Analysis

```sh
| stats count by src
| iplocation src
| geostats latfield=lat longfield=lon count by src
```

This maps attacker IP addresses geographically and supports threat intelligence analysis.

---

# 11. Windows Log Investigation

## Longest Command Executed (Sysmon)

```sh
index="botsv1"
sourcetype="xmlwineventlog:microsoft-windows-sysmon/operational"
| eval lenCmdLine=len(CommandLine)
| table CommandLine lenCmdLine
| sort -lenCmdLine
| head 1
```

Detected command:

```
taskkill /t /f /im "osk.exe" ... del osk.exe
```

This suggests cleanup behavior after malicious execution.

---

## Most Common Security Events

```sh
index="botsv1" sourcetype="wineventlog:security"
| stats count by EventCode
| sort -count
```

Notable Event Codes:

- 4625 – Failed login
- 5140 – Network share access
- 5145 – Detailed file share access

These indicate authentication attempts and potential lateral movement.

---

## Failed Login Analysis

```sh
index="botsv1" sourcetype="wineventlog:security" EventCode=4625
| stats count by Account_Name
| sort -count
```

Repeated failed logins confirm internal authentication abuse.

---

# 12. Final Attack Timeline

Based on the investigation:

1. Reconnaissance
   - Access to Joomla admin pages
   - Attempts to read system files

2. Brute Force
   - Hundreds of POST login attempts
   - Dictionary-based passwords

3. Possible Access
   - Use of common credentials

4. Post-Compromise Activity
   - Suspicious command execution
   - File deletion
   - Network share access

This reflects a complete attack chain:
Recon → Credential Attack → Access → Internal Activity → Cleanup

---

# Conclusion

This lab provided practical experience in:

- Writing efficient SPL queries
- Extracting fields using regex
- Performing statistical analysis
- Visualizing attack patterns
- Investigating Windows security logs
