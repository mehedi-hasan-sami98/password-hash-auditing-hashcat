# Findings, Observations & Audit Results (Step 9)

## 1. Summary Table

| Attack | Mode | Pattern/Source | Speed | Keyspace / Progress at Crack | Recovery | Execution Time |
|---|---|---|---|---|---|---|
| Dictionary | `-a 0` | `rockyou.txt` (14,344,392 words) | 499.2 kH/s | 32,768 / 14,344,385 (0.23%) | 1/1 (100%) | ~2 sec |
| Mask / Brute-force | `-a 3` | `?l?l?l?l?l?l?l?l` | 590.4 MH/s | 100,663,296 / 208,827,064,576 (0.05%) | 1/1 (100%) | ~1 sec |

**Target hash:** `5f4dcc3b5aa765d61d8327deb882cf99` (MD5)
**Recovered plaintext (both attacks):** `password`

## 2. Verification

Recovery was independently verified via Hashcat's potfile lookup:
```bash
hashcat -m 0 hash.txt --show
# 5f4dcc3b5aa765d61d8327deb882cf99:password
```
This confirms the cracked candidate matches the original hash exactly (hashcat recomputes MD5 of the candidate and compares digests before reporting a crack).

## 3. Observations

- **Extremely weak sample password.** `password` is one of the most common passwords in the world and appears near the very start of `rockyou.txt` (cracked at only 0.23% progress through the wordlist), so the dictionary attack succeeded in about 2 seconds.
- **Mask attack speed vs. dictionary attack speed.** The brute-force mask attack ran roughly **1,000× faster** in raw hash-rate terms (590.4 MH/s vs 499.2 kH/s). This is expected: a mask attack generates candidates directly in-kernel with no disk I/O, whereas a dictionary attack has to stream and hash each wordlist entry.
- **Keyspace size matters more than raw speed for real-world passwords.** Despite the much higher hash rate, the mask attack's total keyspace (8 lowercase-only characters = 26⁸ ≈ 208.8 billion combinations) is enormous compared to a curated wordlist of ~14.3 million known-common passwords. A dictionary attack is often *more effective in practice* than brute-force because real users pick from a small set of common patterns, which is exactly what `rockyou.txt` encodes.
- **Potfile caching.** Once a hash is cracked, Hashcat stores the result in `~/.local/share/hashcat/hashcat.potfile` and will skip re-attacking that hash on subsequent runs ("All hashes found as potfile and/or empty entries!"). This is efficient for production auditing but must be disabled (`--potfile-disable`) when deliberately re-demonstrating or re-timing an attack for a lab/report.
- **`--status` / `--status-timer`** is the correct mechanism for live progress monitoring (Step 8) on longer-running jobs; it reports attack mode, speed, progress %, recovered count, and ETA at the configured interval. For this lab's near-instant crack, the interval never had a chance to trigger mid-run — on a stronger hash (e.g., bcrypt, `-m 3200`) or larger keyspace, recurring status snapshots would be visible.

## 4. Audit Conclusion

The authorized sample MD5 hash was successfully cracked using **both** a dictionary-based attack and a mask-based brute-force attack, each completing in under 2 seconds. This demonstrates that:

1. MD5 is cryptographically fast and unsalted in this sample, making it trivial to brute-force at scale (hundreds of millions of hashes/sec on commodity CPU hardware).
2. The sample password (`password`) offered **no resistance** — it is a textbook example of a weak, dictionary-guessable password.
3. Both attack vectors (known-wordlist and pure brute-force) are viable for short/common passwords; wordlist attacks are generally the faster real-world approach because they exploit human password-choice patterns.

See [`security-assessment-report.md`](security-assessment-report.md) for formal recommendations.
