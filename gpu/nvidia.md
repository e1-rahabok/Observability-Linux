# NVIDIA GPU Metrics

Status: unverified. Source: NVML (`nvidia-smi`). Verify against NVML API reference.

| Metric | Unit | Definition | Tool / field |
|---|---|---|---|
| GPU utilization | % | Share of sample period with at least one kernel running. Not occupancy | `nvidia-smi` `utilization.gpu` |
| Memory controller utilization | % | Share of sample period with device memory read or written | `utilization.memory` |
| Memory used | MiB | Allocated device memory | `memory.used` |
| Memory total | MiB | Device memory | `memory.total` |
| GPU memory used % | % | memory.used / memory.total × 100 | derived |
| Temperature | °C | Direct | `temperature.gpu` |
| Power draw | W | Direct | `power.draw` |
| Power limit | W | Direct | `power.limit` |
| SM clock | MHz | Direct | `clocks.sm` |
| ECC corrected (volatile) | count | Since last reset | `nvidia-smi -q` |
| ECC uncorrected (volatile) | count | Since last reset | `nvidia-smi -q` |
| Per-process GPU memory | MiB | Direct | `nvidia-smi` process list |

## Pitfalls

- High utilization with low SM occupancy is a kernel efficiency issue, not saturation.
- Utilization is time-based, not throughput-based. Two kernels at 50% of SMs each read as 100% utilization when they overlap in time.
- Cross-check with `nvidia-smi dmon` and NVML sample code.
