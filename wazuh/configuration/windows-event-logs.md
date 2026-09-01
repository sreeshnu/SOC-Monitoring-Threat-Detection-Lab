# Windows Event Log Collection

## What are Windows Event Logs?

Windows keeps records about many things that happen on a computer.

For example:

- Login attempts
- User account changes
- System changes
- Application activity
- Security events

These records are called **Windows Event Logs**.

## Why are they important in this lab?

The Windows machine is the system I want to monitor.

If something suspicious happens but the event is never collected, Wazuh has nothing useful to analyse.

So the flow starts here:

```text
Something happens on Windows
          ↓
Windows records the event
          ↓
Wazuh Agent collects it
          ↓
Wazuh Server receives it
```

## Windows event channels

Wazuh can collect Windows events through the Windows event channel.

The Wazuh documentation says that the agent monitors the **System, Application, and Security** event channels by default. Other channels can be added when they are useful for a particular project. citeturn0search0turn0search8

For this project, the Security channel is especially useful because it contains many security-related events.

## Agent configuration file

On a typical 64-bit Windows installation, the Wazuh Agent configuration file is:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

The Wazuh documentation uses this file for local Windows log-collection settings. citeturn0search0turn0search11

## Example: monitoring a Windows event channel

A simple Wazuh configuration block looks like this:

```xml
<localfile>
  <location>Security</location>
  <log_format>eventchannel</log_format>
</localfile>
```

This tells the agent to collect events from the Windows Security event channel.

## Why configure this?

Because the monitoring system needs the right information.

Think of it like a CCTV camera:

```text
Camera pointed at the wrong place
             ↓
      Important event happens
             ↓
        No useful evidence
```

The same basic idea applies to log collection.

If the right Windows events are not collected, detection becomes much harder.

## Collecting only specific events

Wazuh can also use event queries when only certain events are needed.

For example, Wazuh documentation shows that an event channel can be filtered by event ID using a query such as:

```xml
<localfile>
  <location>System</location>
  <log_format>eventchannel</log_format>
  <query>Event/System[EventID=7040]</query>
</localfile>
```

The important idea is that Wazuh does not always have to collect everything. We can focus on events that are useful for a particular detection. citeturn0search0

## Restart after configuration changes

After changing the agent configuration, restart the Wazuh Agent so the new configuration is loaded.

```powershell
Restart-Service -Name wazuh
```

## What I learned from this part

A detection system is only as useful as the information it receives.

That is why log collection is an important part of SOC work, not just an installation step.
