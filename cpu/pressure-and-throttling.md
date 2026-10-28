# CPU Pressure, Throttling, Frequency

Status: unverified.

## CPU PSI (`/proc/pressure/cpu`)

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| some avg10 / avg60 / avg300 | % | Exponential moving average of (time ≥1 task stalled / window) × 100 | `/proc/pressure/cpu` | `kernel/sched/psi.c`; `Documentation/accounting/psi.rst` |
| some total | µs | Cumulative stall time, at least one task | `/proc/pressure/cpu` | `psi.c` |
| full (system CPU) | % | Reported as 0 on most kernels. Confirm for target kernel | `/proc/pressure/cpu` | `psi.c` |

- Pitfall: `full` for CPU is not meaningful at system level on most kernels.

## cgroup v2 CPU throttling (`cpu.stat`)

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| usage_usec | µs | Cumulative | `cpu.stat` | `kernel/sched/core.c` `cpu_stat_show` |
| user_usec, system_usec | µs | Cumulative | `cpu.stat` | same |
| nr_periods | count | Cumulative periods | `cpu.stat` | `kernel/sched/fair.c` |
| nr_throttled | count | Cumulative periods throttled | `cpu.stat` | `fair.c` |
| throttled_usec | µs | Cumulative throttled time | `cpu.stat` | `fair.c` |
| Throttle ratio | % | nr_throttled / nr_periods × 100 | derived | |
| Cores used | cores | Δusage_usec / (Δt_s × 10⁶) | derived | |
| CPU limit | cores | quota / period (`cpu.max`, both µs). `max` = unlimited | `cpu.max` | `fair.c` |

- Pitfall: non-zero `nr_throttled` means the quota was hit. Latency spikes follow.

## CPU frequency

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Current frequency | kHz | Direct reading | `/sys/devices/system/cpu/cpuN/cpufreq/scaling_cur_freq` | `drivers/cpufreq/cpufreq.c` |
| Governor | enum | Direct reading | `scaling_governor` | `cpufreq.c` |

- Pitfall: frequency far below base under load points to thermal or power caps.

## Thermal throttling events

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| core_throttle_count | count | Cumulative | `/sys/devices/system/cpu/cpuN/thermal_throttle/` | `arch/x86/kernel/cpu/mce/therm_throt.c` |
| package_throttle_count | count | Cumulative | same | same |
