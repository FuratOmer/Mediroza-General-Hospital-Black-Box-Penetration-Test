# 🏥 Mediroza General Hospital — Black-Box Penetration Test

**Week 4 | Cybersecurity & Ethical Hacking | Networkwalks**

This repository documents a full black-box penetration test against a training target, **Mediroza General Hospital** (`medirozahospital.com`). The engagement covered all four phases of a real pentest: initial access, data extraction, critical exposure discovery, and professional reporting.

> ⚠️ **Disclaimer:** All testing was performed under written authorization, strictly within the agreed scope, for educational purposes only. Exploit payloads and recovered confidential data are intentionally omitted from this repository; the full technical report was submitted to the instructor.

---

## 🗺️ Where this fits in a pentest

| Milestone | Phase | What it covered |
|---|---|---|
| M1 | Initial Access | Reconnaissance, WAF/header/CLI checks, and exploiting a login vulnerability to reach restricted patient data |
| M2 | Data Extraction | Recovering the passwords protecting three encrypted PDF files, using a different strategy for each |
| M3 | Critical Exposure | Discovering a server-side misconfiguration that exposed internal HR and ownership data |
| M4 | Reporting | Consolidating every finding into a professional report with severity ratings and remediation steps |

---

## 🔍 M1: Reconnaissance & Initial Access

- Performed reconnaissance using HTTP header inspection, WAF detection checks, and basic Linux CLI tools (`curl`, `grep`), alongside browser inspection of the site's source and login page.
- Identified the CMS version in use and located a staff/patient login endpoint.
- Testing how the login form handled input revealed that submitted values were inserted directly into a backend database query without proper sanitization, a classic **SQL Injection** weakness.
- Exploiting this allowed bypassing the login entirely, without valid credentials, and reaching a restricted area containing three confidential PDF lab reports.

**How I read it:** When user input reaches a database query unsanitized, an attacker can alter the query's logic instead of just supplying a value. This is one of the most common and most damaging web vulnerabilities, and the fix (parameterized queries) is well established.

---

## 🔑 M2: Cracking Three Encrypted PDFs, Three Different Ways

All three retrieved reports were password-protected. Rather than assuming one method would work for all of them, I treated each as its own puzzle:

| File | Approach | Outcome |
|---|---|---|
| Report 1 | Verified with two independent methods: the NetworkWalks Password Cracking Web Tool and a custom Python (`pypdf`) script | Password recovered, cross-confirmed |
| Report 2 | Small wordlists failed, so I escalated to a larger dictionary (`rockyou.txt`) via a custom Python script | Password recovered |
| Report 3 | `rockyou.txt` and standard hash workflows failed entirely, indicating the password wasn't a dictionary word | Pivoted to a custom Python script generating symbol/keyboard-walk permutations, password recovered |

**How I read it:** This is the real shape of password cracking in practice, it's rarely "run one tool and done." When a method fails, that failure is itself information: it rules out a category of password (e.g., "not a common word") and tells you where to look next.

---

## 🚨 M3: Discovering a Critical Server-Side Exposure

- Reviewing file and page metadata from the earlier steps pointed toward an old, forgotten path on the server.
- Checking `robots.txt` revealed a disallowed directory that search engines are told to skip, but that a human (or attacker) can still request directly.
- That directory had **auto-indexing enabled**, listing its files publicly with no authentication.
- It contained an old **database backup file**, directly downloadable, holding complete internal HR and ownership records.

**How I read it:** `robots.txt` is a request to well-behaved search engines, not an access control. Anything listed there as "disallowed" is often exactly where an attacker looks first. Combined with directory listing left enabled, this turned an old forgotten backup into a complete data breach.

---

## ⚖️ Risk Summary

| Finding | Rating |
|---|---|
| Public exposure of a full database backup | 🔴 Critical |
| SQL Injection authentication bypass on the login form | 🔴 Critical |
| Weak, guessable passwords on encrypted PDF reports | 🟠 High |
| Directory listing enabled + sensitive path disclosed via `robots.txt` | 🟠 High |
| CMS/version and environment details exposed in headers and metadata | 🟡 Low |

Full technical detail, proof of exploitation, and remediation steps for each finding are in the complete report submitted for this project.

---

## 💡 Key Learnings

- **Unsanitized input is still one of the most damaging bugs in web apps.** A single SQL Injection flaw bypassed authentication entirely, no credentials needed.
- **One cracking method rarely fits every file.** Three PDFs needed three different strategies; a failed attempt is a clue, not a dead end.
- **`robots.txt` is a suggestion, not a security control.** Hiding a sensitive path there, instead of actually restricting access to it, is a false sense of safety.
- **Small misconfigurations compound.** An old backup file + directory listing left on turned a minor oversight into a critical, organization-wide data exposure.
- **A good pentest report does more than list bugs.** It rates severity, explains real-world impact, and gives the client concrete steps to fix each issue, that's the difference between a finding and a useful report.

---

## 👤 Author

**Furat Omer**
Cybersecurity Intern @ NetworkWalks (Batch B084)

[![GitHub](https://img.shields.io/badge/GitHub-FuratOmer-181717?logo=github&logoColor=white)](https://github.com/FuratOmer)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Furat%20Omer-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/furat-omer-a195572b4)

---

*This repository reflects my ongoing learning journey in cybersecurity.*
