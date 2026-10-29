# Tools and man pages

These are the userspace layers between the metric names and the kernel. Most metric pages in this repo are derived from them.

Man pages are not pinned to a kernel version. The Linux man-pages project documents the interface as it stands in its latest release. Confirm field details against the kernel source at the tags in [README.md](README.md).

## Man pages (man7.org, Linux man-pages project)

| Page | Covers | URL |
|---|---|---|
| proc(5) | `/proc` files, including `/proc/[pid]/` | https://man7.org/linux/man-pages/man5/proc.5.html |
| sysfs(5) | `/sys` layout | https://man7.org/linux/man-pages/man5/sysfs.5.html |
| cgroups(7) | cgroup concepts, v1 and v2 | https://man7.org/linux/man-pages/man7/cgroups.7.html |
| namespaces(7) | Namespaces | https://man7.org/linux/man-pages/man7/namespaces.7.html |
| tcp(7) | TCP socket options and `/proc/sys/net/ipv4` | https://man7.org/linux/man-pages/man7/tcp.7.html |
| ss(8) | Socket statistics | https://man7.org/linux/man-pages/man8/ss.8.html |
| ip(8) | Interfaces, routes, neighbours | https://man7.org/linux/man-pages/man8/ip.8.html |

## Tool projects (source of the userspace readers)

| Project | Reads | Source |
|---|---|---|
| procps-ng | `free`, `vmstat`, `uptime`, `top`, `ps` (`/proc` readers) | https://gitlab.com/procps-ng/procps |
| sysstat | `iostat`, `mpstat`, `sar`, `pidstat` | https://github.com/sysstat/sysstat |
| iproute2 | `ip`, `ss` (netlink and `/proc/net`) | https://github.com/iproute2/iproute2 |

When a metric looks wrong, read the tool's parser before the kernel. The parser shows which file and field the tool reads and how it converts units.

## Kernel documentation index

- Current docs: https://www.kernel.org/doc/html/latest/
- Pinned to 6.18: https://www.kernel.org/doc/html/v6.18/
- Pinned to 7.3: https://www.kernel.org/doc/html/v7.3/

For the pages these links point to, see [kernel-sources.md](kernel-sources.md).
