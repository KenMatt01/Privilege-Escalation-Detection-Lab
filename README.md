# Privilege Escalation Detection Lab (Wazuh SIEM)

![Security](https://img.shields.io/badge/Category-Cybersecurity-red) ![SIEM](https://img.shields.io/badge/SIEM-Wazuh-blue) ![MITRE](https://img.shields.io/badge/MITRE-T1548.003%20%7C%20T1136-orange) ![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

## 📌 Overview

This project simulates a privilege escalation attack on a Linux system through a misconfigured sudo rule and demonstrates detection, analysis, and response using **Wazuh SIEM**. The lab replicates a real-world post-compromise scenario where an attacker gains elevated privileges and establishes persistence.

---

## 🧠 Lab Architecture

| Role          | System                        |
|---------------|-------------------------------|
| **Target**    | Ubuntu Server (Wazuh Agent)   |
| **SIEM**      | Wazuh (Manager + Dashboard)   |
| **Scenario**  | Local privilege escalation + persistence |

---

## ⚔️ Attack Simulation

A low-privileged user was created and granted misconfigured sudo permissions:

```bash
analyst ALL=(ALL) NOPASSWD: /usr/bin/find
```

Privilege escalation was achieved by abusing the allowed binary:

```bash
sudo find / -exec /bin/bash \; -quit
```

This resulted in **root-level access**.

---

## 💣 Post-Exploitation Activity

After escalation, the following actions were performed:

- Created a backdoor user
- Modified credentials
- Accessed sensitive files (`/etc/shadow`)

---

## 🔍 Detection (Wazuh SIEM)

Wazuh successfully detected:

- Privilege escalation via sudo
- Root session creation
- New user creation (persistence)
- Password changes

### MITRE ATT&CK Mapping

| Technique ID | Name |
|---|---|
| [T1548.003](https://attack.mitre.org/techniques/T1548/003/) | Abuse Elevation Control Mechanism: Sudo and Sudo Caching |
| [T1136](https://attack.mitre.org/techniques/T1136/) | Create Account |

---

## 📊 Log Analysis

Relevant logs were observed in:

```
/var/log/auth.log
```

**Example log entries:**

```
sudo: analyst : COMMAND=/usr/bin/find
sudo: pam_unix(sudo:session): session opened for user root
```

---

## 🔄 SOC Workflow

```
1. Privilege escalation via sudo misconfiguration
        ↓
2. Log generation (auth.log)
        ↓
3. Log ingestion (Wazuh agent → manager)
        ↓
4. Alert generation (SIEM rules triggered)
        ↓
5. Investigation (log correlation + user activity analysis)
        ↓
6. Response (remove sudo access, delete backdoor user)
```

---

## 🛡️ Response Actions

1. **Removed** misconfigured sudo permissions from the analyst account
2. **Deleted** unauthorized backdoor account
3. **Enforced** least privilege principle across user accounts

---

## 📸 Screenshots

### Privilege Escalation
<img width="900" height="723" alt="Screenshot 2026-04-14 203105" src="https://github.com/user-attachments/assets/583029fd-7fd7-4f6f-abf3-003e51f9bb20" />


### Authentication Logs
<img width="1866" height="809" alt="Screenshot 2026-04-14 210017" src="https://github.com/user-attachments/assets/4f706bbb-6bcb-491e-808f-c79ee2e18fd4" />


### Wazuh Alerts
<img width="1840" height="801" alt="Screenshot 2026-04-14 210218" src="https://github.com/user-attachments/assets/6e615d03-7bb8-4761-ba2a-487f27d6bfe8" />
<img width="1230" height="346" alt="Screenshot 2026-04-14 211325" src="https://github.com/user-attachments/assets/eb98034d-ec29-486d-85c8-f01169607008" />


---

## 🧠 Skills Demonstrated

| Skill | Description |
|---|---|
| SIEM Monitoring | Alert detection and analysis with Wazuh |
| Privilege Escalation Detection | Identifying sudo abuse and root session creation |
| Log Analysis | Parsing and correlating Linux `auth.log` entries |
| Threat Detection & Correlation | Linking events across the attack chain |
| MITRE ATT&CK Mapping | Classifying techniques using the ATT&CK framework |
| Incident Response | Containing and remediating post-compromise activity |

---

## 🎯 Why This Matters

Privilege escalation is a critical step in most cyber attacks. Detecting and responding to unauthorized elevation of privileges is essential for preventing full system compromise.

This lab demonstrates real-world SOC operations involving **post-compromise detection and response** — from initial escalation through persistence and remediation.

---

## 🚀 Future Improvements

- [ ] Add detection for additional escalation vectors (SUID binaries, cron jobs)
- [ ] Implement automated response via Wazuh active response module
- [ ] Extend lab to cover lateral movement detection

---

> ⚠️ **Disclaimer:** This project was conducted in a controlled, isolated lab environment for educational purposes only. Do not attempt to replicate these techniques on systems you do not own or have explicit permission to test.
