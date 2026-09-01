# Custom Wazuh Rules

## What is a Wazuh rule?

A Wazuh rule is a condition that tells Wazuh what kind of event it should pay attention to.

A simple way to think about it is:

```text
Windows event
     ↓
Wazuh reads the event
     ↓
Rule checks the event
     ↓
Does it match?
   ↙       ↘
 Yes        No
  ↓          ↓
Alert      No alert
```

A log is a record of something that happened. A rule helps decide whether that record should become an alert.

## Why use custom rules?

Wazuh already includes many rules. But every lab and organisation can have different things that need special attention.

Custom rules let me create detections for activity that matters in my lab. Wazuh recommends using the custom rules area instead of changing the built-in rule files directly. citeturn0search4

## Where do custom rules go?

The Wazuh custom rules directory is:

```text
/var/ossec/etc/rules/
```

For small local changes, Wazuh also supports `local_rules.xml`. For a larger set of rules, separate XML files can be kept in the same custom rules directory. citeturn0search4

## Rule IDs

Wazuh recommends IDs between **100000 and 120000** for custom rules so they do not conflict with built-in rules. citeturn0search4

In this project, the account-creation rule uses:

```text
100100
```

## The account creation rule

The rule is in:

```text
wazuh/custom-rules/account-creation.xml
```

The main condition is:

```xml
<field name="win.system.eventID">^4720$</field>
```

Windows Security Event ID **4720** means a user account was created.

The rule therefore looks for that event and creates a Wazuh alert with a clear message.

The rule also maps the activity to **MITRE ATT&CK T1136 (Create Account)**. The mapping gives the analyst a standard name for the type of behaviour being detected. Wazuh supports adding MITRE ATT&CK IDs to custom rules. citeturn0search10

## Understanding the important parts

### Rule ID

```xml
id="100100"
```

This gives the rule its unique ID.

### Alert level

```xml
level="8"
```

This gives the alert a severity level. It does not automatically mean that the activity is malicious. The analyst still needs to investigate it.

### Event condition

```xml
<field name="win.system.eventID">^4720$</field>
```

This is the part that checks the Windows event ID.

### Description

```xml
<description>Windows: A new user account was created.</description>
```

The description gives the analyst a quick explanation of why the rule fired.

## Testing the rule

Do not assume that a rule works just because the XML looks correct.

Wazuh provides the `wazuh-logtest` tool for testing rules against event data. After changing a rule, test it with the real event data collected in the lab. citeturn0search4

The workflow is:

```text
Write rule
    ↓
Test rule with real event data
    ↓
Does the rule match correctly?
    ↓
Fix the rule if needed
    ↓
Restart Wazuh manager
    ↓
Generate the real event
    ↓
Check the alert
```

## Why keep custom rules separate?

Built-in Wazuh rule files can be replaced during an upgrade. Keeping my own rules in the custom rules area makes them easier to maintain. citeturn0search4

## What I learned

Writing a rule is not just about making an alert appear.

A useful rule should make an important event easier for an analyst to notice and understand, while avoiding unnecessary alerts.
