# RDP Login Test

## What is RDP?

RDP (Remote Desktop Protocol) is a Windows feature that allows a computer to be accessed remotely.

## Why did I test it?

Remote access is useful for normal administration, but attackers can also try to use remote access to get into a system.

I wanted to see what an RDP login attempt looks like in the Windows logs and how Wazuh handles it.

## What I checked

- What Windows records when an RDP login is attempted.
- Whether the Wazuh Agent sends the event to the server.
- Whether Wazuh creates an alert.
- What information an analyst can use when looking at the alert.

## Why this matters in a SOC

A SOC analyst may need to investigate unexpected remote login activity. The source of the connection, account involved, time, and result of the login can all help the analyst understand what happened.

## Result

The test was performed in the controlled lab to understand how RDP-related activity moves from the Windows event log into Wazuh monitoring.

> All testing was performed in my lab environment.
