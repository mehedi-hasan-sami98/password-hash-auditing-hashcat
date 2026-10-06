# Password Hash Auditing with Hashcat

> A practical, in-depth guide to auditing password hash strength using **Hashcat**, covering hash identification, dictionary attacks, mask/brute-force attacks, attack monitoring, and how to turn results into an actionable security report — with a full worked example.

![Tool](https://img.shields.io/badge/tool-hashcat%20v7.1.2-blue)
![Platform](https://img.shields.io/badge/platform-Kali%20Linux-557C94)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

## ⚠️ Scope & Disclaimer

This repository is for **educational and authorized security-testing purposes only**. All examples here were run against a single sample hash in a controlled, isolated environment.

- No real user credentials, production systems, or third-party data were involved.
- Do **not** use the techniques documented here against any hash, system, or account you are not explicitly authorized to test.
- Unauthorized password cracking is illegal in most jurisdictions. Always operate under written authorization and defined scope.

## 📖 What This Repository Covers

Hashcat is the industry-standard tool for offline password hash recovery and is widely used in penetration testing, red teaming, and internal credential audits. This repository walks through how to use it properly, end to end:

- Identifying an unknown hash and mapping it to the correct Hashcat mode
- Running a **dictionary attack** against a hash using a known password list
- Running a **mask-based brute-force attack** with custom character patterns
- Verifying and displaying recovered passwords
- Monitoring long-running cracking jobs (speed, progress, ETA)
- Translating raw cracking results into a formal **password security risk assessment** with remediation recommendations

A complete practical walkthrough — commands, terminal output, and screenshots — is included as a worked example so the process is easy to follow and reproduce in your own authorized environment.

## 🗂 Repository Structure

```
.
├── README.md                          ← you are here
├── hash-reference.md                  ← Hashcat hash-mode & attack-mode cheat sheet
├── hash.txt                           ← sample target hash used in the walkthrough
├── docs/
│   ├── methodology.md                 ← step-by-step auditing methodology
│   ├── commands-log.md                ← every command run, in order, with explanations
│   ├── findings.md                    ← results, analysis & technical observations
│   └── security-assessment-report.md  ← formal risk assessment & recommendations
└── screenshots/                       ← terminal captures for each stage
```

## 🧪 Worked Example

| Attack Type | Mode | Source | Result | Time to Crack |
|---|---|---|---|---|
| Dictionary | `-a 0` | `rockyou.txt` | `password` | ~2 sec |
| Mask (brute-force) | `-a 3` | `?l?l?l?l?l?l?l?l` | `password` | ~1 sec |

**Target hash:** `5f4dcc3b5aa765d61d8327deb882cf99` (MD5) → **Recovered: `password`**

This single example is intentionally simple (a well-known weak password against unsalted MD5) so the *process* is easy to follow — the same methodology applies directly to SHA-1, SHA-256, NTLM, bcrypt, and other hash types by changing the Hashcat mode (`-m`) and attack strategy. See [`hash-reference.md`](hash-reference.md) for the full mode/attack reference.

Full breakdown: [`docs/findings.md`](docs/findings.md) · Formal report: [`docs/security-assessment-report.md`](docs/security-assessment-report.md)

## 🖥 Environment

- **OS:** Kali Linux
- **Hashcat:** v7.1.2
- **Backend:** OpenCL 3.0 PoCL (CPU: AMD Ryzen 9 9950X 16-Core)
- **Wordlist:** `rockyou.txt` (14,344,392 passwords)

## 📸 Screenshots

All terminal output is captured step-by-step in [`screenshots/`](screenshots/), referenced inline from [`docs/commands-log.md`](docs/commands-log.md).

## 🔗 Further Reading

- [Hashcat official documentation](https://hashcat.net/wiki/)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)

---

*Maintained as part of ongoing password security / offensive security research.*
