# Filesystem Metrics

Status: unverified.

## Capacity (`statfs`, `df`)

| Metric | Unit | Formula | Source | Pitfall |
|---|---|---|---|---|
| Used % (df Use%) | % | (f_blocks − f_bfree) / (f_blocks − f_bfree + f_bavail) × 100 | `statfs()`; `df` | Reserved blocks (5% on ext4 default) excluded from denominator |
| Reserved blocks | blocks | f_bfree − f_bavail | `statfs()` | Reserved for root |
| Inodes used % | % | (f_files − f_ffree) / f_files × 100 | `statfs()`; `df -i` | Full inodes block writes even with free space |

- Pitfall: deleted files held open keep their space. Check with `lsof +L1`.

## Filesystem errors

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| ext4 errors_count | count | `/sys/fs/ext4/DEV/errors_count` | `fs/ext4/sysfs.c` |
| ext4 first_error_time, last_error_time | timestamp | `/sys/fs/ext4/DEV/` | `fs/ext4/sysfs.c` |
| XFS error state | enum/count | `/sys/fs/xfs/DEV/error/` | `fs/xfs/xfs_sysfs.c` |
| Read-only remount | state | `/proc/mounts` option `ro` | `fs/namespace.c` |

## Writeback

| Counter | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| nr_dirty | pages | Gauge | `/proc/vmstat` | `mm/page-writeback.c` |
| nr_writeback | pages | Gauge | `/proc/vmstat` | `page-writeback.c` |
| nr_dirtied | pages | Cumulative | `/proc/vmstat` | `page-writeback.c` |
| nr_written | pages | Cumulative | `/proc/vmstat` | `page-writeback.c` |
| Dirty backlog growth | pages | Δ(nr_dirtied − nr_written) / Δt | derived | |

- Pitfall: a growing gap means writes outpace the disk. The gap is approximate; overwrites reduce it.
