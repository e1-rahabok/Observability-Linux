# Disk and Storage Metrics

## Pages

- [block-io.md](block-io.md): `/proc/diskstats`, derived iostat metrics, queue settings, per-process I/O, PSI
- [filesystem.md](filesystem.md): capacity, inodes, filesystem errors, writeback
- [device-health.md](device-health.md): SMART, NVMe SMART log, kernel I/O errors

## Source files (kernel)

- `block/genhd.c`: `/proc/diskstats`, `/sys/block/DEV/stat`
- `Documentation/admin-guide/iostats.rst`: field definitions
- `block/blk-sysfs.c`: queue settings
- `fs/proc/base.c`: `/proc/PID/io`
- `drivers/nvme/host/core.c`: NVMe log pages
