# SSH-Brute-Force-Detection-and-Response-Lab-Using-Splunk
Hands-on SOC lab demonstrating SSH brute-force detection using Splunk SIEM, Ubuntu authentication logs, Kali Linux Hydra, and UFW firewall-based response.
# SSH Brute Force Detection & Response using Splunk SIEM

## 📌 Project Overview

A hands-on SOC lab demonstrating SSH brute-force detection using Splunk Enterprise.

Simulated SSH login attempts using Hydra from Kali Linux, monitored Ubuntu authentication logs through Splunk Universal Forwarder, detected repeated login failures using SPL, and manually blocked the suspicious IP using UFW.

## 🏗️ Lab Environment

| Component | Configuration |
|---|---|
| SIEM Server | Windows 11 – Splunk Enterprise |
| Victim | Ubuntu 22.04.5 LTS |
| Attacker | Kali Linux 2026 |
| Log Collection | Splunk Universal Forwarder |
| Attack Tool | Hydra |
| Firewall | UFW |

## 🔄 Lab Workflow

```text
Kali Linux (Hydra)
        |
        | SSH Login Attempts
        v
Ubuntu 22.04 (Victim)
        |
        | /var/log/auth.log
        v
Splunk Universal Forwarder
        |
        v
Splunk Enterprise (Windows 11)
        |
        v
SSH Brute Force Detection
        |
        v
Identify Suspicious Source IP
        |
        v
Manual IP Blocking using UFW
```

## 🔎 Splunk Detection Query

**Original SPL Query:**

```spl
host="Ubuntu22" source="/var/log/auth.log" "Failed password"
| stats count by src_ip
| where count >= 5
| sort -count
```

**Query Breakdown:**

- `host`: Filters events from the Ubuntu machine.
- `source`: Searches the authentication log.
- `"Failed password"`: Matches failed SSH password attempts.
- `stats count by src_ip`: Counts failures from each source IP.
- `where count >= 5`: Filters IPs with five or more failures.
- `sort -count`: Displays the highest failure count first.

**Detection threshold:** 5 or more failed attempts per source IP within the selected search time range.

**Technical note:** The original query requires `src_ip` to be extracted by Splunk. If unavailable, the following suggested version extracts it from the raw event:

```spl
host="Ubuntu22" source="/var/log/auth.log" "Failed password"
| rex "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| stats count by src_ip
| where count >= 5
| sort -count
```

## 🚨 Detection & Response

**Detection:**
- Generated SSH authentication failures using Hydra.
- Forwarded Ubuntu authentication logs to Splunk.
- Used SPL to identify repeated failed login attempts.
- Configured a custom Splunk alert.

**Response:**

Identified the suspicious source IP and manually blocked SSH traffic using UFW.

```bash
sudo ufw deny from <Attacker-IP> to any port 22 proto tcp
```

Verify the firewall rule:

```bash
sudo ufw status numbered
```

*Note: IP blocking is performed manually, not automatically through Splunk.*

## 📸 Lab Screenshots

Add actual screenshots demonstrating:

1. Ubuntu SSH authentication logs.
2. Splunk log ingestion.
3. SPL detection results.
4. Custom alert configuration.
5. UFW firewall blocking rule.

## 🎯 Skills Demonstrated

- Splunk Enterprise and SIEM monitoring
- Splunk Universal Forwarder
- SPL search and event analysis
- Linux authentication log monitoring
- SSH brute-force detection
- Custom alert configuration
- Incident investigation
- UFW firewall mitigation

## 🗺️ MITRE ATT&CK

| Tactic | Technique |
|---|---|
| Credential Access | T1110.001 – Password Guessing |

[MITRE ATT&CK Reference](https://attack.mitre.org/techniques/T1110/001/)

## 🚀 Future Improvements

- Splunk security dashboard.
- Time-based brute-force detection.
- Improved detection and field extraction.
- Automated response integration.
- Additional SSH authentication detections.

## ⚠️ Disclaimer

This lab was developed for educational purposes and authorized security testing in a controlled environment.

## 👨‍💻 Author

**Abhijith N V**  
B.Tech IT Graduate | Cybersecurity Learner | SOC Analyst Aspirant
