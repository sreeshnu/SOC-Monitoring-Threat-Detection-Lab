# SOC Monitoring & Threat Detection Lab

## What is this project?

This is a small SOC (Security Operations Center) lab that I built to get hands-on experience and understand how a SOC works.

In a real SOC, security tools collect information from computers, look for unusual activity, and show alerts to the security team. I wanted to understand that process by building a simple lab of my own.

I used **Wazuh** to collect and check security logs from a Windows machine. I then created different test situations and checked whether Wazuh could detect them.

The basic idea is:

```text
Something happens on Windows
        ↓
Windows creates a security log
        ↓
Wazuh Agent sends the log
        ↓
Wazuh Server receives and checks it
        ↓
Wazuh creates an alert when a rule matches
        ↓
I investigate the alert
```

---

## Why did I build this?

I did not want to learn SOC work only from theory.

I wanted to see what actually happens when a suspicious activity takes place on a computer:

- How does Windows record the activity?
- How does a security tool receive that information?
- How does the tool decide that something is suspicious?
- What does the alert look like?
- How can a SOC analyst investigate it?

This lab was built to answer those questions by doing the work myself.

---

## Lab setup

I used three main systems in the lab:

| System | What I used it for |
|---|---|
| **Windows** | The machine where security activity happens and Windows creates the logs |
| **Kali Linux** | Used to generate test and attack-like activity against the Windows machine |
| **Ubuntu** | Used to run the Wazuh server |

I used **VirtualBox** to run the virtual machines.

### Why are these machines needed?

**Windows** is the system I want to monitor. When someone logs in, fails to log in, creates a user, connects through RDP, or performs other actions, Windows can record those activities in its event logs.

**Kali Linux** is used as the testing machine. From here I can create controlled attack-like activity in the lab and then check whether that activity appears in the Windows logs.

**Ubuntu** runs the Wazuh server. Wazuh receives the logs, checks them against its rules, and shows the results as alerts.

---

## Tools used

- **Wazuh** – the main security monitoring tool used in this lab.
- **Wazuh Agent** – the small Wazuh component installed on Windows. Its job is to collect information from the Windows machine and send it to the Wazuh server.
- **Wazuh Server / Manager** – the main Wazuh system running on Ubuntu. It receives logs, checks them, and creates alerts.
- **Windows Event Logs** – records created by Windows about things happening on the system.
- **VirtualBox** – used to run the lab machines as virtual machines.
- **Kali Linux** – used to generate controlled testing activity.

---

## How the lab is connected

```text
┌──────────────────┐
│   Kali Linux     │
│                  │
│ Test activity    │
└────────┬─────────┘
         │
         │ Activity happens
         ▼
┌──────────────────┐
│     Windows      │
│                  │
│ Wazuh Agent      │
│ Windows Logs     │
└────────┬─────────┘
         │
         │ Security logs
         ▼
┌──────────────────┐
│ Ubuntu           │
│                  │
│ Wazuh Server     │
│ Rules + Alerts   │
└────────┬─────────┘
         │
         ▼
   SOC investigation
```

This is the main idea behind the whole project.

---

# Building the lab

## 1. Set up the virtual machines

The first step was to prepare the machines needed for the lab.

I used VirtualBox so that the Windows, Kali Linux, and Ubuntu systems could be run separately without needing three physical computers.

The important thing here is that the machines must be able to communicate with each other on the lab network.

### Why?

Kali needs to communicate with Windows so that I can create test activity.

Windows needs to communicate with the Ubuntu Wazuh server so that its logs can be sent for monitoring.

---

## 2. Install Wazuh on Ubuntu

I used Ubuntu as the machine for the Wazuh server.

The Wazuh server is the central part of the lab. It is where the incoming logs are processed and where the alerts are generated.

### Why install Wazuh here?

If the monitoring tool was running on the same Windows machine that I was testing, it would be harder to understand the idea of central monitoring.

By keeping Wazuh on a separate machine, the setup is closer to the basic idea of a SOC:

```text
Computer being monitored
          ↓
       Sends logs
          ↓
 Central security system
          ↓
      Creates alerts
```

---

## 3. Install the Wazuh Agent on Windows

After setting up the Wazuh server, I installed the Wazuh Agent on the Windows machine.

The agent acts as the connection between Windows and the Wazuh server.

It collects the required Windows information and sends it to Wazuh.

### Why is the Agent needed?

The Wazuh server needs information from the Windows machine to know what is happening there.

The agent helps collect that information and send it to the server.

---

## 4. Configure Windows log collection

Windows creates many different logs.

For this lab, the focus was mainly on security-related activity recorded by Windows Event Logs.

