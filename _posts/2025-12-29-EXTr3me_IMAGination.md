---
title: "EXTr3me_IMAGination"
date: 2025-12-28 00:00:00
categories: [Cyber_Talents]
tags: [Disk_Forensics, Hard, Corrupted_Disk]
image:
  path: /assets/img/4/4.jpg
  in_post: false
toc: true
---

# [Artifacts](https://hubchallenges.s3.eu-west-1.amazonaws.com/forensics/extreme_imagination.zip)

## Description:

The flag is 20 pairs of hex numbers (40 digits)
Flag format Flag{pairs}

# Let’s go have fun!

After downloading and unzipping the file, you will find a disk image named filesystem.dd. I used FTK Imager to mount it, but no files were found

![Image](/assets/img/4/4_1.jpg)

after a long time and a lot of search if you use a binwalk command you can found a lot of png file

![Image](/assets/img/4/4_2.jpg)

# This challenge took me a lot of time because I initially tried to extract these files using the “foremost” command, as there were a huge number of images. So I started thinking of an alternative solution.

![Image](/assets/img/4/4_3.jpg)

from this photo you can show a huge number of png file, So I started thinking if this disk image is Corrupted

use a file command :

![Image](/assets/img/4/4_4.jpg)

you can show this image is a FAT16
Let’s find tools for repairing or recovering data from corrupted FAT16 disks.
Okay i found tool to fix it `fsck.ext3`

![Image](/assets/img/4/4_5.jpg)

As expected, the file was corrupted.
Once it’s fixed, you can open it with FTK or mount it on Linux.

![Image](/assets/img/4/4_6.jpg)

Alright, the file has been successfully repaired, and the files on the hard drive can now be browsed. There’s also an interesting file named “solution”—you can save it to your device and start working on it.

Once again, there are more than 200 images, each containing only two numbers. However, the challenge description says the flag consists of 20 pairs of numbers.

After doing some research and checking whether any image contained steganographic data, I ran exiftool and found an interesting User Comment field

![Image](/assets/img/4/4_7.jpg)

After repeating this process on a set of images, I found that the User Comment is actually the MD5 hash of the next image in the correct sequence—until you collect the 20 images that contain the flag. The last one has a User Comment that says “Next Nowhere.”

![Image](/assets/img/4/4_8.jpg)

But if I keep repeating this process manually, it would be difficult and prone to errors. So, with the help of AI, I wrote a Python script that automates the whole process and extracts the 20 images that contain the flag.

```python
import os
import hashlib
import subprocess
import re

# Path to your image directory
IMAGES_DIR = ""  # Change if needed

REQUIRED_LENGTH = 19  # Acceptable minimum

def get_md5(file_path):
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()

def get_user_comment(image_path):
    try:
        result = subprocess.run(["exiftool", "-UserComment", image_path], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
        for line in result.stdout.splitlines():
            if "User Comment" in line:
                match = re.search(r'Next\s+([a-fA-F0-9]{32})', line)
                if match:
                    return match.group(1)
    except Exception as e:
        print(f"[!] Error reading EXIF from {image_path}: {e}")
    return None

def build_chain(start_image, images, md5_to_image):
    sequence = [start_image]
    current_image = start_image

    for _ in range(19):
        next_md5 = images[current_image]["next_md5"]
        if not next_md5:
            break
        next_image = md5_to_image.get(next_md5)
        if not next_image or next_image in sequence:
            break
        sequence.append(next_image)
        current_image = next_image

    return sequence

def main():
    images = {}
    md5_to_image = {}

    for filename in os.listdir(IMAGES_DIR):
        path = os.path.join(IMAGES_DIR, filename)
        if os.path.isfile(path):
            md5 = get_md5(path)
            images[filename] = {
                "path": path,
                "md5": md5,
                "next_md5": get_user_comment(path)
            }
            md5_to_image[md5] = filename

    # Try each image as a potential starting point
    successful_chain = None
    for start_image in images.keys():
        chain = build_chain(start_image, images, md5_to_image)
        if len(chain) >= REQUIRED_LENGTH:
            successful_chain = chain
            break

    print(f"[+] Valid chain found starting from image: {successful_chain[0]}")
    print(f"[+] Image sequence ({len(successful_chain)} images):")
    for idx, img in enumerate(successful_chain, 1):
        print(f"{idx:02d}. {img}")

    if len(successful_chain) == 20:
        print("\n[✓] Perfect! Found a full sequence of 20 images.")
    else:
        print("this not a 20 images") 

if __name__ == "__main__":
    main()
```

In the end, I obtained the 20 images that contain the flag.

# Thanks For Reading
