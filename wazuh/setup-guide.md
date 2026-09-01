# Wazuh Setup Guide

This page explains the setup in simple terms.

The idea is:

```text
Windows
   ↓
Wazuh Agent
   ↓
Wazuh Server
   ↓
Rules
   ↓
Alerts
```

The exact package version can change. When rebuilding the lab, use the current Wazuh documentation for that release.

## 1. Prepare the Ubuntu machine

I used Ubuntu for the Wazuh central components.

Before installing Wazuh, the Ubuntu machine and Windows machine need to be able to communicate on the lab network.

### Why?

The Windows agent has to reach the Wazuh server. If the machines cannot communicate, the agent cannot send its data.

## 2. Install Wazuh

For a small lab, Wazuh provides an installation assistant that can install the central components on one host.

Current Wazuh quickstart example:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

After the installation finishes, the installer shows the dashboard address and login information.

### Why install the central components first?

I need the central monitoring system ready before I connect the Windows machine. That way I can check the agent connection immediately after installing it.

## 3. Install the Wazuh Agent on Windows

The Wazuh Agent runs on the Windows machine that I want to monitor.

It collects information from Windows and sends it to the Wazuh server.

A current Windows deployment can be done with the Wazuh MSI installer. Example:

```powershell
msiexec.exe /i .\wazuh-agent-<version>-1.msi /q WAZUH_MANAGER="<WAZUH_SERVER_IP>"
```

Replace the placeholders with the version and Wazuh server address used in the lab.

### Why is the agent needed?

The Wazuh server needs information from the endpoint. The agent is the part that collects the endpoint information and sends it to the server.

## 4. Check the Windows agent configuration

On a typical 64-bit Windows installation, the configuration file is:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

The manager address is configured inside the `<client>` section:

```xml
<client>
  <server>
    <address>WAZUH_MANAGER_IP</address>
  </server>
</client>
```

Replace `WAZUH_MANAGER_IP` with the Ubuntu Wazuh server address.

## 5. Start or restart the agent

After installation or configuration changes, start or restart the service.

PowerShell:

```powershell
Start-Service wazuh
```

or:

```powershell
Restart-Service -Name wazuh
```

Check the service:

```powershell
Get-Service wazuh
```

### Why check the service?

Installing the software does not automatically prove that monitoring is working. The service needs to be running before the agent can send information.

## 6. Check the agent in Wazuh

Open the Wazuh dashboard and check the agent list.

The Windows endpoint should appear there after successful enrollment and connection.

### Why check this before testing?

If the agent is not connected, Windows may create an event but Wazuh will not receive it. That would make it impossible to tell whether a detection problem is caused by the rule or simply by missing data.

## 7. Check Windows before blaming Wazuh

When a test does not produce an alert, I use this order:

```text
Did the activity actually happen?
          ↓
Did Windows create an event?
          ↓
Did the Wazuh Agent collect it?
          ↓
Did the Wazuh Server receive it?
          ↓
Did a rule match it?
          ↓
Was an alert created?
```

This makes troubleshooting much easier.

## 8. Common problems

### Agent service is not running

```powershell
Get-Service wazuh
```

Start it if needed:

```powershell
Start-Service wazuh
```

### Agent is not connected

Check:

- The Wazuh server address in the agent configuration.
- Network communication between Windows and Ubuntu.
- The Wazuh services on Ubuntu.
- The Windows agent service.

### Windows event exists but Wazuh does not show it

First check whether the event belongs to a log channel that the agent is collecting. Then check the agent configuration and agent logs.

Only after confirming that the event reached Wazuh should I start changing detection rules.

## Important note

Do not put real passwords, private keys, or other secrets in this repository. Use placeholders such as `<WAZUH_SERVER_IP>` when documenting the lab.
