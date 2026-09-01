# Lateral Movement Test

## What is lateral movement?

Lateral movement means moving from one computer to another after getting access to a network.

For example, an attacker may first get access to one computer and then try to reach another computer on the same network.

## Why did I test this?

I wanted to understand whether activity across systems could help an analyst notice that someone may be moving through the network.

## What I checked

- Authentication activity between systems.
- The source information shown in the logs.
- Whether Wazuh received the events.
- Whether activity across systems could be connected during investigation.

## Why this matters in a SOC

Looking at one computer alone may not always tell the full story.

An analyst may need to compare events from more than one system and look at details such as the account, time, and source of the activity.

This test helped me understand why central log collection is useful.

> All testing was performed in my lab environment.
