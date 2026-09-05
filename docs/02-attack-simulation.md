# Attack Simulation

## Objective

Simulate a brute-force password-guessing attack (MITRE ATT&CK T1110.001) against the Windows target's SSH service, to generate realistic log data for detection engineering.

## Tooling

- THC-Hydra (v9.7), run from the Kali attacker VM
- A custom 5-entry wordlist, with the correct password placed last, to simulate a realistic sequence of failed attempts followed by a successful compromise

## Target

- Windows 11 VM, OpenSSH Server, port 22
- Target IP: 192.168.56.101 (host-only network)
- Target account: LAP1

## Command

hydra -l LAP1 -P wordlist.txt ssh://192.168.56.101

- -l LAP1 — attack a single, known username
- -P wordlist.txt — attempt every password in the supplied wordlist
- ssh://192.168.56.101 — target protocol and host

## Result

Add attack simulation documentation

[22][ssh] host: 192.168.56.101   login: LAP1   password: SocLab2026!
1 of 1 target successfully completed, 1 valid password found

Hydra attempted 4 incorrect passwords before succeeding on the 5th attempt, generating:
- 4x Windows Security Event ID 4625 (failed logon)
- 1x Windows Security Event ID 4624 (successful logon)

These events were captured by Splunk in real time via the Windows Security Event Log input, forming the raw evidence used in the detection stage of this project.
