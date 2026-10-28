# Linux Metrics Reference

Formula, unit, source, and kernel-code reference for Linux system metrics.
Covers CPU, memory, disk, network, system, cgroups, and GPU.

## Verification status

- **Status of every claim: unverified.** These pages were written from knowledge of the mainline tree. No kernel source was opened in producing them.
- Each page lists a grep target. Run it against a kernel tree to confirm.
- Kernel versions matter. Field counts and some semantics changed across 4.x, 5.x, and 6.x.
- Verify against: `git clone --depth 1 --branch vX.Y https://github.com/torvalds/linux`, or browse `elixir.bootlin.com`.
- Record the kernel version (`uname -r`) with any verification result.

## Conventions

- **Δ**: difference between two samples of a cumulative counter.
- **Δt**: elapsed seconds between samples.
- **Rate**: Δcounter / Δt.
- **CLK_TCK** (USER_HZ): jiffies per second. `getconf CLK_TCK`, usually 100.
- **PAGE_SIZE**: bytes per page. `getconf PAGESIZE`, usually 4096.
- **Sector**: 512 bytes in block-layer counters, regardless of device sector size.
- Counters are cumulative unless marked as a rate or gauge.

## Index

| Folder | Scope |
|---|---|
| [cpu/](cpu/README.md) | Time accounting, load, scheduler, pressure, throttling |
| [memory/](memory/README.md) | Availability, cache, kernel memory, swap, reclaim, OOM, process memory |
| [disk/](disk/README.md) | Block I/O, filesystems, device health |
| [network/](network/README.md) | Interfaces, IP/TCP/UDP, sockets, conntrack |
| [system/](system/README.md) | Kernel identity, uptime, FDs, PIDs, time sync, hardware errors |
| [cgroups/](cgroups/README.md) | cgroup v2 controllers and container limits |
| [gpu/](gpu/README.md) | NVIDIA GPU metrics (NVML, not kernel source) |

## Page format

Each metric entry gives:

- **Unit**
- **Formula**
- **Source** (file or tool)
- **Kernel grep target** (symbol or file to check)
- **Pitfall**
