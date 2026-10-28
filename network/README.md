# Network Metrics

## Pages

- [interfaces.md](interfaces.md): interface counters, driver counters, link state, softnet
- [ip-tcp-udp.md](ip-tcp-udp.md): IP, TCP, TCPExt, UDP counters; per-socket TCP info
- [sockets.md](sockets.md): socket states, queues, socket memory
- [conntrack-and-neighbors.md](conntrack-and-neighbors.md): conntrack, ARP/ND, active measurements

## Source files (kernel)

- `net/core/net-sysfs.c`, `net/core/net-procfs.c`: interface counters
- `net/core/dev.c`: `dev_get_stats`, backlog, NAPI
- `net/ipv4/proc.c`: `/proc/net/snmp`, `sockstat`
- `net/ipv4/tcp*.c`: TCP MIB increments
- `net/ipv4/udp.c`: UDP MIB
- `net/netfilter/nf_conntrack_*.c`: conntrack
- `net/ipv4/arp.c`, `net/core/neighbour.c`: neighbor states
