# SEMESTER 1 — CS Foundations + How Computers Work

## Weeks 1–6 | 180 hours | Deadline: End of Week 6

**Why this first:** Every confusion you have about why Django behaves a certain way, why JavaScript is weird, why databases are slow — it all roots back to not understanding what a computer is actually doing. Fix the root.

**What you get from the BSc curriculum:** CS50 Harvard (foundational computer science thinking) **What you get from the engineering curriculum:** How the web works, Linux, Git deeply

---

### Week 1 — How a Computer Works

**Deadline: 7 days from start | 30 hours**

#### What to Study (20 hours)

**Topic 1: CPU, Memory, Storage, Operating Systems (16 hours)**

Watch: Crash Course Computer Science — Episodes 1 to 8, Episodes 18 to 21. 
Link: [https://www.youtube.com/playlist?list=PL8dPuuaLjXtNlUrzyH5r6jN9ulIgZBpdo](https://www.youtube.com/playlist?list=PL8dPuuaLjXtNlUrzyH5r6jN9ulIgZBpdo)

What you must understand clearly after watching:

- What does a CPU actually do per clock cycle?
- What is RAM, what is cache (L1/L2/L3), what is SSD/HDD — and why is each one ~1000x slower than the next?
- What is a register? Why is it faster than cache?
- What is binary and why do computers use it? What's a bit, byte, KB, MB, GB?
- What is the difference between a program on disk vs a program running in memory?
- What is an OS? What would happen without one?
- What is a process? What is a thread?
- What is a file system? What happens when you create a file?
- What is virtual memory? Why does your computer not crash when one app uses too much RAM?

**Topic 2: How Code Becomes Execution (6 hours)**

Read: First 3 chapters of "Code: The Hidden Language of Computer Hardware and Software" by Charles Petzold Find: Search "Petzold Code book PDF" — it's widely available free online

What you must understand:

- How does Python source code become something a CPU can run?
- What is an interpreter vs a compiler?
- What is machine code?
- What does "running a program" actually mean at the hardware level?

#### What to Build/Practice (7 hours)

Open your terminal. Run these commands and understand every output:

bash

```bash
# See all running processes
ps aux

# See memory usage
free -h   # Linux / Mac: vm_stat

# See disk usage
df -h

# See what a file actually is at the binary level
xxd yourfile.py | head -20

# Run python and watch it as a process
python3 app.py &
ps aux | grep python
kill [process_id]
```

Write a short note (not for me — for yourself) answering: "What happens between when I type `python app.py` and when my code starts running?"

#### Check-in Deliverable (3 hours)

Come to me at the end of Week 1. I will ask you:

1. Draw me the journey of `python app.py` from terminal command to CPU execution
2. What is the difference between RAM and your SSD in terms of speed and purpose?
3. What is a process? What is a thread? Give me a real analogy.
4. Why does a computer use binary?

You answer these in your own words. No notes. I will ask follow-up questions.

---

### Week 2 — How the Internet Works

**Deadline: 14 days from start | 30 hours**

#### What to Study (20 hours)

**Topic 1: Networks from the Ground Up (8 hours)**

Read: Computer Networking: A Top-Down Approach — Chapter 1 (find PDF online, Kurose & Ross)

What you must understand:

- What is an IP address? How is it different from a domain name?
- What is a packet? Why do we break data into packets?
- What is TCP and what problem does it solve?
- What is UDP and when would you use it instead of TCP?
- What is the difference between a LAN and the wider internet?
- What is a router? What is a switch?
- What is bandwidth vs latency? Why does latency matter more for most web apps?

**Topic 2: DNS — The Internet's Phone Book (4 hours)**

Read: Julia Evans' DNS blog posts — [https://jvns.ca/categories/dns/](https://jvns.ca/categories/dns/) Read the first 5 posts in that category.

What you must understand:

- What happens step by step when you type `bhara.com` in your browser?
- What is a DNS resolver? What is an authoritative nameserver?
- What is a DNS record? What are A, CNAME, MX, TXT records?
- What does TTL mean for DNS and why does it matter for deployment?
- What is DNS caching and why can DNS changes take time to propagate?

**Topic 3: HTTP — The Language of the Web (8 hours)**

Read: MDN Web Docs — HTTP section Links:

- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)

What you must understand deeply:

- HTTP methods: GET, POST, PUT, PATCH, DELETE — what does each one _mean_ semantically?
- Status codes: Know 200, 201, 204, 301, 302, 400, 401, 403, 404, 409, 422, 500, 502, 503 — when each one should be used
- Headers: What is Content-Type? Authorization? Accept? Cache-Control? Cookie? CORS headers?
- What is a request body vs query parameters vs path parameters — when to use each?
- What is HTTPS and what does TLS actually do?
- What is REST and what makes an API "RESTful"?
- What is statelessness and why it matters?

