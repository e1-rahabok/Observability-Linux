# CPU Metrics

## Pages

- [utilization.md](utilization.md): `/proc/stat` time fields, per-CPU, process CPU time, user/system definitions
- [load-and-scheduling.md](load-and-scheduling.md): load average, run queue, context switches, interrupts, softirqs, scheduler wait
- [pressure-and-throttling.md](pressure-and-throttling.md): CPU PSI, cgroup throttling, frequency, thermal throttling

## Source files (kernel)

- `fs/proc/stat.c`: `/proc/stat` output
- `kernel/sched/cputime.c`: time accounting
- `kernel/sched/loadavg.c`: load average
- `kernel/sched/psi.c`: pressure stall information
- `kernel/sched/core.c`, `fair.c`: scheduler and cgroup CPU
