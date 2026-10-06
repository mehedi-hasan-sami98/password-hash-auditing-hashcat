# Password Security Assessment Report (Step 10)

**Lab Reference:** Specification Sheet 05 — Password Hash Auditing Using Hashcat
**Date:** October 6, 2026
**Tool Used:** Hashcat v7.1.2 (Kali Linux)
**Scope:** One authorized sample MD5 password hash, audited under controlled lab conditions.

---

## 1. Executive Summary

A password hash audit was conducted against an authorized sample hash (`5f4dcc3b5aa765d61d8327deb882cf99`, MD5) using Hashcat, applying both a dictionary-based attack (approved `rockyou.txt` wordlist) and a mask-based brute-force attack. **The hash was cracked in both cases in under two seconds**, recovering the plaintext password `password`. This result highlights severe weaknesses in both the password itself and the hashing algorithm used to store it, and is representative of risks commonly found in unmanaged or legacy credential stores.

## 2. Methodology

| Step | Action |
|---|---|
| 1 | Verified Hashcat installation (v7.1.2) |
| 2 | Identified hash as MD5 (32-char hex digest) via `hashid`, mapped to Hashcat mode `-m 0` |
| 3 | Prepared and verified the hash file |
| 4 | Confirmed the approved wordlist (`rockyou.txt`, ~14.3M entries) was available |
| 5 | Ran dictionary attack (`-a 0`) |
| 6 | Ran mask/brute-force attack (`-a 3`, `?l?l?l?l?l?l?l?l`) |
| 7 | Verified recovered password via `--show` |
| 8 | Recorded hash rate, progress, recovery status, and execution time for each attack |

Full command-by-command detail is available in [`commands-log.md`](commands-log.md); raw results and analysis in [`findings.md`](findings.md).

## 3. Results

| Metric | Dictionary Attack | Mask Attack |
|---|---|---|
| Hashcat mode | `-a 0` | `-a 3` |
| Hash rate | 499.2 kH/s | 590.4 MH/s |
| Time to crack | ~2 seconds | ~1 second |
| Recovered password | `password` | `password` |
| Recovery status | 1/1 (100%) | 1/1 (100%) |

## 4. Risk Assessment

| Finding | Risk Level | Rationale |
|---|---|---|
| Password is a top-10 globally common password | 🔴 Critical | Present within the first 0.23% of a standard wordlist; would be among the first guesses in any real-world credential-stuffing or brute-force attack. |
| Hashing algorithm is unsalted MD5 | 🔴 Critical | MD5 is cryptographically broken for integrity purposes and, more importantly for this context, is extremely fast to compute — enabling attackers to test hundreds of millions of candidates per second on commodity hardware. No salt means identical passwords always produce identical hashes, enabling rainbow-table attacks and cross-account pattern detection. |
| Short password length (8 characters, single character class) | 🟠 High | Falls below modern minimum-length recommendations and uses only one character class (lowercase), drastically shrinking the effective keyspace. |

## 5. Recommendations

### A. Password Hashing / Storage (System-Level)
1. **Stop using MD5 (or SHA-1) for password storage.** Migrate to a purpose-built, slow, salted password hashing algorithm: **bcrypt**, **scrypt**, or **Argon2id** (preferred). These algorithms are intentionally computation- and memory-hard, reducing crack rates from hundreds of millions of guesses/sec to only a few thousand/sec or fewer.
2. **Always use a unique, random salt per password.** This defeats rainbow-table attacks and ensures identical passwords don't produce identical hashes.
3. **Apply key-stretching / iteration counts** appropriate to current hardware (e.g., bcrypt cost factor ≥ 12, or Argon2id with tuned memory/time parameters), and re-evaluate these periodically as hardware improves.
4. **Enforce account lockout / rate-limiting** on authentication endpoints to blunt online brute-force attempts, independent of offline hash strength.

### B. Password Policy (User-Level)
1. **Minimum length of 12–16 characters**, not capped unnecessarily high.
2. **Encourage passphrases** over complex-but-short passwords (e.g., four random unrelated words) — longer is more effective against brute-force than complexity alone.
3. **Block known-breached/common passwords** at creation time (e.g., check against Have I Been Pwned's Pwned Passwords API or an internal denylist built from wordlists like rockyou.txt).
4. **Do not enforce arbitrary periodic password rotation** without cause — this often leads to predictable incremental patterns (`Password1`, `Password2`...). Rotate only on evidence of compromise.
5. **Mandate Multi-Factor Authentication (MFA)** for all accounts, especially privileged ones — this is the single most effective mitigation even if a password is eventually compromised.

### C. Organizational / Process
1. **Conduct regular authorized password audits** (as performed in this lab) against internal credential stores to proactively identify weak passwords before attackers do.
2. **Maintain an approved, access-controlled wordlist and tooling policy** — audits should only run against hashes the auditor is explicitly authorized to test, under documented scope (as reflected in this lab's spec sheet).
3. **Educate users** on password manager adoption to make long, unique, random passwords practical for every account.

## 6. Conclusion

This controlled audit demonstrates — in under two seconds — how trivially a weak password combined with a fast, unsalted hashing algorithm can be compromised using freely available tools and wordlists. The findings reinforce industry-standard guidance: **hashing algorithm choice and password strength are both independently necessary controls**, and neither compensates for weakness in the other. Implementing the recommendations above (salted adaptive hashing + strong password policy + MFA) would have prevented this specific credential from being recovered within any practical timeframe.
