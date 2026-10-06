# Lab 05 — Password Hash Auditing Using Hashcat

> Authorized, lab-only password hash auditing exercise performed on Kali Linux using Hashcat v7.1.2.

![Status](https://img.shields.io/badge/lab-completed-brightgreen)
![Tool](https://img.shields.io/badge/tool-hashcat%20v7.1.2-blue)
![Platform](https://img.shields.io/badge/platform-Kali%20Linux-557C94)

## ⚠️ Scope & Disclaimer

This repository documents a **controlled, authorized classroom lab exercise** on a single sample hash, performed for educational purposes under a course specification sheet (see [`docs/specification-sheet.md`](docs/specification-sheet.md)). It demonstrates password-strength auditing concepts only.

- No real user credentials, production systems, or third-party data were involved.
- Do **not** use the techniques here against any hash file, system, or account you are not explicitly authorized to test.
- Unauthorized password cracking is illegal in most jurisdictions.

## 📋 Lab Overview

| | |
|---|---|
| **Title** | Specification Sheet 05 — Password Hash Auditing Using Hashcat |
| **Time Allowed** | 30 Minutes |
| **Tools Required** | Kali Linux, Hashcat, Approved Wordlist (`rockyou.txt`) |
| **Target** | Authorized Password Hash File (sample MD5) |
| **Hash Types Covered (theory)** | MD5, SHA-1, SHA-256, NTLM, bcrypt |
| **Hashcat Version** | v7.1.2 |

## 🗂 Repository Structure

```
hashcat-lab-05/
├── README.md                     ← you are here
├── docs/
│   ├── specification-sheet.md    ← the original 10-task lab brief
│   ├── commands-log.md           ← every command run, in order (Step 9)
│   ├── findings.md                ← observations & results summary (Step 9)
│   └── security-assessment-report.md  ← final report (Step 10)
├── hash-reference.md             ← hashcat hash-mode & attack-mode cheat sheet
├── hash.txt                      ← the sample MD5 hash used in this lab
└── screenshots/                  ← terminal captures for every step
```

## 🎯 Job Tasks Completed

- [x] 1. Install and verify Hashcat on Kali Linux
- [x] 2. Identify the hash type and appropriate Hashcat mode
- [x] 3. Prepare and verify the hash file
- [x] 4. Configure Hashcat with the wordlist
- [x] 5. Dictionary-based password audit
- [x] 6. Mask-based password audit
- [x] 7. Display and verify recovered password
- [x] 8. Monitor and record attack mode, hash rate, progress, status, execution time
- [x] 9. Document commands, screenshots, findings, observations
- [x] 10. Password Security Assessment Report

## 🔑 Result Summary

| Attack | Mode | Pattern/Source | Result | Time |
|---|---|---|---|---|
| Dictionary | `-a 0` | `rockyou.txt` | `password` | ~2 sec |
| Mask (brute-force) | `-a 3` | `?l?l?l?l?l?l?l?l` | `password` | ~1 sec |

Target hash: `5f4dcc3b5aa765d61d8327deb882cf99` (MD5) → **Cracked: `password`**

Full breakdown in [`docs/findings.md`](docs/findings.md) and the final writeup in [`docs/security-assessment-report.md`](docs/security-assessment-report.md).

## 🖥 Environment

- **OS:** Kali Linux
- **Hashcat:** v7.1.2
- **Backend:** OpenCL 3.0 PoCL (CPU: AMD Ryzen 9 9950X 16-Core)
- **Wordlist:** `/usr/share/wordlists/rockyou.txt` (14,344,392 passwords)

## 📸 Screenshots

All terminal output is captured step-by-step in [`screenshots/`](screenshots/), referenced inline from [`docs/commands-log.md`](docs/commands-log.md).

---

*Prepared as part of a password security / ethical hacking coursework module.*
