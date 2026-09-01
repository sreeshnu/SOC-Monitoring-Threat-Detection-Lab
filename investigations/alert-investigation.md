# Alert Investigation

This page explains how I approached an alert in the lab.

## The basic idea

When Wazuh shows an alert, the job is not finished.

The alert is a starting point for investigation.

```text
Alert appears
     ↓
What happened?
     ↓
When did it happen?
     ↓
Which computer was involved?
     ↓
Which account was involved?
     ↓
Where did the activity come from?
     ↓
Was it expected?
     ↓
What should I do next?
```

## Example: unexpected account creation

Suppose Wazuh reports that a new Windows account was created.

I would first look at the details of the event instead of immediately assuming that it is an attack.

### Questions to ask

1. Which account was created?
2. When was it created?
3. Which computer was affected?
4. Who performed the action?
5. Was the action expected?
6. Are there other suspicious events around the same time?

### Why look at other events?

A single event may not tell the whole story.

For example, an unexpected account creation becomes more interesting if it happens shortly after several failed logins or other suspicious activity.

This is why a SOC analyst looks at the surrounding events instead of treating every alert as proof of an attack.

## What I learned

The important part of SOC monitoring is not simply getting an alert.

The useful part is being able to understand **why the alert appeared, what actually happened, and whether the activity needs further action.**
