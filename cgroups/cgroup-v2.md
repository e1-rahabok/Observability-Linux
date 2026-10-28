# cgroup v2 Metrics

Status: unverified. Path: `/sys/fs/cgroup/<group>/`.

## CPU

| File | Unit | Formula | Grep target |
|---|---|---|---|
| cpu.stat usage_usec | µs | Cumulative | `kernel/sched/core.c` `cpu_stat_show` |
| cpu.stat user_usec, system_usec | µs | Cumulative | same |
| cpu.stat nr_periods, nr_throttled | count | Cumulative | `kernel/sched/fair.c` |
| cpu.stat throttled_usec | µs | Cumulative | `fair.c` |
| cpu.max | µs quota, µs period | Cores = quota / period. `max` = unlimited | `fair.c` |
| Cores used | cores | Δusage_usec / (Δt_s × 10⁶) | derived |
| Throttle ratio | % | nr_throttled / nr_periods × 100 | derived |

## Memory

| File | Unit | Meaning | Grep target |
|---|---|---|---|
| memory.current | bytes | Usage including page cache | `mm/memcontrol.c` |
| memory.max | bytes | Hard limit. Reclaim, then OOM | `memcontrol.c` |
| memory.high | bytes | Throttle threshold | `memcontrol.c` |
| memory.min, memory.low | bytes | Protection thresholds | `memcontrol.c` |
| memory.stat anon, file, kernel, slab, shmem | bytes | Breakdown | `memcontrol.c` |
| memory.events low, high, max, oom, oom_kill | count | Event counters | `memcontrol.c` |
| memory.pressure | PSI | Per-cgroup pressure | `kernel/sched/psi.c` |
| Memory used % | % | memory.current / memory.max × 100 | derived |

## I/O

| File | Unit | Meaning | Grep target |
|---|---|---|---|
| io.stat rbytes, wbytes | bytes | Cumulative per device | `block/blk-cgroup.c` |
| io.stat rios, wios | count | Cumulative per device | `blk-cgroup.c` |
| io.max rbps, wbps | bytes/s | Limit | `block/blk-throttle.c` |
| io.max riops, wiops | IOPS | Limit | `blk-throttle.c` |
| io.pressure | PSI | Per-cgroup I/O pressure | `psi.c` |
| IO rate | B/s | Δbytes / Δt | derived |

## PIDs

| File | Unit | Meaning | Grep target |
|---|---|---|---|
| pids.current | count | Current task count | `kernel/cgroup/pids.c` |
| pids.max | count | Limit. `max` = unlimited | `pids.c` |
| PIDs used % | % | pids.current / pids.max × 100 | derived |

## Pitfalls

- A cgroup at memory.max will reclaim and then OOM-kill. Check memory.events.
- Container CPU % from `docker stats` uses its own formula: Δcontainer_usage / Δsystem_usage × online_cpus × 100. It can exceed 100. Source: Docker engine, not kernel.
