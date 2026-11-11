# Lab 006 — DNS Troubleshooting

**Incident:** INC-006  
**Type:** Simulated incident  
**Status:** Resolved in simulation  
**Focus:** DNS resolution, connectivity testing, endpoint security logs

## Scenario

Jordan could access the internal company portal but could not open public websites. A coworker could browse normally.

## Investigation

1. `ping 1.1.1.1` succeeded, showing external IP connectivity at the time of the test.
2. `nslookup example.com` timed out when querying DNS server `192.168.10.5`.
3. A coworker's `nslookup` against the same DNS server succeeded.
4. `ipconfig /all` showed matching relevant configuration: same subnet, gateway, DNS server, and DHCP enabled.
5. The administrator noted that Jordan's company-managed endpoint security client had recently received a policy update.
6. Authorized review of diagnostic logs showed:

```text
Event: DNS Traffic Restricted
Destination: 192.168.10.5
Protocol: UDP
Destination Port: 53
Action: Blocked
```

## Resolution

The administrator confirmed the restriction was unintended and applied an approved policy correction. `nslookup example.com` then returned an address, and Jordan confirmed public websites loaded normally.

## Lessons learned

- `ping` to an IP and `nslookup` test different things.
- A successful ping to an external IP makes a general connectivity failure less likely, but does not prove all internet services are working.
- DNS commonly uses UDP port 53; other DNS transport may also be used in some circumstances.
- Endpoint security clients can filter network traffic.
- Inspect diagnostic logs through approved access; do not disable security controls as a shortcut.
