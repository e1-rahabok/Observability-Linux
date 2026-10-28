# Hardware Errors, Temperature, Power

Status: unverified.

## EDAC (ECC memory)

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| ce_count | count (cumulative) | `/sys/devices/system/edac/mc/mcN/ce_count` | `drivers/edac/edac_mc.c` |
| ue_count | count (cumulative) | `/sys/devices/system/edac/mc/mcN/ue_count` | `edac_mc.c` |

- Pitfall: rising corrected errors predict uncorrectable ones. Plan DIMM replacement.

## Machine check (MCE)

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| MCE events | count | `/dev/mcelog`, `rasdaemon`, kernel log | `arch/x86/kernel/cpu/mce/core.c` |

## PCIe AER

| Metric | Unit | Source | Grep target |
|---|---|---|---|
| Correctable errors | count | `/sys/bus/pci/devices/*/aer_dev_correctable` | `drivers/pci/pcie/aer.c` |
| Non-fatal errors | count | `/sys/bus/pci/devices/*/aer_dev_nonfatal` | `aer.c` |
| Fatal errors | count | `/sys/bus/pci/devices/*/aer_dev_fatal` | `aer.c` |

## Temperature

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Thermal zone temp | °C | raw / 1000 (raw is millidegrees) | `/sys/class/thermal/thermal_zoneN/temp` | `drivers/thermal/thermal_core.c` |
| Sensor readings | °C | Direct | `sensors` (lm-sensors) | userspace |

## Power (RAPL)

| Metric | Unit | Formula | Source | Grep target |
|---|---|---|---|---|
| Energy | µJ | Cumulative | `/sys/class/powercap/intel-rapl*/energy_uj` | `drivers/powercap/intel_rapl_common.c` |
| Wrap point | µJ | `max_energy_range_uj` | same directory | same |
| Power | W | Δenergy_uj / 10⁶ / Δt_s. If Δ < 0, add `max_energy_range_uj` before dividing | derived | |

- Pitfall: the counter wraps. Handle negative deltas.
