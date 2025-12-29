---
title: "L3akCTF_2025_(Ghost_In_The_Dark)"
date: 2025-12-28 00:00:00
categories: [CTF_competitions]
tags: [Disk_Forensics, Medium,]
toc: true
---

![Image](assets/img/6/6_1.jpg)

After download artifact from this [link](https://drive.google.com/file/d/1eP6wWmbFaBNTNa2IxNHPDIuyN1gXyBCz/view)

connect to server to get question

![Image](assets/img/6/6_2.jpg)

## Q1: What was the ComputerName of the device?

use RegRipper tool to analysis SYSTEM hive Register and search in result file to ComputerName

# answer: 99PHOENIXDOWNS

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q2: What was the SSID of the first Wi-Fi network they connected to?

from winvet Logs file you can found called `Microsoft-Windows-WLAN-AutoConfig%4Operational.evtx` this file included a logs related to Wi-Fi connection and setting sort event by time to get answer

![Image](assets/img/6/6_3.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q3:When did they obtain the DHCP lease at the first café?

back to result you got it from RegRipper and search by SSID

![Image](assets/img/6/6_4.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q4: What IP address was assigned at the first café?

![Image](assets/img/6/6_5.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q5: What GitHub page did they visit at the first café?

check browser database from this path `C\Users\NotVi\AppData\Local\Microsoft\Edge\User Data\Default`

![Image](assets/img/6/6_6.jpg)

## Q6: What did they download at the first café?

![Image](assets/img/6/6_7.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q7: What was the name of the notes file?

open “ntuser.dat” by Registry Explorer

![Image](assets/img/6/6_8.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q8: What are the contents of the notes?

This question took me quite a bit of time because the file was deleted, even though you were provided with files. If it had been a disk image, we could’ve tried to recover the file in several ways.

But after some research and with help from a friend, we managed to recover the file from the `$MFT` file.

open `MFTECmd.exe` use option ` — dr` this options use to dump $MFT resident files to dir specified by — csv or — json, in ‘Resident’ subdirectory.

```bash
MFTECmd.exe -f path\to\MFT\file --csv path\to\output\file\ --dr

```

in output file you can show file called “Resident” search in file on “HowToHackTheWorld.txt”

![Image](assets/img/6/6_9.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q9:What was the SSID of the second Wi-Fi network they connected to?

back to result you got it from RegRipper

![Image](assets/img/6/6_10.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q10:When did they obtain the second lease?

![Image](assets/img/6/6_11.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q11:What was the IP address assigned at the second café?

![Image](assets/img/6/6_12.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q12: What website did they log into at the second café?

check Chrome database from this path `C\Users\NotVi\AppData\Local\Google\Chrome\User Data\Default`

![Image](assets/img/6/6_13.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q13: What was the MAC address of the Wi-Fi adapter used?

back to Q2 file

![Image](assets/img/6/6_14.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Q14: What city did this take place in?

google is your friends search to AllyCat coffee pleace

![Image](assets/img/6/6_15.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## flag 

![Image](assets/img/6/6_16.jpg)

<p align="center">🔹🔹🔹🔹🔹🔹</p>

# Thanks For Reading




