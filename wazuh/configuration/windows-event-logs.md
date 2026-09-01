# Windows Event Log Collection

## What are Windows Event Logs?

Windows keeps records about many things that happen on a computer.

For example:

- Login attempts
- User account changes
- Remote logins
- System changes
- Security events

These records are called **Windows Event Logs**.

## Why are they important in this lab?

The Windows machine is the system I want to monitor.

If something happens but the event is never collected, Wazuh has nothing useful to analyse.

So the flow starts here:

```text
Something happens on Windows
          ↓
Windows records the event
          ↓
Wazuh Agent collects it
          ↓
Wazuh Server receives it
          ↓
Wazuh checks the event
```

## Windows event channels

Wazuh can collect Windows events through the Windows event channel.

The current Wazuh documentation says that the agent monitors the **System, Application, and Security** event channels by default. Other channels can be added when they are needed. citeturn0search11

For this project, the Security channel is especially useful because it contains many login and account-related events.

## Agent configuration file

On a typical 64-bit Windows installation, the Wazuh Agent configuration file is:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

The actual location can depend on the Windows installation. citeturn0search1

## Example: monitoring the Security channel

A basic configuration block is:

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

Wazuh can also use queries when only certain events are needed.

The exact query depends on the channel and the event you want to collect. This can reduce unnecessary data and focus the monitoring on events that matter to the use case. citeturn0search11

## Restart after configuration changes

After changing the agent configuration, restart the Wazuh Agent so the new configuration is loaded.

```powershell
Restart-Service -Name wazuh
```

## Troubleshooting order

If an expected alert does not appear, I check the problem from the beginning:

1. Did the activity happen?
2. Did Windows create an event?
3. Is the correct event channel being collected?
4. Is the Wazuh Agent running?
5. Is the agent connected to the Wazuh server?
6. Did Wazuh receive the event?
7. Does a rule match the event?
8. Was an alert created?

This order helps separate a **log collection problem** from a **detection rule problem**.

## What I learned

A detection system is only as useful as the information it receives.

That is why log collection is an important part of SOC work, not just an installation step.
