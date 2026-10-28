# cgroups (v2)

## Pages

- [cgroup-v2.md](cgroup-v2.md): per-controller files, formulas, units

## Source files (kernel)

- `kernel/cgroup/cgroup.c`: cgroup filesystem
- `kernel/sched/core.c`, `fair.c`: `cpu.*`
- `mm/memcontrol.c`: `memory.*`
- `block/blk-cgroup.c`, `block/blk-throttle.c`, `block/blk-iocost.c`: `io.*`
- `kernel/cgroup/pids.c`: `pids.*`
