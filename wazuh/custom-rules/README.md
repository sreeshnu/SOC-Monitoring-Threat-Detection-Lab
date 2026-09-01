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
Condition matches?
   ↙       ↘
 Yes        No
  ↓          ↓
Alert      No alert
```

A log is simply a record of something that happened. A rule helps decide whether that record should become an alert.

## Why use custom rules?

Wazuh already includes many rules. But every lab or organisation can have different things that need special attention.

Custom rules let us create detections for activity that is important to our own environment. citeturn0search2

## Where do custom rules go?

Wazuh recommends using the custom rules area under:

```text
/var/ossec/etc/rules/
```

For small local changes, the `local_rules.xml` file can be used. For larger sets of custom rules, separate files can be created in the same directory. citeturn0search2

## Rule IDs

Custom rules should use IDs in the **100000–120000** range so they do not conflict with Wazuh's built-in rules. citeturn0search2

## Basic example

```xml
<group name="windows,custom,">
  <rule id="100100" level="10">
    <description>Example custom Windows detection</description>
  </rule>
</group>
```

This is only a simple example to explain the structure. A useful detection needs conditions that match the event we actually want to detect.

## Important parts

### Rule ID

```xml
id="100100"
```

This gives the rule its unique ID.

### Level

```xml
level="10"
```

This gives the alert a severity level.

### Description

```xml
<description>Example custom Windows detection</description>
```

This is the message that helps the analyst understand what the rule is about.

### Conditions

A real rule normally contains conditions that check fields from the event. The exact fields depend on the Windows event being detected.

## Testing a rule

Wazuh provides the `wazuh-logtest` tool for testing rules against log data. The official documentation recommends using it to verify that a rule behaves as expected. citeturn0search2turn0search3

The useful workflow is:

```text
Create rule
    ↓
Test rule with event data
    ↓
Does it match correctly?
    ↓
Fix the rule if needed
    ↓
Restart Wazuh manager
    ↓
Generate the real event
    ↓
Check the alert
```

## Why not edit built-in rule files?

Wazuh's built-in rules can be replaced during an upgrade. For that reason, custom changes should be kept in the custom rules area instead of directly changing files under the built-in ruleset directory. citeturn0search2

## What this taught me

Writing a rule is not just about making an alert appear.

A good rule should make an important event easier for an analyst to notice and understand without creating a large number of useless alerts.
