# Linux Investigation 004 — File Permissions and Metadata Analysis

## 1. Overview

**Lab ID:** LINUX-004  
**Environment:** Ubuntu 26.04.1 LTS — VirtualBox VM  
**Investigation Type:** File permissions, ownership, and metadata  
**Target File:** `sample.sh`  
**Assessment:** Lab objectives completed; permission changes and timestamp interpretation examined.

## 2. Objective

Investigate Linux file permissions and metadata to understand how access restrictions affect security.

The investigation focused on:
- Understanding owner, group, and other-user permissions.
- Comparing broad and restricted permission configurations.
- Examining file timestamps.
- Distinguishing observations from assumptions about suspicious activity.

## 3. Investigation Procedure

### Step 1 — Create a Test Environment

**Commands:**

```bash
mkdir -p ~/linux-lab-004
cd ~/linux-lab-004
```

A dedicated directory was created to keep the investigation separate from system files.

### Step 2 — Create a Test Script

**Command:**

```bash
printf '#!/bin/bash\necho "Linux permissions lab"\n' > sample.sh
```

This created a harmless shell script that prints a message when executed.

The script was inspected but not executed during the investigation.

### Step 3 — Apply Broad Permissions

**Command:**

```bash
chmod 777 sample.sh
```

**Purpose:** Grant read, write, and execute permissions to the file owner, group, and others.

**Security implications:**

- Owner: read, write, execute.
- Group: read, write, execute.
- Others: read, write, execute.

These permissions are unnecessarily broad for many scripts because any user covered by these permissions may modify the file. If a privileged process later executes a script that an untrusted user can modify, this could create a security risk.

### Step 4 — Apply More Restrictive Permissions

**Commands:**

```bash
chmod 750 sample.sh
ls -l sample.sh
```

**Expected permission string:**

```text
-rwxr-x---
```

**Permission breakdown:**

| Category | Permissions | Meaning |
|---|---|---|
| Owner | `rwx` | Read, write, execute |
| Group | `r-x` | Read and execute |
| Others | `---` | No permissions granted |

**Analysis:** Compared with `777`, mode `750` removes write access from the group and removes all permissions for others.

This is a more restrictive configuration when group members need to read and execute the script but do not need to modify it.

However, appropriate permissions depend on the file's intended purpose and the users who require access. Mode `750` does not guarantee that the script itself is safe.

### Step 5 — Examine File Metadata

**Command:**

```bash
stat sample.sh
```

**Observed findings:**

- Access timestamp: approximately `12:42:02`
- Modification timestamp: approximately `12:42:02`
- Birth timestamp: approximately `12:42:02`
- Change timestamp: approximately `12:43:51`

The access, modification, and birth timestamps were reported at approximately the same time. The change timestamp was roughly two minutes later.

**Analysis:**

The later change timestamp was consistent with the permission changes performed during the lab.

Linux timestamps have different meanings:

- **Access time (`atime`):** Records the last access time as maintained by the filesystem.
- **Modification time (`mtime`):** Records when the file's contents were last modified.
- **Change time (`ctime`):** Records when the file's inode metadata was last changed, including changes to permissions or ownership.
- **Birth time:** Records the file's creation time when supported by the filesystem.

The `ctime` field is not a creation timestamp. Changing permissions with `chmod` can update it without changing the file's contents.

The observed timestamps were consistent with the operations performed, but timestamps alone cannot establish who made a change or prove malicious intent.

## 4. Security Assessment

The lab demonstrated how broad file permissions can expose scripts to unnecessary modification and execution risks.

A file's permissions must be considered alongside its owner, group, contents, location, execution context, and intended purpose.

When investigating a suspicious file on an unfamiliar system, useful next steps may include:
- Reviewing the file's contents without executing it.
- Checking ownership and permissions.
- Examining timestamps and relevant metadata.
- Identifying processes or services that use the file.
- Reviewing available logs and audit records for corroborating evidence.

Logs may not record every file operation, particularly if appropriate auditing was not enabled beforehand. If the available evidence is insufficient, the investigator should document that limitation rather than claim certainty.

## 5. Key Lessons

- Linux file permissions determine which access rights are granted to the owner, group, and others.
- Broad write permissions can create security risks, especially when privileged processes execute modifiable files.
- More restrictive permissions reduce unnecessary access but do not guarantee that a file is trustworthy.
- File timestamps provide useful context but must be interpreted correctly.
- A timestamp correlation is not proof of who performed an action.
- Security conclusions should distinguish verified observations, hypotheses, and unresolved questions.

## 6. Commands Reference

| Command | Purpose |
|---|---|
| `mkdir -p ~/linux-lab-004` | Create the lab directory |
| `cd ~/linux-lab-004` | Navigate to the lab directory |
| `printf '...' > sample.sh` | Create a harmless test script |
| `chmod 777 sample.sh` | Grant broad file permissions |
| `chmod 750 sample.sh` | Apply more restrictive permissions |
| `ls -l sample.sh` | Inspect permissions and ownership |
| `stat sample.sh` | Inspect file metadata and timestamps |

## 7. Conclusion

This investigation demonstrated how Linux permissions affect file access and why excessively broad permissions can introduce security risks. It also established the importance of interpreting file metadata carefully.

The observed change timestamp was consistent with the permission modifications performed during the lab. In a real investigation, additional evidence would be required to establish the cause of an unfamiliar metadata change or attribute it to a particular user.

**Skills Practiced:** Linux file permissions, ownership interpretation, metadata analysis, timestamp interpretation, and evidence-based security assessment.
