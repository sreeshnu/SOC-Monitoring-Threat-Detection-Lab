# SOC Monitoring & Threat Detection Lab

A centralized Security Operations Center (SOC) lab built around **Wazuh**, designed to collect, monitor, and analyze security logs from Windows endpoints — then validate detection coverage by simulating real attack techniques.

---

## 🎯 Objective

Set up an end-to-end log monitoring pipeline and confirm that common attacker behaviors (brute-force login, RDP access, account creation, lateral movement, log tampering) actually trigger alerts — not just theoretically, but by generating and observing them.

## 🧰 Tools & Stack

| Category | Tools |
|---|---|
| SIEM / Log Management | Wazuh (Manager + Agent) |
| Endpoints | Windows 10/11 VM |
| Virtualization | VirtualBox |
| Attack Simulation | Manual brute-force scripts, RDP tools, native Windows account creation |

## 🏗️ Lab Architecture

```
┌─────────────────┐        logs        ┌──────────────────┐
│ Windows Endpoint │ ──────────────────▶│  Wazuh Manager    │
│ (Wazuh Agent)    │                     │  (Rules Engine +  │
└─────────────────┘                     │   Dashboard)       │
                                          └──────────────────┘
                                                   │
                                                   ▼
                                          Alerts & Dashboards
```

## 🔍 What Was Done

1. **Deployed Wazuh Manager** on a dedicated host and installed the **Wazuh Agent** on Windows endpoints.
2. **Configured log forwarding** for Windows Event Logs (Security, System, Application channels).
3. **Simulated attack scenarios**, including:
   - Repeated failed logins (brute-force simulation)
   - Remote Desktop (RDP) access attempts
   - Creation of new local user accounts
   - Lateral movement between hosts on the lab network
   - Tampering with/clearing of Windows event logs
4. **Reviewed generated alerts** in the Wazuh dashboard, mapping each simulated action to the corresponding detection rule.
5. **Tuned detection rules** where default coverage was too noisy or missed an event, and documented the before/after alert behavior.

## 📊 Key Findings

- Default Wazuh rules reliably caught brute-force and RDP login attempts, but **account creation events needed a custom rule** to surface clearly as high-priority alerts.
- **Log tampering attempts were detected** via Wazuh's file integrity monitoring (FIM) module watching the Windows Event Log files.
- Lateral movement was visible through a combination of authentication logs across multiple endpoints correlated by source IP.

## 🛡️ Skills Demonstrated

`SIEM Monitoring` · `Log Analysis` · `Detection Engineering` · `MITRE ATT&CK Mapping` · `Windows Security Logging` · `File Integrity Monitoring`

---

*Part of my cybersecurity portfolio — see more at [github.com/yourusername](https://github.com/yourusername)*
