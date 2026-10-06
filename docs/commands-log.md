# Commands Log (Step 9)

Every command executed during the lab, in order, with purpose and corresponding screenshot.

---

### Step 1 — Install & Verify Hashcat

```bash
hashcat --version
```
**Purpose:** Confirm Hashcat is installed and functioning.
**Result:** `v7.1.2`

---

### Step 2 — Identify Hash Type

```bash
hashid 5f4dcc3b5aa765d61d8327deb882cf99
```
**Purpose:** Determine the likely hash algorithm from the sample hash.
**Result:** 32-character hex digest — candidate algorithms included MD2, **MD5**, MD4, Double MD5, LM, NTLM, etc. Confirmed as **MD5** (Hashcat mode `-m 0`) per the lab's hash-type scope.

📸 `screenshots/02-hashcat-version-hashid.png`

---

### Step 3 — Prepare & Verify Hash File

```bash
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
cat hash.txt
```
**Purpose:** Save the target hash into a file Hashcat can consume, and verify its contents.
**Result:** `hash.txt` contains exactly one line: `5f4dcc3b5aa765d61d8327deb882cf99`

📸 `screenshots/03-hash-file-created.png`

---

### Step 4 — Configure Hashcat with Wordlist

```bash
ls -la /usr/share/wordlists/
```
**Purpose:** Verify the approved wordlist (`rockyou.txt`) exists and check whether it needs extracting.
**Result:** `rockyou.txt` present and already extracted (139,921,507 bytes). No `gzip -d` needed.

📸 `screenshots/04-wordlist-check.png`

---

### Step 5 — Dictionary-Based Password Audit

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```
**Purpose:** Run a straight dictionary attack using the approved wordlist against the MD5 hash.

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

📸 `screenshots/05-dictionary-attack-1.png`, `screenshots/06-dictionary-attack-2.png`

---

### Step 7 — Display & Verify Recovered Password

```bash
hashcat -m 0 hash.txt --show
```
**Purpose:** Re-display the cracked hash:password pair from Hashcat's potfile to verify the result.
**Result:** `5f4dcc3b5aa765d61d8327deb882cf99:password`

📸 `screenshots/07-dictionary-attack-show.png`

---

### Step 6 — Mask-Based Password Audit

No predefined mask pattern was supplied by the instructor, so an 8-character all-lowercase mask was chosen (matching the known recovered password's length/character set) to demonstrate brute-force/mask auditing.

**First attempt:**
```bash
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l
```
**Result:** Hashcat returned immediately —
```
INFO: All hashes found as potfile and/or empty entries! Use --show to display them.
```
This is expected behavior: the hash was already cracked and cached in Hashcat's potfile from Step 5, so Hashcat skipped re-cracking it.

📸 `screenshots/08-mask-attack-potfile.png`

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

📸 `screenshots/09-mask-attack-fresh-1.png`, `screenshots/10-mask-attack-fresh-2.png`

---

### Step 8 — Monitor Attack Status (live progress demo)

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --status --status-timer=5
```
**Purpose:** Demonstrate Hashcat's automatic status-reporting feature (`--status`, `--status-timer=5`) for monitoring attack mode, hash rate, progress, and recovery status at regular intervals during longer-running attacks.
**Result:** Hash already in potfile, so Hashcat reported it as already cracked without re-running:
```
INFO: All hashes found as potfile and/or empty entries! Use --show to display them.
```

📸 `screenshots/11-status-timer-monitor.png`

> For this specific hash/wordlist combination the crack is near-instant, so the periodic status timer never had the chance to fire mid-run. On a larger keyspace or slower hash algorithm (e.g. bcrypt, `-m 3200`), `--status-timer` would print recurring progress/speed/ETA snapshots throughout the run — this is the standard technique for monitoring longer audits.

---

## Full Command Reference (copy-paste order)

```bash
# Step 1
hashcat --version

# Step 2
hashid 5f4dcc3b5aa765d61d8327deb882cf99

# Step 3
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
cat hash.txt

# Step 4
ls -la /usr/share/wordlists/

# Step 5
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

# Step 7
hashcat -m 0 hash.txt --show

# Step 6
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l
hashcat -m 0 -a 3 hash.txt ?l?l?l?l?l?l?l?l --potfile-disable

# Step 8
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --status --status-timer=5
```
