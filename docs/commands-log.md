# Commands Log

Every command executed during the walkthrough, in order, with purpose and corresponding screenshot. See [`methodology.md`](methodology.md) for the overall process this follows.

---

### 1 — Install & Verify Hashcat

```bash
hashcat --version
```
**Purpose:** Confirm Hashcat is installed and functioning.
**Result:** `v7.1.2`

---

### 2 — Identify Hash Type

```bash
hashid 5f4dcc3b5aa765d61d8327deb882cf99
```
**Purpose:** Determine the likely hash algorithm from the sample hash.
**Result:** 32-character hex digest — candidate algorithms included MD2, **MD5**, MD4, Double MD5, LM, NTLM, etc. Confirmed as **MD5** (Hashcat mode `-m 0`).

📸 `screenshots/01-hashcat-version-hashid.png`

---

### 3 — Prepare & Verify Hash File

```bash
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
cat hash.txt
```
**Purpose:** Save the target hash into a file Hashcat can consume, and verify its contents.
**Result:** `hash.txt` contains exactly one line: `5f4dcc3b5aa765d61d8327deb882cf99`

📸 `screenshots/02-hash-file-created.png`

---

### 4 — Configure Hashcat with Wordlist

```bash
ls -la /usr/share/wordlists/
```
**Purpose:** Verify the wordlist (`rockyou.txt`) exists and check whether it needs extracting.
**Result:** `rockyou.txt` present and already extracted (139,921,507 bytes). No `gzip -d` needed.

📸 `screenshots/03-wordlist-check.png`

---

### 5 — Dictionary-Based Password Audit

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```
**Purpose:** Run a straight dictionary attack using the wordlist against the MD5 hash.

**Result:**
```
5f4dcc3b5aa765d61d8327deb882cf99:password
```
- Hash Mode: 0 (MD5)
- Attack Mode: 0 (Straight/Dictionary)
- Speed: 499.2 kH/s
- Progress at crack: 32,768 / 14,344,385 (0.23%)
- Recovered: 1/1 (100%)
- Started: 08:45:30 → Stopped: 08:45:32 (~2 sec)

📸 `screenshots/04-dictionary-attack-1.png`, `screenshots/05-dictionary-attack-2.png`

---

### 6 — Verify Recovered Password

```bash
hashcat -m 0 hash.txt --show
```
**Purpose:** Re-display the cracked hash:password pair from Hashcat's potfile to independently verify the result.
**Result:** `5f4dcc3b5aa765d61d8327deb882cf99:password`

📸 `screenshots/06-dictionary-attack-show.png`

---

### 7 — Mask-Based Password Audit

No specific mask pattern was mandated for this example, so an 8-character all-lowercase mask was chosen (matching the known recovered password's length/character set) to demonstrate brute-force/mask auditing.

**First attempt:**
```bash
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l
```
**Result:** Hashcat returned immediately —
```
INFO: All hashes found as potfile and/or empty entries! Use --show to display them.
```
This is expected: the hash was already cracked and cached in Hashcat's potfile from the dictionary attack, so Hashcat skipped re-cracking it.

📸 `screenshots/07-mask-attack-potfile.png`

**Re-run forcing a fresh attack (bypassing the potfile):**
```bash
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l --potfile-disable
```
**Result:**
```
5f4dcc3b5aa765d61d8327deb882cf99:password
```
- Hash Mode: 0 (MD5)
- Attack Mode: 3 (Brute-force/Mask)
- Mask: `?l?l?l?l?l?l?l?l` [8]
- Speed: 590.4 MH/s
- Total Keyspace: 208,827,064,576
- Progress at crack: 100,663,296 / 208,827,064,576 (0.05%)
- Recovered: 1/1 (100%)
- Started: 08:57:17 → Stopped: 08:57:18 (~1 sec)

📸 `screenshots/08-mask-attack-fresh-1.png`, `screenshots/09-mask-attack-fresh-2.png`

---

### 8 — Monitor Attack Status (live progress)

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --status --status-timer=5
```
**Purpose:** Demonstrate Hashcat's automatic status-reporting feature (`--status`, `--status-timer=5`) for monitoring attack mode, hash rate, progress, and recovery status at regular intervals during longer-running attacks.
**Result:** Hash already in potfile, so Hashcat reported it as already cracked without re-running:
```
INFO: All hashes found as potfile and/or empty entries! Use --show to display them.
```

📸 `screenshots/10-status-timer-monitor.png`

> For this specific hash/wordlist combination the crack is near-instant, so the periodic status timer never had the chance to fire mid-run. On a larger keyspace or slower hash algorithm (e.g. bcrypt, `-m 3200`), `--status-timer` would print recurring progress/speed/ETA snapshots throughout the run — this is the standard technique for monitoring longer audits.

---

## Full Command Reference (copy-paste order)

```bash
# 1
hashcat --version

# 2
hashid 5f4dcc3b5aa765d61d8327deb882cf99

# 3
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
cat hash.txt

# 4
ls -la /usr/share/wordlists/

# 5
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

# 6
hashcat -m 0 hash.txt --show

# 7
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l --potfile-disable

# 8
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --status --status-timer=5
```
