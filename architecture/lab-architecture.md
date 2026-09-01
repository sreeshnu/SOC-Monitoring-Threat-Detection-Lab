# Lab Architecture

This page explains how the machines in my lab work together.

## The three main machines

### 1. Kali Linux

I used Kali Linux as the testing machine.

Its job in this lab is to create controlled test activity against the Windows machine. This lets me see what a security event looks like when something suspicious happens.

### 2. Windows

Windows is the machine I wanted to monitor.

When activities such as failed logins or account changes happen, Windows records information about them in its event logs.

The Wazuh Agent runs on this machine and sends the required logs to the Wazuh server.

### 3. Ubuntu

Ubuntu is used to run the Wazuh server.

The Wazuh server is the central part of the lab. It receives logs from the Windows machine, checks them against detection rules, and produces alerts when a rule matches.

## How information moves

```text
Kali Linux
    |
    | Test activity
    v
Windows
    |
    | Windows event logs
    v
Wazuh Agent
    |
    | Sends logs
    v
Wazuh Server on Ubuntu
    |
    | Checks rules
    v
Alert
    |
    v
Investigation
```

## Why this setup?

I wanted to understand the basic workflow of a SOC rather than only install Wazuh and look at its dashboard.

So I separated the testing machine, the machine being monitored, and the central monitoring server.

This makes it easier to follow what happens from the original activity all the way to the final alert.

## VirtualBox

I used VirtualBox to run the virtual machines. This makes it possible to build the lab on one physical computer while keeping the systems separate from each other.

## Important point

This is a controlled lab. The testing is meant for learning and security testing, not for attacking systems that I do not own or have permission to test.
