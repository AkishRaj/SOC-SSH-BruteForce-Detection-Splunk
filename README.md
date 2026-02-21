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

### Hydra Attack Running
![Hydra Attack](screenshots/hydra_attack.png)

### Splunk Search Results
![Splunk Search](screenshots/splunk_search.png)

### SOC Dashboard
![Dashboard](screenshots/dashboard.png)

---

## 🔑 Key Findings

- **60+ failed login attempts** generated in under 2 minutes
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

**Your Name**
- LinkedIn: [your linkedin url]
- GitHub: [your github url]

---

## 🏷 Tags

`Cybersecurity` `SOC` `Splunk` `SIEM` `Hydra` `BruteForce` 
`LogAnalysis` `KaliLinux` `EthicalHacking` `BlueTeam`
```

---

## Step 4 — Create Screenshots Folder

1. On GitHub → Click **Add File → Create New File**
2. Type: `screenshots/.gitkeep`
3. Commit it
4. Now upload your 6 screenshots into the `screenshots` folder

---

## Step 5 — Upload auth.log

1. GitHub → **Add File → Upload Files**
2. Upload your `auth.log` file
3. This shows recruiters **real evidence** of your work

---

## Step 6 — Add Topics to Repository

1. On your repo page click **⚙️ gear icon** next to About
2. Add these topics:
```
cybersecurity splunk siem soc brute-force kali-linux 
hydra log-analysis blue-team ethical-hacking
```


