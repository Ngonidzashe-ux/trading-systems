# Low-Latency Systems Engineering

End-to-end implementation of a low-latency electronic trading ecosystem in C++,
alongside Linux kernel systems programming and performance engineering.

## What this covers
- Low-latency C++ trading system — market data feed, order book, matching engine, execution
- Linux systems programming — process management, memory, networking, concurrency
- Linux kernel internals — kernel modules, scheduler, memory subsystem
- eBPF observability — performance profiling and kernel tracing

## Structure
```
trading-systems/
├── ghosh/          # C++ trading system — chapter by chapter (Ghosh book)
├── csapp-labs/     # CS:APP lab implementations (Shell, Malloc, Cache, Proxy)
├── billimoria/     # Linux kernel module exercises (Billimoria book)
├── bpf-tutorial/   # eBPF lesson implementations
├── benchmarks/     # Standalone performance measurements
└── notes/          # Technical notes and benchmark results
```

## Benchmarks
Performance results documented in notes/benchmarks.md.