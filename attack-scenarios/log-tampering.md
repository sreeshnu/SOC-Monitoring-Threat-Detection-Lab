# Windows Event Log Tampering Test

## What did I test?

I tested activity involving the Windows event logs themselves.

## Why did I test this?

Logs are important during an investigation because they can tell an analyst what happened on a system.

If an attacker tries to remove or interfere with logs, useful evidence may be lost.

I wanted to understand whether Wazuh could notice changes related to the files it was watching.

## File Integrity Monitoring

Wazuh can use File Integrity Monitoring (FIM) to watch important files and notice changes to them.

In simple words:

```text
File is normal
     ↓
Something changes the file
     ↓
Wazuh notices the change
     ↓
The event can be investigated
```

## What I checked

- What happens when the Windows event log files are changed or cleared.
- Whether Wazuh can notice the related activity.
- What information is available for investigation.

## Why this matters in a SOC

An analyst should consider not only suspicious activity itself, but also attempts to hide that activity.

Log tampering is therefore an important type of behaviour to understand when learning security monitoring.

> All testing was performed in my lab environment.
