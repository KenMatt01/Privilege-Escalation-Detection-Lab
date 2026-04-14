# Incident Report: Privilege Escalation via Sudo Misconfiguration

![Type](https://img.shields.io/badge/Incident%20Type-Privilege%20Escalation-red) ![Severity](https://img.shields.io/badge/Severity-High-critical) ![Status](https://img.shields.io/badge/Status-Resolved-brightgreen) ![MITRE](https://img.shields.io/badge/MITRE-T1548.003%20%7C%20T1136-blue)

---

## 1. Executive Summary

A privilege escalation attack was simulated by exploiting a misconfigured sudo rule that allowed execution of the `find` binary without authentication. The attacker gained root access and performed persistence actions, including creating a backdoor user. The activity was successfully detected and analyzed using **Wazuh SIEM**.

---

## 2. Incident Details

| Field                  | Details                                      |
|------------------------|----------------------------------------------|
| **Incident Type**      | Privilege Escalation                         |
| **MITRE ATT&CK**       | [T1548.003 – Abuse Elevation Control Mechanism](https://attack.mitre.org/techniques/T1548/003/) |
|                        | [T1136 – Create Account](https://attack.mitre.org/techniques/T1136/) |
| **Severity Level**     | High                                         |
| **Detection Source**   | Wazuh SIEM                                   |
| **Log Source**         | `/var/log/auth.log`                          |

---

## 3. Timeline of Events

| Time (UTC) | Event Description                             |
|------------|-----------------------------------------------|
| **T0**     | Analyst user created                          |
| **T1**     | Misconfigured sudo rule applied               |
| **T2**     | Privilege escalation via `find` binary        |
| **T3**     | Root session established                      |
| **T4**     | Backdoor user created                         |
| **T5**     | Password changed                              |
| **T6**     | SIEM alerts triggered                         |

---

## 4. Detection & Analysis

### 🔍 Detection

Wazuh SIEM generated alerts indicating:

- Successful sudo to root
- Root session activity
- New user creation
- Password modification

### 📋 Log Evidence

The following entries were observed in `/var/log/auth.log`:

```
sudo: analyst : COMMAND=/usr/bin/find
sudo: pam_unix(sudo:session): session opened for user root
```

### 🧠 Analysis

| Field               | Details                                              |
|---------------------|------------------------------------------------------|
| **Attack Vector**   | Misconfigured sudo rule (`NOPASSWD: /usr/bin/find`)  |
| **Escalation Method** | GTFOBins abuse — `find` binary used to spawn shell |
| **Resulting Access** | Full root privileges                                |
| **Persistence**     | Backdoor user account created post-escalation       |

---

## 5. Impact Assessment

| Area                        | Status                             |
|-----------------------------|------------------------------------|
| Unauthorized Root Access    | ⚠️ Obtained                       |
| Persistence Mechanism       | ⚠️ Established (backdoor user)    |
| Password Modification       | ⚠️ Detected                       |
| Full System Compromise      | ⚠️ Potential risk identified      |

> 🔴 This attack chain — escalation → persistence → credential modification — reflects a realistic post-compromise scenario that could lead to full system takeover if undetected.

---

## 6. Response Actions

1. **Removed** the misconfigured sudo rule from the analyst account
2. **Deleted** the unauthorized backdoor user account
3. **Reviewed** system logs for any additional malicious activity

---

## 7. Recommendations

| Priority  | Recommendation                                              |
|-----------|-------------------------------------------------------------|
| 🔴 High   | Enforce least privilege — restrict sudo to necessary commands only |
| 🔴 High   | Audit all sudo rules regularly for misconfigurations        |
| 🟠 Medium | Monitor and alert on all privilege escalation attempts      |
| 🟠 Medium | Implement MFA and enhanced logging controls                 |
| 🟡 Low    | Cross-reference sudo rules against GTFOBins periodically    |

---

## 8. Conclusion

The attack demonstrated how misconfigured privileges can lead to full system compromise. Effective SIEM monitoring and log analysis enabled timely detection and response, reflecting real-world SOC operations. This incident underscores the importance of the **least privilege principle** and continuous configuration auditing as core elements of a secure environment.

---

> ⚠️ **Disclaimer:** This incident report documents a simulated attack conducted in a controlled, isolated lab environment for educational purposes only.
