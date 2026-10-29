Linux Host Investigation 002 — Process-to-Network Correlation

Lab Overview

Platform: Ubuntu 26.04.1 LTS
Environment: VirtualBox — Ubuntu-Cyber-Lab
Lab type: Hands-on Linux host investigation
Status: Completed

Objective

Investigate a Python process with UDP sockets associated with port 3702. Identify its user, parent process, executable, package ownership, and intended purpose.

The goal was to determine whether the available evidence supported a plausible legitimate explanation or warranted further suspicion.

1. Initial Network Observation

Command

sudo ss -tunap

Observations

The output included a Python process with PID 4780 and multiple UDP sockets.

One observed socket used port 3702. This port is commonly associated with WS-Discovery.

Analysis

An unfamiliar process communicating over a network deserves investigation, but neither Python nor UDP port 3702 is inherently malicious.

The process needed to be identified before its activity could be assessed.

2. Process Identification

Command

ps -p 4780 -o pid,ppid,user,lstart,etime,comm,args

Observations

Field

Value

PID

4780

Parent PID

4775

User

jay

Start time

October 17, 2026, 08:53:41

Elapsed time at capture

Approximately 14 minutes

Command

python3 /usr/bin/wsdd ...

The complete command line was truncated in the terminal output.

Analysis

The process ran under the ordinary user account jay, not root.

The command referenced /usr/bin/wsdd, providing a specific executable path to investigate.

The process start time established when the process began according to the recorded system clock, but did not establish what network activity occurred throughout its lifetime.

3. Parent Process Investigation

Command

ps -p 4775 -o pid,ppid,user,comm,args

Observation

The parent process was identified as:

gvfsd-wsdd

Its command line referenced /usr/libexec/gvfsd-wsdd.

Analysis

The parent process name and command were consistent with desktop integration for device discovery.

This strengthened the hypothesis that the Python process was associated with expected desktop functionality.

A parent process name alone does not prove that execution is legitimate, so additional evidence was collected.

4. Executable and Package Verification

Command

readlink -f /proc/4780/exe

Observation

The command returned:

/usr/bin/python3.14

Analysis

The running process used the Python interpreter. The interpreter path alone did not identify the script's purpose, so the script path and package metadata were examined separately.

Package ownership

Command:

dpkg -S /usr/bin/wsdd

Output:

wsdd: /usr/bin/wsdd

This established that the installed package database associated the path /usr/bin/wsdd with the wsdd package.

Package status and description

Command:

dpkg -s wsdd

Observed status:

Status: install ok installed

The package description identified wsdd as a Python Web Services Discovery daemon used to announce Linux hosts to Windows computers for network browsing.

Analysis

The package description provided a plausible explanation for the process and its network activity.

Package ownership and installation status are useful evidence, but do not independently verify file integrity or prove that every instance of the process is authorized.

5. Findings

Observed facts

A Python process with PID 4780 was running as jay.

The process command referenced /usr/bin/wsdd.

Its parent process was gvfsd-wsdd.

The running executable resolved to /usr/bin/python3.14.

The installed package database associated /usr/bin/wsdd with the wsdd package.

The package was installed, and its description documented Windows network-discovery functionality.

UDP port 3702 was present in the observed socket information.

Assessment

The collected evidence was consistent with legitimate WS-Discovery functionality associated with the Ubuntu desktop environment.

No evidence collected during this investigation established malicious activity.

The investigation did not independently verify package integrity, examine every command-line argument, or determine the full contents and destinations of all network traffic.

Conclusion

The initial unfamiliar Python process had a plausible, evidence-supported benign explanation.

The investigation demonstrated the value of correlating network sockets, process identity, parent processes, executable paths, and package metadata before deciding whether activity is suspicious.

6. Skills Practised

Inspecting TCP and UDP sockets.

Identifying processes associated with network activity.

Examining process ownership and parent-child relationships.

Investigating executable paths.

Checking package ownership and installation metadata.

Evaluating evidence without assuming that unfamiliar activity is malicious.

Documenting findings, conclusions, and limitations.

7. Commands Reference

Command

Purpose

sudo ss -tunap

Inspect sockets and associated processes

ps -p 4780 -o pid,ppid,user,lstart,etime,comm,args

Inspect process details

ps -p 4775 -o pid,ppid,user,comm,args

Inspect the parent process

readlink -f /proc/4780/exe

Resolve the running executable path

dpkg -S /usr/bin/wsdd

Check installed package ownership

dpkg -s wsdd

Inspect package status and metadata

Next Steps

Compare process behavior against expected service behavior.

Learn to inspect service configurations and logs.

Practise correlating process activity with relevant network records.

Introduce basic Nmap scanning in an authorized lab environment.
