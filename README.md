# SOC Monitoring & Threat Detection Lab

## Project overview

A controlled Windows/Kali/Ubuntu lab built to understand the basic SOC detection workflow: endpoint activity → Windows event logs → Wazuh Agent → Wazuh Manager → detection rule → alert → analyst triage.

The project focuses on understanding the data path rather than claiming that every test represents a production detection.

## Lab architecture

| System | Role |
|---|---|
| Windows | Monitored endpoint and source of Windows Security events |
| Kali Linux | Generates controlled test activity |
| Ubuntu | Runs the central Wazuh components |
| VirtualBox | Hosts the lab virtual machines |

```text
Windows endpoint
      │
      │ Windows event logs
      ▼
 Wazuh Agent
      │
      ▼
 Wazuh Manager
      │
      ▼
 Detection rule
      │
      ▼
    Alert
      │
      ▼
 Analyst triage / investigation
```

Keep the testing environment isolated and use only systems you own or are authorized to test.

## Test scenarios

The repository documents five controlled scenarios:

1. [Repeated failed logins](attack-scenarios/brute-force.md)
2. [RDP activity](attack-scenarios/rdp.md)
3. [Windows account creation](attack-scenarios/account-creation.md)
4. [Lateral-movement activity](attack-scenarios/lateral-movement.md)
5. [Windows event-log tampering](attack-scenarios/log-tampering.md)

These scenarios are detection exercises. A scenario description is not, by itself, evidence that an attack was successfully detected.

## What I built

- Windows event collection with the Wazuh Agent.
- Central processing with Wazuh.
- A custom detection rule for Windows Event ID **4720** (user account creation).
- MITRE ATT&CK mapping for account creation: **T1136**.
- A repeatable analyst workflow for validating whether an event reached Wazuh and whether the expected rule fired.

### Custom rule

The custom rule is stored at `wazuh/custom-rules/account-creation.xml`.

It matches `win.system.eventID` `4720`, assigns level 8, and maps the event to ATT&CK T1136. The rule ID is in Wazuh's custom-rule range. The rule should be validated against the actual event structure produced by the installed Wazuh/Windows configuration before reuse elsewhere.

## Validation workflow

For each scenario, the investigation path is:

```text
Generate controlled activity
        ↓
Confirm the Windows event exists
        ↓
Confirm the Wazuh Agent is connected
        ↓
Confirm the event reached the manager
        ↓
Test/identify the matching rule
        ↓
Review the alert fields
        ↓
Correlate related events
        ↓
Decide: expected, suspicious, or requires escalation
```

An alert is a signal for investigation, not automatic proof of compromise.

## Rebuilding the lab

Wazuh releases and installation commands change. For a fresh installation, use the current official Wazuh documentation rather than copying an old command unchanged.

Example installation commands are intentionally kept out of this README when they are release-specific. See `wazuh/setup-guide.md` for the lab notes.

## What I learned

- Windows event logs are a key source of endpoint security telemetry.
- A SIEM/EDR-style workflow is only useful when telemetry actually reaches the detection engine.
- Detection rules should be validated against real event data.
- An alert needs context and related events before an analyst can decide what it means.
- Custom rules are useful, but they must be tested to avoid false positives and incorrect assumptions.

## Evidence and scope

This repository documents the lab methodology and recorded results available from the project. It does not claim screenshots, raw event exports, or other evidence that is not stored in the repository.

## Safety

All testing is intended for a controlled lab. Do not test authentication, RDP, account creation, log tampering, or other security scenarios against systems without authorization.

Never commit passwords, private keys, API tokens, real credentials, or other sensitive data.

## References

- Wazuh documentation: https://documentation.wazuh.com/
- Wazuh custom rules: https://documentation.wazuh.com/current/user-manual/ruleset/rules/custom.html
- MITRE ATT&CK T1136: https://attack.mitre.org/techniques/T1136/
