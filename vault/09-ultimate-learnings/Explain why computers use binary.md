[[Computer|Computers]] use [[Binary Numbers|binary]] for two main reasons:

1. Using two states to store data aligns with how computers operate (e.g., 0V means no data, and 1V means some data). The on/off states can also be mapped to 0 and 1. Computers store data as electrical pulses, and this binary pattern can represent those pulses.
2. If computers used multiple states (e.g., 0 to 9), translating those states into specific voltages would be almost impossible. For example, if we assigned:
    - 0V to 0
    - 1V to 1
    - 2V to 2
    - 3V to 3
    - 4V to 4then even a small accidental increase or decrease in voltage could corrupt the data. This is another reason computers use binary.

# Why Zeros and Ones? The Logic Behind Binary

Computers do not think in numbers, letters, or images. At their lowest physical level, they are electrical machines that represent all information using [[Binary Numbers|binary]], which is a system of representing data using only two states: `0` (off) and `1` (on).

---

## The Physical Reality: Voltage

Inside a computer's processor, data travels along microscopic wires as electrical signals. To represent numbers, we measure the [[Voltage|voltage]], which is the pressure from an electrical power source that pushes electrons through a circuit.

It might seem intuitive to use ten different voltage levels (e.g., 0 Volts for `0`, 1 Volt for `1`, up to 9 Volts for `9`) to match our human base-10 counting system. However, this is practically impossible for two main reasons:
1. **Precision:** Building components that can reliably distinguish between ten tiny differences in voltage is extremely difficult and expensive.
2. **Electrical Noise:** Electrical circuits are constantly subjected to [[Electrical Noise|noise]], which is random, unwanted electrical static caused by heat and nearby components. 

If we used ten levels, a minor fluctuation of 0.2 Volts due to noise could turn a stored `3` into a `2` or a `4`, instantly corrupting your data.

---

## The Solution: Two States and Noise Margins

By using binary, we only have to distinguish between two states: "low voltage" (off/`0`) and "high voltage" (on/`1`). This simplicity makes computers incredibly reliable because we can establish wide buffers called **noise margins**.

A **noise margin** is a safety range of voltages that a circuit accepts as a valid `0` or `1`, protecting the data from electrical noise.

For example, in a typical 5-Volt system:
* **Valid `0`:** Any voltage between **0.0 Volts and 0.8 Volts**.
* **Valid `1`:** Any voltage between **2.0 Volts and 5.0 Volts**.

```
   5.0 V +------------------------------+
         |                              |  Valid "1" (ON)
   2.0 V +------------------------------+
         |                              |  Undefined Zone (Danger!)
   0.8 V +------------------------------+
         |                              |  Valid "0" (OFF)
   0.0 V +------------------------------+
```

### The Undefined Zone
The gap between 0.8 Volts and 2.0 Volts is the **undefined zone** (or transition zone). If a signal falls within this zone, the circuit cannot reliably tell if it is a `0` or a `1`. 

Because voltages cannot teleport instantly, a signal must pass through this undefined zone when switching from `0` to `1`. Electronic components are designed to switch as fast as possible so that signals spend almost zero time in this unstable zone.

---

## How We Control the States

To switch these voltages on and off, the computer uses billions of tiny electronic switches called [[Explain how a bit is physically stored - transistor as switch → logic gate → flip-flop|transistors]]. 

By turning these switches on and off, we can route voltages, perform calculations, and store data reliably, even if the electrical power fluctuates slightly.