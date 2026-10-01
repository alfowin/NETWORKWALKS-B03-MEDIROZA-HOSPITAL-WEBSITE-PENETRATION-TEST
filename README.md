# NETWORKWALKS-B03-MEDIROZA-HOSPITAL-WEBSITE-PENETRATION-TEST
trying to gain access into medirozahospitsal.com

![Batch](https://img.shields.io/badge/Batch-B083-blue) ![Program](https://img.shields.io/badge/Networkwalks-Cybersecurity%20Internship-red) ![Phase](https://img.shields.io/badge/Phase-Exploitation%20%26%20Pentest-green) ![Status](https://img.shields.io/badge/Week%204-Complete-brightgreen)

Week 4 capstone of the Networkwalks Cybersecurity & Ethical Hacking internship: a full black-box penetration test of an authorised training target, Mediroza General Hospital*. The engagement runs the complete attack lifecycle - reconnaissance, content discovery, exploitation, post-exploitation and a professional report.


> LIABILITY DISCLAIMER
>  All testing was performed under written authorisation from Networkwalks, against a target they provided for this exercise, and limited to the target domain only (no denial-of-service, no social engineering). Everything here is for education. Unauthorised testing of systems you do not own is illegal.



## OBJECTIVE/RESULT

| # | Objective | Result |
|---|-----------|--------|
| M1 | Attack the site and retrieve 3 confidential patient PDF reports | Achieved via SQL injection auth bypass |
| M2 | Crack the encryption on all 3 retrieved files | Achieved offline (weak passwords, redacted) |
| M3 | Find staff salaries and shareholder details | Achieved via exposed database backup |
| M4 | Write a professional penetration testing report | Included in this repo |

---

## 1. RECONNAINSENCE(footprinting)

Passive/light recon on the target with standard Kali tools before any attack.

![whois]
![whatweb]
![nslookup]
![wafw00f]
![dnsrecon]

**Findings:** Namecheap shared hosting (IP redacted-in-summary), LiteSpeed + OpenResty/CDN, Mediroza CMS 1.4.2, **no WAF**, **SPF `~all`** (softfail) + **DMARC `p=none`** (e-mail spoofing exposure), **DNSSEC unsigned**.

---

## 2. Content discovery & the critical data exposure (Milestone 3)

`robots.txt` advertises a hidden `/old/` directory; **directory listing is enabled**, exposing a full SQL database backup that anyone can download without logging in.

![robots]
![old listing]
![sql backup]

The backup exposes every employee's personal data (names, national IDs, phones, **salaries**) and the hospital's **shareholder register** - satisfying Milestone 3. No exploitation required.

> **Why it matters:** never store a database backup inside the web root. This single misconfiguration is a full confidentiality breach.

---

## 3. Milestone 1 - SQL injection authentication bypass

The patient login builds its SQL query directly from user input. A crafted payload in the username field comments out the password check and logs the attacker in without a password *(payload redacted)*.

![login source]
![sqli login]
![lab reports]

Result: authentication bypassed and access to another user's confidential lab reports - the 3 target PDFs.

> **Why it matters:** the fix is **parameterised queries** (prepared statements). Never concatenate user input into SQL.

---

## 4. Milestone 2 - Cracking the encrypted PDFs

A locked PDF stores a one-way **hash** of its password, so cracking is **offline** - no server contact, no lockout, no logs. All three passwords were weak and fell against a wordlist in seconds *(passwords redacted)*.

![crack1]
![crack2]
![crack3 success]

A generic 100-word list missed the third (a keyboard-pattern password); a **targeted wordlist** recovered it immediately.

> **Why it matters:** the tool is not the skill - **choosing the right wordlist** is. The only real defence is a long, unpredictable passphrase.

---

## Risk summary

| Finding | Rating |
|---------|--------|
| Public database backup (staff PII, salaries, shareholders) | Critical |
| SQL injection - authentication bypass | Critical |
| Weak encryption passwords on patient files | High |
| Directory listing enabled | Medium |
| Username enumeration on patient login | Medium |
| E-mail spoofing exposure (SPF `~all` / DMARC `p=none`) | Medium |
| robots.txt discloses sensitive paths | Low |
| No WAF / no DNSSEC / exposed error log | Low |

Full write-up:(W4-PM-FINAL-Report-B083-Alfred Owino)

---

## What I learned

- Two of the most damaging findings needed **no exploit at all** - just a file left in the web root and an error message that leaks usernames.
- **SQL injection** turns a login into an open door when input is concatenated into a query; prepared statements close it.
- Offline password cracking removes every network defence - **password length and unpredictability are the whole defence**.
- The real cracking skill is **wordlist selection**, not running a tool: a targeted list beat a generic one instantly.
- Document a live engagement responsibly: keep the proof, but **redact the answers** before sharing.

---

## Author
Alfred Owino(Cyber Security Proffessional)
