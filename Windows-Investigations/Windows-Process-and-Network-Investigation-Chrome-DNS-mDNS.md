# Process and Network Investigation

## Objective

Investigate how a running application relates to operating-system processes and network activity, then use Wireshark to observe the network traffic generated on the system.

## Tools Used

- Windows Task Manager
- Windows Command Prompt
- `netstat`
- Wireshark

---

## Part 1 — Process Investigation

I examined running processes in Windows Task Manager and selected a `chrome.exe` process for investigation.

The executable was located in the Chrome installation directory under:

`C:\Program Files\Google\Chrome\`

The executable was identified as Google Chrome and had a Google LLC digital signature.

### Observations

Task Manager showed many separate `chrome.exe` processes running simultaneously.

Each process had its own Process ID (PID).

This demonstrated that:

- One application can use multiple processes.
- Each process receives its own PID.
- A process does not necessarily represent one browser tab or one visible window.
- The operating system manages these processes and their use of system resources.

---

## Part 2 — Network Investigation with `netstat`

I used the following command:

`netstat -ano`

The command displayed network-related information including:

- Protocol
- Local address
- Local port
- Remote address
- Remote port
- PID

I then investigated UDP entries and compared their PIDs with the processes shown in Task Manager.

One of the investigated PIDs was associated with `chrome.exe`.

### Important Observation

Some UDP entries used port `5353`.

At first, the repeated port numbers and addresses were confusing, particularly entries using:

`[REDACTED IP]`

and:

`[::]`

These represent wildcard/listening addresses rather than ordinary remote destinations.

---

## Part 3 — DNS Analysis with Wireshark

I used Wireshark to capture and inspect DNS traffic.

I generated a DNS lookup for:

`example.com`

The capture showed a DNS query followed by a response.

The response contained two IPv4 addresses:

- `[REDACTED IP]`
- `[REDACTED IP]`

I examined the packet details, including the source, destination, DNS query name, query type, and response information.

### Understanding the DNS Exchange

The DNS lookup allows the computer to determine an IP address associated with a domain name.

This allows subsequent network communication to use the resolved IP address rather than relying on the human-readable domain name alone.

---

## Part 4 — mDNS Investigation

While investigating UDP traffic, I noticed repeated traffic using port:

`5353`

I created a Wireshark display filter:

`udp.port == 5353`

This revealed multicast DNS (mDNS) traffic.

One observed packet contained:

- Source: `[REDACTED IP]`
- Destination: `[REDACTED IP]`
- Source port: `5353`
- Destination port: `5353`

The packet contained `.local` names and service-discovery information, including:

`_googlecast._tcp.local`

### What This Demonstrated

mDNS is used for name and service discovery on a local network.

Unlike a typical DNS lookup for a public domain, mDNS uses multicast communication so devices on the local network can participate in the discovery process.

The IPv4 multicast address observed was:

`[REDACTED IP]`

The IPv6 mDNS multicast address is:

`ff02::fb`

---

## Key Takeaways

- Applications can create multiple processes.
- Processes can be identified using PIDs.
- `netstat -ano` can connect network activity to a process ID.
- DNS resolves domain names to IP addresses.
- Wireshark allows DNS communication to be inspected at the packet level.
- UDP port `53` is commonly associated with traditional DNS.
- UDP port `5353` is associated with multicast DNS (mDNS).
- mDNS uses multicast communication for local network name and service discovery.
- `.local` names can be associated with local network services.

---

## What I Learned

This session helped connect concepts that I had previously studied separately.

The investigation followed this general relationship:

Application  
↓  
Process  
↓  
Network activity  
↓  
Protocol / Port  
↓  
Packet  
↓  
Network destination

Rather than only learning what DNS, processes, ports, and packets are individually, I was able to observe how they interact on an actual Windows system.

## Evidence

Relevant screenshots and Wireshark captures were saved locally for reference.

## Skills Practiced

- Windows Task Manager investigation
- Process identification
- PID analysis
- `netstat` usage
- Network endpoint analysis
- Wireshark packet capture
- Wireshark display filters
- DNS analysis
- UDP analysis
- mDNS identification
- Multicast traffic analysis
