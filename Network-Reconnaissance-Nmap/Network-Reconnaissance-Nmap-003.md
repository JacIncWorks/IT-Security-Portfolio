# Network Reconnaissance & Nmap — Lab 003
## CUPS Package and Vulnerability Advisory Validation

### Objective
Investigate a locally detected CUPS service and practice checking package security status using installed package versions, APT metadata, and official Ubuntu security advisories.

### Environment
- **Operating system:** Ubuntu 26.04.1 LTS (Resolute)
- **Scanner:** Nmap 7.98
- **Target:** `127.0.0.1` (local loopback)
- **Service:** CUPS — Common UNIX Printing System
- **Port:** TCP 631

> **Scope note:** `127.0.0.1` is a standard loopback address and is retained for reproducibility. No public IP address is needed for this local scan.

### 1. Service discovery

**Command**
```bash
nmap -sV -p 631 127.0.0.1
```

**Observed results**
- TCP port 631 was reported open.
- Nmap identified the service as IPP/CUPS 2.4.

**Interpretation**

The scan confirmed that the service accepted connections from the local loopback scanning position. This does not establish external network exposure or indicate compromise.

### 2. Installed package verification

**Commands**
```bash
dpkg-query -W -f='${Package} ${Version}\n' cups cups-daemon
```

```bash
apt-cache policy cups cups-daemon
```

**Observed results**
- `cups`: `2.4.16-1ubuntu1.3`
- `cups-daemon`: `2.4.16-1ubuntu1.3`
- Installed and candidate versions matched for both packages.
- APT metadata showed the packages available from the configured Resolute updates and security repositories.
- An older `2.4.16-1ubuntu1` revision was also listed in the release repository.

**Interpretation**

The installed CUPS packages matched the candidates shown by the local APT metadata at the time of inspection. This indicates that APT did not identify a newer candidate for these packages in the configured sources at that time.

### 3. Operating system verification

**Command**
```bash
lsb_release -a
```

**Observed results**
- Distributor: Ubuntu
- Description: Ubuntu 26.04.1 LTS
- Release: 26.04
- Codename: `resolute`

The release information was used to interpret Ubuntu-specific package security information.

### 4. Security advisory validation

**Official advisories reviewed**
- [USN-8405-1 — CUPS vulnerabilities](https://ubuntu.com/security/notices/USN-8405-1)
- [USN-8405-2 — CUPS regression](https://ubuntu.com/security/notices/USN-8405-2)

USN-8405-1 listed `2.4.16-1ubuntu1.2` as the corrected CUPS and CUPS daemon revision for Ubuntu 26.04 LTS.

USN-8405-2 addressed a regression introduced by the earlier update and listed `2.4.16-1ubuntu1.3` as the corrected revision for Ubuntu 26.04 LTS.

The installed `cups` and `cups-daemon` versions recorded in this lab were both `2.4.16-1ubuntu1.3`, matching the revision specified in USN-8405-2.

**Finding:** Based on the recorded package details, the installed revisions met the corrected-version requirement for the issues covered by these advisories.

This does not establish that every known or subsequently reported CUPS vulnerability is fixed. Each additional vulnerability must be evaluated against its own advisory, affected release, package revision, and current status.

### 5. Analyst assessment

#### Confirmed by the recorded checks
- CUPS was detected on TCP port 631 from the local scanning position.
- The Ubuntu release was identified as 26.04.1 LTS.
- Installed versions of `cups` and `cups-daemon` were recorded.
- APT reported matching installed and candidate versions for both packages.
- The recorded package revisions met the corrected-version requirement in USN-8405-2.

#### Not established
- Whether every possible CUPS vulnerability is fixed.
- Whether port 631 is reachable from another machine or network.
- Whether the service has additional configuration weaknesses.
- Whether the system is free of compromise.

### Conclusion
This investigation demonstrated a basic vulnerability-validation workflow: discover a service, identify its installed package, confirm the operating system release, and compare the package revision against relevant vendor security advisories.

The recorded evidence supports the conclusion that the installed CUPS packages met the corrected-version requirement for the issues covered by USN-8405-2. It does not justify declaring the service universally secure.

**Key lesson:** An open service is not automatically vulnerable, and a package meeting one advisory's fixed-version requirement does not prove that every vulnerability has been resolved.

### Skills Practiced
- Nmap service and version detection
- Linux package inspection with `dpkg-query`
- APT candidate-version verification
- Ubuntu release identification
- Vendor advisory research
- Evidence-based security conclusions
- Documenting limitations
