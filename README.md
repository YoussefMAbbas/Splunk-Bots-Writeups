<p align="center">
  <img src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fbackupcentral.com%2Fwp-content%2Fuploads%2F2024%2F05%2FBlue-Team-to-the-Rescue-1-1024x1024.png&f=1&nofb=1&ipt=edd88a09cb533476efedf90fd324a84bb2e83d707c8231a1e894de51abf92079" 
       alt="Blue Team Splunk BOTS Investigation" 
       width="250">
</p>

<h1 align="center">Splunk BOTS Solutions & SOC Investigation Walkthroughs</h1>

<p align="center">
Hands-on blue-team investigations from real-world <strong>Splunk BOTS (Boss of the SOC)</strong> attack simulations.<br>
Step-by-step threat hunting, SPL breakdowns, detection logic, and analyst methodology.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Splunk-SPL-orange">
  <img src="https://img.shields.io/badge/Focus-Blue%20Team-blue">
  <img src="https://img.shields.io/badge/Type-CTF%20Writeups-success">
  <img src="https://img.shields.io/badge/Status-Active%20Development-brightgreen">
</p>

---

## 📑 Table of Contents

- [About BOTS](#-about-bots)
- [What This Repository Contains](#-what-this-repository-contains)
- [Methodology Used](#-methodology-used)
- [Repository Structure](#-repository-structure)
- [Example SPL Query](#-example-spl-query)
- [Skills Demonstrated](#-skills-demonstrated)
- [Prerequisites](#-prerequisites)
- [Progress](#-progress)
- [Contributing](#-contributing)
- [Connect With Me](#-connect-with-me)
- [Why This Repository Exists](#-why-this-repository-exists)

---

## 📌 About BOTS

**Boss of the SOC (BOTS)** is a blue-team CTF-style challenge created by :contentReference[oaicite:0]{index=0}.

It simulates realistic enterprise attack scenarios where analysts must investigate logs using:

- Splunk Enterprise  
- Splunk Enterprise Security (ES)  
- SPL (Search Processing Language)

Participants analyze datasets, detect attacker behavior, pivot on indicators, and answer forensic-style questions — just like a real SOC analyst.

---

## 🔎 What This Repository Contains

For each BOTS challenge, I document:

- 🔍 Full investigation workflow
- 🧠 SPL queries used
- 📊 Evidence correlation & analysis
- 🛡 Detection logic explanation
- 🏁 Final answers with reasoning
- 📝 Lessons learned & defensive insights

This repository is structured as a **SOC analyst notebook**, not just final answers.

---

## 🧠 Methodology Used

Each investigation follows a structured blue-team process:

1. Understand the objective and scope
2. Identify relevant indexes and sourcetypes
3. Narrow timeframe based on suspected activity
4. Pivot on indicators (IP, username, hash, process, domain, etc.)
5. Build & refine SPL queries
6. Validate findings with supporting evidence
7. Document detection logic and response considerations

This mirrors real-world:

- Threat Hunting
- Incident Investigation
- Detection Engineering
- Log Correlation

---

## 📂 Repository Structure

```
.
└── Getting Started with Splunk for Security/
    └── botsv1_getting_started.md
```

Each folder represents a course or BOTS module.

Each markdown file contains:
- Challenge overview
- Step-by-step analysis
- SPL queries
- Evidence findings
- Final answers with explanation

---

## 🔍 Example SPL Query

```spl
index=botsv1 sourcetype=wineventlog EventCode=4624
| stats count by Account_Name
| sort -count
````

Example use case:
Detecting authentication patterns and identifying suspicious login behavior.

---

## 📈 Skills Demonstrated

* Threat Hunting
* Blue Team Analysis
* SOC Investigation Workflow
* Detection Engineering
* Log Correlation
* Incident Response Methodology
* SPL Optimization
* Analytical Documentation

---

## 📋 Prerequisites

To follow along, you should have:

* Access to BOTS datasets
* Basic familiarity with SPL
* A working Splunk environment (Enterprise or ES)
* Fundamental understanding of SOC investigations

> This repository focuses on analysis and methodology — not Splunk installation setup.

---

## 🚀 Progress

* ✅ Getting Started with Splunk for Security
* ⬜ Additional BOTS Challenges (In Progress)

Repository will be updated as new challenges are completed.

---

## 🤝 Contributing

If you have:

* Alternative SPL approaches
* Improved detection logic
* Optimization suggestions
* Additional pivot strategies

Feel free to open an issue or submit a pull request.

Collaboration improves detection thinking.

---

## 💬 Connect With Me

If you're also working on BOTS or interested in blue-team investigations, feel free to connect.

<a href="https://youssefmabbas.medium.com/">![Medium](https://img.shields.io/badge/Medium-black?style=for-the-badge&logo=medium)</a>
<a href="https://www.linkedin.com/in/youssefmabbas/">![LinkedIn](https://img.shields.io/badge/linkedin-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)</a>
<a href="mailto:youssefmabbasofficial@proton.me">![Protonmail](https://img.shields.io/badge/ProtonMail-8B89CC?style=for-the-badge&logo=protonmail&logoColor=white)</a>

---

## ⭐ Why This Repository Exists

This project serves as:

* A documented blue-team learning journey
* A practical demonstration of SOC investigation skills
* A public portfolio of detection engineering methodology
* A reference for other analysts working through BOTS
