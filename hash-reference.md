# Hashcat Reference Cheat Sheet

Quick reference used during this lab for hash modes and attack modes.

## Common Hash Modes (`-m`)

| Mode | Hash Type |
|---|---|
| `0` | MD5 |
| `10` | md5($pass.$salt) |
| `100` | SHA1 |
| `1400` | SHA-256 |
| `1700` | SHA-512 |
| `3200` | bcrypt `$2*$` |
| `1000` | NTLM |
| `3000` | LM |
| `5500` | NetNTLMv1 |
| `5600` | NetNTLMv2 |
| `500` | md5crypt `$1$` (older Linux shadow) |
| `1800` | sha512crypt `$6$` (Linux shadow) |
| `7400` | sha256crypt `$5$` |
| `22000` | WPA-PBKDF2 (WiFi handshake) |

Full list:
```bash
hashcat --help | grep -A 400 "Hash modes"
```

## Attack Modes (`-a`)

| Mode | Name | Description |
|---|---|---|
| `0` | Straight (Dictionary) | Tries each word from a wordlist directly |
| `1` | Combination | Combines words from two wordlists |
| `3` | Brute-force / Mask | Tries every combination matching a character-pattern mask |
| `6` | Hybrid Wordlist + Mask | Appends a mask pattern to each wordlist entry |
| `7` | Hybrid Mask + Wordlist | Prepends a mask pattern to each wordlist entry |
| `9` | Association | Candidate directly associated with a specific hash |

## Mask Placeholders (for `-a 3`)

| Symbol | Meaning |
|---|---|
| `?l` | lowercase letter (a–z) |
| `?u` | uppercase letter (A–Z) |
| `?d` | digit (0–9) |
| `?s` | special character |
| `?a` | all of the above |

**Example:** `?u?l?l?l?d?d?d?d` → 1 uppercase + 3 lowercase + 4 digits (e.g. `Pass1234`)

## Useful Flags Used in This Lab

| Flag | Purpose |
|---|---|
| `--show` | Display already-cracked hash:password pairs from the potfile |
| `--potfile-disable` | Force a fresh attack, ignoring previously cracked results |
| `--status` | Enable periodic status/progress reporting |
| `--status-timer=N` | Set status report interval (seconds) |
| `-b` | Run a benchmark to verify Hashcat performance |
