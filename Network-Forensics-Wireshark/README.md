 Lab Report: Packet Analysis & Protocol Forensics using Wireshark

🎯 Objective
To intercept, inspect, and forensically analyze raw network layer traffic running through a live virtual interface card. This lab focuses on understanding network packet anatomy, protocol behaviors, and analyzing cleartext transmissions versus encrypted channels—a core technical competency for a Tier-1 SOC Analyst.

🛠️ Environment & Tools
* **Attacker OS:** Kali Linux (Rolling Release)
* **Hypervisor:** VMware Workstation Pro
* **Network Interface:** Bridged/NAT (`eth0`)
* **Analysis Tool:** Wireshark Protocol Analyzer

---

## 🔍 Phase 1: Isolating DNS Core Infrastructure (Port 53)
Using Wireshark's display filtering mechanism, I isolated Domain Name System (DNS) traffic using the filter keyword: `dns`.

### Forensic Observations:
* **The Request:** My local host machine (`192.168.140.128`) initiated a `Standard Query` over **UDP Port 53** to the network gateway resolver (`192.168.140.2`) requesting address resolution for `www.google.com`.
* **The Response:** The DNS server responded with a `Standard Query Response` providing the direct A-record IP address string: `142.251.151.119`.
* **Security Insight:** Monitoring DNS resolution patterns allows a SOC Analyst to trace domain anomalies and identify potential Command and Control (C2) beaconing setups used by stealth malware.

---

## 🕵️‍♂️ Phase 2: Cleartext Exploitation & Stream Reconstruction (HTTP Port 80)
To observe how data exposure occurs over unencrypted transport protocols, I monitored traffic over the **Hypertext Transfer Protocol (HTTP)** by initializing a new live capture and applying the display filter: `http`.

### 🚨 Critical Vulnerability Identified: Raw Data Leakage
While capturing, my system generated background traffic hitting an unencrypted portal (`detectportal.firefox.com`). Because HTTP utilizes no native Transport Layer Security (TLS), all application data leaked directly onto the wire in plaintext.

By utilizing Wireshark's advanced **"Follow TCP Stream"** functionality, I reconstructed the fragmented communication packets into a human-readable session transcript:

### Extracted Raw Session Transcript:
```text
GET /success.txt?ipv4 HTTP/1.1
Host: detectportal.firefox.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Connection: keep-alive

HTTP/1.1 200 OK
Server: nginx
Content-Type: text/plain
Content-Length: 8

success
