# Linux Host Investigation 003 — Service and Process Verification

## 1. Overview

**Lab ID:** LINUX-003  
**Environment:** Ubuntu 26.04.1 LTS — VirtualBox VM  
**Investigation Type:** Linux service and process investigation  
**Target Service:** `colord`  
**Main PID:** `1644`  
**Assessment:** Consistent with expected system functionality; no clear indicators of compromise identified in the evidence examined.

## 2. Objective

Investigate a running Linux service to establish its identity, execution context, and relationship to an installed software package.

The objective was to evaluate the available evidence without assuming that an unfamiliar service was malicious or that a recognized package was automatically safe.

## 3. Investigation Procedure

### Step 1 — Identify Running Services

**Command:**

```bash
systemctl --type=service --state=running
```

**Purpose:** List currently running systemd services and identify a service for further examination.

**Observation:** The `colord` service was selected for investigation.

### Step 2 — Examine Service Status

**Command:**

```bash
systemctl status colord
```

**Observed findings:**

- Service was loaded.
- Service was actively running.
- Main process ID was `1644`.
- Reported runtime was approximately 59 minutes.

**Analysis:** These findings established that the service was active. Its runtime alone did not establish whether it was legitimate or safe.

### Step 3 — Inspect the Main Process

**Command:**

```bash
ps -p 1644 -o pid,ppid,user,comm,args
```

**Observed findings:**

| Field | Result |
|---|---|
| PID | `1644` |
| PPID | `1` |
| User | `colord` |
| Executable path | `/usr/libexec/colord` |

**Analysis:**

- PID `1644` identified the process under investigation.
- PPID `1` indicated that its recorded parent was PID 1, normally `systemd` on this Ubuntu installation.
- The process ran under the dedicated `colord` service account rather than directly as root.
- The executable path was consistent with the service's name and expected purpose.

These characteristics were consistent with normal service execution but did not independently establish executable integrity.

### Step 4 — Verify Package Ownership

**Command:**

```bash
dpkg -S /usr/libexec/colord
```

**Observation:** The package database associated `/usr/libexec/colord` with the `colord` package.

**Analysis:** This established the recorded package ownership of the executable path. It did not prove that the file's current contents were authentic or unmodified.

### Step 5 — Inspect Package Metadata

**Command:**

```bash
dpkg -s colord
```

**Observed findings:**

- Package status: `install ok installed`
- Package description: Related to managing, installing, and generating color profiles for accurate color assignment.

**Analysis:** The package metadata described functionality consistent with the service's name and observed execution path. Package status confirmed that the package database recorded the package as installed, not that the executable had passed an integrity check.

## 4. Findings and Assessment

The evidence collected was consistent with a legitimate Linux desktop color-management service.

No clear indicators of compromise were identified in the specific evidence examined.

However, the investigation did not include an independent integrity verification of the executable, an in-depth log review, or a network activity audit. The assessment is therefore preliminary and limited to the available evidence.

### Key Lessons

- A running service is not inherently malicious.
- A process running under a dedicated service account may reflect least-privilege design.
- PPID information helps establish process relationships but does not prove legitimacy.
- Package ownership and installation status are useful evidence, not guarantees of safety.
- Suspicious indicators should guide further investigation rather than automatically determine the conclusion.

## 5. Conclusion

The `colord` service exhibited characteristics consistent with expected Ubuntu desktop functionality. Its service state, process identity, executable path, package ownership, and package description supported this assessment.

No clear evidence of malicious activity was established during this investigation. Further integrity verification and log analysis would be required to increase confidence beyond the scope of the checks performed.

## 6. Commands Reference

| Command | Purpose |
|---|---|
| `systemctl --type=service --state=running` | List running services |
| `systemctl status colord` | Inspect service status and details |
| `ps -p 1644 -o pid,ppid,user,comm,args` | Examine process identity and parent |
| `dpkg -S /usr/libexec/colord` | Identify recorded package ownership |
| `dpkg -s colord` | Inspect installed package metadata |

**Skills Practiced:** Linux service enumeration, process inspection, parent-process interpretation, package verification, and evidence-based security assessment.
