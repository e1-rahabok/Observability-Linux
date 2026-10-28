# Load, Scheduling, Interrupts

Status: unverified.

## Load average

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Load 1/5/15 min | dimensionless (average task count) | `load = load_old × EXP + active × (FIXED_1 − EXP)`. FSHIFT = 11, FIXED_1 = 2048. EXP_1 = 1884, EXP_5 = 2014, EXP_15 = 2037. Sampled every 5 s | `/proc/loadavg` | `kernel/sched/loadavg.c` `calc_load`, `calc_global_load` |
| Active tasks | tasks | `nr_running + nr_uninterruptible` | kernel | `loadavg.c` |
| Display value | dimensionless | Fixed-point value / 2048, two decimals | `/proc/loadavg` | `fs/proc/loadavg.c` |

- Decay per 5 s sample: e^(−5/60) for 1 min, e^(−5/300) for 5 min, e^(−5/900) for 15 min.
- Pitfall: high load with low CPU use usually means D-state tasks (disk I/O, NFS).
- Compare load to logical CPU count.

## `/proc/loadavg` field 4

- Format: `running/total`.
- `running` = `nr_running` now. `total` = all threads.
- Unit: tasks. Source: `fs/proc/loadavg.c`.

## Run queue and blocked

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| procs_running (`r`) | tasks | Sum of `nr_running` across CPUs | `/proc/stat` | `fs/proc/stat.c` |
| procs_blocked (`b`) | tasks | Sum of `nr_iowait` across CPUs | `/proc/stat` | `fs/proc/stat.c` |

- Pitfall: `procs_blocked` counts tasks marked `in_iowait`. It does not count all D-state tasks. D-state count is `nr_uninterruptible`, used by loadavg.

## Context switches

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| ctxt (total) | count | Cumulative | `/proc/stat` `ctxt` | `nr_context_switches()` in `kernel/sched/core.c` |
| Context switch rate | switches/s | Δctxt / Δt | derived | |
| voluntary_ctxt_switches | count | Cumulative per task | `/proc/PID/status` | `kernel/sched/core.c` |
| nonvoluntary_ctxt_switches | count | Cumulative per task | `/proc/PID/status` | `kernel/sched/core.c` |

- Pitfall: high nonvoluntary switches mean CPU contention (preemption).

## Interrupts

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| intr (total) | count | Cumulative; sum of per-IRQ counts | `/proc/stat` `intr` | `fs/proc/stat.c` |
| Interrupt rate | /s | Δintr / Δt | derived | |
| Per-IRQ count | count per CPU | Cumulative | `/proc/interrupts` | `kernel/irq/proc.c` |

- Pitfall: one IRQ dominating one CPU suggests poor affinity. Check `/proc/irq/N/smp_affinity`.

## Softirqs

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Per-type softirq count | count per CPU | Cumulative | `/proc/softirqs` | `kernel/softirq.c`, `fs/proc/softirqs.c` |

- Types: HI, TIMER, NET_TX, NET_RX, BLOCK, IRQ_POLL, TASKLET, SCHED, HRTIMER, RCU.
- Pitfall: `NET_RX` concentrated on one CPU suggests RSS or RPS misconfiguration.

## Scheduler wait (run queue delay)

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| sum_exec_runtime | ns | Field 1 of `/proc/PID/schedstat` | `/proc/PID/schedstat` | `kernel/sched/debug.c` `proc_pid_schedstat` |
| run_delay | ns | Field 2. Time waiting on run queue | `/proc/PID/schedstat` | same |
| pcount | count | Field 3. Timeslices run | `/proc/PID/schedstat` | same |
| Average wait per slice | ns | Δrun_delay / Δpcount | derived | |

- Pitfall: `run_delay` is not PSI. It is per-task runqueue wait.
