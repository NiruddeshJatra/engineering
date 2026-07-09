How do [[Computer|computers]] turn everything into zeros and ones in memory, and how do they tell the difference between a text file, an image, and an audio or video file?  
  
Let’s dive into how [[Binary Numbers|binary]] conversion works for different types of data.

- **Text:** Each character is represented by a binary code using [[ASCII & Unicode|ASCII]] (a standard set of 7- or 8-bit codes for characters). For special characters and symbols, including emojis, the Unicode standard uses code points and can be encoded in formats like UTF-8, UTF-16, or UTF-32.
- **Images:** Each pixel on the screen can be broken down into three basic colors: red, green, and blue. We can represent each color as binary (for example, 8 bits per color channel), and store whether each pixel has red, green, and blue using those binary values. By combining all those binary values for each pixel, we can store a whole image. (Many formats also use compression or different color depths.)
- **Sound:** Digital audio is stored as samples, representing the amplitude of the sound over time. Commonly, these samples have a fixed bit depth (such as 16 or 24 bits) and are sampled at a regular rate (the sample rate). The resulting binary data can then be interpreted as the sound when played back.

That’s how we turn different types of communication into binary.


# Translating the World into Zeros and Ones

We know that a computer physically stores data as voltages representing [[Explain why computers use binary|zeros and ones (bits)]]. But how do we represent things we actually care about—like a text message, an image of a cat, or a music track—as a sequence of bits? 

---

## 1. Numbers: Place Value in Binary

Humans count in base-10 (decimal), likely because we have ten fingers. We have ten digits (`0` through `9`), and each column represents a power of 10 (Ones, Tens, Hundreds, etc.).

Computers count in base-2 (binary) because they use two-state switches. They only have two digits (`0` and `1`), and each column represents a power of 2 (Ones, Twos, Fours, Eights, Sixes, etc.).

For example, the decimal number `13` is written as `1101` in binary:
$$1101_2 = (1 \times 8) + (1 \times 4) + (0 \times 2) + (1 \times 1) = 13_{10}$$

---

## 2. Text: ASCII and Unicode

To store text, we map each letter, number, and punctuation mark to a specific binary number.

### ASCII
The earliest standard was **ASCII** (American Standard Code for Information Interchange), which is a character encoding standard that assigns a unique 7-bit number to 128 different characters (English letters, numbers, and basic symbols).
* The character `'A'` is mapped to decimal `65`, which is `01000001` in binary.
* The character `'a'` is mapped to decimal `97`, which is `01100001` in binary.

### Unicode and UTF-8
ASCII worked fine for English, but it couldn't represent characters from other languages, let alone emojis. To solve this, the world adopted **Unicode**, which is an international standard that assigns a unique number (called a code point) to every character and symbol in every language.

The most common way to encode Unicode into bits is **UTF-8**. UTF-8 is a variable-width encoding, meaning it uses 8 bits (1 byte) for English characters (making it compatible with ASCII) but dynamically scales up to 32 bits (4 bytes) to represent complex characters or emojis (like 🍕, which is represented by a specific 4-byte sequence).

---

## 3. Images: Grid of Pixels

A digital image is divided into a grid of tiny colored dots called **pixels**, which are the smallest controllable elements of a picture on a screen.

To represent color, we use the RGB model. In this model, every pixel is a mixture of three primary light colors: **Red**, **Green**, and **Blue**.

For each color channel, we store a number indicating its brightness (usually from `0` for off to `255` for maximum brightness). This requires 8 bits (1 byte) per channel, leading to **24-bit color depth** (8 bits for Red + 8 bits for Green + 8 bits for Blue):
* **Red Pixel:** `255, 0, 0` $\rightarrow$ `11111111 00000000 00000000`
* **Purple Pixel:** `128, 0, 128` $\rightarrow$ `10000000 00000000 10000000`

By lining up the 24-bit sequences of every pixel row-by-row, the computer builds a complete digital image file.

---

## 4. Sound: Sampling a Wave

Sound travels through the air as a continuous physical wave of pressure. To store this digitally, we use a process called **sampling**.

We measure the height (amplitude) of the sound wave at regular intervals and record that height as a binary number.

```
   Amplitude
     ^      _.-*'-._          o (Sample point)
     |    o'        'o      o
     |   o            o    o
     |  o              o  o
     | o                o
     +---------------------------> Time
```

To capture high-quality sound, we must sample the wave very frequently:
* **Sample Rate:** The number of samples taken per second, measured in Hertz (Hz). For CD-quality audio, the sample rate is **44,100 Hz** (44,100 measurements every second).
* **Bit Depth:** The number of bits used to store the height of each sample (usually 16 bits or 24 bits). A higher bit depth allows for more precise measurements, reducing background hiss.

---

## 5. The Critical Lesson: Context is Everything

To a CPU, the binary sequence `01000001` is just a set of voltages. It has no intrinsic meaning.
* If loaded into a text editor, it is interpreted as the letter `'A'`.
* If loaded into a calculator, it is interpreted as the number `65`.
* If loaded into an image viewer, it might represent a dark blue pixel.

The meaning of the bits is determined entirely by the instructions in the program currently processing them.