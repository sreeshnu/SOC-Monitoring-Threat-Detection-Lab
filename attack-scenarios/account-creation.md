# Windows Account Creation Test

## What did I test?

I tested what happens when a new local user account is created on Windows.

## Why did I test this?

A new account can be completely normal when an administrator creates one for a legitimate reason.

But an unexpected account can also be useful to an attacker. If an attacker gets access to a system, they may create another account so they can use it later.

Because of this, I wanted to see whether Wazuh could clearly show this type of activity.

## What I checked

- What Windows records when a new user is created.
- Whether the Wazuh Agent collects the event.
- How the event appears in Wazuh.
- Whether the default rules make the activity easy to notice.
- Whether a custom rule can make the detection clearer.

## Custom rule

During this testing, account creation was one of the activities where a custom Wazuh rule was useful for making the event easier to identify.

This helped me understand an important SOC concept: the monitoring tool may already have the log, but the detection rule decides how clearly that activity is presented to the analyst.

## Why this matters in a SOC

The important question is not simply:

> "Was a user created?"

The better question is:

> "Was this user creation expected, and if not, why did it happen?"

That is the type of question an analyst should ask when investigating an alert.

> All testing was performed in my lab environment.
