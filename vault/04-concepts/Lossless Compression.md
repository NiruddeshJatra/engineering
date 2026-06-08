---
tags:
  - medium
source: "[[crash-course-cs-ep21]]"
---
### Definition
 This guarantees the decompressed data is bit-for-bit identical the original, which is required for files like source code or text. It relies on two main techniques:
	- **Run-Length Encoding (RLE):** Reduces redundancy by replacing long sequences of identical data (e.g., seven yellow pixels in a row) with a simple length-and-value pair.
	- **Dictionary Coding & Huffman Trees:** Replaces common blocks of data with shorter codes. A Huffman Tree Algorithm sorts data blocks by frequency and assigns the shortest, prefix-free binary codes to the most common blocks. The generated "dictionary" must be prepended to the file so the software knows how to decode it later.

### Why it matters to me as a dev

Text-based payloads (JSON, HTML, JavaScript source code) _must_ use lossless compression. You can drastically optimize web performance by ensuring your web servers have **Gzip** or **Brotli** compression enabled for text assets.

### Open questions

- Haven't understood the Huffman Tree Algorithm. 

## In my words
In lossless compression, the decompressed data remains bit by bit identical to the original, just the information gets stored in efficient manner. For example, by Run-Length encoding or by using Dictionary coding and Huffman Trees.