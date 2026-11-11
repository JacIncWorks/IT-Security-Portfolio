# Lab 002 — Troubleshooting & Escalation

**Incident:** INC-002  
**Type:** Simulated incident  
**Status:** Resolved through escalation/workaround in simulation  
**Focus:** Intermittent connectivity, fault isolation, evidence-based escalation

## Scenario

Marcus experienced internet drops lasting approximately 20–30 seconds. Wi-Fi remained connected, a coworker was unaffected, and Marcus had an upcoming meeting in 25 minutes.

## Initial assessment

- Treated as a single-user issue with time-sensitive business impact (P2 in the exercise).
- Considered a local wireless or device-side problem, without treating that as confirmed.
- Clarified scope and connection type before making changes.

## Investigation

1. Compared Marcus's experience with a coworker's.
2. Used ping tests to distinguish local gateway connectivity from external IP connectivity.
3. Both the gateway (`192.168.1.1`) and external IP (`1.1.1.1`) showed intermittent failures.
4. The coworker on the same access point remained stable and signal strength was strong.
5. Wireless logs showed disconnect/reconnect events associated with the adapter/driver.

## Response

A temporary hotspot was considered only as a policy-approved workaround. The issue was escalated with the collected evidence and the user's immediate meeting requirement.

## Lessons learned

- A successful ping to the gateway tests a different segment from a successful ping to an external IP.
- Ping results help narrow the fault domain; they do not independently identify root cause.
- A driver/adapter association in logs is evidence, not proof that the driver is definitively faulty.
- Escalation notes should include symptoms, timestamps, tests, results, workaround, and remaining uncertainty.
