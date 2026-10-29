# Books

Secondary sources. Use them for method and concepts. Confirm field names, file paths, and default values against the kernel source at `v6.18` or `v7.3`.

Both books below were published before 6.18. Their concepts still apply. Specific kernel details may not, so each entry says what to trust.

## Systems Performance: Enterprise and the Cloud, 2nd edition

- **Author:** Brendan Gregg
- **Publisher:** Addison-Wesley, 2020
- **Info:** https://www.brendangregg.com/systems-performance-2nd-edition-book.html
- **Use for:** Methodology (USE method, workload characterisation), what to measure per resource, and how the CPU, memory, disk, and network subsystems interact.
- **Check before relying on:** Field-level details, `/proc` layouts, and tunable defaults. Confirm them in the kernel source.

## BPF Performance Tools

- **Author:** Brendan Gregg
- **Publisher:** Addison-Wesley, 2019
- **Info:** https://www.brendangregg.com/bpf-performance-tools-book.html
- **Use for:** Tracing approach with eBPF and the event sources it uses (kprobes, tracepoints, perf events).
- **Check before relying on:** Probe names and tool behaviour. Kernel tracepoints change between releases, so confirm them in the tree at `v6.18` or `v7.3`.

## Not included

- *Linux Kernel Development* (Love) and *Understanding the Linux Kernel* (Bovet and Cesati) describe kernel internals as of kernel 2.6. Most of their detail predates 6.18 and has been rewritten since. They are excluded under the 6.18+ rule. Use `kernel/` and `Documentation/` for internals instead.
