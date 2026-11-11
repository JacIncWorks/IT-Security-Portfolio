# Lab 001 — IT Support Incident Triage

**Incident:** INC-001  
**Type:** Simulated incident  
**Status:** Resolved in simulation  
**Focus:** Scope, prioritization, DNS diagnosis, user verification

## Scenario

Sarah could not access the network share `\\FS-02\Department`. A colleague could access it, and Sarah's internet connection worked. Windows reported that it could not access the network path.

## Initial assessment

- Classified as a single-user incident (initially P2 in the exercise).
- Initial hypothesis: a device-specific configuration or connectivity issue.
- Compared Sarah's experience with a working colleague rather than assuming the file server was down.

## Investigation

1. Confirmed the issue affected Sarah rather than multiple employees.
2. Tested name resolution for `FS-02`.
3. `nslookup FS-02` failed on Sarah's device but succeeded on the colleague's device.
4. Compared DNS configuration and found Sarah was using the obsolete DNS server `10.20.4.53`, while the colleague used the approved DNS server `10.20.4.10`.

## Resolution

An administrator corrected Sarah's DNS configuration. The lookup subsequently resolved to `10.20.4.15`, and Sarah confirmed she could open the shared folder and open/save her report.

## Verification

The issue was not considered resolved merely because DNS lookup succeeded. The actual business task was tested: Sarah opened the folder and successfully saved a report.

## Lessons learned

- Compare affected and known-good systems.
- A successful internet connection does not guarantee that every internal resource can be reached.
- DNS evidence can point to a configuration problem, but verify access to the actual resource.
- Close the ticket only after the user-facing task succeeds.
