# Lab Report: Automated Threat Detection via Custom Snort IDS Signatures

🎯 Objective
To transition from manual network forensics to automated defensive monitoring by architecting signature-based detection rules for an Intrusion Detection System (IDS). This lab outlines the creation of custom rules designed to mitigate network reconnaissance and application-layer exploits.

🛠️ Architecture & Logic
Snort utilizes signature-based analytics to evaluate incoming raw packet data. Rules are structured via static headers to track routing vectors and modular option blocks to perform deep packet inspection (DPI) on raw data payloads.

---

 🛡️ Engineered Rule Implementations

### 1. Reconnaissance Detection (ICMP Ping Sweep)
Designed to alert the SOC whenever an external adversary attempts network mapping via host discovery pings.
```text
alert icmp any any -> 192.168.140.128 any (msg:"ICMP Ping Scan Detected - Potential Network Reconnaissance"; sid:1000002; rev:1;)
2. Remote Command Injection Detection (System Files)
Monitors web traffic on Port 80 (HTTP) to intercept exploitation attempts targeting host system authentication files (/etc/passwd).

Plaintext
alert tcp any any -> 192.168.140.128 80 (msg:"CRITICAL: Remote Command Injection - /etc/passwd Access Attempt"; content:"cat /etc/passwd"; nocase; sid:1000003; rev:1;)
3. Web Reconnaissance Identification (whoami)
Catches initial post-exploitation activity where an attacker executes host environment discovery commands.

Plaintext
alert tcp any any -> 192.168.140.128 80 (msg:"WARNING: Web Reconnaissance - whoami Command Executed"; content:"whoami"; nocase; sid:1000004; rev:1;)
4. Data Exfiltration Mitigation (mysqldump)
High-severity rule written to flag malicious attempts to export localized SQL databases via command execution payloads.

Plaintext
alert tcp any any -> 192.168.140.128 80 (msg:"CRITICAL: Database Export Attempt - mysqldump Command Executed"; content:"mysqldump"; nocase; sid:1000005; rev:1;)
🧠 Key Defensive Takeaways
Deep Packet Inspection (DPI): By evaluating packet payload contents (content:"..."), defenders can look past simple IP/Port blocks and inspect the exact data payloads being delivered.

False Positive Reduction: Fine-tuning variables like nocase ensures comprehensive pattern matching across a wide array of modified attacker injection variations.
