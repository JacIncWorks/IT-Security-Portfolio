Linux Host Investigation 001 — Processes, Users, and Network Sockets

Lab Overview

Platform: Ubuntu 26.04.1 LTS virtual machine
Environment: VirtualBox — Ubuntu-Cyber-Lab
Lab type: Beginner hands-on host investigation
Status: Completed

Objective

Investigate a Linux host to understand user identity, running processes, parent-child process relationships, and listening network sockets.

The purpose was to establish a basic system baseline and practise interpreting evidence without assuming that unfamiliar activity is automatically malicious.

1. User Identity

Command

whoami

Observation

The command returned:

jay

Analysis

The command identified the user identity under which the command was executed.

Linux uses user identities and permissions to control access to files, processes, and administrative operations.

Although my account owns the VM, that does not mean every command runs with root privileges. Administrative operations may require sudo.

2. Running Processes

Command

ps aux

Observation

The process list included PID 1, running:

/usr/lib/systemd/systemd --switched-root ...

The process ran as root, with approximately 0.3% CPU and 0.4% memory usage at the time observed.

Analysis

systemd is the system and service manager commonly used by Ubuntu. It is normally the first userspace process and has PID 1.

Its role includes managing system startup and services.

The root identity is expected for this process. Low resource usage is consistent with normal operation but is not, by itself, proof that a process is safe.

3. Process Relationships

Commands

ps -p 1 -o pid,ppid,user,comm,args

pstree -p

echo $$

ps -p $$ -o pid,ppid,user,comm

Observations

The investigation identified:

Process

PID

PPID

User

systemd

1

0

root

Bash shell

4151

4113

jay

ptyxis-agent

4113

Not recorded

jay

The parent of the Bash shell was identified as ptyxis-agent.

Analysis

A PID identifies a process, while a PPID identifies its parent process.

Linux reports PPID 0 for PID 1 because it has no ordinary userspace parent. The kernel starts the initial userspace process during boot.

The Bash shell was a separate process running under my user identity. Its parent process was ptyxis-agent, which is associated with the terminal environment.

Tracing parent-child relationships helps investigators understand how a process started and which program or session may have initiated it.

4. Listening Network Sockets

Commands

ss -tulpn

sudo ss -tulpn

Observations

The output included ports 53, 323, 631, and 5353.

A TCP socket associated with the following process was identified:

users:(("cupsd",pid=4262,fd=6))

The local address was:

[::1]:631

The TCP socket was in the LISTEN state, and its peer address was shown as [::]:*.

Analysis

ss displays socket information and can help identify listening services.

The command options used were:

-t: TCP sockets

-u: UDP sockets

-l: listening sockets

-p: process information, when available

-n: numeric addresses and ports

Port 631 is commonly associated with IPP printing, and cupsd is the CUPS printing daemon.

The IPv6 loopback address ::1 indicates that this particular socket was listening on the local machine's loopback interface.

The peer address [::]:* does not mean a remote machine was connected. For a listening TCP socket, it generally indicates that no specific remote endpoint is associated with the socket.

Other ports observed were associated with common services or protocols, but their actual purpose should be verified using the process information and system configuration.

Network Exposure Assessment

A service listening on 127.0.0.1 or ::1 is bound to a loopback interface rather than directly to the VM's external network interface.

A service listening on [REDACTED IP] generally listens on all local IPv4 interfaces. This can increase potential network exposure, depending on routing, firewall rules, and network configuration.

Neither address alone proves that a system has been compromised or that another device has connected.

The CUPS socket observed in this lab was consistent with a local service. This finding applies to the specific socket examined, not necessarily every socket belonging to CUPS.

5. Findings and Conclusion

Findings

Identified the current user using whoami.

Inspected running processes with ps aux.

Identified systemd as PID 1.

Traced the Bash shell to its parent process, ptyxis-agent.

Examined TCP and UDP sockets using ss.

Identified a CUPS printing service listening on the IPv6 loopback address at port 631.

Learned to distinguish a local listening socket from a connection to a remote peer.

Conclusion

The investigation established a basic baseline of user identity, process relationships, and network sockets on my Ubuntu virtual machine.

The observed systemd, Bash, terminal-agent, and CUPS activity was consistent with the expected roles of those components.

No suspicious activity was established by the evidence examined in this lab. However, this was a limited investigation rather than a comprehensive security audit.

6. Skills Practised

Linux command-line navigation and inspection

User identity and privilege awareness

Process identification and parent-child relationships

Process-tree interpretation

TCP and UDP socket inspection

Network listening-address interpretation

Evidence-based analysis and documentation

7. Commands Reference

Command

Purpose

whoami

Identify the current user

ps aux

List running processes

ps -p 1 -o pid,ppid,user,comm,args

Inspect PID 1

pstree -p

Display process relationships

echo $$

Print the current Bash shell's PID

ps -p PID -o pid,ppid,user,comm,args

Inspect a specific process

ss -tulpn

Inspect listening TCP and UDP sockets

sudo ss -tulpn

Inspect sockets with elevated privileges

Next steps: Investigate service ownership, inspect listening sockets more comprehensively, and practice correlating process activity with network connections.
