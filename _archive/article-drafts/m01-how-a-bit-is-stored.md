# The Transistor: The World's Smallest Water Tap

If computers only understand `0`s and `1`s (voltages), how do we control and store those voltages? We do it using [[Transistors|transistors]], which are microscopic, electronic switches that control the flow of electricity.

---

## 1. The Transistor as a Switch

In modern computer processors, we use a type of transistor called a **MOSFET** (Metal-Oxide-Semiconductor Field-Effect Transistor). Rather than thinking about complex physics, you can think of a MOSFET as an electronic water tap. 

A transistor has three main terminals (connections):
1. **The Source:** Where electrons (electricity) enter the transistor (like the water main pipe).
2. **The Drain:** Where electrons exit the transistor to do work (like the tap nozzle).
3. **The Gate:** The control valve. By applying or removing voltage to the Gate, we turn the switch ON or OFF.

```
                  [Gate] (Valve Handle)
                    |
                    | (Control Voltage)
                    v
   [Source] ================ [Drain]
   (Water In)   (Channel)    (Water Out)
```

In computers, we use two complementary types of transistors:
* **N-Type (NMOS):** Turns **ON** when we apply a high voltage (`1`) to the Gate, allowing electricity to flow.
* **P-Type (PMOS):** Turns **ON** when we apply a low voltage (`0`) to the Gate, allowing electricity to flow.

By combining NMOS and PMOS transistors, we can build circuits that switch voltages cleanly between `0` and `1` without wasting power.

---

## 2. Building Logic Gates

By connecting a few transistors together, we build **logic gates**, which are physical circuits that perform basic logical operations (like AND, OR, NOT) on binary inputs.

For example, a **NOT gate** (which turns a `1` into a `0`, and a `0` into a `1`) can be made using just one PMOS and one NMOS transistor:
* If the input is High (`1`), the NMOS switch closes, connecting the output to Ground (`0`).
* If the input is Low (`0`), the PMOS switch closes, connecting the output to Power (`1`).

---

## 3. Creating Memory: The Feedback Loop

If we turn off a computer, the voltages in the wires disappear. How do we keep a voltage active—how do we store a bit of data? 

The secret is **feedback**: feeding a logic gate's output back into its own input.

Imagine two NOT gates connected in a circle:
* If the first gate outputs `1`, it goes into the second gate, which outputs `0`.
* That `0` goes back into the first gate, which outputs `1`.

```
         +-----[ NOT Gate 1 ]-----+
         |                        |
         +<----[ NOT Gate 2 ]<----+
```

This circle will hold its state (`1` at one end, `0` at the other) forever, as long as power is supplied. This basic memory circuit is called a **latch**.

---

## 4. The Flip-Flop: Storing a Bit on Time

A basic latch changes its stored value the instant the inputs change. In a fast computer, this would cause chaos because data would slide through circuits uncontrollably.

To solve this, we add a control signal called the [[Clock|clock]], which is an oscillator that sends regular electrical pulses billions of times a second to synchronize the system.

When we combine latches with a clock signal, we get a **flip-flop**. A flip-flop is the smallest electronic memory component, capable of storing exactly one [[m01-why-binary|bit]] of data. It only updates its stored value at the precise moment the clock pulse changes (e.g., when it rises from low to high voltage). 

In the next article, we will see how we group these flip-flops together to build CPU [[m01-gates-to-cpu|registers and RAM]].