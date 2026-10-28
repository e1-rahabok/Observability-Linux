# System Metrics

## Pages

- [kernel-and-uptime.md](kernel-and-uptime.md): uptime, kernel identity, kernel log events
- [fds-and-pids.md](fds-and-pids.md): file descriptors, PIDs, thread counts, dentry and inode caches
- [time-sync.md](time-sync.md): clock source, chrony and ntpq metrics
- [hardware-errors.md](hardware-errors.md): EDAC, MCE, PCIe AER, temperature, power, RAPL
- [services-and-security.md](services-and-security.md): systemd units, logs, auth events, tuning parameters

## Source files (kernel)

- `fs/proc/uptime.c`: `/proc/uptime`
- `kernel/time/timekeeping.c`: boot time
- `kernel/pid.c`: PIDs
- `fs/file_table.c`: FD limits
- `kernel/printk/printk.c`, `kernel/hung_task.c`, `kernel/watchdog.c`
- `drivers/edac/edac_mc.c`, `arch/x86/kernel/cpu/mce/core.c`
- `drivers/thermal/thermal_core.c`, `drivers/powercap/intel_rapl_common.c`
