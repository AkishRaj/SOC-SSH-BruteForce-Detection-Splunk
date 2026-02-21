# SOC-SSH-BruteForce-Detection-Splunk
cybersecurity labs involving SIEM tools, brute-force detection, and SOC workflows

# 🔐 SOC Lab: SSH Brute Force Detection Using Splunk

![SIEM](https://img.shields.io/badge/SIEM-Splunk-orange)
![Attack](https://img.shields.io/badge/Attack-BruteForce-red)
![Tool](https://img.shields.io/badge/Tool-Hydra-blue)
![OS](https://img.shields.io/badge/OS-KaliLinux-purple)
![Status](https://img.shields.io/badge/Status-Completed-green)

---

## 📌 Project Overview

This project simulates a real-world SOC (Security Operations Center) environment
where an SSH brute-force attack is launched, logged, and detected using 
Splunk SIEM — exactly how security analysts work in real environments.

---

## 🎯 Objectives

- Simulate a real SSH brute-force attack using Hydra
- Generate and collect authentication failure logs
- Ingest logs into Splunk for analysis
- Detect attack patterns using SPL queries
- Build a SOC-style detection dashboard

---

## 🛠 Tools & Technologies

| Tool | Purpose |
|------|---------|
| Kali Linux | Attacker & Victim Machine |
| VirtualBox | Virtualization |
| Hydra | Brute-force attack tool |
| OpenSSH | SSH target service |
| Splunk Free | SIEM for log analysis |
| auth.log | Linux authentication log file |

---

## 🏗 Lab Architecture
```
+------------------+          SSH Brute Force          +-------------------+
|   Kali Linux     |  --------------------------------> |   Kali Linux      |
|   (Attacker)     |                                    |   (Victim/SSH)    |
|   Hydra Tool     |                                    |   auth.log        |
+------------------+                                    +-------------------+
                                                                |
                                                    Log File Exported
                                                                |
                                                                v
                                                    +-------------------+
                                                    |   Windows Host    |
                                                    |   Splunk SIEM     |
                                                    |   Dashboard       |
                                                    +-------------------+
```

---

## 📋 Step-by-Step Implementation

### Step 1 — Environment Setup
- Installed Kali Linux on VirtualBox
- Configured Network Adapter (NAT)
- Verified connectivity with `ip a`

### Step 2 — Enable SSH on Kali
```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```

### Step 3 — Create Target User
```bash
sudo adduser testuser
```

### Step 4 — Launch Brute Force Attack
```bash
hydra -l testuser -P /usr/share/wordlists/rockyou.txt ssh://192.168.66.109 -t 2 -V -W 3
```

**What this does:**
- `-l testuser` → Target username
- `-P rockyou.txt` → Password wordlist (14 million passwords)
- `-t 2` → 2 parallel threads
- `-V` → Verbose output showing each attempt
- `-W 3` → 3 second wait between attempts

### Step 5 — Extract Log File
```bash
sudo cp /var/log/auth.log /home/tester/auth.log
sudo chmod 644 /home/tester/auth.log
python3 -m http.server 8888
```

### Step 6 — Upload to Splunk
- Settings → Add Data → Upload
- Source type: `linux_secure`
- Index: `main`

---

## 🔍 SPL Queries Used

### Query 1 — All Failed Authentication Events
```
index=main sourcetype=linux_secure "failed authentication"
| table _time, _raw
```

### Query 2 — Count Attacks by Source IP
```
index=main sourcetype=linux_secure "failed authentication"
| rex "from \[(?<src_ip>\d+\.\d+\.\d+\.\d+)\]"
| stats count by src_ip
| sort -count
```

### Query 3 — Brute Force Detection (10+ attempts)
```
index=main sourcetype=linux_secure "failed authentication"
| rex "from \[(?<src_ip>\d+\.\d+\.\d+\.\d+)\]"
| stats count by src_ip
| where count > 10
```

### Query 4 — Attack Timeline
```
index=main sourcetype=linux_secure "failed authentication"
| timechart count as "Failed Attempts"
```

---

## 📊 Dashboard Panels

| Panel | Visualization | Purpose |
|-------|--------------|---------|
| Total Failed Attempts | Single Value | Quick attack count |
| Top Attacking IPs | Bar Chart | Identify attack source |
| Attack Timeline | Line Chart | See attack pattern over time |
| Raw Attack Logs | Table | Detailed forensic view |

---

## 📸 Screenshots

### SSH Enable
<img width="1120" height="543" alt="Screenshot 2026-02-21 181036" src="https://github.com/user-attachments/assets/9fd011d9-285d-428e-b3e7-0314d429231a" />


### Hydra Attack Running
<img width="1122" height="1079" alt="Screenshot 2026-02-21 183135" src="https://github.com/user-attachments/assets/22b29f19-9dae-438a-a800-cb10d4cdf784" />

### Splunk Search Results
<img width="1919" height="945" alt="Screenshot 2026-02-21 181228" src="https://github.com/user-attachments/assets/b6f0d06e-3a1e-46e7-a4f6-b9e8e1c9f176" />

### SOC Dashboard
<img width="1919" height="955" alt="Screenshot 2026-02-21 182444" src="https://github.com/user-attachments/assets/8494a102-6f03-4fbb-bac2-5cb28759f475" />


---

## 🔑 Key Findings

- **failed login attempts** generated in under 2 minutes
- All attempts originated from IP `192.168.66.109`
- Attack pattern shows rapid sequential password attempts
- Splunk successfully detected and visualized the brute-force pattern

---

## 📚 What I Learned

- How SSH brute-force attacks appear in real system logs
- How to use Splunk SPL queries for threat detection
- How SOC analysts build detection dashboards
- Importance of log monitoring in security operations

---

## ⚠️ Disclaimer

> This project was conducted in a controlled virtual lab environment
> for educational purposes only. All attacks were performed on
> machines I own. Never perform these attacks on systems
> you do not have explicit permission to test.

---

## 👤 Author

**Akish Raj.A**
- LinkedIn: [your linkedin url]


---

## 🏷 Tags

`Cybersecurity` `SOC` `Splunk` `SIEM` `Hydra` `BruteForce` 
`LogAnalysis` `KaliLinux` `EthicalHacking` `BlueTeam`
```


```
cybersecurity splunk siem soc brute-force kali-linux 
hydra log-analysis blue-team ethical-hacking
```


