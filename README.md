# SOC Monitoring & Threat Detection Lab

## What is this project?

This is a small SOC lab that I built to get hands-on experience and understand how a SOC works.

I wanted to see what happens when suspicious activity takes place on a computer:

```text
Activity happens on Windows
        ↓
Windows records the event
        ↓
Wazuh Agent collects it
        ↓
Wazuh Server receives it
        ↓
Wazuh checks its rules
        ↓
Alert is created
        ↓
I investigate the alert
```

In simple words, I created activity in a controlled lab, watched Windows record it, sent the information to Wazuh, and then investigated what Wazuh showed me.

---

## Why did I build this?

I did not want to learn SOC work only from theory.

I wanted to understand:

- How Windows records security activity.
- How a monitoring tool gets those records.
- How a rule decides that an event is worth an alert.
- What information an alert contains.
- How a SOC analyst starts investigating an alert.

The main goal was simple:

> **Get hands-on experience and understand how a SOC works.**

---

## Lab setup

I used three main systems:

| System | Purpose |
|---|---|
| **Windows** | The computer I wanted to monitor |
| **Kali Linux** | Used to create controlled test activity |
| **Ubuntu** | Used to run the Wazuh central components |

I used **VirtualBox** to run the virtual machines.

### Why are these machines needed?

**Windows** is the endpoint. It creates the event records that I want to monitor.

**Kali Linux** is the testing machine. It lets me create controlled activity in the lab.

**Ubuntu** runs Wazuh. Wazuh receives the information from Windows, checks it against rules, and creates alerts.

---

## What did I test?

I worked with these test scenarios:

1. [Repeated failed logins](attack-scenarios/brute-force.md)
2. [RDP activity](attack-scenarios/rdp.md)
3. [Windows account creation](attack-scenarios/account-creation.md)
4. [Lateral movement](attack-scenarios/lateral-movement.md)
5. [Windows event-log tampering](attack-scenarios/log-tampering.md)

The point of each test was not just to create an alert. I wanted to understand the full path from the activity to the alert and then investigate it.

---

## Project structure

```text
SOC-Monitoring-Threat-Detection-Lab/
│
├── README.md
│
├── architecture/
│   └── lab-architecture.md
│
├── wazuh/
│   ├── setup-guide.md
│   ├── configuration/
│   │   └── windows-event-logs.md
│   └── custom-rules/
│       ├── README.md
│       └── account-creation.xml
│
├── attack-scenarios/
│   ├── brute-force.md
│   ├── rdp.md
│   ├── account-creation.md
│   ├── lateral-movement.md
│   └── log-tampering.md
│
└── investigations/
    ├── alert-investigation.md
    └── alert-triage-checklist.md
```

---

# How the lab works

## Step 1 — Something happens on Windows

For example, a login can fail or a new account can be created.

Windows records information about the activity in its event logs.

## Step 2 — Wazuh Agent collects the event

The Wazuh Agent runs on Windows.

Its job is to collect information from the endpoint and send it to the Wazuh server.

## Step 3 — Wazuh Server receives the event

The Wazuh server is the central part of the lab.

It receives the event and processes it.

## Step 4 — Wazuh checks its rules

A rule is basically a condition that tells Wazuh what kind of event it should pay attention to.

For example, this project contains a custom rule for Windows Event ID 4720, which means a user account was created.

## Step 5 — Wazuh creates an alert

When the event matches a rule, Wazuh can create an alert.

The alert is not automatically proof that an attack happened. It is a signal that the analyst should look at the event.

## Step 6 — I investigate

I then ask questions such as:

- What happened?
- When did it happen?
- Which computer was involved?
- Which account was involved?
- Was it expected?
- Are there other related events?

More detail is in [Alert Investigation](investigations/alert-investigation.md) and [Alert Triage](investigations/alert-triage-checklist.md).

---

# Building the lab

## 1. Prepare the virtual machines

I used VirtualBox to run Windows, Kali Linux, and Ubuntu as separate virtual machines.

The machines need to be able to communicate on the lab network.

### Why?

Kali needs to communicate with Windows for the test activity.

Windows needs to communicate with Ubuntu so that the Wazuh Agent can send its data.

More detail: [Lab Architecture](architecture/lab-architecture.md)

## 2. Install Wazuh

I used Ubuntu for the Wazuh central components.

For a current installation, Wazuh provides an installation assistant for a small setup. The exact command depends on the Wazuh release, so the [official Wazuh documentation](https://documentation.wazuh.com/current/quickstart.html) should be checked when rebuilding the lab.

Example from the current documentation:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

## 3. Install the Wazuh Agent on Windows

The agent runs on the Windows machine I want to monitor.

A current Windows deployment uses the Wazuh MSI installer and the Wazuh manager address. For example:

```powershell
msiexec.exe /i .\wazuh-agent-<version>-1.msi /q WAZUH_MANAGER="<WAZUH_SERVER_IP>"
```

The placeholders should be replaced with the values used in the lab.

More detail: [Wazuh Setup Guide](wazuh/setup-guide.md)

## 4. Configure Windows event collection

The Wazuh Agent can collect Windows event channels. The Security channel is especially useful for the login and account-related tests in this project.

More detail: [Windows Event Logs](wazuh/configuration/windows-event-logs.md)

## 5. Make sure the agent is connected

Before testing, I check that the Wazuh Agent is running and visible in the Wazuh dashboard.

This is important because there is no point investigating a detection rule if the Windows event never reached Wazuh.

---

# Testing and investigation

For each test, I follow the same basic process:

```text
Create test activity
        ↓
Check Windows Event Viewer
        ↓
Check that Wazuh receives the event
        ↓
Check the matching rule
        ↓
Read the alert
        ↓
Investigate the details
        ↓
Decide what the activity means
```

This makes it easier to find problems. If an alert is missing, I can check whether the problem is with the activity, Windows logging, the Wazuh Agent, the server, or the detection rule.

---

# Custom detection rule

I included a custom Wazuh rule for Windows account creation.

File:

```text
wazuh/custom-rules/account-creation.xml
```

The rule looks for Windows Security Event ID **4720**.

That event means a user account was created.

The rule also includes the MITRE ATT&CK mapping **T1136 – Create Account**.

More detail: [Custom Wazuh Rules](wazuh/custom-rules/README.md)

The rule should be tested with the actual event data from the Wazuh installation before being treated as production-ready.

---

# What I learned

This project helped me understand that SOC work is not just about looking at a dashboard.

I learned how:

- Windows records security activity.
- A Wazuh Agent collects endpoint information.
- A central Wazuh server receives and processes that information.
- Rules turn certain events into alerts.
- An alert is a starting point for investigation, not automatically proof of an attack.
- Looking at related events can give more information than looking at one event alone.
- Custom rules can make useful activity easier to identify.
- Troubleshooting should start with the event itself and follow the data until the alert is created.

---

## Safety note

All testing in this project is intended for a controlled lab environment. Do not use the testing steps against systems you do not own or do not have permission to test.

Also, do not commit passwords, private keys, real credentials, or other sensitive information to the repository.
