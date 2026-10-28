# Device Health

Status: unverified. SMART values come from drive firmware, not kernel source.

## SMART (ATA/SCSI, via `smartctl`)

| Attribute | Unit | Meaning | Pitfall |
|---|---|---|---|
| Reallocated_Sector_Ct | count | Sectors remapped | Rising count = failing media |
| Current_Pending_Sector | count | Unreadable sectors awaiting remap | Any non-zero is a warning |
| Offline_Uncorrectable | count | Unreadable during offline scan | |
| UDMA_CRC_Error_Count | count | Interface CRC errors | Usually cabling |
| Power_On_Hours | hours | Lifetime | |
| Temperature_Celsius | °C | Drive temperature | Raw format varies by vendor |

- Pitfall: raw value formats vary by vendor. Use normalized value and threshold from the drive.

## NVMe SMART log (log page 0x02)

| Field | Unit | Formula / meaning | Source | Grep target |
|---|---|---|---|---|
| critical_warning | bitmask | Bit flags for critical conditions | `nvme smart-log` | `drivers/nvme/host/core.c` `nvme_get_log` |
| temperature | Kelvin (raw) | Convert: °C = K − 273.15 | `nvme smart-log` | same |
| available_spare | % | Remaining spare capacity | `nvme smart-log` | same |
| percentage_used | % | Estimate of rated endurance used. Can exceed 100 | `nvme smart-log` | same |
| media_errors | count | Unrecovered media errors | `nvme smart-log` | same |
| data_units_read | units of 1000 × 512 B | Cumulative | `nvme smart-log` | same |
| data_units_written | units of 1000 × 512 B | Cumulative. Bytes = value × 1000 × 512 | `nvme smart-log` | same |

## Kernel I/O error messages

| Pattern | Meaning | Source |
|---|---|---|
| `blk_update_request: I/O error` | Block request failed | `dmesg -T` |
| Controller reset, timeout messages | Device or driver fault | `dmesg -T` |

- Pitfall: retries and timeouts often precede device failure.
