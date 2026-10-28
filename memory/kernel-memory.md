# Kernel Memory, Commit, Hugepages, Fragmentation, NUMA

Status: unverified.

## Slab

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Slab | kB | `NR_SLAB_RECLAIMABLE_B + NR_SLAB_UNRECLAIMABLE_B` / 1024 | `/proc/meminfo` | `mm/slab_common.c`, `mm/slub.c` |
| SUnreclaim | kB | Slab without `SLAB_RECLAIM_ACCOUNT` | `/proc/meminfo` | `slub.c` |
| Per-cache objects | count | `active_objs`, `num_objs` | `/proc/slabinfo` | `mm/slab_common.c` |
| Per-cache object size | bytes | `objsize` | `/proc/slabinfo` | |

- Pitfall: unreclaimable slab growth over time indicates a kernel leak (often a module).
- Detail: `slabtop` reads `/proc/slabinfo`.

## Page tables and kernel stacks

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| PageTables | kB | `/proc/meminfo` | `mm/vmstat.c` (`NR_PAGETABLE`) |
| KernelStack | kB | `/proc/meminfo` | `kernel/fork.c` |
| VmallocUsed | kB | `/proc/meminfo` | `mm/vmalloc.c` |

## Commit accounting

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| CommitLimit | kB | `(totalram_pages − hugetlb_pages) × overcommit_ratio / 100 + total_swap_pages` (enforced when `vm.overcommit_memory = 2`) | `/proc/meminfo` | `mm/util.c`, `mm/mmap.c` |
| Committed_AS | kB | Sum of committed virtual memory (percpu counter `vm_committed_as`) | `/proc/meminfo` | `mm/util.c` `vm_memory_committed` |
| Commit ratio | % | Committed_AS / CommitLimit × 100 | derived | |

- Pitfall: Committed_AS above CommitLimit matters only with `vm.overcommit_memory = 2`.

## Hugepages

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| HugePages_Total | pages | Configured count | `/proc/meminfo` | `mm/hugetlb.c` |
| HugePages_Free | pages | Direct reading | `/proc/meminfo` | `hugetlb.c` |
| HugePages_Rsvd | pages | Reserved, not yet faulted | `/proc/meminfo` | `hugetlb.c` |
| HugePages_Surp | pages | Surplus over configured | `/proc/meminfo` | `hugetlb.c` |
| HugePages used | pages | HugePages_Total − HugePages_Free | derived | |
| Hugepagesize | kB | Default huge page size | `/proc/meminfo` | `hugetlb.c` |
| AnonHugePages | kB | `NR_ANON_THPS` | `/proc/meminfo` | `mm/huge_memory.c` |
| THP mode | enum | `always`, `madvise`, `never` | `/sys/kernel/mm/transparent_hugepage/enabled` | `mm/huge_memory.c` |
| THP defrag | enum | Compaction policy | `/sys/kernel/mm/transparent_hugepage/defrag` | `huge_memory.c` |

- Pitfall: THP compaction stalls cause latency spikes. Check `compact_stall` (see swap-and-reclaim.md).

## Fragmentation

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Free blocks per order k | count | Count column k of `/proc/buddyinfo` | `/proc/buddyinfo` | `mm/vmstat.c` `frag_show` |
| Order k block size | bytes | 2^k × PAGE_SIZE | derived | |

- Pitfall: few high-order blocks means large allocations fail or stall.

## NUMA

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| numa_hit | count | Cumulative | `numastat`, `/sys/devices/system/node/nodeN/numastat` | `mm/vmstat.c` |
| numa_miss | count | Cumulative | same | same |
| numa_foreign | count | Cumulative | same | same |
| NUMA miss ratio | % | numa_miss / (numa_hit + numa_miss) × 100 | derived | |

- Pitfall: high miss ratio means cross-node memory access, adding latency.
