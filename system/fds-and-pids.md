# File Descriptors, PIDs, Threads, Caches

Status: unverified.

## Threads and processes

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| nr_running | tasks | Field 4 of `/proc/loadavg`, numerator | `/proc/loadavg` | `kernel/sched/loadavg.c` |
| nr_threads | threads | Field 4 of `/proc/loadavg`, denominator | `/proc/loadavg` | `fs/proc/loadavg.c` |
| Threads per process | threads | `Threads:` field | `/proc/PID/status` | `fs/proc/array.c` |

## PIDs

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| pid_max | count | Sysctl. Default scales with CPUs, up to 4194304 on 64-bit | `/proc/sys/kernel/pid_max` | `kernel/pid.c` |
| PID usage % | % | Current PIDs / pid_max × 100 | derived | |
| pids.current / pids.max | count | cgroup (see cgroups/) | cgroup | `kernel/cgroup/pids.c` |

## File descriptors

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| System FD allocated | count | Field 1 of `/proc/sys/fs/file-nr` | `/proc/sys/fs/file-nr` | `fs/file_table.c` `files_stat` |
| System FD free | count | Field 2 | same | same |
| System FD max | count | Field 3 (`fs.file-max`) | same | same |
| System FD in use | count | allocated − free | derived | |
| System FD usage % | % | (allocated − free) / max × 100 | derived | |
| Process FD count | count | Entries in `/proc/PID/fd` | `/proc/PID/fd` | `fs/proc/fd.c` |
| Process FD limit | count | "Max open files" soft limit | `/proc/PID/limits` | `fs/proc/base.c` |
| Process FD usage % | % | FD count / soft limit × 100 | derived | |

- Pitfall: on modern kernels `free` is typically 0 because freed file structs are not kept on a free list. In-use is therefore `allocated`. Confirm in `fs/file_table.c` for the target kernel.
- Pitfall: reaching the soft limit causes `EMFILE` errors.

## Dentry and inode caches

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| nr_dentry | count | `/proc/sys/fs/dentry-state` field 1 | `fs/dcache.c` |
| nr_unused (dentry) | count | `/proc/sys/fs/dentry-state` field 2 | `fs/dcache.c` |
| nr_inodes | count | `/proc/sys/fs/inode-state` field 1 | `fs/inode.c` |
| nr_unused (inode) | count | `/proc/sys/fs/inode-state` field 2 | `fs/inode.c` |

- Pitfall: large numbers are normal. Pressure shows in reclaim counters (see memory/swap-and-reclaim.md).
