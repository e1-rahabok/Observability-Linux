# Kernel and Uptime

Status: unverified.

## Uptime (`/proc/uptime`)

| Field | Unit | Meaning | Source | Grep target |
|---|---|---|---|---|
| Field 1 | seconds | Time since boot, including suspend | `/proc/uptime` | `kernel/time/timekeeping.c` `ktime_get_boottime`; `fs/proc/uptime.c` |
| Field 2 | seconds | Idle time summed across CPUs | `/proc/uptime` | `fs/proc/uptime.c` `get_idle_time` |

- Pitfall: field 2 can exceed field 1 on multi-CPU systems.

## Kernel identity

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| Kernel release | string | `uname -r` | `kernel/sys.c` `utsname()` |
| OS release | string | `/etc/os-release` | userspace |

- Pitfall: kernel version mismatch across a fleet causes inconsistent behavior.

## Kernel log events

| Event | Unit | Source | Grep target |
|---|---|---|---|
| Hung task | event | `dmesg -T`. "blocked for more than N seconds" | `kernel/hung_task.c` (default timeout `kernel.hung_task_timeout_secs` = 120) |
| Soft lockup | event | "watchdog: BUG: soft lockup" | `kernel/watchdog.c` (default threshold 20 s) |
| OOM kill | event | "Out of memory" / "Killed process" | `mm/oom_kill.c` |
| Kernel BUG / WARNING | event | Stack trace | `kernel/panic.c` |
| MCE | event | Machine check | see hardware-errors.md |

Read with `dmesg -T` or `journalctl -k -p err`. Source: `kernel/printk/printk.c`.
