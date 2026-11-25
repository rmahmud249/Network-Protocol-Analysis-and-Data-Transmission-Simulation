# 🌐 Network Design, IP Protocol Analysis & Selective Repeat ARQ Simulation
A Computer Networking course project that covers **(1)** designing a multi-site network with subnetting, **(2)** analyzing IPv4 packets, routing behavior and fragmentation with Wireshark, and **(3)** simulating and benchmarking the **Selective Repeat** flow-control algorithm against **Stop-and-Wait** and **Go-Back-N**.

---

## 📑 Table of Contents

1. [Project Information](#-project-information)
2. [Project Overview](#-project-overview)
3. [Part 1 – Network Design and Analysis](#-part-1--network-design-and-analysis)
4. [Part 2 – IP Protocol Analysis Using Wireshark](#-part-2--ip-protocol-analysis-using-wireshark)
5. [Part 3 – Selective Repeat Flow Control Algorithm](#-part-3--selective-repeat-flow-control-algorithm)
6. [Proposed Improvements](#-proposed-improvements)
7. [Tools & Technologies](#-tools--technologies)
8. [Repository Structure](#-repository-structure)
9. [How to Run / Reproduce](#-how-to-run--reproduce)
10. [Key Learning Outcomes](#-key-learning-outcomes)
11. [Author](#-author)

---<img width="769" height="457" alt="Network Design" src="https://github.com/user-attachments/assets/0454df7e-be7a-4e28-9f96-bcc8d2efdab7" />


## 🔍 Project Overview

| Part | Topic | Tool |
|---|---|---|
| 1 | Network topology design, IP addressing & layered architecture | Cisco Packet Tracer |
| 2 | IPv4 header, TTL/traceroute, and fragmentation analysis | Wireshark, `tracert`, `ping` |
| 3 | Selective Repeat ARQ simulation and performance comparison | MATLAB |

---

## 🏢 Part 1 – Network Design and Analysis

### Topology

The network connects three internal departments (**Sales, Engineers, HR**), two **remote offices**, and external **DNS** and **HTTPS** servers through four routers (R0–R3) and multiple Cisco 2960-24TT switches.

<img width="769" height="457" alt="Network Design" src="https://github.com/user-attachments/assets/808c300c-004a-48ce-ab11-417fd42e89a3" />

### Address Allocation

| Department / Office | Subnet | Usable IP Range | Subnet Mask |
|---|---|---|---|
| Sales | `192.168.1.0/27` | 192.168.1.1 – 192.168.1.30 | 255.255.255.224 |
| Engineers | `192.168.1.32/27` | 192.168.1.33 – 192.168.1.62 | 255.255.255.224 |
| HR | `192.168.1.64/27` | 192.168.1.65 – 192.168.1.94 | 255.255.255.224 |
| Remote Office 1 | `192.168.1.96/28` | 192.168.1.97 – 192.168.1.110 | 255.255.255.240 |
| Remote Office 2 | `192.168.1.112/28` | 192.168.1.113 – 192.168.1.126 | 255.255.255.240 |
| Servers (DNS, HTTPS) | `209.165.200.18/30`, `8.8.8.9/30` | – | 255.255.255.252 |

> VLSM-style subnetting is used: `/27` subnets (30 hosts) for larger departments and `/28` subnets (14 hosts) for the remote offices.

### Layered Architecture (OSI / TCP-IP)

| Layer | Role in This Network |
|---|---|
| **Physical** | Ethernet cables, router interfaces (e.g., Gig0/1, Fa0/3) |
| **Data Link** | All Cisco 2960-24TT switches |
| **Network** | Routers R0, R1, R2, R3 |
| **Transport** | TCP and UDP |
| **Session** | Manages sessions between clients (PC0–PC10) and servers (DNS, HTTPS) |
| **Presentation** | Data translation, encryption and compression (HTTPS encryption) |
| **Application** | HTTPS and DNS servers |

### Core Protocols & Justification

- **TCP/IP** – The core protocol suite that lets every device send and receive data reliably.
- **TCP (Transport Layer)** – Reliable, error-checked delivery between clients and servers.
- **DHCP** – Automatically assigns IP addresses and network settings, which removes manual configuration errors, supports dynamic/temporary devices, and improves scalability.
- **DNS** – Translates human-readable names (e.g., `www.google.com`) to IP addresses, enabling user-friendly access and internal/external name resolution.

---

## 📡 Part 2 – IP Protocol Analysis Using Wireshark

### 2.1 IPv4 Header Analysis

A captured TCP packet (Frame 57840, `192.168.0.106 → 178.128.135.159`, destination port 443) was analyzed field by field.

<img width="638" height="293" alt="Screenshot_1" src="https://github.com/user-attachments/assets/ad77693f-0e0f-46c1-84b0-ce8d1793daa6" />

| Field | Size | Description |
|---|---|---|
| **Version** | 4 bits | IP version; always `4` for IPv4. |
| **Header Length (HLEN)** | 4 bits | Header size in 4-byte words (value × 4 = bytes). Observed: `5` → 20 bytes. |
| **Total Length** | 16 bits | Size of header + data in bytes (max 65,535). Observed: `52`. |
| **Identification** | 16 bits | Identifies fragments belonging to the same datagram. Observed: `0x8e0e (36366)`. |
| **Flags / Fragment Offset** | 3 / 13 bits | Control fragmentation. Observed: Don't Fragment set, offset `0`. |
| **Time-to-Live (TTL)** | 8 bits | Max hops before discard; decremented by each router. Observed: `128`. |
| **Protocol** | 8 bits | Upper-layer protocol (TCP = 6, UDP = 17, ICMP = 1). Observed: TCP (6). |
| **Header Checksum** | 16 bits | 1's complement checksum of the header, recalculated at each router. |
| **Source Address** | 32 bits | Sender's IP: `192.168.0.106`. |
| **Destination Address** | 32 bits | Receiver's IP: `178.128.135.159`. |

### 2.2 Routing Behavior Analysis (Traceroute)

`tracert 8.8.8.8` was used to map the path to Google DNS (`dns.google`). It completed in **12 hops**.

<img width="596" height="241" alt="Screenshot_2" src="https://github.com/user-attachments/assets/c6983e61-6c72-4fe6-96b9-6674713b14e1" />

**How it works:** Traceroute sends packets with TTL starting at 1 and increasing by 1. Each router decrements TTL; when it reaches 0, the router drops the packet and returns an **ICMP "Time Exceeded"** message, revealing its identity. Latency is low in the local network (<1 ms) and grows as packets travel farther (≈28–32 ms near the destination).

### 2.3 Error and Fragmentation Analysis

A large ping (`ping 192.168.0.1 -l 2000`) forced IP fragmentation.

<img width="664" height="334" alt="Screenshot_3" src="https://github.com/user-attachments/assets/35246e55-f550-4847-a222-a58b0ae9340c" />

**Findings (Frame 168):**
- Total Length `1500`, **More Fragments (MF) flag set**, Fragment Offset `0` → this is the **first fragment**.
- All fragments share the same **Identification** value (`0x36dc`), allowing reassembly.
- Wireshark reports successful **reassembly in Frame 169**.
- Fragmentation occurs when a packet exceeds the link **MTU**; reassembly happens at the destination.

### 2.4 Conclusions

- **TTL** prevents routing loops and enables traceroute.
- **Header checksum** detects header corruption; corrupted packets are discarded (IP itself does not retransmit — TCP handles recovery).
- **Fragmentation** adapts packets to MTU limits but reduces reliability, since losing one fragment loses the whole datagram. **Path MTU Discovery** helps avoid it.

---

## 🔁 Part 3 – Selective Repeat Flow Control Algorithm

### Principles

Selective Repeat (SR) is a **sliding-window ARQ protocol**. The sender transmits multiple packets without waiting for individual ACKs, and **only lost or corrupted packets are retransmitted**. The receiver buffers out-of-order packets and delivers them in order.

**Key features**
- Window-based transmission on both sender and receiver
- Individual acknowledgment of every correctly received packet
- Selective retransmission of only erroneous/lost packets
- Receiver-side buffering for out-of-order packets

### Simulation Parameters

| Parameter | Value |
|---|---|
| Packet size | 1000 |
| Window size | 5 |
| Total packets | 10 |

### Simulation Highlights

- Packets 0–4 are sent at time 0; **packet 3 is lost** and recovered by timeout-based retransmission.
- As ACKs arrive, the window slides and packets 5–7 are sent.
- **Packet 6** is lost multiple times and repeatedly retransmitted; packets 8 and 9 are also lost once before recovery.
- By **time 60**, all packets (0–9) are received and acknowledged.


### Protocol Comparison

| Metric | Selective Repeat | Stop-and-Wait | Go-Back-N |
|---|---|---|---|
| Throughput (packets/s) | **4.549** | 1.225 | 4.448 |
| Average Delay (s) | 0.416 | 0.416 | 0.416 |
| Total Retransmissions | **11** | 20 | 20 |
| Bandwidth Utilization | **0.036** | 0.010 | 0.036 |

**Takeaway:** Selective Repeat delivers the highest throughput with the fewest retransmissions, because it resends only the lost frames. Stop-and-Wait wastes time idling for each ACK, while Go-Back-N wastes bandwidth resending whole blocks of frames.

### Analysis

| Aspect | Summary |
|---|---|
| **Impact of window size** | SR benefits most from larger windows, but window size must be chosen carefully to avoid sequence-number overlap. |
| **Best conditions** | High-bandwidth, high-delay, error-prone links with a large bandwidth-delay product. |
| **Strengths** | High throughput, efficient bandwidth use, fast error recovery (ideal for satellite, wireless and WANs). |
| **Weaknesses** | Needs larger buffers and more complex synchronization; less suitable for simple, low-resource networks. |

**Practical applications:** satellite communication, wireless networks, high-speed WANs.

---

## 🚀 Proposed Improvements

1. **Dynamic window size adjustment** – Adapt the window to real-time packet loss and RTT to balance buffer usage and throughput.
2. **Enhanced acknowledgment strategies** – Combine cumulative and selective ACKs to cut control overhead and latency.
3. **Hybrid error control** – Add **Forward Error Correction (FEC)** to Selective Repeat so small errors are corrected without retransmission.

---

## 🛠 Tools & Technologies

| Tool | Purpose |
|---|---|
| **Cisco Packet Tracer** | Network topology design and simulation |
| **Wireshark** | Packet capture and IPv4 header analysis |
| **Windows CMD (`tracert`, `ping`)** | Routing and fragmentation experiments |
| **MATLAB** | Selective Repeat / Stop-and-Wait / Go-Back-N simulation and plots |

---

## 📂 Repository Structure

```
.
├── README.md
├── docs/
│   └── RM-2023000000046.pdf        # Full assignment report
├── images/
│   ├── topology.png
│   ├── wireshark-ip-header.png
│   ├── traceroute.png
│   ├── fragmentation.png
│   ├── sr-simulation.png
│   ├── throughput.png
│   ├── latency.png
│   ├── retransmissions.png
│   ├── bandwidth.png
│   └── arq-comparison.png
├── packet-tracer/
│   └── network-topology.pkt        # Packet Tracer project file
├── wireshark/
│   └── capture.pcapng              # (optional) packet captures
└── matlab/
    └── selective_repeat.m          # Simulation scripts
```

> Adjust the folders above to match the files you actually upload.

---

## ▶️ How to Run / Reproduce

**Network design**
1. Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer).
2. Open `packet-tracer/network-topology.pkt` and run the simulation.

**Wireshark analysis**
1. Install [Wireshark](https://www.wireshark.org/).
2. Open the capture file, or generate traffic with:
   ```bash
   tracert 8.8.8.8
   ping 192.168.0.1 -l 2000
   ```

**ARQ simulation**
1. Open MATLAB.
2. Run the script from the `matlab/` folder:
   ```matlab
   selective_repeat
   ```

---

## 🎯 Key Learning Outcomes

- Designing and subnetting a multi-site network with VLSM
- Mapping network functions to OSI and TCP/IP layers
- Reading and interpreting IPv4 headers in Wireshark
- Understanding TTL, traceroute, checksums and fragmentation
- Implementing and evaluating sliding-window ARQ protocols
- Comparing protocol performance with throughput, latency, retransmissions and bandwidth utilization

---

## 👤 Author

**Md. Rasel Mahmud**
Department of Computer Science and Engineering (CSE)
Southeast University
---

