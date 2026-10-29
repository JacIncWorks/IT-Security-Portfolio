# Process-to-Network Investigation: Adobe Premiere Pro

## Objective

Investigate a running Windows application by following its activity from system resource usage to its process ID, network socket, and actual network traffic.

The goal was to practice correlating Windows process information with network activity using Task Manager, Command Prompt, `netstat`, and Wireshark.

---

## Tools Used

- Windows Task Manager
- Windows Command Prompt
- `netstat`
- Wireshark

---

## 1. Initial Task Manager Observation

I opened Adobe Premiere Pro 2021 along with several other applications and observed the system through Task Manager.

### Overall system usage

- CPU: 4%
- Memory: 64%
- Disk: 0%
- Network: 0%

### Adobe Premiere Pro 2021

- CPU: 0%
- Memory: approximately 3.1 GB
- Disk: 0 MB/s
- Network: 0 Mbps

### Initial Assessment

Adobe Premiere Pro was using significantly more RAM than most of the other visible applications.

However, high memory usage alone was not considered suspicious because video-editing software can legitimately use substantial amounts of RAM to maintain project data, media information, cached data, effects, and other resources needed for editing and playback.

The process was essentially idle at the moment despite maintaining a relatively large memory allocation.

---

## 2. Verify the Executable Location

I used Task Manager to locate the executable associated with Adobe Premiere Pro.

The executable was located within:

```text
C:\Program Files\Adobe\Adobe Premiere Pro 2021\
Assessment

The executable was located inside the expected Adobe Premiere Pro installation directory.

This provided evidence that the process was associated with the installed Adobe application rather than an obviously unusual location such as a temporary or random user directory.

3. Verify the Digital Signature

I opened the executable's properties and inspected the Digital Signatures tab.

The file showed:

Signer: Adobe Inc.
Digest Algorithm: SHA-256
Assessment

The digital signature provided additional evidence that the executable was associated with Adobe.

A digital signature is useful for verifying the publisher and file integrity, but a signature by itself should not be treated as absolute proof that software is safe.

4. Identify the Process ID

The Adobe Premiere Pro process was located in Task Manager's Details tab.

The process was associated with:

PID: 11524

The PID was then used to correlate the Windows process with network activity.

5. Investigate Network Connections with Netstat

The following command was executed:

netstat -ano | findstr 11524

The results showed several TCP connections using the loopback address:

127.0.0.1 → 127.0.0.1

These connections represent communication occurring within the local computer rather than communication with an external Internet host.

A UDP socket was also observed:

[REDACTED IP]:9763

The UDP entry did not show a specific remote endpoint.

Assessment

The netstat results did not show an external TCP connection associated with the Premiere process at the time of observation.

The UDP socket on port 9763 was investigated further using Wireshark.

6. Capture Network Traffic with Wireshark

I opened Wireshark and filtered traffic using:

udp.port == 9763

Multiple packets were captured.

The packets showed:

Source:         [LOCAL-IP]
Destination:    [LOCAL-BROADCAST]
Protocol:       UDP
Source Port:    9763
Destination Port: 9763

The observed destination was the local network broadcast address.

Assessment

The traffic was being sent to the local network broadcast address rather than directly to an external Internet host.

This was different from an unexplained connection to an unfamiliar public IP address.

7. Examine the UDP Packet

One captured packet showed:

Source Port:      9763
Destination Port: 9763
UDP Length:       48 bytes

Wireshark displayed:

UDP Payload: 40 bytes

The UDP header is 8 bytes:

48-byte UDP datagram
- 8-byte UDP header
= 40-byte payload

The payload appeared as hexadecimal data rather than immediately readable text.

Assessment

The hexadecimal payload alone was not considered suspicious.

Network applications can transmit binary or encoded information that is not directly readable as normal text.

The exact meaning of the payload could not be established from this observation alone.

Findings

The investigation produced the following evidence:

Category	Observation
Application	Adobe Premiere Pro 2021
CPU Usage	~0%
Memory Usage	~3.1 GB
Executable Location	C:\Program Files\Adobe\Adobe Premiere Pro 2021\
Digital Signer	Adobe Inc.
Process ID	11524
Network Socket	UDP port 9763
TCP Activity	Localhost/loopback connections observed
UDP Destination	Local network broadcast address
UDP Payload	40 bytes
Conclusion

The investigation found no evidence that the Adobe Premiere Pro process was malicious.

The relatively high memory usage had a reasonable explanation given the application's purpose as video-editing software.

The executable was located within the expected Adobe installation directory and contained a digital signature associated with Adobe Inc.

The process also had a UDP socket on port 9763. Wireshark confirmed that traffic using this port was being sent to the local network broadcast address rather than an unfamiliar external Internet address.

The investigation therefore provided multiple pieces of evidence consistent with legitimate application behavior.

This investigation did not attempt to prove that Adobe Premiere Pro is universally safe. Instead, it demonstrated how multiple observations can be correlated to determine whether a specific process's behavior appears reasonable in context.

Investigation Lessons
1. High resource usage does not automatically indicate malware

CPU, RAM, disk, or network usage must be interpreted in context.

For example, video-editing software can legitimately consume several gigabytes of RAM.

2. Process names alone are not enough

A process should be investigated using additional information such as:

Executable location
Publisher
Digital signature
PID
Network activity
Expected application behavior
3. A network socket does not automatically mean Internet communication

A process can have a network socket without communicating with an external Internet server.

The actual source and destination addresses must be examined.

4. Local broadcast traffic is different from external traffic

Traffic sent to the local broadcast address is intended for devices on the local network.

It should not automatically be interpreted as Internet communication.

5. Packet payloads are not always human-readable

Applications may transmit binary or encoded data.

Hexadecimal-looking data does not automatically indicate malicious activity.

6. Suspicious does not mean malicious

A good investigation separates observations from conclusions.

The general process used was:

Observation
    ↓
Gather Evidence
    ↓
Identify the Process
    ↓
Correlate Network Activity
    ↓
Establish Context
    ↓
Determine Whether Behavior Is Expected
Evidence

A raw Wireshark capture from this investigation was saved locally as:

Verify the Adobe App.pcapng

The capture contains the UDP/9763 traffic examined during the investigation.

The raw capture was retained locally rather than uploaded publicly because it contains information about the local network environment.
