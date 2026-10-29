# Network Reconnaissance & Nmap — Lab 001
## Basic Port Scanning and Service Identification

### Objective
Use Nmap to identify an open TCP port on a local Ubuntu virtual machine, then correlate the scan results with host-level socket information.

### Environment
- **Operating system:** Ubuntu in VirtualBox
- **Virtual network mode:** NAT
- **VM address:** `[REDACTED PRIVATE IP]/24`
- **Scanner:** Nmap 7.98
- **Scan target:** `127.0.0.1` (IPv4 loopback)

> **Scope note:** `127.0.0.1` is the standard IPv4 loopback address. It refers to the local machine and is intentionally retained because it is essential to reproducing this lab. The private VM address has been redacted.

### Commands
```bash
nmap 127.0.0.1
nmap -sV -p 631 127.0.0.1
sudo ss -ltnp | grep ':631'
```

### Findings

#### 1. Initial port scan
The default Nmap scan reported:
- Target host: up
- TCP port 631: open
- Service label: `ipp`
- 999 other scanned TCP ports: closed

#### 2. Service identification
The targeted service-detection scan reported:

```text
PORT    STATE SERVICE VERSION
631/tcp open  ipp     CUPS 2.4
```

Nmap identified the service as CUPS 2.4, which is commonly associated with printing functionality.

#### 3. Host-level correlation
The socket inspection reported:

```text
LISTEN 0 4096 127.0.0.1:631 0.0.0.0:* users:(("cupsd",pid=1200,fd=7))
LISTEN 0 4096 [::1]:631    [::]:*     users:(("cupsd",pid=1200,fd=6))
```

Both listeners were associated with the `cupsd` process, reported as PID 1200 during this investigation. The listening addresses indicate IPv4 and IPv6 loopback bindings.

### Assessment
The Nmap results and socket output are consistent with a local CUPS printing service. The two sources provide complementary evidence: Nmap identifies the service from a network-scanning perspective, while `ss` shows local listening sockets and their associated process.

No clear indicators of compromise were identified within the limited scope of this lab.

### Limitations
- The scan targeted `127.0.0.1`; it did not test access from another computer.
- The investigation focused on the default Nmap TCP scan and TCP port 631.
- No comprehensive vulnerability assessment or configuration audit was performed.
- Software integrity and patch status were not independently verified.
- The results do not establish whether other network-access paths exist.

### Conclusion
This lab demonstrated how Nmap can identify open TCP ports and probable services, and how `ss` can correlate a listening socket with a local process. The evidence supports a preliminary assessment of the local CUPS service; an open port alone is not proof of vulnerability or compromise.

### Skills Practiced
- Basic Nmap scanning
- TCP port-state interpretation
- Service and version identification
- Linux socket inspection
- Process-to-network correlation
- Evidence-based assessment
- Documenting investigative limitations
