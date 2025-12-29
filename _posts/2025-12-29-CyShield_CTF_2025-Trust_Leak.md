---
tilte: "Trust_Leak_DFIR_Challenge_From_CyCTF{2025}"
date: 2025-12-28 00:00:00
categories: [CTF_competitions]
tags: [Disk_Forensics, Medium]
toc: true
---

# Trust Leak

## Description:

A mid-sized company reports suspicious activity on one of its domain controllers. A privileged account appears to have exported sensitive directory information and then attempted to remove all local traces before leaving for the weekend. The triage was taken by on-site staff after the incident. No network captures are available — everything you need is on the Triage. Answer these five questions from the evidence in the triage package. Provide the values in the exact flag format shown.

Q1 → Which tool was used to drop the executable onto the host?

Q2 → What is the SHA-1 hash of the link that was used to drop the executable?

Q3 → What was the original filename of the dumped file?

Q4 → What filename was used for exfiltration to bypass detection?

Q5 → Which tool was used to exfiltrate the file from the host?

Q6 → When did the exfiltration occur (timestamp)? Note: provide only tool names (no file extensions). Format->

CyCTF{tool_sha1(url)_secret.txt_secret2.txt_tool_YYYY-MM-DD HH:MM:SS}

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q1 → Which tool was used to drop the executable onto the host?

![Image](assets/img/2/2_1.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q2 → What is the SHA-1 hash of the link that was used to drop the executable?

in → C/Users/Administrator/AppData/LocalLow/Microsoft/CryptnetUrlCache
parse file use ` CryptnetUrlCacheParser.py `

CryptnetUrlCacheParser.py -d C/Users/Administrator/AppData/LocalLow/Microsoft/CryptnetUrlCache | grep "ftk"

```bash
"2025-09-21T10:13:46.730754","2016-09-16T01:05:12","https://download.informer.com/win-1192693607-c5308488-66c7422f-27868c05d6c0543f17-aff9fe3f5b80d0e2e-6412598663-1188631454/accessdata_ftk_imager.exe",29756784,"57db4548-1c60d70","C/Users/Administrator/AppData/LocalLow/Microsoft/CryptnetUrlCache/MetaData/030C8B8E7B5E90F882E3E77DF362A56D"

```
url → https://download.informer.com/win-1192693607-c5308488-66c7422f-27868c05d6c0543f17-aff9fe3f5b80d0e2e-6412598663-1188631454/accessdata_ftk_imager.exe

convert to sha1

## ✅ Answer 4621cdc24718ed95bd6271e26b0e28307f159b32

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q3 What was the original filename of the dumped file?
## Q4 → What filename was used for exfiltration to bypass detection?

parse $J, $LogFile, $MFT using `ntfs log tracker`

![Image](assets/img/2/2_3.jpg)


## ✅ Answer Q3 → ntds.dit

## ✅ Answer Q4 → DemonSlayer.mp4

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q5 → Which tool was used to exfiltrate the file from the host?
## Q6 → When did the exfiltration occur (timestamp)?

\triage\C\Users\Administrator\AppData\Local\Mega Limited\MEGAsync\logs

![Image](assets/img/2/2_3.jpg)

## ✅ Answer Q5 → MEGA

## ✅ Answer Q6 → 2025–09–21 10:50:28

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## finally flag is → CyCTF{certutil_4621cdc24718ed95bd6271e26b0e28307f159b32_ntds.dit_DemonSlayer.mp4_MEGAsync_2025–09–21 10:50:28}

# Thanks for Reading