# Wazuh Setup Guide

## What is this file for?

This file explains the basic setup of the Wazuh part of the lab.

The idea is simple:

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

The exact commands can change between Wazuh releases, so the official Wazuh installation guide should be used for the release being installed.

## 1. Prepare Ubuntu

Ubuntu is used as the machine that runs the Wazuh server.

Before installing Wazuh, I made sure the Ubuntu virtual machine could communicate with the Windows machine on the lab network.

### Why?

The Windows agent needs a network path to the Wazuh server. If the two machines cannot communicate, the server cannot receive the agent's data.

## 2. Install the Wazuh server

The Wazuh server is the central part of the lab. It receives data from the monitored endpoint and processes it for detection.

For the current installation procedure, use the official Wazuh documentation:

https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html

## 3. Install the Wazuh Agent on Windows

The Wazuh Agent is installed on the Windows machine that I want to monitor.

The agent collects information from Windows and sends it to the Wazuh server.

The current Windows agent documentation uses the Wazuh manager address during deployment. The installer can also be configured with an agent name, group, and enrollment settings.

Official guide:

https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html

## 4. Connect the agent to the server

The important setting is the address of the Wazuh manager.

A simplified example looks like this:

```xml
<client>
  <server>
    <address>WAZUH_MANAGER_IP</address>
  </server>
</client>
```

Replace `WAZUH_MANAGER_IP` with the address of the Wazuh server in the lab.

The Windows agent configuration file is normally located at:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

The actual location can depend on the Windows installation architecture.

## 5. Start or restart the agent

After changing the configuration, the Wazuh Agent service needs to be started or restarted so the new settings are used.

PowerShell example:

```powershell
Restart-Service -Name wazuh
```

## 6. Check the connection

The next step is to check the Wazuh dashboard and confirm that the Windows agent is connected.

I do not treat the installation as complete just because the software is installed.

The real test is:

```text
Agent installed
      ↓
Agent connected
      ↓
Windows event generated
      ↓
Wazuh receives event
      ↓
Event appears in monitoring
```

## Why this setup matters

Installing Wazuh is only the first part.

For a SOC lab, the useful part is seeing information move from the endpoint to a central monitoring system and then using that information during an investigation.

## Note

The commands and paths in this document are examples for explaining the project. Always check the current Wazuh documentation before applying installation commands to a real system.