The Wazuh Agent was configured to collect the required Windows events and send them to the Wazuh server.

### Why?

A SOC cannot investigate an event if the monitoring system never receives information about it.

So the first important part of detection is making sure the right logs reach the monitoring system.

---

# Testing the setup

Once the basic monitoring setup was ready, I started creating controlled test situations.

The purpose was not simply to generate alerts. I wanted to follow the complete process:

```text
Create activity
      ↓
Check what Windows recorded
      ↓
Check whether Wazuh received it
      ↓
Check whether Wazuh detected it
      ↓
Look at the alert
      ↓
Understand why the alert was created
```

---

## Test 1 – Repeated failed logins

I created repeated failed login attempts against the Windows machine.

### Why test this?

If someone repeatedly tries different passwords, it can be a sign that they are trying to break into an account.

A SOC should be able to notice this type of activity.

### What I checked

1. Did Windows record the failed login?
2. Did the Wazuh Agent send the event?
3. Did Wazuh create an alert?
4. What information was shown in the alert?
5. Could the activity be connected to repeated login failures?

---

## Test 2 – RDP activity

I also tested Remote Desktop Protocol (RDP) login activity.

**RDP** is a Windows feature that allows someone to connect to a Windows computer remotely.

### Why test RDP?

Remote access is common in real networks. At the same time, attackers may try to abuse remote access to get into a computer.

Because of that, RDP-related login activity is useful to monitor.

I checked the Windows logs and Wazuh alerts to see how this activity appeared in the monitoring system.

---

## Test 3 – Creating a new Windows user

I tested the creation of a new local user account on Windows.

### Why test this?

An attacker who gets access to a computer may create another account so that they can come back later.

A new account is not automatically malicious, because administrators may create accounts for normal reasons. But unexpected account creation is something a SOC analyst should investigate.

During testing, I found that this activity needed clearer detection through a custom Wazuh rule so that it could stand out as an important alert.

---

## Test 4 – Lateral movement

I also looked at activity related to movement between systems on the lab network.

**Lateral movement** simply means moving from one computer to another after getting access to a network.

### Why test this?

An attacker may not stop after getting access to one computer. They may try to reach other computers on the same network.

I looked at authentication activity and the source information in the logs to understand whether activity across systems could be connected.

---

## Test 5 – Windows event log tampering

I tested activity involving the Windows event logs themselves.

### Why test this?

Logs are important because they tell a SOC analyst what happened.

An attacker may try to remove or interfere with logs to make their activity harder to investigate.

I checked whether Wazuh could notice changes related to the files being monitored by its File Integrity Monitoring feature.

**File Integrity Monitoring (FIM)** means watching important files and checking whether they have changed.

---

# Detection and alerts

One of the main things I wanted to understand was how an ordinary Windows event becomes a security alert.

A simplified example is:

```text
Failed login
     ↓
Windows records the event
     ↓
Wazuh Agent collects it
     ↓
Wazuh Server receives it
     ↓
Wazuh checks its rules
     ↓
Matching rule found
     ↓
Alert appears
```

The important lesson is that **a log and an alert are not exactly the same thing**.

A log is a record of something that happened.

An alert is Wazuh saying, in effect:

> "This event matches something I have been told to look for, so you should take a look at it."

That difference is an important part of understanding SOC work.

---

# Custom detection rule

During the testing, I found that some activity was not presented as clearly as I wanted in the default Wazuh rules.

One example was Windows user account creation.

So I worked with a custom Wazuh rule to make this type of event easier to identify and investigate.

This is one of the parts of the project that helped me understand **detection engineering**.

In simple words, detection engineering means creating or improving the rules that help a security tool notice suspicious activity.

---

# What I learned

This project helped me understand that SOC work is not just about looking at a dashboard.

I learned the basic flow from an activity happening on a computer to an alert being shown to the analyst.

The main things I learned were:

- How Windows records security-related activity.
- How a Wazuh Agent collects information from a Windows machine.
- How a central Wazuh server receives and checks those logs.
- How detection rules turn certain events into alerts.
- Why the same type of activity may need different levels of investigation.
- Why good log collection is important before detection can work.
- How custom rules can improve detection.
- How an analyst can start with an alert and work backwards to understand what happened.

---

# What I wanted to understand from this project

The main goal was simple:

**I wanted to stop learning SOC only from theory and actually see how a SOC monitoring system works.**

This lab gave me a way to create activity, see the logs produced by that activity, watch Wazuh process the logs, and investigate the resulting alerts.

That hands-on process is the main reason I built this project.

---

## Safety note

All testing described in this project was done in a controlled lab environment using virtual machines. The activities were performed for learning and security testing purposes.
