# Low-Latency Systems Engineering — Notes & Resources
# Ngoni Maposa

---

## All Resources

### C++ Resources
| Resource | URL | When to use |
|---|---|---|
| cppreference.com | https://en.cppreference.com | Always open. Exact lookup for any C++ function or feature. |
| learncpp.com | https://www.learncpp.com | When Ghosh uses a concept you don't fully understand. Look up that specific section only. |
| Ghosh reference repo | https://github.com/PacktPublishing/Building-Low-Latency-Applications-with-CPP | Check your code against this when something doesn't work. Never copy from it first. |
| Stacy Gaudreau companion blog | https://stacygaudreau.com/blog/cpp/low-latency-cpp-for-hft-part2 | Stuck on a specific Ghosh chapter. Read after you've attempted it yourself. |
| Google Benchmark | https://github.com/google/benchmark | Measuring performance Ch1-10. Install with: sudo apt install -y libbenchmark-dev |

### Linux & Performance Resources
| Resource | URL | When to use |
|---|---|---|
| Brendan Gregg perf examples | https://www.brendangregg.com/perf.html | Profiling your programs — Ghosh Ch11-12, after CS:APP labs. |
| Brendan Gregg Linux perf map | https://www.brendangregg.com/linuxperf.html | Reference map of all Linux performance tools. Which tool to use when. |
| Flamegraph scripts | https://github.com/brendangregg/FlameGraph | Visualising where time is spent — Ghosh Ch11-12. |
| Mechanical Sympathy blog | https://mechanical-sympathy.blogspot.com | Read every new post when it appears. Check Feedly on Sundays. |
| Billimoria book | https://z-lib.fm/book/VZw2EOlWAm/linux-kernel-programming-second-edition.html | Alongside CS:APP labs — specific chapters mapped below. |
| LFD103 Linux Foundation free course | https://trainingportal.linuxfoundation.org/learn/course/a-beginners-guide-to-linux-kernel-development-lfd103 | After finishing Billimoria Ch4-5. Hands-on kernel patch assignments. |

### Labs & Projects
| Resource | URL | When to use |
|---|---|---|
| CS:APP labs repo | https://github.com/yyqian/csapp-labs | Saturday sessions. Starter code for all four labs. |
| bpf-developer-tutorial | https://github.com/eunomia-bpf/bpf-developer-tutorial | Phase 2, month 4+. After CS:APP labs are done. |
| CodeCrafters Redis | https://app.codecrafters.io/courses/redis/overview | After CS:APP labs. Networking and concurrency in a new context with automated tests. |

---

## Benchmarking — Two Tools, Two Purposes

### Google Benchmark (use Ch1-10)
The right tool for learning. Handles statistical analysis, warmup, and variance automatically.

Install once on Ubuntu:
```
sudo apt install -y libbenchmark-dev
```
What this does: apt is Ubuntu's package manager. install -y says yes to all prompts automatically.
libbenchmark-dev installs Google Benchmark including the header files you need to use it in your code.
The -dev suffix always means "includes headers for development" not just the compiled library.

Write a benchmark file — example for lock-free queue from Ghosh Ch4:
```cpp
#include <benchmark/benchmark.h>

static void BM_LockFreeQueue(benchmark::State& state) {
    // set up your queue here
    for (auto _ : state) {
        // put the operation you want to measure here
        // e.g. queue.push(42); queue.pop(val);
    }
}
BENCHMARK(BM_LockFreeQueue);
BENCHMARK_MAIN();
```

Compile and run:
```
g++ -std=c++20 -O2 -o bench my_bench.cpp -lbenchmark -lpthread
./bench
```
Breaking this down:
- g++ is the C++ compiler
- -std=c++20 uses the C++20 standard (Ghosh requires this)
- -O2 enables level 2 compiler optimisation — use this always for benchmarks
- -o bench names the output binary
- -lbenchmark links the Google Benchmark library
- -lpthread links POSIX threads which Benchmark needs internally

