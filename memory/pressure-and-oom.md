# Memory Pressure and OOM

Status: unverified.

## Memory PSI (`/proc/pressure/memory`)

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| some avg10/60/300 | % | (time ≥1 task stalled on memory / window) × 100, EMA | `/proc/pressure/memory` | `kernel/sched/psi.c` |
| full avg10/60/300 | % | (time all non-idle tasks stalled on memory / window) × 100 | `/proc/pressure/memory` | `psi.c` |
| some/full total | µs | Cumulative stall time | `/proc/pressure/memory` | `psi.c` |

- Pitfall: any sustained `full` is severe. All runnable work stalled at once.

## OOM events

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| oom_kill | count | Cumulative system OOM kills | `/proc/vmstat` | `mm/oom_kill.c` |
| OOM log entries | events | Count of "Out of memory" / "Killed process" | `dmesg -T`, `journalctl -k` | `mm/oom_kill.c` |

## OOM score

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| oom_score | 0–1000 (dimensionless) | Approx. `(RSS + swap + page_tables) / total_pages × 1000`, adjusted by `oom_score_adj` | `/proc/PID/oom_score` | `mm/oom_kill.c` `oom_badness` |
| oom_score_adj | −1000 to 1000 | Direct setting | `/proc/PID/oom_score_adj` | `oom_kill.c` |

- Pitfall: the formula is approximate and changed across kernels. Confirm in `oom_badness`.

## cgroup memory events (`memory.events`)

| Counter | Unit | Meaning | Source | Grep target |
|---|---|---|---|---|
| low | count | Reclaim below memory.low | `memory.events` | `mm/memcontrol.c` |
| high | count | Usage hit memory.high | `memory.events` | `memcontrol.c` |
| max | count | Usage hit memory.max | `memory.events` | `memcontrol.c` |
| oom | count | OOM invoked in cgroup | `memory.events` | `memcontrol.c` |
| oom_kill | count | Process killed in cgroup | `memory.events` | `memcontrol.c` |
| oom_group_kill | count | Group kill events | `memory.events` | `memcontrol.c` |
