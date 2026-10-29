# Linux Investigation 005 — Chrony Service Verification

## 1. Overview

**Lab ID:** LINUX-005  
**Environment:** Ubuntu 26.04.1 LTS — VirtualBox VM  
**Investigation Type:** Service and process verification  
**Target:** `chronyd`  
**Process ID:** `1136`  
**Assessment:** Evidence consistent with expected Chrony functionality; no clear indicators of compromise identified in the checks performed.

## 2. Objective

Investigate an unfamiliar Linux process by examining its execution context, package association, and service configuration.

The investigation aimed to determine whether the observed evidence was consistent with legitimate system functionality while identifying any remaining verification gaps.

## 3. Investigation Procedure

### Step 1 — Enumerate Processes

**Command:**

```bash
ps aux
```

**Purpose:** Display running processes, including their owners, process IDs, resource usage, and command lines.

**Observation:** A process named `chronyd` was identified.

Initial findings included:
- Process user: `_chrony`
- PID: `1136`
- Executable: `/usr/sbin/chronyd`
- Arguments: `-n -F 1`

**Analysis:** The executable name and path were consistent with Chrony, a network time synchronization service. The process was selected for further investigation rather than being classified as suspicious based solely on unfamiliarity.

### Step 2 — Examine Process Relationships

**Command:**

```bash
ps -p 1136 -o pid,ppid,user,comm,args
```

**Observed findings:**

| Field | Result |
|---|---|
| PID | `1136` |
| PPID | `1116` |
| User | `_chrony` |
| Command | `/usr/sbin/chronyd -n -F 1` |

The parent process was subsequently examined using:

```bash
ps -p 1116 -o pid,ppid,user,comm,args
```

The reported parent PID was `1030`, and the process was associated with the `_chrony` account.

**Analysis:** The process hierarchy and execution account provided additional context. PPID information establishes a reported parent-child relationship, but neither the parent process nor CPU usage alone establishes the legitimacy of a process.

### Step 3 — Verify Package Ownership

**Command:**

```bash
dpkg -S /usr/sbin/chronyd
```

**Observation:** The package database associated `/usr/sbin/chronyd` with the `chrony` package.

**Analysis:** This supported the hypothesis that the executable was associated with the expected software package. Package ownership alone does not establish that the executable's current contents are authentic or unmodified.

### Step 4 — Examine Package Verification Output

**Command:**

```bash
dpkg -V chrony
```

**Observed output:**

```text
?????????     /usr/share/chrony/chrony.keys
```

**Analysis:** The output indicated that the usual verification checks could not be reported for this file. The question marks were not evidence of nine separate failures and did not establish that the file was malicious.

The finding remained a verification limitation requiring interpretation in the context of the package's available metadata.

### Step 5 — Inspect File Ownership and Permissions

**Command:**

```bash
ls -l /usr/share/chrony/chrony.keys
```

**Observed findings:**

```text
-rw-r----- 1 root root 481 ...
```

**Interpretation:**

- Owner: `root`
- Group: `root`
- Permissions: `640`
- Size: `481` bytes

The owner had read and write permissions, the group had read permission, and others had no permissions granted by the file's mode.

**Analysis:** These restrictive permissions were consistent with protecting a configuration or key-related file. However, the file's ownership and permissions alone did not establish its integrity or explain the package verification output.

The file contents were not displayed during the investigation.

### Step 6 — Inspect Service Configuration

**Command:**

```bash
systemctl cat chrony.service
```

**Observed findings included:**

- Description identifying Chrony as an NTP client/server.
- A configured service user of `_chrony`.
- A startup command invoking a Chrony-related startup script.
- Multiple systemd security restrictions, including `NoNewPrivileges=yes`, `PrivateTmp=yes`, and `ProtectHome=yes`.

**Analysis:** The service description and execution configuration were consistent with the observed process and Chrony's expected purpose. The security restrictions were consistent with an effort to limit the service's access to system resources.

Service configuration describes how the service is configured to run; it does not independently prove that every executable or configuration file is authentic.

## 4. Findings and Assessment

The investigation collected several mutually consistent observations:

1. The process name, executable path, and arguments matched expected Chrony functionality.
2. The process ran under the `_chrony` account.
3. The package database associated the executable with the `chrony` package.
4. The service configuration identified Chrony as an NTP client/server and included systemd security restrictions.
5. The key-related file had restrictive ownership and permissions.
6. Package verification output for the key file remained insufficient to establish its integrity.

**Assessment:** The available evidence supported the conclusion that the process was consistent with the expected Chrony time-synchronization service. No clear indicators of compromise were identified in the checks performed.

The investigation did not independently establish the integrity of every relevant file, and the package verification output for `chrony.keys` remained unresolved.

## 5. Limitations and Further Investigation

Additional checks could include:

- Reviewing the package's verification metadata to understand the reported question marks.
- Examining relevant service logs for unexpected errors or activity.
- Verifying executable integrity using appropriate trusted package or repository metadata.
- Investigating unexpected network activity if further evidence justified doing so.

The absence of an identified indicator of compromise does not prove that compromise is impossible.

## 6. Key Lessons

- Process enumeration is a useful starting point for discovering investigation targets.
- PID and PPID information helps establish process relationships.
- Package ownership provides useful context but is not a guarantee of executable integrity.
- File permissions must be interpreted alongside ownership and intended purpose.
- Service configuration can corroborate a process's expected execution context.
- Verification discrepancies require investigation rather than automatic classification as malicious.
- A defensible security assessment should distinguish observations, hypotheses, conclusions, and limitations.

## 7. Commands Reference

| Command | Purpose |
|---|---|
| `ps aux` | Enumerate running processes |
| `ps -p 1136 -o pid,ppid,user,comm,args` | Inspect process identity |
| `ps -p 1116 -o pid,ppid,user,comm,args` | Examine the reported parent process |
| `dpkg -S /usr/sbin/chronyd` | Identify recorded package ownership |
| `dpkg -V chrony` | Examine available package verification results |
| `ls -l /usr/share/chrony/chrony.keys` | Inspect file permissions and ownership |
| `systemctl cat chrony.service` | Inspect service configuration |

## 8. Conclusion

The investigation demonstrated how to correlate process information, package ownership, file permissions, and service configuration to assess an unfamiliar Linux process.

The collected evidence was consistent with legitimate Chrony functionality. No clear evidence of malicious activity was established during the checks performed, although independent file integrity verification remained incomplete.

**Skills Practiced:** Linux process investigation, process hierarchy analysis, package ownership verification, file permission analysis, service configuration review, and evidence-based security assessment.
