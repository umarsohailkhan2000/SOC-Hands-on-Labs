
Windows Security Log Analysis (Hydra RDP Brute-Force Lab)
Objective

In this lab, I analyzed Windows Security Logs by generating real authentication events using a controlled brute-force attempt. I used Hydra from Kali Linux to simulate RDP brute-force attacks on a Windows Server VM. This helped me understand how Windows logs failed and successful login attempts, which is crucial for SOC detection and investigation.

What I Explored

Windows Security Logs capture events such as:

4624 – Successful Logon

4625 – Failed Logon

4672 – Special Privileges Assigned

4740 – Account Lockout

4732 – Group Membership Changes

SOC analysts rely heavily on these logs to detect brute-force attacks, unauthorized access, and privilege escalation.

Lab Setup

Attacker Machine: Kali Linux

Target Machine: Windows Server (RDP Enabled)

Tool Used: Hydra

Service Targeted: RDP (Port 3389)

Wordlist: rockyou.txt

Test User: haxuser1

1️⃣ Generating Failed Login Attempts Using Hydra

To simulate real-world brute-force behavior, I used Hydra against the Windows Server RDP service:

hydra -l haxuser1 -P /usr/share/wordlists/rockyou.txt rdp://<Windows-IP> -t 4 -V


What this does:

Flag	Description
-l	Specifies the username
-P	Loads a password list
rdp://IP	Targets the RDP service
-t 4	4 parallel threads
-V	Verbose mode to see every attempt

Every invalid password attempt generates a 4625 Failed Logon event in Windows Security Logs.

This gives a realistic picture of how brute-force attacks appear from the defender’s point of view.

2️⃣ Analyzing Brute-Force Logs in Event Viewer

After running Hydra, I opened:

Event Viewer → Windows Logs → Security


Then I filtered logs for Event ID 4625.

For each failed attempt, I reviewed:

Target Username: haxuser1

Failure Reason: Unknown username or bad password

Logon Type: 3 (Network Logon – typical for RDP brute force)

Source Network Address: Kali VM IP

Workstation / Logon Process: NtLmSsp or seclogo depending on scenario

These patterns are extremely useful for detecting brute-force attacks in enterprise SOC environments.

3️⃣ Successful Login – Event ID 4624

After the brute-force test, I logged in successfully with the correct password to generate a 4624 Successful Logon event.

This helped me compare:

What changes between failed vs successful logons

Privilege information

Logon type differences

Token privileges and special rights provided after login

Key SOC Takeaways

Hydra brute force attempts clearly show repeating 4625 events in rapid succession.

Windows logs make it possible to detect dictionary attacks, password spraying, or credential stuffing.

Source IP visibility helps identify attack origin.

RDP brute-force attacks often produce:

Logon Type 3 (Network)

Failure Code: 0xC000006A or 0xC0000064

SOC Analysts must monitor for multiple 4625 events within short time windows.
