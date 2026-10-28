# Block I/O

Status: unverified. Sector = 512 bytes in all block-layer counters.

## `/proc/diskstats` fields (per device)

| # | Field | Unit | Meaning |
|---|---|---|---|
| 1 | reads completed | count | Cumulative |
| 2 | reads merged | count | Cumulative |
| 3 | sectors read | sectors | Cumulative. × 512 = bytes |
| 4 | ms reading | ms | Cumulative time spent on reads |
| 5 | writes completed | count | Cumulative |
| 6 | writes merged | count | Cumulative |
| 7 | sectors written | sectors | Cumulative |
| 8 | ms writing | ms | Cumulative |
| 9 | I/Os in progress | count | Instantaneous gauge (queue depth now) |
| 10 | ms doing I/O | ms | Cumulative time device had work |
| 11 | weighted ms doing I/O | ms | Busy time weighted by queue depth |
| 12–14 (5.x+) | discards completed, merged, sectors, ms | | Discard counters |
| 15–16 (5.5+) | flushes completed, ms | | Flush counters |

Source: `/proc/diskstats`, `/sys/block/DEV/stat`. Grep: `block/genhd.c` `diskstats_show`, `part_stat_show`.

## Derived metrics (iostat)

| Metric | Unit | Formula |
|---|---|---|
| r/s | IOPS | Δfield1 / Δt |
| w/s | IOPS | Δfield5 / Δt |
| rkB/s | kB/s | Δfield3 × 512 / 1024 / Δt |
| wkB/s | kB/s | Δfield7 × 512 / 1024 / Δt |
| rrqm/s | merges/s | Δfield2 / Δt |
| wrqm/s | merges/s | Δfield6 / Δt |
| r_await | ms | Δfield4 / Δfield1 |
| w_await | ms | Δfield8 / Δfield5 |
| await (overall) | ms | Δ(field4 + field8) / Δ(field1 + field5) |
| aqu-sz | dimensionless | Δfield11 / Δt_ms |
| %util | % | Δfield10 / Δt_ms × 100 |
| avgrq-sz | sectors | Δ(field3 + field7) / Δ(field1 + field5). × 512 for bytes |

- Pitfall: `%util` = 100 means at least one I/O was in flight the whole interval. It does not mean saturation. NVMe and RAID handle parallel requests.
- Pitfall: `r_await` and `w_await` are per-request latency including queue time. This is what applications feel.
- Reference: SSD typically < 1 ms, HDD 5–15 ms (device-dependent).

## Queue settings

| Setting | Unit | Path | Grep target |
|---|---|---|---|
| scheduler | enum | `/sys/block/DEV/queue/scheduler` | `block/elevator.c` |
| nr_requests | count | `/sys/block/DEV/queue/nr_requests` | `block/blk-sysfs.c` |
| read_ahead_kb | kB | `/sys/block/DEV/queue/read_ahead_kb` | `block/blk-sysfs.c` |
| rotational | 0 or 1 | `/sys/block/DEV/queue/rotational` | `block/blk-sysfs.c` |

## Per-process I/O (`/proc/PID/io`)

| Field | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| rchar | bytes | Cumulative bytes passed to `read()` syscalls. Includes cache hits | `/proc/PID/io` | `fs/read_write.c` |
| wchar | bytes | Cumulative bytes passed to `write()` | `/proc/PID/io` | `fs/read_write.c` |
| read_bytes | bytes | Bytes fetched from storage | `/proc/PID/io` | `fs/proc/base.c` `proc_tgid_io_accounting` |
| write_bytes | bytes | Bytes sent to storage | `/proc/PID/io` | `proc_tgid_io_accounting` |
| cancelled_write_bytes | bytes | Dirty bytes truncated before writeback | `/proc/PID/io` | `proc_tgid_io_accounting` |
| Per-process read rate | B/s | Δread_bytes / Δt | derived | |

## I/O pressure (`/proc/pressure/io`)

| Metric | Unit | Formula | Grep target |
|---|---|---|---|
| some avgN | % | (time ≥1 task stalled on I/O / window) × 100 | `kernel/sched/psi.c` |
| full avgN | % | (time all non-idle tasks stalled on I/O / window) × 100 | `psi.c` |

## Latency histograms

| Metric | Unit | Formula | Source |
|---|---|---|---|
| Block I/O latency | µs or ms | `complete_ts − issue_ts` per request | tracepoints `block:block_rq_issue`, `block:block_rq_complete`; bcc `biolatency` |

- Pitfall: report p95 and p99, not only the mean.
