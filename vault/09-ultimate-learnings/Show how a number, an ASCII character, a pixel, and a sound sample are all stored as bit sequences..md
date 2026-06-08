  
How do computers turn everything into zeros and ones in memory, and how do they tell the difference between a text file, an image, and an audio or video file?  
  
Let’s dive into how binary conversion works for different types of data.

- **Text:** Each character is represented by a binary code using ASCII (a standard set of 7- or 8-bit codes for characters). For special characters and symbols, including emojis, the Unicode standard uses code points and can be encoded in formats like UTF-8, UTF-16, or UTF-32.
- **Images:** Each pixel on the screen can be broken down into three basic colors: red, green, and blue. We can represent each color as binary (for example, 8 bits per color channel), and store whether each pixel has red, green, and blue using those binary values. By combining all those binary values for each pixel, we can store a whole image. (Many formats also use compression or different color depths.)
- **Sound:** Digital audio is stored as samples, representing the amplitude of the sound over time. Commonly, these samples have a fixed bit depth (such as 16 or 24 bits) and are sampled at a regular rate (the sample rate). The resulting binary data can then be interpreted as the sound when played back.

That’s how we turn different types of communication into binary.