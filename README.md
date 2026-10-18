# Wireless Attacks and Countermeasures: Wi-Fi Packet Analysis and WEP/WPA2 Cracking

A hands-on module covering wireless network security: analyzing 802.11 traffic in Wireshark, then using the Aircrack-ng suite to crack both a WEP-protected and a WPA2-protected network from sample packet captures.

## Scenario
Understanding wireless security requires seeing both how traffic looks on the air and how different encryption schemes actually fail. Since capturing live wireless traffic needs a physical adapter in monitor mode (not available in a virtualized lab), this module used pre-captured sample files to demonstrate the full workflow: analysis, then exploitation.

## What I did

### Lab 1: Wi-Fi packet analysis with Wireshark
- Opened a sample capture (`WEPcrack-01.cap`) in Wireshark to examine 802.11 wireless frames
- Noted that the 802.11 protocol label clearly identifies wireless traffic, which could otherwise be disguised to resemble standard Ethernet traffic
- Reviewed how Wireshark, with the Npcap library, can decode management/control/data frames and Radiotap header fields to surface encryption type, frame length, and MAC addresses — wireless reconnaissance before any attack is attempted

### Lab 2: Cracking WEP with Aircrack-ng
- Ran Aircrack-ng directly against the sample WEP capture:
```
aircrack-ng '/home/attacker/Desktop/Sample Captures/WEPcrack-01.cap'
```
- The WEP key was recovered immediately — no wordlist or brute force needed, because WEP's vulnerability is structural (RC4 + a short initialization vector), not password-dependent

### Lab 3: Cracking WPA2 with Aircrack-ng
- Ran a dictionary attack against a captured WPA2 handshake:
```
aircrack-ng -a2 -b [target BSSID] -w [wordlist path] '[WPA2 capture file]'
```
- `-a2` specified the WPA attack technique, `-b` the target access point's BSSID, `-w` the wordlist
- Aircrack-ng tested each wordlist entry against the captured four-way handshake and returned a `KEY FOUND!` result with the plaintext password

## Key takeaways
- WEP and WPA2 fail in fundamentally different ways: WEP's weakness is in the protocol's cryptographic design (RC4 + short IV), so no password strength saves it; WPA2's cryptography (CCMP/AES) holds up, and what's exploitable is a weak, guessable passphrase
- "We're using WPA2" isn't a security guarantee on its own — passphrase strength does as much work as the protocol itself
- A captured WPA2 handshake lets an attacker test candidate passwords entirely offline, without ever authenticating live against the real network — the attack's success is bounded only by the wordlist and the passphrase's actual strength
- WEP should be retired entirely; no configuration mitigates its core flaw
- WPA3 directly addresses the handshake weakness this lab exploited, via Simultaneous Authentication of Equals (SAE), making offline dictionary attacks against the handshake impractical even for weaker passwords

## Tools
Wireshark, Npcap, Aircrack-ng, Parrot Security OS

---
*Completed in an isolated EC-Council training lab environment using pre-captured sample Wi-Fi traffic files (no live wireless adapter or real network involved).*
