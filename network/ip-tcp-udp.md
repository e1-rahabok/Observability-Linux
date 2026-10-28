# IP, TCP, UDP Metrics

Status: unverified. Source for all counters: `/proc/net/snmp` and `/proc/net/netstat`. Rate = Δcounter / Δt.

## IP

| Counter | Unit | Meaning | Grep target |
|---|---|---|---|
| Ip InDiscards | count | Inbound discarded without protocol error | `net/ipv4/proc.c`, `include/uapi/linux/snmp.h` |
| Ip OutDiscards | count | Outbound discarded | same |
| Ip InHdrErrors | count | Header errors | same |
| Ip InAddrErrors | count | Address errors | same |
| Ip ReasmFails | count | Reassembly failures | same |
| Ip FragFails | count | Fragmentation failures | same |
| Ip Forwarding | enum | 1 = routing enabled | same |

- Pitfall: fragmentation failures can indicate MTU mismatch.

## ICMP

| Counter | Unit | Meaning |
|---|---|---|
| Icmp InErrors, OutErrors | count | ICMP error messages |
| Icmp InDestUnreachs | count | Destination unreachable received |

## TCP (`/proc/net/snmp`, `Tcp:`)

| Counter | Unit | Formula | Grep target | Meaning |
|---|---|---|---|---|
| ActiveOpens | count | Δ / Δt | `net/ipv4/tcp_ipv4.c`, `tcp.c` | Outbound connections initiated |
| PassiveOpens | count | Δ / Δt | same | Inbound connections accepted |
| AttemptFails | count | Δ / Δt | same | SYN_SENT failures |
| EstabResets | count | Δ / Δt | same | Resets from ESTABLISHED |
| CurrEstab | count (gauge) | Direct reading | same | Current ESTABLISHED sockets |
| InSegs | count | Δ / Δt | same | |
| OutSegs | count | Δ / Δt | same | |
| RetransSegs | count | Δ / Δt | `tcp_retransmit_skb()` | Retransmitted segments |
| InErrs | count | Δ / Δt | same | Segments with errors |
| OutRsts | count | Δ / Δt | same | RST sent |
| Retransmit ratio | % | ΔRetransSegs / ΔOutSegs × 100 | derived | Above about 1% indicates loss |

## TCP extended (`/proc/net/netstat`, `TcpExt:`)

| Counter | Unit | Meaning | Grep target | Pitfall |
|---|---|---|---|---|
| ListenOverflows | count/s | Accept queue full | `net/ipv4/tcp_ipv4.c` `tcp_v4_syn_recv_sock` | App not calling accept() fast enough, or backlog too small |
| ListenDrops | count/s | SYN dropped, listen queue full | `tcp_ipv4.c` | Same |
| TCPSynRetrans | count/s | SYN retransmissions | `tcp_output.c` | Lost SYNs or overloaded server |
| TCPTimeouts | count/s | RTO expirations | `tcp_timer.c` | |
| TCPBacklogDrop | count/s | Socket backlog full | `tcp_ipv4.c` | |
| TCPAbortOnData | count/s | Aborted with unread data | `tcp_input.c` | |
| TCPAbortOnClose | count/s | Aborted on close | `tcp.c` | |
| TCPLostRetransmit | count/s | Lost retransmissions | `tcp_input.c` | |
| TCPFastRetrans, TCPSlowStartRetrans | count/s | Retransmission type | `tcp_input.c` | |

## UDP (`/proc/net/snmp`, `Udp:`)

| Counter | Unit | Meaning | Grep target | Pitfall |
|---|---|---|---|---|
| InDatagrams | count | Received | `net/ipv4/udp.c` | |
| OutDatagrams | count | Sent | `udp.c` | |
| NoPorts | count | To closed ports | `udp.c` | |
| InErrors | count | Receive errors | `udp.c` | |
| RcvbufErrors | count | Receive buffer full (`__udp_enqueue_schedule_skb`) | `udp.c` | Raise `net.core.rmem_max` or app buffer |
| SndbufErrors | count | Send buffer issues | `udp.c` | |

## Per-socket TCP info (`ss -ti`, `struct tcp_info`)

| Field | Unit | Meaning | Grep target |
|---|---|---|---|
| tcpi_rtt | µs | Smoothed RTT | `net/ipv4/tcp.c` `tcp_get_info`; `include/uapi/linux/tcp.h` |
| tcpi_rttvar | µs | RTT variance | same |
| tcpi_snd_cwnd | segments | Congestion window | same |
| tcpi_snd_ssthresh | segments | Slow start threshold | same |
| tcpi_total_retrans | count | Total retransmissions | same |
| tcpi_delivery_rate | bytes/s | Delivery rate estimate | same |
| BDP | bytes | bandwidth_bps × RTT_s / 8 | derived |

- Pitfall: small cwnd with large RTT is a common slow-transfer pattern.
