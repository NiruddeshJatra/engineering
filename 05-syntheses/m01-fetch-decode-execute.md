---
type: synthesis
module: 1
question: "Describe one full fetch-decode-execute cycle, what each stage does and what synchronizes them"
score:
last_recalled:
---
Let’s walk through what happens when we type `print 3 + 4`.
The Python [[Interpreter|interpreter]] stores it in [[RAM]] as strings, not as meaningful values. When the [[Programs|program]] runs, a parser reads those strings and runs [[ALU]] operations to turn them into usable values, like the integers 3 and 4.
After that, they are loaded into the [[Cache|cache]]. If the [[CPU]] finishes its current work, they are loaded into the instruction [[Register|registers]]. This is called fetching. In one CPU cycle, it fetches the instructions and the data and loads the registers. In the next cycle, it decodes what the instructions mean, what the data means, and what to do. In the next cycle, it executes the operation. Everything is synchronized by the [[Clock|clock]], which sends pulses billions of times a second, and each operation or phase (fetch, decode, execute) happens in each clock pulse.


