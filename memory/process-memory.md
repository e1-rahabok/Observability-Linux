# Process Memory

Status: unverified.

| Metric | Unit | Formula | Source | Grep target | Pitfall |
|---|---|---|---|---|---|
| VmSize (VSZ) | kB | Direct reading | `/proc/PID/status` | `fs/proc/task_mmu.c` `task_mem` | Virtual address space. Not usage |
| VmRSS (RSS) | kB | Resident pages (RssAnon + RssFile + RssShmem) | `/proc/PID/status` | `task_mmu.c` | Shared pages counted in each process |
| RssAnon, RssFile, RssShmem | kB | Direct reading (kernel ≥ 4.5) | `/proc/PID/status` | `task_mmu.c` | |
| VmSwap | kB | Swapped-out private pages | `/proc/PID/status` | `task_mmu.c` | |
| PSS | kB | Σ over mapped pages of (PAGE_SIZE / mapcount) | `/proc/PID/smaps_rollup` | `task_mmu.c` | Best for totals across processes |
| USS | kB | Private_Clean + Private_Dirty | `/proc/PID/smaps_rollup` | `task_mmu.c` | Freed if process exits |
| Shared_Clean, Shared_Dirty | kB | Shared mappings | `/proc/PID/smaps_rollup` | `task_mmu.c` | |
| Process minflt, majflt | count | Fields 10, 12 of `/proc/PID/stat` | `/proc/PID/stat` | `fs/proc/array.c` | Major = needed I/O |

## Notes

- Summing RSS across processes double-counts shared pages. Sum PSS instead.
- `ps` and `top` RSS follow the RSS definition above.
