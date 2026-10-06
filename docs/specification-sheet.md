# Specification Sheet — 05 | Password Hash Auditing Using Hashcat

**Time: 30 Minutes**

| Field | Detail |
|---|---|
| Time Allowed | 30 Minutes |
| Tools Required | Kali Linux, Hashcat, Approved Wordlist (rockyou.txt) |
| Target | Authorized Password Hash File |
| Hash Types | Sample MD5 / SHA-1 / SHA-256 / NTLM / bcrypt Hashes |
| Also Applicable | Linux `/etc/shadow` File or Windows SAM Hashes |

## Perform the Following Job Tasks

1. Install and configure Hashcat on Kali Linux and verify that the tool is functioning correctly.
2. Obtain the authorized password hash file and identify the hash type (e.g., MD5, SHA-1, SHA-256, bcrypt) and appropriate Hashcat mode.
3. Prepare and verify the authorized hash file for password auditing.
4. Configure Hashcat with the approved wordlist and required parameters.
5. Perform a dictionary-based password audit against the authorized hash.
6. Perform a mask-based password audit using the predefined lab pattern.
7. Display and verify recovered passwords against the original hashes.
8. Monitor and record attack mode, hash rate, progress, recovery status, and execution time.
9. Document commands, screenshots, findings, observations, and audit results.
10. Prepare a brief Password Security Assessment Report with recommendations for strengthening password security.

> Note: No predefined mask pattern was provided by the instructor for Task 6, so a representative 8-character lowercase mask (`?l?l?l?l?l?l?l?l`) was selected to demonstrate the technique, matching the length/character set of the recovered password.
