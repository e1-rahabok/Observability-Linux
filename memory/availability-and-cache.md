# Memory Availability and Cache

Status: unverified. All values from `/proc/meminfo` are in kB (1024 bytes).

| Metric | Unit | Formula | Source | Grep target | Pitfall |
|---|---|---|---|---|---|
| MemTotal | kB | Direct reading | `/proc/meminfo` | `fs/proc/meminfo.c` | Physical RAM visible to kernel |
| MemFree | kB | `NR_FREE_PAGES` × PAGE_SIZE / 1024 | `/proc/meminfo` | `meminfo.c` | Usually low by design. Not a health signal |
| MemAvailable | kB | Estimate of memory reclaimable without swapping. Approx.: `free − totalreserve + pagecache + reclaimable_slab`, where each reclaimable term subtracts half of its LRU or slab size clamped to the low watermark | `/proc/meminfo` | `mm/page_alloc.c` `si_mem_available` | Exact expression varies by kernel. Confirm |
| MemUsed (derived) | kB | MemTotal − MemAvailable | derived | | Matches `free` "used" only on newer procps |
| Memory used % | % | (MemTotal − MemAvailable) / MemTotal × 100 | derived | | |
| Cached | kB | `NR_FILE_PAGES − total_swapcache_pages() − Buffers` | `/proc/meminfo` | `meminfo.c` | File-backed page cache. Reclaimable |
| Buffers | kB | `nr_blockdev_pages()` | `/proc/meminfo` | `meminfo.c` | Block device metadata cache |
| SReclaimable | kB | Slab with `SLAB_RECLAIM_ACCOUNT` | `/proc/meminfo` | `mm/slab_common.c` | |
| Reclaimable cache (derived) | kB | Cached + SReclaimable | derived | | Approximate. Not all reclaimable immediately |
| Dirty | kB | `NR_FILE_DIRTY` | `/proc/meminfo` | `mm/page-writeback.c` | Modified pages awaiting writeback |
| Writeback | kB | `NR_WRITEBACK` | `/proc/meminfo` | `page-writeback.c` | Pages being written |
| AnonPages | kB | `NR_ANON_MAPPED` | `/proc/meminfo` | `mm/vmstat.c` | Heap, stack, private data. Needs swap to reclaim |
| Shmem | kB | `NR_SHMEM` | `/proc/meminfo` | `mm/shmem.c` | tmpfs and SysV shm. Not reclaimable like page cache |
| Mapped | kB | `NR_FILE_MAPPED` | `/proc/meminfo` | `mm/vmstat.c` | File pages mapped into processes |

## LRU lists

| Metric | Unit | Source |
|---|---|---|
| Active(anon), Inactive(anon) | kB | `/proc/meminfo` |
| Active(file), Inactive(file) | kB | `/proc/meminfo` |

Reclaim takes from inactive lists first.

## Dirty thresholds

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| vm.dirty_ratio | % of available memory | `/proc/sys/vm/dirty_ratio` | `mm/page-writeback.c` |
| vm.dirty_background_ratio | % | `/proc/sys/vm/dirty_background_ratio` | `page-writeback.c` |
| vm.dirty_bytes, dirty_background_bytes | bytes | `/proc/sys/vm/dirty_bytes` | `page-writeback.c` (override ratios) |
