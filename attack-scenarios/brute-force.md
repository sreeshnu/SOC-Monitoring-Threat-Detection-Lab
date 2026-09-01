# Brute-Force Login Test

## What was the idea?

I wanted to see what happens when many failed login attempts are made against a Windows machine.

A large number of failed logins can be a sign that someone is trying different passwords to get into an account.

## What I wanted to learn

I wanted to follow the activity from the first failed login to the final Wazuh alert.

```text
Failed login
    ↓
Windows records the event
    ↓
Wazuh Agent sends the event
    ↓
Wazuh Server checks it
    ↓
Alert
    ↓
Investigation
```

## What I checked

- Whether Windows recorded the failed login attempts.
- Whether the Wazuh Agent collected the events.
- Whether Wazuh created an alert.
- What information was available in the alert.
- Whether repeated attempts could be recognised as suspicious activity.

## Why this matters in a SOC

One failed login by itself may not mean much. Many failed attempts in a short period can be more interesting to an analyst.

This test helped me understand how a SOC can use logs to notice that pattern.

## Result

The purpose of this test was to generate the activity in a controlled lab and check how Wazuh handled the resulting Windows security events.

> All testing was performed in my lab environment.
