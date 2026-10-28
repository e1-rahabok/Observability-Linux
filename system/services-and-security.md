# Services, Logs, Security, Tuning

Status: unverified. systemd and auth items are userspace, not kernel.

## systemd units

| Metric | Unit | Source | Notes |
|---|---|---|---|
| Failed units | count | `systemctl --failed` | |
| NRestarts | count | `systemctl show UNIT -p NRestarts` | systemd ≥ 235 |
| Last exit code | enum | `systemctl status UNIT` | |

## Logs

| Metric | Unit | Source | Notes |
|---|---|---|---|
| Log events per minute | events/min | `journalctl` | Spikes often mean an error loop |
| Journal disk usage | bytes | `journalctl --disk-usage` | |

## Security events

| Metric | Unit | Source | Notes |
|---|---|---|---|
| Failed logins | events | `journalctl _COMM=sshd`, `/var/log/auth.log` | Rate = Δ / Δt |
| sudo use | events | `journalctl _COMM=sudo` | |
| SELinux/AppArmor denials | events | `ausearch -m avc`, `dmesg` | Kernel audit subsystem (`kernel/audit.c`) |
| Listening sockets | list | `ss -tlnp` | Investigate unknown listeners |

## Sysctl tuning state

| Parameter | Unit | Default | Source | Grep target |
|---|---|---|---|---|
| vm.swappiness | 0–200 | 60 | `/proc/sys/vm/swappiness` | `mm/vmscan.c` |
| vm.overcommit_memory | 0, 1, 2 | 0 | `/proc/sys/vm/overcommit_memory` | `mm/util.c` |
| vm.overcommit_ratio | % | 50 | `/proc/sys/vm/overcommit_ratio` | `mm/util.c` |
| net.core.somaxconn | connections | 4096 (recent kernels) | `/proc/sys/net/core/somaxconn` | `net/core/sysctl_net_core.c` |
| net.ipv4.tcp_max_syn_backlog | count | Varies with RAM | `/proc/sys/net/ipv4/tcp_max_syn_backlog` | `net/ipv4/tcp_ipv4.c` |
| fs.file-max | count | Varies with RAM | `/proc/sys/fs/file-max` | `fs/file_table.c` |

- Pitfall: defaults vary by kernel version and RAM. Record actual values when diagnosing. Confirm defaults in the target kernel.
