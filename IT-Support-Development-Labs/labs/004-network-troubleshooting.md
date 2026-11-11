# Lab 004 — Network Troubleshooting

**Incident:** INC-004  
**Type:** Simulated incident  
**Status:** Resolved in simulation  
**Focus:** Network layers, IP connectivity, DNS, security client policy

## Scenario

Priya's Wi-Fi showed connected, but public websites would not open. An internal site worked, and a coworker on the same office Wi-Fi could browse normally. Priya needed presentation files within 40 minutes.

## Investigation

1. Confirmed that Wi-Fi association does not guarantee internet access.
2. Verified that the internal company site worked.
3. Tested the default gateway and external IP; both responded.
4. Ran `nslookup example.com`; the request timed out against DNS server `192.168.10.5`.
5. Compared the coworker's configuration. The coworker used the same DNS server and could resolve names.
6. Avoided substituting a public DNS server without authorization.

## Findings and resolution

The administrator found a stale network policy in Priya's managed security client. After the approved policy refresh, DNS resolution succeeded and public websites loaded. Priya confirmed she could download the presentation files.

## Lessons learned

- Successful IP connectivity plus failed name resolution makes DNS a strong suspect.
- Internal and external resource access provide useful clues about the fault domain.
- DNS failure is not automatically proof of a DNS-server outage.
- Managed security software can affect network traffic.
- Do not bypass company security controls or change DNS settings without authorization.
