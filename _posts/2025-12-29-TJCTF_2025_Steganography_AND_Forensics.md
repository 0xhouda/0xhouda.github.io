---
title: "TJCTF_2025_Steganography_AND_Forensics"
date: 2025-12-29 00:00:00
categories: [CTF_competitions]
tags: [Steganography, Medium]
toc: true
---

## Tools
 - exiftool
 - binwalk
 - foremost 
 - zsteg 
 - python  
 - sonic visualizer



## Challenge 1:

![Image](assets/img/7/7_1.jpg)

just open the photo there’s nothing interesting
use exiftool to check metadata

```bash
exiftool chall.png

```

![Image](assets/img/7/7_2.jpg)

ok found password encoding base64 decode that and follow me
use `binwalk` to scan embedded file in Chall.png

![Image](assets/img/7/7_3.jpg)

you can use `binwalk` OR `formost` to extract this file by following command

```bash
binwalk -e --dd=".*" chall.png

```

```bash
foremost chall.png

```

after extracting the files you will find a ZIP file protected use password “p0lygl0tp3ssw0rd” to extract files , now you have i a new archived file .gz contain a secret file

![Image](assets/img/7/7_4.jpg)

## ✅ Answer flag → tjctf{p0lygl0t_r3bb1t_h0l3}

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Challenge 2

![Image](assets/img/7/7_5.jpg)

challenge.zip contain a .DS_store file and data in this file encoded by base64 i think python is useful in this challenge

```python

from ds_store import DSStore
import base64

def decode_base64_line(line):
    # Add padding to make length multiple of 4
    padding = '=' * (-len(line) % 4)
    try:
        decoded_bytes = base64.urlsafe_b64decode(line + padding)
        return decoded_bytes.decode('utf-8', errors='ignore').strip()
    except Exception:
        return None

with DSStore.open(".DS_Store", "r") as d:
    for entry in d:
        if entry.code == b"Iloc":
            line = entry.filename  # this is the base64-like string
            decoded = decode_base64_line(line)
            print(decoded)

```

![Image](assets/img/7/7_6.jpg)

## ✅ Answer flag → tjctf{ds_store_is_useful?}

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## Challenge 3

![Image](assets/img/7/7_7.jpg)

suspicious.png

![Image](assets/img/7/7_8.jpg)

in this challenge i am usage stegsolver , strings , pngcheck , exiftool , binwalk , but zsteg will be useful

![Image](assets/img/7/7_9.jpg)

## ✅ Answer flag → tjctf{steganography_is_fun}

<p align="center">🔹🔹🔹🔹🔹🔹</p>

## challege 4

![Image](assets/img/7/7_10.jpg)

in this challenge we have a 2 file png and python i am open enc.py to know what is doing

```python

import wave
from PIL import Image
import numpy as np
#sample_rate = 44100
with wave.open('flag.wav', 'rb') as w:
    frames = np.frombuffer(w.readframes(w.getnframes()), dtype=np.int16)
    print(w.getnframes())
    sampwidth = w.getsampwidth() # 2
    nchannels = w.getnchannels() # 1
    w.close()
arr = np.array(frames)
img = arr.reshape((441, 444))
img = (img + 32767) / 65535 * 255
img = img.astype(np.uint8)
img = Image.fromarray(img)
img = img.convert('L')
img.save('albumcover.png')

```

this python script open file .wav to read audio data and convert it to pixels and use it to make a photo i am use AI to help me to recovery audio file by other python script like

```python

from PIL import Image
import numpy as np
import wave

# Load the image
img = Image.open('albumcover.png').convert('L')  # Ensure grayscale
data = np.array(img)

# Reverse normalization used in the original script
# Original was: img = (samples + 32767) / 65535 * 255
# Reverse that:
data = data.astype(np.float32) / 255 * 65535 - 32767
data = data.astype(np.int16)

# Flatten the array to 1D (as audio is linear)
samples = data.flatten()

# Save as WAV file
output_filename = 'extracted.wav'
with wave.open(output_filename, 'wb') as wav_file:
    nchannels = 1
    sampwidth = 2  # bytes (16-bit)
    framerate = 44100  # original assumed sample rate
    nframes = len(samples)

    wav_file.setnchannels(nchannels)
    wav_file.setsampwidth(sampwidth)
    wav_file.setframerate(framerate)
    wav_file.writeframes(samples.tobytes())

print(f"[+] Extracted WAV saved as: {output_filename}")

```

ok recover audio file successfully but no thing with out noise i am try open file in sonic visualizer and add spectrogram layer in file, great i am got the flag

![Image](assets/img/7/7_11.jpg)

## ✅ Answer flag → tjctf{THIS-EASTER-EGG-IS-PRETTY-COOL}

<p align="center">🔹🔹🔹🔹🔹🔹</p>

# Thanks For Reading
