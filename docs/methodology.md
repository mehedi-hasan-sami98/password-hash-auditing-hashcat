# Methodology

This document describes the general methodology used for auditing password hash strength with Hashcat. The worked example in this repository follows these steps exactly; the same approach generalizes to any authorized hash auditing engagement.

## Process Overview

| Step | Action |
|---|---|
| 1 | Install and verify Hashcat is functioning correctly on the auditing machine |
| 2 | Obtain the authorized password hash(es) and identify the hash type (e.g., MD5, SHA-1, SHA-256, NTLM, bcrypt) and corresponding Hashcat mode |
| 3 | Prepare and verify the hash file in the format Hashcat expects |
| 4 | Select and configure an appropriate wordlist and attack parameters |
| 5 | Run a dictionary-based attack against the hash(es) |
| 6 | Run a mask-based (brute-force) attack using a defined character pattern |
| 7 | Display and independently verify any recovered passwords against the original hash(es) |
| 8 | Monitor and record attack mode, hash rate, progress, recovery status, and execution time |
| 9 | Document every command, result, and observation |
| 10 | Translate findings into a formal password security risk assessment with actionable recommendations |

## Hash & Target Scope

- **Target type:** Authorized password hash file (applicable equally to Linux `/etc/shadow`, Windows SAM hashes, or exported application credential stores, provided authorization covers them)
- **Hash types covered (theory + reference):** MD5, SHA-1, SHA-256, NTLM, bcrypt — see [`hash-reference.md`](../hash-reference.md) for the full Hashcat mode table
- **Tooling:** Kali Linux, Hashcat, an approved wordlist (e.g. `rockyou.txt`)

## Attack Selection Notes

- **Dictionary attacks (`-a 0`)** are generally run first — they exploit the fact that real users pick from a relatively small set of common/known-breached passwords, and are often faster in *practice* even though their raw hash rate is lower than brute-force.
- **Mask attacks (`-a 3`)** are used when no match is found via dictionary, or to test specific known password patterns/policies (e.g., "8 characters, all lowercase" or a known corporate password format). Where no specific pattern is mandated, a representative mask matching the suspected password length/character set is a reasonable starting point for demonstration or policy-testing purposes.
- Both attack types should be run with `--status --status-timer=N` enabled on any job expected to take more than a few seconds, so progress can be monitored without interrupting the run.

The full practical walkthrough using this methodology is in [`commands-log.md`](commands-log.md), with results and analysis in [`findings.md`](findings.md).
