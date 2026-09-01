# Alert Triage Checklist

## What is alert triage?

Alert triage means taking a new alert and quickly working out what happened and whether it needs more investigation.

The goal is not to immediately call every alert an attack.

The goal is to understand the event.

## Step 1 — Read the alert

Start with the basic information:

- What happened?
- When did it happen?
- Which computer was involved?
- Which account was involved?
- What source or IP address is shown?
- What rule created the alert?

## Step 2 — Understand the event

Do not stop at the alert title.

Open the event details and look at the fields available in the original event.

For Windows events, useful information can include the event ID, account names, source information, timestamps, and other event data.

## Step 3 — Ask whether it was expected

This is one of the most important questions.

For example:

```text
New user created
      ↓
Was this expected?
   ↙       ↘
 Yes        No
 ↓           ↓
Normal     Investigate
activity   further
```

A new account, remote login, or failed login is not automatically an attack.

The surrounding context matters.

## Step 4 — Look for related events

Search around the same time for other activity.

For example:

```text
Failed logins
      ↓
Successful login
      ↓
New account created
      ↓
Remote access
```

Each event alone may have a normal explanation. Together, they may tell a different story.

## Step 5 — Decide what happens next

A simple triage decision can be:

- **Benign / expected** — no further action needed.
- **Suspicious** — collect more information and investigate.
- **Confirmed malicious** — follow the organisation's incident-response process.

## What I learned

The important skill is not memorising alert names.

It is learning how to ask the right questions and use the available evidence to understand what happened.

That is the approach I am practising with this lab.
