# Lab 008 — Windows Performance & Storage

**Incident:** INC-008  
**Type:** Simulated incident  
**Status:** Escalated to Hardware Support; replacement pending  
**Focus:** Task Manager, Resource Monitor, disk latency, storage health

## Scenario

Sarah reported that applications were slow to open, window switching lagged, and the computer intermittently froze. Internet access worked, no error messages appeared, and other employees were unaffected.

## Initial triage

The incident was treated as a single-user performance issue (P3 initially, subject to business-impact reassessment). Task Manager was selected to inspect CPU, memory, disk, and network utilization.

Initial snapshot:

| Resource | Utilization |
|---|---:|
| CPU | 94% |
| Memory | 68% |
| Disk | 99% |
| Network | 3% |

Top processes included Windows Update, Antimalware Service Executable (Microsoft Defender), and Chrome. Sarah reported that Windows updates had installed during Friday's shutdown.

## Investigation

The update history showed legitimate post-update maintenance and a scheduled Defender scan. Chrome had 14 active tabs. No suspicious processes or security alerts were identified.

Resource Monitor was then used to inspect disk activity:

| Finding | Result |
|---|---:|
| Disk active time | 99% |
| Average disk response time | 1,850 ms |
| Disk queue | Consistently elevated |
| CPU after maintenance progressed | 61% |
| Disk utilization after CPU decreased | Around 99% |

The `System` process and Windows Update accounted for substantial disk activity. However, update activity decreased while disk utilization remained very high.

Additional storage-health diagnostics reported potential hardware degradation, and storage-related warnings appeared in Event Viewer. The workstation contained an aging SSD.

## Recommended action

The administrator recommended:
1. Back up important data immediately using approved procedures.
2. Escalate to Hardware Support.
3. Arrange SSD replacement.
4. Verify storage health and workstation performance after replacement.

The drive was not yet replaced in the simulation, so the incident remained escalated rather than resolved.

## Lessons learned

- Disk utilization measures how busy the drive is, not how full it is.
- High response time indicates slow completion of storage requests; it does not alone prove hardware failure.
- `System` is a legitimate Windows process that can perform work on behalf of the OS, drivers, and other components.
- High resource usage by legitimate processes is not automatically malicious.
- The persistence of high disk activity after CPU load decreased justified further storage investigation.
- Back up data and avoid unnecessary write-heavy stress tests when a drive reports reliability warnings.
