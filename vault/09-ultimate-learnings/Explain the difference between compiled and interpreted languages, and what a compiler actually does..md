Both [[Compiler|compilers]] and [[Interpreter|interpreters]] convert human-written code into machine code. The main difference is how they handle the process:

- Compilers read the entire file and convert it all at once. If any line contains an error, the code will not compile or run.
- Interpreters execute the code one line at a time and stop as soon as they find an error.


# Compilers, Interpreters, and Virtual Machines: Translating Code

Humans write code in high-level programming languages (like Python, Rust, or JavaScript) because they use readable text, loops, and English-like words. However, as we saw in the [[Describe one full fetch-decode-execute cycle, what each stage does and what synchronizes them|Fetch-Decode-Execute Cycle]], a CPU can only execute binary machine code.

To close this gap, we use translation software. There are two traditional ways to translate code—compilation and interpretation—and a modern hybrid approach that combines both.

---

## 1. Compiled Languages (Ahead-Of-Time Translation)

In a compiled language, code is translated completely *before* the program is run.

A **compiler** is a software tool that reads your entire source code file and translates it all at once into a standalone [[Explain the difference between a program on disk and a program running in memory|executable file]] containing binary machine code.

* **Examples:** C, C++, Rust, Go.
* **How it works:** You run the compiler on your code. If there is a syntax error anywhere in the file, the compiler fails and refuses to build the executable. If it succeeds, you get an executable file (like a `.exe`) that you can distribute.
* **Pros:** Extremely fast, because the CPU runs the raw machine code directly without any translation overhead during runtime.
* **Cons:** Platforms-specific. A compiler converts code for a specific operating system and CPU architecture; a binary compiled for a Windows PC will not run on an Android phone.

---

## 2. Interpreted Languages (On-The-Fly Translation)

In an interpreted language, code is translated *while* the program is running.

An **interpreter** is a software program that reads your source code line-by-line, parses its meaning, and immediately executes the corresponding actions on the fly.

* **Examples:** Python, Ruby, PHP.
* **How it works:** You run the interpreter program and hand it your source code file. The interpreter reads line 1, executes it, reads line 2, executes it, and so on. If line 50 has a syntax error, the program will run fine for the first 49 lines and then crash when it reaches the error.
* **Pros:** Cross-platform and quick to test. The same Python script can run on Windows, Mac, or Linux, as long as that system has a Python interpreter installed.
* **Cons:** Slower. The computer has to spend CPU time translating each line of code into action while the program is running.

---

## 3. The Modern Hybrid: Bytecode and Virtual Machines

Most modern programming languages do not fit neatly into these two boxes. Instead, they use a hybrid approach to get the speed of compilation with the flexibility of interpretation.

* **Examples:** Java, C#, Python, JavaScript.

### The Hybrid Workflow

```
[ High-Level Code ]
        |
        | (Compile AOT)
        v
   [ Bytecode ]
        |
        | (Translate at Runtime)
        v
 [ Virtual Machine ]
        |
        | (Execute on Hardware)
        v
   [ Physical CPU ]
```

1. **Compile to Bytecode:** Instead of translating code all the way to native machine code, a compiler first translates the source code into **bytecode**. Bytecode is a highly optimized, compact, intermediate instruction set designed for a simulated, software-defined computer.
2. **Execute via Virtual Machine:** When you run the program, a software program called a **virtual machine (VM)** (like the Java Virtual Machine) reads the bytecode. The VM acts as a translator, reading the bytecode and translating it into the physical machine code of your specific CPU.

### JIT (Just-In-Time) Compilation
To make hybrid languages run even faster, modern virtual machines use a **JIT (Just-In-Time) compiler**. A JIT compiler monitors the running bytecode, identifies sections of code that run repeatedly (called "hot loops"), compiles those specific sections into native physical machine code on the fly, and saves them. The next time that loop runs, the CPU executes it at full native speed.