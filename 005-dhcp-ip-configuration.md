# Lab 005 — DHCP & IP Configuration

**Incident:** INC-005  
**Type:** Simulated incident  
**Status:** Resolved in simulation; underlying cause not confirmed  
**Focus:** DHCP, APIPA, IP configuration, safe escalation

## Scenario

Jordan's laptop connected to office Wi-Fi but could not access the internet or internal company portal. Other employees were unaffected.

## Initial assessment

- Classified as a single-user issue (P3 in the exercise).
- Suspected an IP configuration or DHCP lease problem.
- Checked configuration before changing settings.

## Investigation

Jordan's `ipconfig` output showed:

```text
IPv4 Address:    169.254.82.17
Subnet Mask:     255.255.0.0
Default Gateway:
```

The `169.254.x.x` address is an APIPA address that Windows can self-assign when it cannot obtain an IPv4 address from DHCP. The missing default gateway was another important clue.

A coworker's working configuration showed:

```text
IPv4 Address:    192.168.10.44
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
DHCP Enabled:    Yes
```

Jordan's adapter also had DHCP enabled. Running `ipconfig /renew` returned an error indicating that the DHCP server could not be contacted and the request timed out. Jordan confirmed the correct office Wi-Fi was selected, and the administrator confirmed the access point was operating normally.

## Resolution

The IT administrator reviewed the adapter settings and performed an approved network-adapter reset. Jordan then received a valid configuration:

```text
IPv4 Address:    192.168.10.62
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

Jordan confirmed access to both the internet and internal company portal.

## Verification and uncertainty

The reset restored service, but the exact underlying cause was not established. The ticket should not claim that a driver fault was proven.

## Lessons learned

- Wi-Fi connected does not necessarily mean the device has usable network configuration.
- APIPA plus a missing gateway suggests DHCP lease acquisition failed.
- `ipconfig` displays current configuration; `ipconfig /renew` requests a DHCP lease renewal.
- Compare a working device, but remember that its exact IP address should differ.
- Verify the original user-facing outcome after a corrective action.
