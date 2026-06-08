---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---

### Definition

Storage hardware like [[SSD]] and [[HDD]] has no native concept of "files". The [[Operating System]] manages this abstraction using a special "Directory File" (usually at index 0) that acts as a table of contents. It stores file names, extensions, permissions, and pointers to exactly where the file's data begins and ends in storage.

### Details

- **Block Allocation:** To allow files to grow without overwriting their neighbors, file systems allocate data in standardized "blocks". A single file is often split into chunks and stored across a list of non-sequential blocks.
- **Deletions & Forensics:** When you delete a file, the OS doesn't actually erase the data on the disk; it simply removes the file's entry from the Directory File and marks the blocks as free. The data physically remains until it is overwritten by something else.
- **Hierarchical Systems:** Modern file systems use a tree structure starting with a "Root Directory" that points to files and other subdirectories. Because of this pointer-based system, moving a large file to a different folder is an instant operation-it just updates the pointers in the directory files without moving the actual data blocks.

### Why it matters to me as a dev

This explains why moving a 10 GB video file from one folder to another on the same drive is instantaneous: the OS just updates a pointer in the directory file. But moving that same file to a different drive requires physically copying every single block across the hardware bus.

## In my words
Directory file is some metadata which contains file name, extension, permission and pointer to the start and end of the file. Moving file just requires updating the pointer in the directory file. So, it works as a table of index.