#### What to Build/Practice (7 hours)

**Exercise 1:** Use `curl` to make HTTP requests from terminal

bash

```bash
# Make a GET request and see full headers
curl -v https://api.github.com

# Make a POST request with JSON body
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"name": "test", "value": 123}'

# See the redirect chain
curl -v -L https://github.com

# Test different status codes
curl -v https://httpbin.org/status/404
curl -v https://httpbin.org/status/500
```

Read every line of every output. Understand what each header means.

**Exercise 2:** Read the full "What happens when" document Link: [https://github.com/alex/what-happens-when](https://github.com/alex/what-happens-when) This document describes exactly what happens when you type a URL and press enter. Read it carefully. Take notes.

**Exercise 3:** Open your Bhara project in the browser. Open DevTools → Network tab. Do a full page load and a login. Look at every request. For each request, note:

- What URL was hit?
- What method?
- What was in the request headers?
- What was the response status?
- How long did it take?

#### Check-in Deliverable

Come to me at end of Week 2. I will ask you to walk me through — step by step, in detail — what happens when a user types `bhara.com` and presses Enter, all the way until the page renders. Every step from DNS to browser paint.

---

### Week 3 — Linux, Terminal & Git Properly

**Deadline: 21 days from start | 30 hours**

#### What to Study (18 hours)

**Topic 1: Linux and the Terminal (10 hours)**

Read: The Linux Command Line by William Shotts — Chapters 1 through 12 Free online: [https://linuxcommand.org/tlcl.php](https://linuxcommand.org/tlcl.php)

Commands you must be able to use without thinking:

bash

```bash
# Navigation
cd, ls, ls -la, pwd, mkdir, rm, rm -rf, cp, mv, touch

# File reading
cat, less, head, tail, tail -f (for live log watching)

# Searching
grep, grep -r, find, grep -i

# Permissions
chmod, chown, ls -la (read rwx)

# Processes
ps aux, kill, pkill, top, htop

# Networking
curl, wget, netstat, lsof -i :8000

# Text processing
echo, sed, awk (basics), sort, uniq, wc

# Piping
command1 | command2 | command3

# Redirection
command > file.txt
command >> file.txt
command 2>&1 | tee file.txt

# Environment
export, env, echo $PATH, which python3

# SSH
ssh user@server, scp file user@server:/path
```

Practice: For 2 full days, use ONLY the terminal for file operations. No file explorer, no GUI.

**Topic 2: Git — Actually Understanding It (8 hours)**

Read: Pro Git — Chapters 1 through 3 Free: [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)

What you must understand:

- What is a Git object? What is a commit at the data structure level?
- What is a branch really? (It's just a pointer to a commit)
- What is HEAD?
- What does `git merge` do step by step?
- What does `git rebase` do and when should you use it instead of merge?
- What is a merge conflict and what are you actually choosing between?
- What is `git stash`? `git cherry-pick`? `git bisect`?
- What is the difference between `git reset --soft`, `--mixed`, `--hard`?
- What is `git reflog` and why is it your safety net?
- How does a pull request work internally?

#### What to Build/Practice (9 hours)

**Git Practice — Do this entire exercise:**

bash

```bash
# 1. Create a new repo
mkdir git-practice && cd git-practice && git init

# 2. Create 5 commits with meaningful messages
echo "line 1" > file.txt && git add . && git commit -m "Add line 1"
echo "line 2" >> file.txt && git add . && git commit -m "Add line 2"
# ... continue

# 3. Create a branch, make changes, merge it
git checkout -b feature/experiment
echo "feature work" >> feature.txt
git add . && git commit -m "Add feature work"
git checkout main
git merge feature/experiment

# 4. Intentionally create a merge conflict and resolve it manually
git checkout -b branch-a
echo "version A" > conflict.txt && git add . && git commit -m "Branch A version"
git checkout main
git checkout -b branch-b
echo "version B" > conflict.txt && git add . && git commit -m "Branch B version"
git checkout main
git merge branch-a
git merge branch-b   # This will conflict — resolve it manually

# 5. Practice rebase
git checkout -b rebase-practice
# make 3 commits
git rebase main

# 6. Use git bisect to find a bug
# Make 10 commits, introduce a bug in commit 5
# Use git bisect to find exactly which commit introduced it
```

#### Check-in Deliverable

Come to me at end of Week 3. I will ask:

1. What is the difference between `merge` and `rebase`? When would you use each in a real project?
2. You accidentally committed sensitive data 3 commits ago. How do you remove it from history?
3. Explain to me what `HEAD~3` means.
4. Walk me through what `git push origin main` is actually doing at the network level.

---

### Week 4 — CS50 + How Your Languages Work (Python)

**Deadline: 28 days from start | 30 hours**

#### What to Study (20 hours)

**Topic 1: CS50 — The Parts You Need (8 hours)**

Watch: CS50 Weeks 0, 1, and 6 (the Python week) Link: [https://cs50.harvard.edu/x/](https://cs50.harvard.edu/x/)

Week 0 and 1: This covers how computers represent data — binary, ASCII, memory addresses, pointers. Even if basic, watch carefully for the mental models.

Week 6: CS50 Python — watch this and note the things that are explained differently from how you learned them.

**Topic 2: Python — How It Actually Works (12 hours)**

You use Python. Now understand it.

Read: Python documentation — Data Model section Link: [https://docs.python.org/3/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)

Concepts you must understand deeply:

- What is a Python object? Everything in Python is an object — what does that mean?
- What is a reference in Python? What is reference counting?
- Mutable vs immutable — what does this mean in memory? Why does it matter?
- What happens when you pass a list to a function vs an integer?
- What is the GIL (Global Interpreter Lock)? Why does Python have it?
- What happens when you `import` a module? What is `__pycache__`?
- What is a decorator? Write one from scratch that times function execution.
- What is `*args` and `**kwargs` — not syntax, but what they actually are
- What is a generator and why does it use less memory than a list?
- What is a context manager (`with` statement) and how do you write one?
- What is a class in Python at the memory level?
- What is `__init__`, `__str__`, `__repr__`, `__eq__`?
- What is multiple inheritance and the MRO (Method Resolution Order)?

#### What to Build (8 hours)

Build these from scratch — no frameworks, no libraries except Python standard library, no AI:

**Project 1: Timer Decorator (1 hour)**

python

```python
# Write a decorator that:
# 1. Times how long any function takes to run
# 2. Logs the function name and time
# 3. Works on any function regardless of arguments
```

**Project 2: Memoization Cache (2 hours)**

python

```python
# Write a decorator @memoize that:
# 1. Caches function results based on arguments
# 2. Returns cached result if same args called again
# 3. Has a max_size limit — evicts oldest when full
# Do NOT use functools.lru_cache
```

**Project 3: Context Manager (1 hour)**

python

```python
# Write a context manager for database connections that:
# 1. Opens a connection on enter
# 2. Commits on successful exit
# 3. Rolls back on exception
# 4. Always closes the connection
```

**Project 4: Generator Pipeline (2 hours)**

python

```python
# Process a large CSV file (generate a fake one with 1 million rows)
# Using generators (NOT reading whole file into memory):
# 1. Read line by line
# 2. Filter rows where age > 25
# 3. Transform: capitalize name
# 4. Write results to new file
# Compare memory usage with the non-generator approach
```

**Project 5: Class System (2 hours)**

python

```python
# Build a simple ORM-like system:
# A base Model class where:
# 1. Subclasses define fields as class attributes
# 2. Model.create(**kwargs) creates an instance
# 3. instance.save() prints SQL it would run
# 4. Model.filter(**kwargs) prints SQL it would run
# This teaches you what Django's ORM is doing underneath
```

#### Check-in Deliverable

Come to me. I will ask you to live-code the memoization cache from scratch while explaining what you're doing.

---

### Week 5 — How JavaScript Actually Works

**Deadline: 35 days from start | 30 hours**

#### What to Study (18 hours)

**Topic 1: The JavaScript Runtime (10 hours)**

Read: You Don't Know JS — Scope & Closures and this & Object Prototypes Free: [https://github.com/getify/You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS)

Watch: Jake Archibald "In The Loop" — JSConf talk on YouTube (best event loop explanation ever made) Link: Search "Jake Archibald In The Loop JSConf"

Concepts you must understand:

- What is the JavaScript engine? (V8 in Chrome/Node)
- What is the call stack? Draw it.
- What is the heap?
- What is the event loop? Draw it. Understand exactly what it does on each tick.
- What is the task queue (macrotask queue)?
- What is the microtask queue? Why do Promises resolve before setTimeout?
- What is a closure? Write an example where removing the closure would break things.
- What is `this`? Why does it change? When is it lexically bound vs dynamically bound?
- What is hoisting? Why does `var` behave differently from `let`?
- What is the prototype chain? How does inheritance work in JS?
- What is a Promise under the hood — what are its three states?
- What does `async/await` actually compile to?
- What is the difference between `null` and `undefined`?
- What is type coercion and why is `[] + {}` not an error?

**Topic 2: Node.js Runtime (8 hours)**

Read: Node.js official documentation — "About Node.js" and "The Node.js Event Loop" Links:

- [https://nodejs.org/en/about](https://nodejs.org/en/about)
- [https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick)

What you must understand:

- What is libuv?
- How does Node handle many requests without threads?
- What is non-blocking I/O?
- When does Node.js actually use threads?
- What is `process.nextTick` vs `setImmediate` vs `setTimeout`?
- What is a stream? Why is it better than loading a full file?

#### What to Build (9 hours)

**Project 1: Explain the output (do these before writing any code)**

Predict the output of each snippet, then run it to verify. For each wrong prediction, understand exactly why:

javascript

```javascript
// Snippet 1
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);

// Snippet 2
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}

// Snippet 3
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}

// Snippet 4
function outer() {
  let count = 0;
  return function inner() {
    count++;
    return count;
  }
}
const counter1 = outer();
const counter2 = outer();
console.log(counter1()); // ?
console.log(counter1()); // ?
console.log(counter2()); // ?
```

**Project 2: Build a Promise from scratch (3 hours)**

javascript

```javascript
// Implement MyPromise class with:
// - constructor that takes executor function
// - .then() method
// - .catch() method
// - static MyPromise.resolve()
// - static MyPromise.all()
// Do NOT use native Promise anywhere inside it
```

**Project 3: Build a simple event emitter (2 hours)**

javascript

```javascript
// Build EventEmitter class with:
// - .on(event, listener)
// - .off(event, listener)
// - .emit(event, ...args)
// - .once(event, listener) — fires only once then removes itself
```

**Project 4: Async rate limiter (2 hours)**

javascript

```javascript
// Build a function rateLimiter(fn, limit, interval) that:
// Wraps any async function
// Allows max `limit` calls per `interval` milliseconds
// Queues additional calls and processes them when slots open
```

#### Check-in Deliverable

I will ask you to draw the event loop on paper and explain exactly why Snippet 1 logs 1, 4, 3, 2.

---

### Week 6 — TypeScript + CS50 Completion + Semester Review

**Deadline: 42 days from start | 30 hours**

#### What to Study (15 hours)

**Topic 1: TypeScript — Why It Exists (7 hours)**

Read: TypeScript Handbook — The full official handbook Link: [https://www.typescriptlang.org/docs/handbook/intro.html](https://www.typescriptlang.org/docs/handbook/intro.html)

What you must understand:

- What is a type system? What problem does TypeScript solve that JavaScript doesn't?
- What is the difference between static and dynamic typing?
- What is type inference?
- What is the difference between `interface` and `type`?
- What is `any` and why is it dangerous?
- What is `unknown` and how is it safer than `any`?
- What is a generic? Write 5 generic functions and explain why each needs to be generic.
- What are utility types: `Partial<T>`, `Required<T>`, `Pick<T,K>`, `Omit<T,K>`, `Record<K,V>`, `Readonly<T>`?
- What is a discriminated union? When is it useful?
- What is type narrowing?
- What does TypeScript actually compile to?

**Topic 2: CS50 — Remaining Weeks (8 hours)**

Watch: CS50 Weeks 3 (Algorithms), 4 (Memory), 5 (Data Structures) Link: [https://cs50.harvard.edu/x/](https://cs50.harvard.edu/x/)

Week 4 on Memory is critical. This will make everything about pointers, references, memory leaks click.

#### What to Build (10 hours)

**Semester 1 Consolidation Project:**

Build a fully typed TypeScript CLI application — a simple HTTP request tester (like a basic Postman):

Features:

- Save named requests to a JSON file (url, method, headers, body)
- Execute a saved request using Node's native fetch
- Show response status, headers, body
- Show response time
- Support environment variables in URLs (like `{{BASE_URL}}/users`)

Requirements:

- Full TypeScript — zero `any` types allowed
- Proper error handling for network failures
- Proper generics for the response type system

This project forces you to use: TypeScript, Node.js, file I/O, HTTP, and CLI argument parsing — all from this semester.

#### Semester 1 Final Check-in

**This is your semester exam.** I will ask you 15 questions — mix of concepts and code. You must pass with 12/15 to move on.

Sample questions I might ask:

1. What happens when you type `node server.js` — trace it from terminal to first request being handled
2. What is the difference between a process and a thread? Give me a real-world analogy.
3. Why does JavaScript only have one thread but can handle many requests?
4. Write a memoize decorator in Python from memory
5. Explain DNS to me as if I'm a 15-year-old who is smart but not technical
6. What is `git rebase` and why would you use it instead of merge?
7. What does the `Authorization` header do and how does it work?
8. What is a closure? Write one that demonstrates why it matters
9. What is TCP and why does HTTP use it instead of UDP?
10. What is the TypeScript `unknown` type and why is it safer than `any`?