When a key is pressed, the keyboard wires connect to an [[Encoder|encoder]] that converts the pressed key into its [[ASCII & Unicode|ASCII]] equivalent and stores it in the [[RAM]] buffer as a string. At that point, it does not matter whether it is an integer, another character, a pixel, or something else; it is converted by the encoder and stored in RAM.

The GPU uses a special [[Decoder|decoder]], takes input from RAM, and outputs a graphic on the screen representing the character. That is how the encoder, decoder, RAM, and GPU are connected from the moment a key is pressed to when it appears on the screen. 


# The Journey of a Keystroke: From Finger to Screen

When you press the key `'A'` on your keyboard, it appears almost instantly on your screen. In that tiny fraction of a millisecond, an incredible relay race occurs, passing messages from physical switches, through electrical interrupts, operating system kernels, application event loops, and memory buffers, all the way to the display screen.

Here is the step-by-step journey of a keystroke.

---

## Step 1: The Physical Keypress (Keyboard Hardware)

Underneath your keyboard keys is a grid of wires called a key matrix. 
* Pressing a key pushes a conductive pad down, bridging two wires and completing an electrical circuit.
* Inside the keyboard is a **microcontroller**, which is a tiny, self-contained computer chip dedicated to managing the keyboard.
* The microcontroller constantly scans the key matrix. When it detects a completed circuit, it generates a **scancode**. A scancode is a raw identifier indicating the physical location of the pressed key (e.g., "key row 3, column 2").
* The microcontroller packages this scancode as a packet of [[Explain why computers use binary|binary bits]] and sends it over a USB cable to the computer.

---

## Step 2: The Interrupt (CPU Attention)

When the scancode packet arrives at the computer's USB controller chip, it triggers an event that demands immediate attention.
* The USB controller sends an **interrupt request (IRQ)**, which is an electrical signal sent directly to the CPU to request immediate attention, overriding normal software execution.
* The CPU immediately pauses its current [[Describe one full fetch-decode-execute cycle, what each stage does and what synchronizes them|Fetch-Decode-Execute Cycle]], saves its current register values so it doesn't lose its place, and switches from User Mode to Kernel Mode.
* The CPU looks up the interrupt number in a table and runs the corresponding **Interrupt Service Routine (ISR)**, which is a specialized piece of operating system driver code that handles hardware events.

---

## Step 3: Mapping and Application Hand-off (OS & Software)

The OS now takes over the raw hardware event.
* The OS keyboard driver reads the raw scancode from the USB controller's memory.
* The driver translates the physical scancode into a standard character code (like [[Show how a number, an ASCII character, a pixel, and a sound sample are all stored as bit sequences.|ASCII or Unicode]]) using the system's active keyboard layout (e.g., translating scancode `30` to character `'a'`, or `'A'` if the Shift key was also active).
* The [[Describe what an OS does - process, thread, file system, virtual memory, kernel vs user space|Operating System]] packages this character into an "input event" and drops it into the event queue of the active application (like a text editor).
* The application process reads the event from its queue, processes the logic (e.g., adding `'A'` to the document string stored in [[Compare the memory hierarchy - register, L1-L2-L3 cache, RAM, SSD, HDD — what each is for, rough speed and cost differences, why we have multiple levels.|RAM]]), and requests a redraw of its window.

---

## Step 4: Drawing and Display (Graphics & GPU)

Finally, the letter must be turned back into physical light.
* To render the character, the application looks up the glyph (visual design) of `'A'` in its active font file.
* The application draws this glyph into a **framebuffer**, which is a dedicated region of memory that stores the color values of every pixel to be displayed on the screen.
* The application writes the red, green, and blue (RGB) color bytes for the character into the framebuffer grid.
* The computer's graphics card (GPU) or display controller reads the framebuffer at a regular rate (e.g., 60 times a second for a 60Hz monitor).
* The controller transmits the pixel colors as electrical signals over a HDMI or DisplayPort cable, lighting up the red, green, and blue sub-pixels of your monitor to display the character `'A'`.