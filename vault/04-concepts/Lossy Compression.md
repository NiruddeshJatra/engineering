---
tags:
  - medium
source: "[[crash-course-cs-ep21]]"
---
### Definition

This drastically reduces the file size by intentionally discarding data based on human perception limits, meaning the result is only an approximation of the original.
	- **Perceptual Coding:** Algorithms use psychophysics to throw away imperceptible data. For audio, this means dropping ultrasonic frequencies and reducing precision in bass tones. For images, the algorithm breaks the picture into 8X8 pixel blocks and discards subtle color variations, keeping only the sharp contrasts.
- **Temporal Redundancy:** Video compression algorithms take advantage of inter-frame similarity. Instead of saving every pixel of every frame, the algorithm simply copies unchanging background patches forward. or mathematically shifts/rotates a patch of pixels from a previous frame to represent movement.


### Why it matters to me as a dev

you should never aggressively compress source code or raw data with lossy algorithms, while images and videos should always be compressed using lossy web formats (like WebP or MP4) to keep network payloads lightweight.

## In my words
Here, the decompressed data is an approximation to the original. It discards data based on human perception limits, like removing ultrasonic sound data, discarding subtle color variations from an image data, copying unchanging background patches of previous frame to the next in video data. For video, audio and image, we can use lossy compression. But for source code, text files, we must use [[Lossless Compression]].