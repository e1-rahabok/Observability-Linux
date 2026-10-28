# Swap, Paging, Reclaim

Status: unverified.

## Swap

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| SwapTotal | kB | Direct reading | `/proc/meminfo` | `mm/swapfile.c` |
| SwapFree | kB | Direct reading | `/proc/meminfo` | `swapfile.c` |
| Swap used | kB | SwapTotal − SwapFree | derived | |
| Swap used % | % | (SwapTotal − SwapFree) / SwapTotal × 100 | derived | |
| SwapCached | kB | Swap pages also in RAM | `/proc/meminfo` | `mm/swap_state.c` |
| Swap in rate (si) | pages/s | Δpswpin / Δt | `/proc/vmstat` | `mm/page_io.c` |
| Swap out rate (so) | pages/s | Δpswpout / Δt | `/proc/vmstat` | `page_io.c` |

- Unit note: `pswpin`/`pswpout` count pages. Bytes/s = pages/s × PAGE_SIZE.
- Pitfall: swap size alone is not an error. Watch `si`/`so` activity.

## Paging counters (`/proc/vmstat`)

| Counter | Unit | Meaning | Rate formula | Grep target |
|---|---|---|---|---|
| pgfault | count | All minor and major faults | Δ / Δt | `mm/memory.c` |
| pgmajfault | count | Faults needing block I/O | Δ / Δt | `mm/memory.c` |
| pgpgin | **kB** | KB read from block devices | Δ / Δt (kB/s) | `mm/vmstat.c` |
| pgpgout | **kB** | KB written to block devices | Δ / Δt (kB/s) | `mm/vmstat.c` |
| pswpin | pages | Pages swapped in | Δ / Δt | `mm/page_io.c` |
| pswpout | pages | Pages swapped out | Δ / Δt | `mm/page_io.c` |

- Correction: `pgpgin`/`pgpgout` are already in kB. Do not multiply by PAGE_SIZE.

## Reclaim counters

| Counter | Unit | Meaning | Grep target |
|---|---|---|---|
| pgscan_kswapd | pages | Scanned by background reclaim | `mm/vmscan.c` |
| pgscan_direct | pages | Scanned by allocating process (latency) | `mm/vmscan.c` |
| pgsteal_kswapd, pgsteal_direct | pages | Actually reclaimed | `mm/vmscan.c` |
| allocstall_* | count | Synchronous reclaim events in allocation | `mm/vmscan.c` |
| compact_stall | count | Direct compaction events | `mm/compaction.c` |
| workingset_refault_anon, _file | count | Pages re-read soon after eviction. High means working set exceeds RAM | `mm/workingset.c` |

- Rate for any counter: Δcounter / Δt.
- Pitfall: non-zero `pgscan_direct` means allocating processes stalled to reclaim.

## Swappiness

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| vm.swappiness | 0–200 (dimensionless) | `/proc/sys/vm/swappiness`. Default 60 | `mm/vmscan.c` |
