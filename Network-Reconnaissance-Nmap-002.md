# Network Reconnaissance & Nmap — Lab 002
## CUPS Service and Process Investigation

### Objective
Investigate a locally detected network service by correlating Nmap service identification with Linux socket and process information. Record what the evidence establishes and identify the limits of the investigation.

### Environment
- **Operating system:** Ubuntu in VirtualBox
- **Network mode:** NAT
- **Scanner:** Nmap
- **Target:** `127.0.0.1` (IPv4 loopback)
- **Service:** CUPS / Internet Printing Protocol (IPP)
- **Port:** TCP 631

> **Scope note:** `127.0.0.1` is a standard loopback address, not a public IP address. It is retained to keep the commands reproducible.

### Method

#### 1. Inspect listening TCP sockets
```bash
sudo ss -ltnp | grep ':631'
```
**Purpose:** Identify listening TCP sockets associated with port 631 and display process information where available.

#### 2. Inspect the associated process
```bash
ps -p 1279 -o pid,ppid,user,comm,args
```
**Purpose:** Examine the process ID, parent process ID, execution user, command name, and command arguments.

#### 3. Identify the service with Nmap
```bash
nmap -sV -p 631 127.0.0.1
```
**Purpose:** Probe TCP port 631 on the local loopback target and attempt to identify the service and version.

### Findings

#### Listening sockets
Two listening sockets were observed on TCP port 631:
- `127.0.0.1:631` — IPv4 loopback
- `[::1]:631` — IPv6 loopback

Both sockets were associated with the `cupsd` process, reported as PID 1279 during this investigation.

#### Process information
The process inspection returned:
- **PID:** 1279
- **PPID:** 1
- **User:** `root`
- **Command:** `cupsd`

The parent process ID and command are consistent with a system-managed service. The process running as root is worth understanding in the context of the service's privilege requirements, but does not independently indicate malicious activity.

#### Nmap results
Nmap reported:

```text
PORT    STATE SERVICE VERSION
631/tcp open  ipp     CUPS 2.4
```

The scan reported one host up and completed in approximately 6.42 seconds. The service identification is consistent with CUPS providing IPP functionality on TCP port 631.

### Security Assessment
The combined evidence is consistent with an expected local printing service. The observed sockets are bound to IPv4 and IPv6 loopback rather than all network interfaces, supporting a preliminary assessment that these particular listeners are restricted to local connections.

No clear indicators of compromise were identified within the limited scope of this investigation.

The results do not independently establish:
- Whether the installed software is fully patched or free from vulnerabilities.
- Whether other CUPS listeners or network access paths exist.
- Whether the service configuration and privileges meet every applicable security requirement.
- Whether the software binaries have been independently verified for integrity.

The scan targeted loopback. It did not test reachability from another computer or establish internet accessibility.

### Limitations
- The investigation focused on TCP port 631.
- Nmap service/version detection identifies a likely service; it is not a definitive vulnerability assessment.
- Process and socket inspection provide local-host evidence, not a complete service security audit.
- Firewall behavior, remote reachability, service logs, patch applicability, and software integrity were not comprehensively examined.

### Conclusion
This lab demonstrated how to correlate Nmap service identification with Linux socket and process information. The findings support a preliminary assessment that CUPS was operating as a local printing service. No clear indicators of compromise were identified, but this limited investigation does not prove the service is free of vulnerabilities.

**Key lesson:** Separate observed facts from assumptions, and document what remains unverified.

### Skills Practiced
- Nmap service and version detection
- TCP port-state interpretation
- Listening-socket inspection with `ss`
- Process identification with `ps`
- Network-to-process evidence correlation
- Preliminary security assessment
- Evidence-based reporting and limitations
