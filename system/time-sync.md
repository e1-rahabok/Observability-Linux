# Time Synchronization

Status: unverified.

## Clock source

| Metric | Unit | Source | Grep target | Pitfall |
|---|---|---|---|---|
| current_clocksource | enum | `/sys/devices/system/clocksource/clocksource0/current_clocksource` | `kernel/time/clocksource.c` | `tsc`, `hpet`, `kvm-clock`, `acpi_pm`. VMs may fall back to slower sources |

## chrony (`chronyc tracking`)

| Field | Unit | Meaning |
|---|---|---|
| System time | s | Offset of system clock from reference |
| Last offset | s | Most recent correction |
| RMS offset | s | Root mean square offset |
| Frequency | ppm | Clock frequency error |
| Root delay | s | Round trip to stratum-1 |
| Root dispersion | s | Error bound |

## ntpq (`ntpq -p`)

| Field | Unit | Meaning |
|---|---|---|
| offset | ms | Clock offset from peer |
| delay | ms | Round-trip to peer |
| jitter | ms | Offset variation |

- Pitfall: offsets above a few ms affect logs, TLS, and distributed systems.
- Note: chrony and ntpq report in different units (s vs ms). Convert before comparing.
