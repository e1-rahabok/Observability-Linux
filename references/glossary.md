# Glossary

Each entry gives a short definition and where the term is defined. Paths are relative to the kernel tree, and each one exists at `v6.18` and `v7.3`.

## Time and scheduling

| Term | Definition | Defined in |
|---|---|---|
| USER_HZ (CLK_TCK) | Tick rate of `/proc` CPU time fields. Generic value is 100. Architectures may override it. | `include/uapi/asm-generic/param.h`; `arch/*/` overrides |
| HZ | Kernel timer tick rate, set by `CONFIG_HZ`. Not the same as USER_HZ. | `include/uapi/asm-generic/param.h` |
| Load average | Exponentially damped average of runnable plus uninterruptible (D-state) tasks. | `kernel/sched/loadavg.c`, `include/linux/sched/loadavg.h` |
| iowait | Idle time while at least one task waits on I/O. It is idle time, not CPU work. | `kernel/sched/cputime.c`; `fs/proc/stat.c` |
| steal | Time the vCPU was runnable but the hypervisor ran something else. | `kernel/sched/cputime.c` |
| CFS | Completely Fair Scheduler, the fair-class scheduler before EEVDF. | `Documentation/scheduler/sched-design-CFS.rst` |
| EEVDF | Earliest Eligible Virtual Deadline First, the current fair-class policy. | `Documentation/scheduler/sched-eevdf.rst`; `kernel/sched/fair.c` |
| CFS bandwidth throttling | Enforces a cgroup CPU quota per period. Throttled tasks stop until the next period. | `kernel/sched/fair.c` |
| PSI | Pressure Stall Information. Share of time tasks were stalled on CPU, memory, or I/O. | `Documentation/accounting/psi.rst`; `kernel/sched/psi.c` |
| some / full | PSI levels. `some`: at least one task stalled. `full`: all non-idle tasks stalled. | `Documentation/accounting/psi.rst` |

## Memory

| Term | Definition | Defined in |
|---|---|---|
| Page | Unit of memory management. Size is `PAGE_SIZE`. | `Documentation/admin-guide/mm/concepts.rst` |
| Page cache | Cached file data in memory. | `mm/filemap.c` |
| Dirty page | Page-cache page modified and not yet written back. | `mm/page-writeback.c`; `Documentation/admin-guide/sysctl/vm.rst` |
| Writeback | Flushing dirty pages to storage. | `mm/page-writeback.c` |
| Reclaim | Freeing memory by dropping cache or swapping. Background reclaim is kswapd. Direct reclaim runs in the allocating task. | `mm/vmscan.c`; `Documentation/mm/page_reclaim.rst` |
| Working set / MGLRU | Multi-generation LRU, which tracks page age in generations. | `Documentation/admin-guide/mm/multigen_lru.rst` |
| Slab | Kernel object cache allocator (SLUB in current kernels). | `Documentation/admin-guide/mm/slab.rst` |
| Swap | Anonymous pages moved to a swap device or file. | `mm/swapfile.c`; `Documentation/admin-guide/sysctl/vm.rst` |
| zswap | Compressed cache in front of swap. | `Documentation/admin-guide/mm/zswap.rst` |
| THP | Transparent huge pages. | `mm/huge_memory.c`; `Documentation/admin-guide/mm/transhuge.rst` |
| MemAvailable | Estimate of memory available without swapping. Estimate, not a free-page count. | `mm/show_mem.c` (`si_mem_available`); `fs/proc/meminfo.c` |
| OOM killer | Kills a task when allocation fails under pressure. | `mm/oom_kill.c` |
| memory cgroup | Per-cgroup memory accounting and limits. | `mm/memcontrol.c`; `Documentation/admin-guide/cgroup-v2.rst` |
| NUMA node | A memory and CPU locality domain. | `drivers/base/node.c`; `Documentation/admin-guide/mm/numaperf.rst` |

## Block I/O

| Term | Definition | Defined in |
|---|---|---|
| Sector | 512-byte unit used in block-layer counters, whatever the device's own sector size. | `Documentation/block/stat.rst` |
| iostats | Per-device counters in `/sys/block/<dev>/stat` and `/proc/diskstats`. | `Documentation/admin-guide/iostats.rst` |
| blk-mq | Multi-queue block layer. | `block/blk-mq.c` |

## Network

| Term | Definition | Defined in |
|---|---|---|
| Neighbour table | ARP and neighbour discovery cache, with states such as REACHABLE and STALE. | `net/core/neighbour.c` |
| conntrack | Netfilter connection tracking table. | `net/netfilter/nf_conntrack_core.c` |
| Socket | Kernel endpoint for network or local IPC. | `net/socket.c` |

## Process and system limits

| Term | Definition | Defined in |
|---|---|---|
| File descriptor (FD) | Per-process index into the open-file table. | `fs/file.c` |
| `file-max` / `file-nr` | System-wide FD limit and current usage. | `Documentation/admin-guide/sysctl/fs.rst` |
| PID | Process or thread ID. | `kernel/pid.c` |
| `pid_max` | Upper bound on PIDs. | `Documentation/admin-guide/sysctl/kernel.rst` |
| Softirq | Deferred interrupt work run in softirq context. | `kernel/softirq.c` |

## Control groups

| Term | Definition | Defined in |
|---|---|---|
| cgroup v2 | Unified hierarchy. Controllers are enabled per subtree. | `Documentation/admin-guide/cgroup-v2.rst`; `kernel/cgroup/cgroup.c` |
| cpu controller | The CPU controller in cgroup v2. Its interface files include `cpu.stat`. | `kernel/sched/core.c` (`cpu_cgroup_*`) |

## Notes

- `Documentation/` describes intent and interface. Source describes the exact behaviour, so confirm a field's meaning in the source when the documentation is unclear.