Output looks like:
```
Benchmark            Time      CPU   Iterations
BM_LockFreeQueue    45 ns     44 ns   15000000
```
Time = wall clock. CPU = CPU time. Iterations = how many times it ran to get a stable number.
Write these numbers in benchmarks.md with a date.

### rdtsc (learn in Ghosh Ch11)
What real HFT systems use. Measures in CPU cycles — more precise than any clock.
Ghosh teaches this directly in Ch11. Don't try to use it before then.

---

## C++ Weekly — Monday Morning Habit
Watch every Monday when the new episode drops.
youtube.com/@cppweekly
One episode, 15-20 minutes, every Monday.
Not mapped to specific chapters — consistent weekly input that compounds.
When an episode covers something you are working on in Ghosh that week, go deeper on learncpp.com for that topic.

---

## How to Start a Coding Session

### Starting Ghosh work
```
cd ~/dev/trading-systems/ghosh/ch01
```
What this does: cd means change directory. This moves you into the ch01 folder inside your trading-systems repo.
Replace ch01 with whichever chapter you are on.

Create your file and start coding:
```
touch main.cpp
code .
```
What this does: touch creates an empty file. code . opens VS Code in the current folder.

Compile and run:
```
g++ -std=c++20 -O2 -o main main.cpp
./main
```
What this does:
- g++ compiles your C++ file
- -std=c++20 sets the language standard Ghosh requires
- -O2 enables optimisation — always use this, not -O0, because production code runs with optimisation
- -o main names the output binary "main"
- ./main runs it — the ./ means "in the current directory"

Commit when done:
```
git add .
git commit -m "Ghosh Ch1 complete"
```
What this does:
- git add . stages all changed files in the current folder
- git commit -m saves a snapshot with that message
- Replace Ch1 with the actual chapter number

