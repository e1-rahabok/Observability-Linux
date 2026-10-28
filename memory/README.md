# Memory Metrics

## Pages

- [availability-and-cache.md](availability-and-cache.md): MemTotal, MemAvailable, Cached, Buffers, Dirty
- [kernel-memory.md](kernel-memory.md): Slab, PageTables, KernelStack, commit accounting, hugepages, fragmentation, NUMA
- [swap-and-reclaim.md](swap-and-reclaim.md): swap usage, `/proc/vmstat` paging and reclaim counters
- [pressure-and-oom.md](pressure-and-oom.md): memory PSI, OOM events, OOM score
- [process-memory.md](process-memory.md): RSS, PSS, USS, VSZ, per-process swap

## Source files (kernel)

- `fs/proc/meminfo.c`: `/proc/meminfo`
- `mm/page_alloc.c`: `si_mem_available`
- `mm/vmstat.c`, `include/linux/vm_event_item.h`: `/proc/vmstat`
- `mm/vmscan.c`: reclaim
- `mm/oom_kill.c`: OOM
- `mm/memcontrol.c`: cgroup memory
- `fs/proc/task_mmu.c`: per-process memory
