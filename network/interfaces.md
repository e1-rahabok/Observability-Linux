# Interface Metrics

Status: unverified.

## Interface counters (`/sys/class/net/IF/statistics/`, `/proc/net/dev`)

| Counter | Unit | Rate formula | Grep target | Pitfall |
|---|---|---|---|---|
| rx_bytes | bytes | Δ / Δt (B/s). × 8 for bps | `net/core/net-sysfs.c` | |
| tx_bytes | bytes | Δ / Δt | same | |
| rx_packets | packets | Δ / Δt | same | |
| tx_packets | packets | Δ / Δt | same | |
| rx_errors | count | Δ / Δt | `net/core/dev.c` `dev_get_stats` | Frame or checksum errors. Usually cabling, duplex, or driver |
| tx_errors | count | Δ / Δt | same | |
| rx_dropped | count | Δ / Δt | same | Core stack drops (backlog full, no protocol handler) |
| tx_dropped | count | Δ / Δt | same | |
| rx_fifo_errors | count | Δ / Δt | driver | NIC ring overflow |
| rx_missed_errors | count | Δ / Δt | driver | Missed by hardware |
| rx_crc_errors, rx_frame_errors, rx_length_errors, rx_over_errors | count | Δ / Δt | driver | Physical layer or length issues |
| collisions | count | Δ / Δt | driver | Half duplex only |

- Error rate: Δrx_errors / Δrx_packets × 100 (%).
- Drop rate: Δrx_dropped / Δrx_packets × 100 (%).

## Link state and speed

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| speed | Mb/s (−1 if unknown) | `/sys/class/net/IF/speed` | `net/ethtool/linkinfo.c` |
| duplex | enum | `ethtool IF` | `net/ethtool/linkmodes.c` |
| operstate | enum | `/sys/class/net/IF/operstate` (up, down, unknown...) | `net/core/net-sysfs.c` |
| carrier | 0 or 1 | `/sys/class/net/IF/carrier` | `net-sysfs.c` |
| Interface utilization | % | Δbytes × 8 / (Δt × speed × 10⁶) × 100 | derived |

- Pitfall: unexpected speed (for example 100 Mb/s on a 10 GbE port) is a common cause of slowness.

## Driver counters (`ethtool -S IF`)

| Metric | Unit | Source |
|---|---|---|
| rx_missed, rx_no_buffer, rx_discards, queue counters | count | Driver-specific. `ethtool_ops->get_ethtool_stats` |

## Softnet statistics (`/proc/net/softnet_stat`)

Per CPU. Values are hex (32-bit).

| Column | Unit | Meaning | Formula | Grep target |
|---|---|---|---|---|
| 1 processed | packets | Dequeued by NAPI or backlog | hex → dec, Δ / Δt | `net/core/net-procfs.c` `softnet_seq_show` |
| 2 dropped | packets | Backlog queue full | hex → dec, Δ / Δt | `net/core/dev.c` `enqueue_to_backlog` |
| 3 time_squeeze | count | NAPI budget or time exhausted before queue empty | hex → dec, Δ / Δt | `net/core/dev.c` `net_rx_action` |
| 5 received_rps | count | RPS-steered packets | hex → dec | `net-procfs.c` |
| 6 flow_limit_count | count | Flow limit hits | hex → dec | `net-procfs.c` |

- Pitfall: non-zero dropped or squeeze means the CPU cannot keep up with packet rate. Tune RSS, RPS, or `net.core.netdev_budget`.
- Verify column order against the target kernel. Column 4 was historically `cpu_collision`.
