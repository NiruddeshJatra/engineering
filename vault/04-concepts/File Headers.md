---
tags:
  - medium
source: "[[crash-course-cs-ep20]]"
---

### Definition

Every file is just binary data. The "magic bytes" at the very beginning of a file (the header) tell the reading software how to interpret the rest of the array.

### Details

- Under the hood, all files (whether TXT, WAV, or BMP) are just long arrays of binary numbers. To parse them correctly, software relies on "metadata (like bit rate or image width)" stored in a Header at the very front of the file.

### Why it matters to me as a dev

You should never rely purely on file extensions (like `.jpg`) for security, as users can easily rename an executable malware file to `.jpg`. Safe software parses the file header to verify its true MIME type.

## In my words
Every kind of file: music, image, text is just binary data. There are some bytes to store which kind of data this file holds and they are called file headers. 