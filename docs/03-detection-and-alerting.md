# Detection and Alerting

## Data Source

Windows Security Event Log, ingested into Splunk Enterprise via a local event log input configured in `inputs.conf`.

Relevant event codes:
- 4625 — An account failed to log on
- 4624 — An account was successfully logged on

## Detection Search

The following Splunk search identifies accounts experiencing repeated failed logon attempts within a short window, the core signature of a brute-force attack:

sourcetype="WinEventLog:Security" EventCode=4625 Account_Name=LAP1
| stats count by Account_Name
| where count >= 3

- The base search filters to failed logon events for the target account
- stats count by Account_Name tallies failed attempts per account
- where count >= 3 flags any account with three or more failures, the detection threshold for this lab

## Alert Configuration

This search was saved as a scheduled Splunk alert:

- Name: Brute Force Detection - SSH Failed Logons
- Type: Scheduled
- Schedule: every 5 minutes
- Search window: last 15 minutes
- Trigger condition: Number of Results greater than 0
- Trigger action: Add to Triggered Alerts

Because the search itself already applies the count >= 3 threshold, any result returned by the search represents a genuine detection, so the alert simply fires whenever the search produces output.

## Verification

After running the Hydra attack, the detection search returned:

LAP1   4

This confirmed the alert logic correctly identified the simulated brute-force attempt based on real Windows Security log data.

Add detection and alerting documentation.
