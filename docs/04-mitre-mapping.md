# MITRE ATT&CK Mapping

**Technique:** T1110 - Brute Force
**Sub-technique:** T1110.001 - Password Guessing
**Tactic:** Credential Access

## Overview

Brute force attacks involve an adversary systematically attempting to guess account credentials in order to gain unauthorized access to a system. T1110.001 (Password Guessing) specifically covers scenarios where a known or suspected username is targeted with a list of candidate passwords, as opposed to attacking multiple unknown accounts (T1110.003 - Password Spraying) or attempting to bypass authentication another way.

## Simulation

To reproduce this technique in a controlled lab environment, an attacker VM (Kali Linux) was used to run THC-Hydra against a Windows 11 target VM's OpenSSH service. A custom wordlist containing five candidate passwords was tested against a known local account (LAP1) over SSH (port 22). Hydra attempted each password sequentially, generating four failed authentication attempts before succeeding on the fifth.

## Detection Data Source

Windows Security Event Log, ingested into Splunk Enterprise via a local Windows Event Log input.

- Event ID 4625 - An account failed to log on (generated for each incorrect password attempt)
- Event ID 4624 - An account was successfully logged on (generated once Hydra guessed correctly)

## Detection Logic

A Splunk search was built to identify accounts experiencing repeated failed logon attempts within a short time window, the core signature of a brute-force attack. This search counts failed logon events grouped by account name and flags any account with three or more failures. It was saved as a scheduled Splunk alert ("Brute Force Detection - SSH Failed Logons"), running every 5 minutes against the previous 15-minute window, triggering whenever the threshold is met.

## Visualization

A Splunk dashboard ("SOC Lab - Brute Force Detection") was built with three panels to support triage and investigation:

1. Failed Logon Attempts Over Time - a timechart visualizing the burst pattern of failed logons, making the attack window immediately visible
2. Logon Outcome Summary - a breakdown of failed vs. successful logon counts for the targeted account
3. Raw Logon Events - a chronological table of individual logon events (timestamp, event code, account) for evidence review

## Result

The detection successfully identified the simulated brute-force attempt, demonstrating a working end-to-end SOC pipeline: attack simulation, log generation, SIEM ingestion, correlation search, alerting, and visualization.

Add MITRE ATT&CK mapping documentation.
