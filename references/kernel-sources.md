# Kernel sources

Paths are relative to the kernel tree. Every path below exists at both `v6.18` and `v7.3`.

URL patterns:

- Source: `https://elixir.bootlin.com/linux/<tag>/source/<path>`
- Documentation: `https://www.kernel.org/doc/html/<tag>/<path without .rst>.html`

Example: `https://elixir.bootlin.com/linux/v7.3/source/fs/proc/stat.c`

## Documentation (`Documentation/`)

| Topic | Path |
|---|---|
| `/proc` interface | `filesystems/proc.rst` |
| `/sys` interface | `filesystems/sysfs.rst` |
| VFS | `filesystems/vfs.rst` |
| cgroup v2 | `admin-guide/cgroup-v2.rst` |
| PSI (pressure stall) | `accounting/psi.rst` |
| Task statistics (netlink) | `accounting/taskstats.rst` |
| Block I/O statistics | `admin-guide/iostats.rst`, `block/stat.rst` |
| Scheduler statistics | `scheduler/sched-stats.rst` |
| CFS | `scheduler/sched-design-CFS.rst` |
| EEVDF | `scheduler/sched-eevdf.rst` |
| Page reclaim | `mm/page_reclaim.rst` |
| Memory concepts | `admin-guide/mm/concepts.rst` |
| Transparent huge pages | `admin-guide/mm/transhuge.rst` |
| Slab allocator | `admin-guide/mm/slab.rst` |
| Multi-gen LRU | `admin-guide/mm/multigen_lru.rst` |
| zswap | `admin-guide/mm/zswap.rst` |
| NUMA performance | `admin-guide/mm/numaperf.rst` |
| sysctl: VM (dirty ratios, swappiness) | `admin-guide/sysctl/vm.rst` |
| sysctl: fs (`file-max`, `file-nr`) | `admin-guide/sysctl/fs.rst` |
| sysctl: kernel (`pid_max`) | `admin-guide/sysctl/kernel.rst` |
| sysctl: net | `admin-guide/sysctl/net.rst` |
| IP sysctls | `networking/ip-sysctl.rst` |
| Network statistics | `networking/statistics.rst` |
| CPU vulnerability status | `admin-guide/hw-vuln/index.rst` |
| SysRq | `admin-guide/sysrq.rst` |

## Source

| Topic | Path |
|---|---|
| `/proc/stat`, `show_stat` | `fs/proc/stat.c` |
| `/proc/meminfo` | `fs/proc/meminfo.c` |
| `/proc/loadavg` | `fs/proc/loadavg.c` |
| `/proc/uptime` | `fs/proc/uptime.c` |
| Per-process memory maps | `fs/proc/task_mmu.c` |
| File descriptors | `fs/file.c` |
| Pipes | `fs/pipe.c` |
| Mount namespaces | `fs/namespace.c` |
| PIDs | `kernel/pid.c` |
| Fork | `kernel/fork.c` |
| Softirqs | `kernel/softirq.c` |
| printk | `kernel/printk/` |
| Timekeeping | `kernel/time/timekeeping.c` |
| NTP | `kernel/time/ntp.c` |
| sysctl core | `kernel/sysctl.c` |
| CPU hotplug | `kernel/cpu.c` |
| Scheduler core, cgroup cpu controller | `kernel/sched/core.c` |
| CFS / EEVDF | `kernel/sched/fair.c` |
| Load average | `kernel/sched/loadavg.c`, `include/linux/sched/loadavg.h` |
| CPU time accounting | `kernel/sched/cputime.c` |
| PSI implementation | `kernel/sched/psi.c`, `include/linux/psi_types.h` |
| cgroup core | `kernel/cgroup/cgroup.c`, `include/linux/cgroup-defs.h` |
| Page allocator | `mm/page_alloc.c` |
| Reclaim | `mm/vmscan.c` |
| Dirty page writeback | `mm/page-writeback.c` |
| OOM killer | `mm/oom_kill.c` |
| Swap | `mm/swapfile.c`, `mm/swap_state.c` |
| Memory cgroup | `mm/memcontrol.c` |
| Transparent huge pages | `mm/huge_memory.c`, `mm/khugepaged.c` |
| Zone and node structures | `include/linux/mmzone.h` |
| Block layer core | `block/blk-core.c`, `block/genhd.c` |
| Multi-queue block layer | `block/blk-mq.c` |
| Block I/O statistics | `block/blk-stat.c`, `include/linux/blk_types.h` |
| NUMA node devices | `drivers/base/node.c` |
| EDAC (memory error reporting) | `drivers/edac/` |
| Thermal framework | `drivers/thermal/` |
| `/proc/net` | `net/core/net-procfs.c`, `net/ipv4/proc.c` |
| Neighbour table (ARP / ND) | `net/core/neighbour.c`, `net/ipv4/arp.c` |
| TCP (IPv4) | `net/ipv4/tcp_ipv4.c` |
| Netfilter connection tracking | `net/netfilter/nf_conntrack_core.c`, `net/netfilter/nf_conntrack_standalone.c` |
| Socket layer | `net/socket.c` |
| Unix sockets | `net/unix/` |
| Bonding | `drivers/net/bonding/` |

## Not in the local clones

These were not checked out, so their paths are not checked against the tags here:

- `include/asm-generic/` and `arch/*/` (for example `USER_HZ` overrides)
- `kernel/irq/`
- `Documentation/mm/slub.rst`
