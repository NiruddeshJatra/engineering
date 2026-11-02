---
type: synthesis
module: 1
question: "Trace what happens from pressing a key to a character appearing on screen, at whatever depth asked."
score:
last_recalled:
---
When a key is pressed, the keyboard wires connect to an [[Encoder|encoder]] that converts the pressed key into its [[ASCII & Unicode|ASCII]] equivalent and stores it in the [[RAM]] buffer as a string. At that point, it does not matter whether it is an integer, another character, a pixel, or something else; it is converted by the encoder and stored in RAM.

The GPU uses a special [[Decoder|decoder]], takes input from RAM, and outputs a graphic on the screen representing the character. That is how the encoder, decoder, RAM, and GPU are connected from the moment a key is pressed to when it appears on the screen. 


