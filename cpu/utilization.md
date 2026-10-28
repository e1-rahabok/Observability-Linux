# CPU Utilization

Status: unverified. Kernel grep targets listed per entry.

## `/proc/stat` cpu line

Field order: `user nice system idle iowait irq softirq steal guest guest_nice`.

| Metric | Unit | Formula | Source | Grep target | Pitfall |
|---|---|---|---|---|---|
| user | USER_HZ ticks | cumulative | `/proc/stat` line `cpu` | `fs/proc/stat.c` `show_stat` | Includes guest time |
| nice | ticks | cumulative | `/proc/stat` | `show_stat` | Only for nice > 0 processes |
| system | ticks | cumulative | `/proc/stat` | `show_stat` | Syscalls, kernel threads |
| idle | ticks | cumulative | `/proc/stat` | `get_idle_time` | Nothing runnable |
| iowait | ticks | cumulative | `/proc/stat` | `get_iowait_time` | Idle with `nr_iowait > 0`. Not CPU work |
| irq | ticks | cumulative | `/proc/stat` | `kernel/sched/cputime.c` | Hardware interrupt handlers |
| softirq | ticks | cumulative | `/proc/stat` | `kernel/softirq.c` | Deferred interrupt work |
| steal | ticks | cumulative | `/proc/stat` | `kernel/sched/cputime.c` (steal time) | Hypervisor ran other work |
| guest | ticks | cumulative | `/proc/stat` | `cputime.c` | Already included in `user`. Do not add |
| guest_nice | ticks | cumulative | `/proc/stat` | `cputime.c` | Already included in `nice`. Do not add |

### Derived

| Metric | Unit | Formula |
|---|---|---|
| %X (any field) | % | Δfield / Δtotal × 100 |
| Δtotal | ticks | Sum of user, nice, system, idle, iowait, irq, softirq, steal. Exclude guest, guest_nice |
| %busy | % | (1 − Δ(idle + iowait) / Δtotal) × 100 |
| Cores busy | cores | Δ(total − idle − iowait) / (Δt × CLK_TCK) |

## Per-CPU

- Source: `cpuN` lines in `/proc/stat`, or `mpstat -P ALL`.
- Formula: same as system, using the `cpuN` line.
- Pitfall: one CPU at 100% with others idle suggests a single-threaded bottleneck or IRQ pinning.

## Tick accounting vs precise accounting

- Source: `kernel/sched/cputime.c` (`account_process_tick`, `account_user_time`, `account_system_time`).
- Config: `CONFIG_TICK_CPU_ACCOUNTING` (sampled per tick) vs `CONFIG_VIRT_CPU_ACCOUNTING_GEN` (exact, with `nohz_full`).
- Resolution: 1 / HZ for tick sampling. Nanoseconds for precise.
- Pitfall: tick sampling is statistical. Short tasks can be misattributed.

## Process CPU time

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| utime | ticks | Field 14 of `/proc/PID/stat` | `/proc/PID/stat` | `fs/proc/array.c` `do_task_stat` |
| stime | ticks | Field 15 | `/proc/PID/stat` | `do_task_stat` |
| cutime, cstime | ticks | Fields 16, 17. Waited-for children | `/proc/PID/stat` | `do_task_stat` |
| Process user seconds | s | utime / CLK_TCK | derived | |
| Process user cores (interval) | cores | Δutime / (CLK_TCK × Δt) | derived | |
| `ps %CPU` | % | (utime + stime) / (CLK_TCK × elapsed_s) × 100 | `ps` | Lifetime average, not instantaneous |
| `top` %CPU | % | Δ(utime + stime) / (CLK_TCK × Δt) × 100 | `top` | Exceeds 100 on multithreaded processes |
| user fraction | % | utime / (utime + stime) × 100 | derived | |

## Notes

- `user` includes interpreter and runtime overhead, and spin-wait loops.
- USER_HZ is exposed to user space; kernel tick rate (HZ) is separate.
- `getrusage(RUSAGE_SELF)` returns `ru_utime` with µs resolution. Use it when `/proc` ticks are too coarse.
