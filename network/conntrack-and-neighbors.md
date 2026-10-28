# Conntrack, Neighbors, Active Measurements

Status: unverified.

## Connection tracking

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| nf_conntrack_count | count (gauge) | Current entries | `/proc/sys/net/netfilter/nf_conntrack_count` | `net/netfilter/nf_conntrack_core.c` |
| nf_conntrack_max | count | Sysctl limit | `/proc/sys/net/netfilter/nf_conntrack_max` | `nf_conntrack_standalone.c` |
| conntrack usage % | % | count / max × 100 | derived | |
| entries (stat) | hex | Column 1 of `/proc/net/stat/nf_conntrack` | `/proc/net/stat/nf_conntrack` | `nf_conntrack_standalone.c` |
| drop | count | Cumulative. Rate = Δ / Δt | `conntrack -S` | `nf_conntrack_core.c` |
| insert_failed | count | Cumulative | `conntrack -S` | same |
| early_drop | count | Cumulative | `conntrack -S` | same |

- Pitfall: at max, new connections are dropped. Kernel log: `nf_conntrack: table full, dropping packet`.

## Neighbor table (ARP and ND)

| Metric | Unit | Source | Grep target | Meaning |
|---|---|---|---|---|
| NUD state | enum | `ip neigh` | `net/core/neighbour.c` | REACHABLE, STALE, DELAY, PROBE, FAILED, INCOMPLETE, NOARP, PERMANENT |

- Pitfall: persistent FAILED or INCOMPLETE indicates unreachable neighbor or ARP issue.

## Routing

| Metric | Unit | Source |
|---|---|---|
| Route entries | count | `ip route` |

## Active measurements

| Metric | Unit | Formula | Tool |
|---|---|---|---|
| RTT | ms | Measured at user space | `ping` |
| Packet loss | % | (sent − received) / sent × 100 | `ping` |
| Bandwidth | bps | bytes × 8 / Δt | `iperf3` |
| DNS query time | ms | Send to response | `dig` |

- Passive counters show what happened. Active tests show current path capacity.