### Starting CS:APP Shell Lab
Copy starter files from the source repo:
```
cd ~/dev/trading-systems/csapp-labs/shell-lab
cp -r ../source/shlab/* .
```
What this does:
- cd moves you into your shell-lab working folder
- cp -r copies recursively (folders and their contents)
- ../source/shlab/* means everything inside shlab, which is one level up in the source folder
- . means paste it here in the current folder

Read the writeup first — it is inside the folder and explains exactly what to build.
Open tsh.c in VS Code. This is the file you edit.

Build your shell:
```
make
```
What this does: make reads the Makefile already in the folder and compiles everything automatically.
You don't need to write compile commands yourself — the Makefile handles it.

Run one test:
```
./sdriver.pl -t trace01.txt -s ./tsh -a "-p"
```
What this does:
- ./sdriver.pl is the automated test driver script
- -t trace01.txt specifies which test to run (trace01 through trace16, each tests different behaviour)
- -s ./tsh tells it to test your shell binary
- -a "-p" passes arguments to your shell

Run all 16 tests at once:
```
make test
```

Success: all 16 traces match the reference output exactly. Zero differences shown.

### Starting CS:APP Malloc Lab
```
cd ~/dev/trading-systems/csapp-labs/malloc-lab
cp -r ../source/malloclab/* .
make
./mdriver -V
```
What this does:
- mdriver is the malloc lab grader
- -V means verbose — shows you details about each test
- Reports a score out of 100 covering correctness, memory utilisation, and throughput

Success: score above 90/100.

### Loading a Billimoria Kernel Module
After writing a module in your billimoria/ folder:
```
make
sudo insmod module_name.ko
dmesg | tail -20
sudo rmmod module_name
```
What each command does:
- make compiles the kernel module using the Makefile Billimoria provides
- sudo insmod loads the module into the running kernel. sudo required because this modifies the kernel.
- module_name.ko is your compiled module — .ko means kernel object
- dmesg shows kernel log messages. | tail -20 shows the last 20 lines — where your module's printk output appears
- sudo rmmod unloads the module cleanly

Success: module loads without errors. dmesg shows the output you expected. rmmod unloads cleanly.

---

## Ghosh Book — Chapter Notes

---

### Chapter 1 — Introducing Low Latency Application Development in C++
**Before starting this chapter, watch:**
CppCon — "Efficiency with Algorithms, Performance with Data Structures" — Chandler Carruth
https://www.youtube.com/watch?v=fHNmRkzxHWs
Watch this before writing any code. It resets your mental model of what performance actually means.

**What this chapter covers:**
Why C++ for low-latency, what latency means in practice, overview of the trading ecosystem you will build.
No code to benchmark — this chapter is conceptual context.

**learncpp.com:** Only if you feel rusty on basic C++ — read sections 1-8. Given your intro C++ background you likely don't need this.

**Companion blog:** Read alongside Ch1
https://stacygaudreau.com/blog/cpp/low-latency-cpp-for-hft-part2

**Notes:**
[Write: what surprised you about how C++ achieves low latency vs other languages. 2-3 sentences.]

**Benchmark results:** None for this chapter.

---

### Chapter 2 — Designing Common Low Latency Applications in C++
**What this chapter covers:**
Low-latency constraints in video streaming, gaming, IoT, and trading. Context-setting chapter.
No code to benchmark.

**Notes:**
[Write: one thing you learned about trading latency constraints that wasn't obvious. 2-3 sentences.]

**Benchmark results:** None.

---

### Chapter 3 — Exploring C++ Concepts from a Low-Latency Perspective
**Watch this chapter:**
CppCon — "Want fast C++? Know your hardware" — Timur Doumler
https://www.youtube.com/watch?v=BP6NxVxDQIs

**What this chapter covers:**
C++ features that help vs hurt latency — avoiding heap allocation, avoiding exceptions, avoiding virtual functions, compiler optimisations.

**learncpp.com — read after this chapter if anything was unfamiliar:**
- Move semantics section — if the chapter uses std::move and you are not solid on it
- RAII section — if the chapter discusses resource management and it felt unclear
You have intro C++ background — most of Ch3 should be familiar. Only look up what actually stops you.

**Godbolt — use here for the first time:**
https://godbolt.org
After writing any performance-critical function from this chapter, paste it into Godbolt.
See the assembly the compiler generates. Does it optimise what you expected?
No setup needed — paste code in the browser, select your compiler (GCC with -O2), see assembly instantly.

**Notes:**
[Write: which C++ feature had the most surprising performance impact in this chapter and why. 2-3 sentences.]

**Benchmark results:**
[Record any benchmark numbers the chapter provides, with your VM results alongside.]

---

### Chapter 4 — Building the C++ Building Blocks
**This is the most important chapter in the book. Take more time here than any other.**

**What this chapter covers:**
Threading with CPU affinity, memory pool design, lock-free queues, logging framework, network sockets.
Every subsequent chapter uses what you build here.

**Watch after the lock-free queue section:**
CppCon — "The Speed of Concurrency" — Fedor Pikus
https://www.youtube.com/watch?v=v1gb397uFC4
Watch this after you finish the lock-free queue section, not before.

**Google Benchmark — start using it from this chapter:**
Measure everything you build.
Write benchmark files in ~/dev/trading-systems/benchmarks/
See benchmarking section at the top of this file for how to compile and run.

**learncpp.com — read these AFTER the specific section that used them:**
- After threading section: full concurrency section
  Includes: mutexes, condition variables, atomics, futures, memory ordering
  Read all of it — not just what Ghosh used. This fills in adjacent concepts.
- After memory pool section: full memory management section
  Includes: smart pointers, dynamic allocation, placement new
- After lock-free queue: read std::atomic on cppreference.com carefully
  Every memory_order value — understand what acquire/release actually guarantees
  This is the most important reference lookup in the entire book.

**Companion blog:**
https://stacygaudreau.com/blog/cpp/low-latency-cpp-for-hft-part2
Read after completing the chapter. Different angle on the same material.

**Notes:**
[Write: what memory ordering you used in the lock-free queue and why. What would break if you used seq_cst everywhere instead. 3-4 sentences.]

**Benchmark results:**
- Lock-free SPSC queue latency median:
- Lock-free SPSC queue latency p99:
- vs std::queue + mutex median:
- Speedup factor:
- Memory pool allocation latency:
- vs new/delete median:
- Speedup factor:

---

### Chapter 5 — Designing Our Trading Ecosystem
**Before this chapter, read:**
NASDAQ ITCH 5.0 specification — 2 hours, free PDF
https://www.nasdaqtrader.com/content/technicalsupport/specifications/dataproducts/NQTVITCHspecification.pdf
This is the real protocol that exchanges use to publish market data.
Everything in Ch5-12 makes more sense after reading it.
You don't need to memorise it — just understand the structure of how market data flows.

**What this chapter covers:**
Architecture of the complete system: matching engine, order gateway, market data publisher, trading algorithms.
Design chapter — you understand what you are building before you build it.

**Notes:**
[Write: describe the flow of one order from trader submitting it to it getting matched. 3-4 sentences.]

**Benchmark results:** None for design chapter.

---

### Chapter 6 — Building the C++ Matching Engine
**What this chapter covers:**
Order book data structure, order matching logic, price-time priority.
One of the most performance-critical components in any HFT system.

**Google Benchmark — measure:**
Order insert latency and order match latency.
Target: sub-microsecond median. If your VM gives 2-5 microseconds that is fine — VMs add overhead.

**Godbolt:**
Paste the core order book loop and check if the compiler vectorises it.

**Notes:**
[Write: what data structure Ghosh used for the order book and why not std::map. What price-time priority means. 2-3 sentences each.]

**Benchmark results:**
- Order insert latency median:
- Order insert latency p99:
- Order match latency median:
- Order match latency p99:

---

### Chapter 7 — Communicating with Market Participants
**What this chapter covers:**
Market data protocol, order data protocol, building the order gateway server, market data publisher.
Network programming in C++ — UDP sockets, multicast.

**cppreference.com:** Look up any socket API you haven't used before — SO_RCVBUF, setsockopt, struct sockaddr_in.

**Notes:**
[Write: what UDP gives you over TCP for market data. What you give up. 2-3 sentences.]

**Benchmark results:**
- Message send latency:
- Message receive latency:

---

### Chapter 8 — Processing Market Data and Sending Orders
**What this chapter covers:**
Subscribing to market data, decoding ITCH-format messages, building order books from live data, sending orders.

**Notes:**
[Write: how the market data consumer decodes packets and updates the order book. Where the main bottleneck was.]

**Benchmark results:**
- Market data decode latency per message:
- Order book update latency:

---

### Chapter 9 — Building the Trading Algorithm Building Blocks
**What this chapter covers:**
Position management, PnL tracking, risk management, feature computation, order management.

**Notes:**
[Write: what a "feature" means in the context of a trading algorithm. 2-3 sentences.]

**Benchmark results:**
- Feature computation latency:

---

### Chapter 10 — Building Market Making and Liquidity Taking Algorithms
**What this chapter covers:**
Market making algorithm, liquidity taking algorithm, full trade engine framework.

**Notes:**
[Write: the difference between market making and liquidity taking. One clear sentence each.]

**Benchmark results:**
- End-to-end signal-to-order latency:

---

### Chapter 11 — Adding Instrumentation and Measuring Performance
**Watch this chapter:**
CppCon — "When Nanoseconds Matter: Ultrafast Trading Systems in C++" — David Gross, CppCon 2024
https://www.youtube.com/watch?v=sX2nF1fW7kI

**What this chapter covers:**
Adding rdtsc-based timing throughout the system, measuring latency at every stage.
This is where you graduate from Google Benchmark to rdtsc — the tool real HFT systems use.

**perf stat — run this on your trading system:**
```
sudo perf stat ./your_trading_binary
```
What this does: perf is Linux's built-in performance profiler.
stat mode runs your program and reports CPU performance counters when it finishes.
You get: cache miss rate, branch misprediction rate, instructions per cycle (IPC), context switches.
Record all of these in benchmark results below.

**Generate a flamegraph — visualise where time is spent:**
```
# Install flamegraph scripts first (once only)
git clone https://github.com/brendangregg/FlameGraph.git ~/tools/flamegraph

# Record
sudo perf record -F 99 -g ./your_trading_binary

# Generate
sudo perf script | ~/tools/flamegraph/stackcollapse-perf.pl | ~/tools/flamegraph/flamegraph.pl > flame.svg
```
What each part does:
- perf record -F 99 samples your program 99 times per second while it runs
- -g captures the call stack at each sample
- perf script converts the recorded data to text
- stackcollapse-perf.pl folds the stacks into flamegraph format
- flamegraph.pl generates the SVG image
- flame.svg is the output — open it in a browser
The flamegraph shows you which functions are taking the most time. Wide boxes = more time spent there.

**Reference:**
Brendan Gregg perf examples: https://www.brendangregg.com/perf.html
Brendan Gregg Linux perf map: https://www.brendangregg.com/linuxperf.html

**Notes:**
[Write: what your biggest latency bottleneck was and what surprised you about where time was actually spent. 3-4 sentences.]

**Benchmark results:**
- End-to-end system latency median:
- End-to-end system latency p99:
- Cache miss rate (from perf stat):
- Branch misprediction rate:
- IPC (instructions per cycle):

---

### Chapter 12 — Analyzing and Optimizing Performance
**Watch alongside:**
Brendan Gregg — Linux Performance Tools — USENIX LISA (search YouTube: "Brendan Gregg LISA 2014")

**What this chapter covers:**
Identifying bottlenecks, applying optimisations, measuring improvement.

**Tools — use all of these:**
- perf record + perf report: find which functions take most time
- Flamegraph: before and after every optimisation. If the flamegraph doesn't change, your optimisation did nothing.
- Godbolt: after changing code, check if the compiler output changed as you expected
- gdb for stepping through specific bottlenecks at runtime

**Run perf report:**
```
sudo perf record -g ./your_binary
sudo perf report
```
What this does: perf report opens an interactive view showing which functions consumed the most CPU time.
Arrow keys to navigate, Enter to drill into a function, q to quit.

**Notes:**
[Write: which optimisation had the biggest impact. Before and after numbers. What the flamegraph showed that you wouldn't have found by reading the code.]

**Benchmark results:**
- Before optimisation end-to-end median:
- After optimisation end-to-end median:
- Improvement:

---

## CS:APP Labs — Notes

---

### Shell Lab — Build a Unix Shell
**What this teaches:**
fork/exec/wait, signals, process groups, job control, pipes.
Directly covers process lifecycle — what the Jane Street Linux Engineer JD requires.

**Read alongside — Billimoria:**
Ch6: Kernel Internals Essentials – Processes and Threads
Read this BEFORE starting the lab.
It explains what the kernel actually does when your code calls fork() and exec().
Open the book again during the lab whenever something surprises you.

**How to set up:**
```
cd ~/dev/trading-systems/csapp-labs/shell-lab
cp -r ../source/shlab/* .
```
What this does: copies all starter files from the cloned source repo into your working shell-lab folder.

**Build your shell:**
```
make
```
Reads the provided Makefile and compiles tsh.c into a binary called tsh.

**Run one test:**
```
./sdriver.pl -t trace01.txt -s ./tsh -a "-p"
```
trace01 through trace16 — run them in order. Each tests different behaviour.
The driver shows your output vs reference output side by side.

**Run all tests:**
```
make test
```

**Success:** All 16 traces match. Zero differences.

**Profile with perf after passing all tests:**
```
sudo perf stat ./tsh
```
Record the context switch count and IPC.

**Notes:**
[Write: what happens in the kernel when you call fork(). What two processes exist after it returns and how are they different. 3-4 sentences.]

**Benchmark results:**
- Context switches per command:
- IPC:

---

### Malloc Lab — Implement malloc/free/realloc
**What this teaches:**
Virtual memory, heap layout, memory fragmentation, pointer arithmetic, alignment.
Directly covers virtual memory — what the Jane Street Linux Engineer JD requires.

**Read BEFORE starting — Billimoria:**
Ch7: Memory Management Internals – Essentials
Explains how the kernel manages virtual memory pages and how the heap is laid out.

**Read AFTER finishing — Billimoria:**
Ch8: Kernel Memory Allocation for Module Authors Part 1
Shows how the kernel itself allocates memory. Completes the picture after you've done it yourself.

**Set up:**
```
cd ~/dev/trading-systems/csapp-labs/malloc-lab
cp -r ../source/malloclab/* .
make
```

**Run the grader:**
```
./mdriver -V
```
What this does:
- mdriver is the automated grader
- -V means verbose — shows details about each test case
- Reports three scores: correctness, memory utilisation, throughput
- Total score out of 100

**Success:** Score above 90/100.

**Notes:**
[Write: what internal vs external fragmentation means. Which one your implementation handles better and why. 3-4 sentences.]

**Benchmark results:**
- Your score out of 100:
- Throughput vs system malloc:

---

### Cache Lab — Cache Simulator and Optimisation
**What this teaches:**
How CPU caches work, cache lines, cache misses, writing cache-friendly code.
Foundational for understanding every benchmark number you measured in Ghosh.

**Read alongside — Billimoria:**
Ch7: Memory Management Internals – Essentials
The same chapter as Malloc Lab — it covers cache behaviour and virtual memory together.

**Set up:**
```
cd ~/dev/trading-systems/csapp-labs/cache-lab
cp -r ../source/cachelab/* .
make
```

**Two parts:**
Part A: write a cache simulator in C
Part B: optimise a matrix transpose to minimise cache misses

**Run tests:**
```
./test-csim        # tests your cache simulator
./test-trans       # tests your matrix transpose
```

**Success:**
Part A: your simulator matches reference output exactly.
Part B: fewer cache misses than the naive implementation.

**Notes:**
[Write: what a cache miss costs in cycles vs a cache hit. What spatial locality means and how your transpose exploits it. 3-4 sentences.]

**Benchmark results:**
- Cache misses naive transpose:
- Cache misses optimised transpose:
- Improvement:

---

### Proxy Lab — Concurrent Caching Web Proxy
**What this teaches:**
Sockets, non-blocking I/O, concurrency, synchronisation.
Linux + networking fully converging — the final lab.

**Read alongside — Billimoria:**
Ch6: Kernel Internals Essentials – Processes and Threads (concurrency section)
Ch9: Kernel Memory Allocation for Module Authors Part 2 (shared memory between threads)

**Set up:**
```
cd ~/dev/trading-systems/csapp-labs/proxy-lab
cp -r ../source/proxylab/* .
make
```

**Run the driver:**
```
./driver.sh
```

**After this lab, start CodeCrafters Redis:**
https://app.codecrafters.io/courses/redis/overview
Builds the same networking and concurrency in a new context with automated stage-by-stage tests.

**Success:** Driver reports full marks.

**Notes:**
[Write: what epoll gives you over select for handling many connections. Why it matters for a proxy under load. 3-4 sentences.]

**Benchmark results:**
- Throughput requests per second:
- Connection latency median:

---

## Billimoria — Linux Kernel Internals Notes
**How to use:**
Read the relevant chapter BEFORE the corresponding CS:APP lab — mapped below.
Write and run the kernel module examples from the book in your billimoria/ folder.
Test every module: load it, check dmesg, unload it. See How to Start section above.

---

### Ch1 — Linux Kernel Programming: A Quick Introduction
**When to read:** Before anything else. Setup and orientation.
**Relevance:** Sets up your workspace and introduces the LKM framework you use in every chapter.
**Given your background:** Yes read this — 30 minutes. Your OS course covered theory not the actual Linux LKM setup.

**Notes:**
[Write: what a loadable kernel module is. How it differs from a normal user-space program. 2 sentences.]

---

### Ch2 — Building the 6.x Linux Kernel from Source Part 1
**When to read:** After Ch1.
**Relevance:** Kernel build system, config options.
**Given your background:** Skim it. Your Ubuntu VM has a pre-built kernel. Only go deep if you hit build issues.

**Notes:** [Note here if you come back to this chapter and why.]

---

### Ch3 — Building the 6.x Linux Kernel from Source Part 2
**When to read:** Only if needed.
**Given your background:** Skip unless you need to rebuild the kernel. Mark here if you return to it.

**Notes:** [Skip for now.]

---

### Ch4 — Writing Your First Kernel Module Part 1
**When to read:** Before Shell Lab. This is where practical kernel work begins.
**Relevance:** Core skill. Write hello world module, load it, see it in dmesg. Every subsequent chapter builds on this.
**Given your background:** Read fully. You have OS theory but not kernel module practice in C.

**After finishing Ch4 and Ch5, do LFD103:**
https://trainingportal.linuxfoundation.org/learn/course/a-beginners-guide-to-linux-kernel-development-lfd103
Free course. Has hands-on assignments that build directly on what Ch4-5 teach.

**Notes:**
[Write: what insmod and rmmod do at the kernel level. What init_module and cleanup_module are. 2-3 sentences.]

---

### Ch5 — Writing Your First Kernel Module Part 2
**When to read:** Immediately after Ch4.
**Relevance:** Module parameters, logging with printk, symbol exports. Completes the LKM foundation.
**Given your background:** Read fully alongside Ch4.

**Notes:**
[Write: how printk differs from printf. Why you cannot use standard C library functions in kernel space. 2-3 sentences.]

---

### Ch6 — Kernel Internals Essentials: Processes and Threads
**When to read:** Before Shell Lab.
**Relevance:** Explains what the kernel does when your shell calls fork() and exec().
task_struct, process states, kernel threads vs user threads.
**Given your background:** Yes read this fully. Your OS course covered scheduling theory. This covers the actual Linux implementation — they are different.

**Notes:**
[Write: what task_struct is. The difference between a process and a thread at the kernel level. 2-3 sentences each.]

---

### Ch7 — Memory Management Internals: Essentials
**When to read:** Before Malloc Lab.
**Relevance:** Virtual memory, page tables, memory zones. Directly underpins what you implement in Malloc Lab.
**Given your background:** Yes read this fully. Your OS course covered paging and segmentation theory. This is the Linux-specific implementation with real data structures.

**Notes:**
[Write: what a page fault is, when it happens, what the kernel does in response. 3-4 sentences.]

---

### Ch8 — Kernel Memory Allocation for Module Authors Part 1
**When to read:** After Malloc Lab.
**Relevance:** kmalloc, kzalloc, vmalloc. How the kernel itself allocates memory.
After implementing malloc yourself this chapter will click immediately.
**Given your background:** Yes read this after Malloc Lab.

**Notes:**
[Write: the difference between kmalloc and vmalloc. When you would use each. 2-3 sentences.]

---

### Ch9 — Kernel Memory Allocation for Module Authors Part 2
**When to read:** After Ch8.
**Relevance:** Slab allocator, memory pools in the kernel. Directly connects to the memory pool you built in Ghosh Ch4.
**Given your background:** Yes — you built a memory pool in Ghosh. This shows how the kernel does the same thing internally. The connection is direct.

**Notes:**
[Write: what the slab allocator is and why it is faster than calling kmalloc every time. 2-3 sentences.]

---

### Ch10 — The CPU Scheduler Part 1
**When to read:** After Cache Lab, before or alongside Ghosh Ch11.
**Relevance:** How the kernel schedules processes. CFS, scheduling policies, priority.
Your earlier C++ project used SCHED_FIFO — this chapter explains what that actually does.
**Given your background:** Yes read this. Your OS course covered scheduling theory. This covers Linux's actual CFS implementation which is quite different.

**Notes:**
[Write: what CFS is and how it decides which process runs next. What SCHED_FIFO gives you that CFS does not. 2-3 sentences each.]

---

### Ch11 — The CPU Scheduler Part 2
**When to read:** Immediately after Ch10.
**Relevance:** CPU affinity, NUMA, real-time scheduling.
Connects directly to the thread affinity work in Ghosh Ch4.
**Given your background:** Yes read this. This is where theory meets the performance numbers you measured.

**Notes:**
[Write: what CPU affinity means and why pinning a trading thread to a specific core reduces latency and jitter. 3-4 sentences.]

---

## bpf-developer-tutorial — eBPF Notes
Start month 4 — after CS:APP labs are complete.
https://github.com/eunomia-bpf/bpf-developer-tutorial

Before starting any lessons, read Brendan Gregg's eBPF learning guide:
https://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html

Install bpftrace first:
```
sudo apt install -y bpftrace
```
What this does: installs bpftrace, a high-level eBPF tracing tool. You use this before writing raw eBPF programs.

---

### Lesson 0 — Hello World eBPF
**Notes:**
[Write: what eBPF is in one sentence. How it differs from writing a kernel module. 2 sentences.]

---

### Lesson 1 — Tracing Process Execution
**Connects to:** Billimoria Ch6. You are now observing from outside what Ch6 explained from inside.

**Notes:**
[Write: what a kprobe is and how eBPF attaches to it. 2-3 sentences.]

---

### Lesson 2 — Monitoring unlink System Calls
**Notes:**

---

### Lesson 3 — Monitoring File Writes
**Notes:**

---

### Lesson 4 — Capturing Process Scheduling
**Connects to:** Billimoria Ch10-11. You are tracing the exact scheduler events you read about.

**Notes:**
[Write: what you can now observe about the scheduler that Billimoria Ch10 described theoretically. 2-3 sentences.]

---

### Lesson 5 — Capturing TCP Connect Events
**Connects to:** Ghosh Ch7. You can now observe the TCP connections your trading system makes.

**Notes:**

---

### Lesson 6 — Capturing exec/exit Events
**Notes:**

---

### Lesson 7 — Tracing with User-Space Args
**Notes:**

---

### Lesson 8 — Hiding Processes in /proc
**Notes:**

---

### Lesson 9 — TCP Connection Delay Analysis
**Use alongside:** Brendan Gregg perf examples — https://www.brendangregg.com/perf.html

**Notes:**

---

### Lesson 10 — Performance Analysis with Flamegraphs
**Watch before this lesson:**
Brendan Gregg — Linux Performance Tools, USENIX LISA 2014 (search YouTube: Brendan Gregg LISA 2014)

**Run a flamegraph on your Ghosh trading system here:**
See flamegraph commands in Ghosh Ch11 section above.
This is the culmination of the observability track — you understand what the flamegraph shows because you built the system it is profiling.

**Notes:**
[Write: what a flamegraph shows that perf stat does not. When you would use each. 2-3 sentences.]

---

## Concepts — Surprising Insights
Use this section for anything across any resource that genuinely surprised you.
Date each entry. 2-3 sentences max. Only surprises — no summaries.

Format:
**[Date] — [Concept name]**
What it is.
What surprised you.
What you would have gotten wrong before.

---