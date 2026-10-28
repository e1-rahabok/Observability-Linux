# Sockets

Status: unverified.

## TCP state counts (`ss -tan`, `/proc/net/tcp`)

State values in `/proc/net/tcp` are hex.

| State | Hex | Unit | Meaning | Warning |
|---|---|---|---|---|
| ESTABLISHED | 01 | count | Open connections | |
| SYN_SENT | 02 | count | Outbound connecting | |
| SYN_RECV | 03 | count | Half-open inbound | Many = SYN flood or slow clients |
| FIN_WAIT1 | 04 | count | Closing | |
| FIN_WAIT2 | 05 | count | Closing, waiting for peer FIN | |
| TIME_WAIT | 06 | count | Closed, waiting 2×MSL | Normal on busy clients. Check ephemeral port exhaustion |
| CLOSE | 07 | count | Closed | |
| CLOSE_WAIT | 08 | count | Peer closed, app has not | Many = application not closing sockets |
| LAST_ACK | 09 | count | Waiting final ACK | |
| LISTEN | 0A | count | Listening | |
| CLOSING | 0B | count | Simultaneous close | |

Source: `net/ipv4/tcp_ipv4.c` `get_tcp4_sock`.

## Socket queues

| Metric | Unit | Source | Meaning |
|---|---|---|---|
| Recv-Q (listen) | connections | `ss -tlnp` | Current accept queue length |
| Send-Q (listen) | connections | `ss -tlnp` | Backlog limit |
| Recv-Q (established) | bytes | `ss -tan` | Received, not read by app |
| Send-Q (established) | bytes | `ss -tan` | Sent, not yet ACKed |

- Source: `/proc/net/tcp` fields `tx_queue` and `rx_queue`.
- Pitfall: persistent Recv-Q means the application reads slowly.

## Socket memory (`/proc/net/sockstat`)

| Field | Unit | Meaning | Grep target |
|---|---|---|---|
| sockets: used | count | All sockets in use | `net/ipv4/proc.c` `sockstat_seq_show` |
| TCP inuse | count | TCP sockets in use | same |
| TCP orphan | count | Sockets with no owning file | same |
| TCP tw | count | TIME_WAIT sockets | same |
| TCP alloc | count | Allocated TCP sockets | same |
| TCP mem | pages | TCP memory | same |
| UDP inuse, mem | count, pages | | same |

- Pitfall: large orphan count means sockets closed by the app but still in FIN states.
