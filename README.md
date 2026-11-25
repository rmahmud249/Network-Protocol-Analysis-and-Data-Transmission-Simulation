# Computer Networking: Network Design, IP Protocol Analysis & Selective Repeat ARQ Simulation

![Course](https://img.shields.io/badge/Course-Computer%20Networking-blue)
![Focus](https://img.shields.io/badge/Focus-Network%20Design%20%7C%20IP%20Analysis%20%7C%20ARQ-success)
![Protocol](https://img.shields.io/badge/Network%20Layer-IPv4-orange)
![Status](https://img.shields.io/badge/Project-Academic%20Assignment-lightgrey)

A computer networking project covering the design and analysis of a
multi-department network, IPv4 packet inspection with Wireshark,
routing-path analysis with Traceroute, IP fragmentation, and a Selective
Repeat Automatic Repeat reQuest (ARQ) flow-control simulation.

------------------------------------------------------------------------

## Table of Contents

-   [Project Overview](#project-overview)
-   [Project Objectives](#project-objectives)
-   [Project Modules](#project-modules)
    -   [1. Network Design and Address
        Allocation](#1-network-design-and-address-allocation)
    -   [2. Layered Network
        Architecture](#2-layered-network-architecture)
    -   [3. DHCP and DNS](#3-dhcp-and-dns)
    -   [4. IPv4 Packet Analysis with
        Wireshark](#4-ipv4-packet-analysis-with-wireshark)
    -   [5. Routing Behavior Analysis with
        Traceroute](#5-routing-behavior-analysis-with-traceroute)
    -   [6. IP Error Handling and
        Fragmentation](#6-ip-error-handling-and-fragmentation)
    -   [7. Selective Repeat ARQ
        Simulation](#7-selective-repeat-arq-simulation)
    -   [8. Performance Evaluation and Protocol
        Comparison](#8-performance-evaluation-and-protocol-comparison)
-   [Tools and Technologies](#tools-and-technologies)
-   [Repository Structure](#repository-structure)
-   [How to Explore the Project](#how-to-explore-the-project)
-   [Key Learning Outcomes](#key-learning-outcomes)
-   [Possible Future Improvements](#possible-future-improvements)
-   [Academic Information](#academic-information)
-   [Disclaimer](#disclaimer)

------------------------------------------------------------------------

## Project Overview

Computer networks connect devices and enable communication across local
and geographically distributed environments. Understanding how networks
are designed, how IP packets move between hosts, and how reliable data
transfer is achieved is essential for computer science and network
engineering.

This project explores these concepts through practical network design
and protocol analysis. It combines a multi-office network topology and
subnet allocation with packet-level investigation and a flow-control
simulation.

## Project Objectives

-   Design a network that connects multiple departments and remote
    offices.
-   Apply subnetting and allocate IPv4 address ranges to different
    network segments.
-   Understand the roles of routers, switches, servers, and end devices.
-   Relate practical network components to the OSI and TCP/IP layered
    models.
-   Understand how DHCP provides automatic network configuration and how
    DNS resolves domain names.
-   Inspect IPv4 packet fields and interpret packet-capture evidence in
    Wireshark.
-   Analyze packet paths and TTL behavior using Traceroute.
-   Observe IP fragmentation and understand the role of the
    Identification, Flags, and Fragment Offset fields.
-   Explain the sender/receiver window mechanism and selective
    retransmission in Selective Repeat ARQ.
-   Evaluate throughput, delay, retransmissions, and bandwidth
    utilization using simulation results.

## Project Modules

### 1. Network Design and Address Allocation

The network design represents a business environment with multiple
departments, remote offices, routers, switches, client computers, and
dedicated DNS and HTTPS servers.

The topology includes:

-   Sales department
-   Engineering department
-   HR department
-   Remote Office 1
-   Remote Office 2
-   DNS server
-   HTTPS server
-   Routers, switches, and client PCs

The network is divided into subnets so that each department or office
can use a separate address range.

#### IPv4 Address Allocation
  ----------------------------------------------------------------------------------------------
  Department /      Network / Subnet       Usable IP Range                   Subnet Mask
  Office                                                                     
  ----------------- ---------------------- --------------------------------- -------------------
  Sales             `192.168.1.0/27`       `192.168.1.1 – 192.168.1.30`      `255.255.255.224`

  Engineers         `192.168.1.32/27`      `192.168.1.33 – 192.168.1.62`     `255.255.255.224`

  HR                `192.168.1.64/27`      `192.168.1.65 – 192.168.1.94`     `255.255.255.224`

  Remote Office 1   `192.168.1.96/28`      `192.168.1.97 – 192.168.1.110`    `255.255.255.240`

  Remote Office 2   `192.168.1.112/28`     `192.168.1.113 – 192.168.1.126`   `255.255.255.240`

  Servers (as       `209.165.200.18/30`,   Not specified in the report       `255.255.255.252`
  listed in report) `8.8.8.9/30`                                             
  ----------------------------------------------------------------------------------------------

**Subnetting note:** A `/27` subnet contains 32 addresses (30
conventionally usable host addresses), while a `/28` subnet contains 16
addresses (14 conventionally usable host addresses). The server entries
are reproduced from the assignment report; verify the intended
interface-to-subnet mapping and gateway details against the original
network configuration before deployment.

### 2. Layered Network Architecture

  -----------------------------------------------------------------------
  OSI Layer                           Role in the Project
  ----------------------------------- -----------------------------------
  Application                         Network services such as DNS and
                                      HTTPS

  Presentation                        Data representation and
                                      security-related functions,
                                      including encryption

  Session                             Management of communication
                                      sessions between clients and
                                      servers

  Transport                           TCP and UDP communication

  Network                             IP addressing and routing through
                                      routers

  Data Link                           Local frame delivery through
                                      Ethernet and switches

  Physical                            Cables, ports, and physical network
                                      interfaces
  -----------------------------------------------------------------------
  
### 3. DHCP and DNS

**Dynamic Host Configuration Protocol (DHCP)** automatically provides
client devices with IP configuration, reducing the need for manual
address assignment and helping minimize configuration errors.

**Domain Name System (DNS)** translates human-readable domain names into
IP addresses so that clients can locate network services and internet
hosts.

The report discusses these services in the context of simplifying device
connectivity and enabling clients to access servers and other network
resources.

### 4. IPv4 Packet Analysis with Wireshark

  -----------------------------------------------------------------------
  Field                               Purpose
  ----------------------------------- -----------------------------------
  Version                             Identifies the IP version; IPv4
                                      uses the value `4`.

  Header Length (IHL/HLEN)            Indicates the IPv4 header length in
                                      32-bit words.

  Total Length                        Specifies the total packet length,
                                      including header and payload.

  Identification                      Helps associate fragments that
                                      belong to the same original
                                      datagram.

  Flags                               Includes fragmentation control
                                      information such as Don't Fragment
                                      and More Fragments.

  Fragment Offset                     Indicates where a fragment belongs
                                      in the original datagram.

  Time to Live (TTL)                  Limits how many router hops a
                                      packet can traverse.

  Protocol                            Identifies the next-level protocol,
                                      such as TCP, UDP, or ICMP.

  Header Checksum                     Helps detect errors in the IPv4
                                      header.

  Source IP Address                   Identifies the packet sender.

  Destination IP Address              Identifies the intended packet
                                      recipient.
  -----------------------------------------------------------------------

### 5. Routing Behavior Analysis with Traceroute

Traceroute is used to discover the sequence of router hops between a
source and a destination.

It works by sending probes with progressively increasing TTL values.
When a router reduces a packet's TTL to zero, it discards the packet and
may return an ICMP Time Exceeded message. These responses help reveal
the path taken by packets and the delay observed at each hop.

The assignment includes a Traceroute example to Google's public DNS
address, `8.8.8.8`.

### 6. IP Error Handling and Fragmentation

The report examines IPv4 fragmentation using packet-capture evidence and
a large ICMP ping example.

When a packet is larger than the supported Maximum Transmission Unit
(MTU) on a path, fragmentation may occur if fragmentation is permitted.
The destination uses the Identification value, Fragment Offset, and
fragment flags to associate and reassemble fragments.

The captured example described in the report includes:

-   A fragmented IPv4 packet.
-   The More Fragments (MF) flag set on the first fragment.
-   A Fragment Offset of `0` for that first fragment.
-   A shared Identification value of `0x63dc`.
-   Successful reassembly indicated by Wireshark in the following frame.

The report also discusses why losing a fragment can prevent successful
reassembly and why Path MTU Discovery can help avoid fragmentation.

### 7. Selective Repeat ARQ Simulation

Selective Repeat (SR) is a sliding-window flow-control and
error-recovery protocol. It allows multiple frames to be sent before
individual acknowledgments arrive. Unlike Go-Back-N, it retransmits only
the frames identified as lost or damaged.

#### Simulation Parameters

  Parameter          Value
  --------------- --------
  Packet size       `1000`
  Window size          `5`
  Total packets       `10`

#### How Selective Repeat Works

1.  The sender transmits multiple packets within its sending window.
2.  The receiver checks incoming packets and individually acknowledges
    correctly received packets.
3.  Correctly received out-of-order packets can be buffered.
4.  If a packet is lost or corrupted, the sender retransmits that
    specific packet after detecting the loss, for example through a
    timeout.
5.  Once the missing packet arrives, the receiver can deliver buffered
    data in the correct order and the sender's window advances as
    acknowledgments are processed.

#### Simulation Observations

The event log in the report illustrates packet loss, acknowledgments,
timeouts, retransmissions, and window movement.

Examples from the documented run include:

-   Packets `0`, `1`, `2`, and `4` are received while packet `3` is
    lost.
-   The receiver acknowledges the correctly received packets
    individually.
-   The sender later retransmits packet `3` after a timeout.
-   Packet `6` experiences repeated loss and retransmission before
    successful reception.
-   Packets `8` and `9` are also retransmitted after loss.
-   The simulation eventually records acknowledgments through packet
    `9`.

These events demonstrate the central benefit of Selective Repeat:
recovery can focus on missing packets rather than unnecessarily
resending every later packet.

### 8. Performance Evaluation and Protocol Comparison

The report presents graphs for:

-   Throughput over time
-   Latency per packet
-   Error recovery (retransmissions per packet)
-   Bandwidth utilization over time

It also compares Selective Repeat with Stop-and-Wait and Go-Back-N.

  -----------------------------------------------------------------------
  Protocol                General Behavior        Main Trade-off
  ----------------------- ----------------------- -----------------------
  Stop-and-Wait           Sends one frame and     Simple, but can leave
                          waits for its           the network idle while
                          acknowledgment before   waiting.
                          sending the next.       

  Go-Back-N               Uses a sending window;  Better pipelining than
                          a loss can cause        Stop-and-Wait, but
                          multiple subsequent     retransmissions can
                          frames to be            waste bandwidth.
                          retransmitted.          

  Selective Repeat        Uses individual         Efficient recovery, but
                          acknowledgments and     requires more buffering
                          retransmits only        and protocol
                          missing or damaged      complexity.
                          frames.                 
  -----------------------------------------------------------------------

According to the report's analysis, Selective Repeat is especially
useful in high-delay or error-prone environments, where avoiding
unnecessary retransmissions can improve throughput and bandwidth
efficiency. It requires additional receiver buffering and careful
sequence-number/window management.

## Tools and Technologies

The report documents the use of the following tools and networking
concepts:

  -----------------------------------------------------------------------
  Tool / Technology                   Purpose
  ----------------------------------- -----------------------------------
  Cisco Packet Tracer                 Network topology design and device
                                      connectivity planning

  Wireshark                           Packet capture inspection and IPv4
                                      protocol analysis

  Traceroute (`tracert` on Windows)   Routing-path and TTL behavior
                                      analysis

  IPv4 and subnetting                 Logical addressing and network
                                      segmentation

  DHCP                                Automatic client network
                                      configuration

  DNS                                 Domain-name resolution

  TCP, UDP, and ICMP                  Transport and network diagnostic
                                      protocols

  Selective Repeat ARQ                Sliding-window flow control and
                                      selective retransmission
  -----------------------------------------------------------------------

## Repository Structure

A suggested GitHub repository layout is shown below. Adjust the
filenames to match the actual files you upload.

``` text
computer-networking-project/
│
├── README.md
│
├── network-design/
│   ├── network-topology.png
│   └── ip-address-allocation.md
│
├── wireshark-analysis/
│   ├── ipv4-packet-analysis.png
│   └── fragmentation-analysis.png
│
├── routing-analysis/
│   └── traceroute-output.png
│
├── selective-repeat-arq/
│   ├── simulation-output.png
│   ├── throughput.png
│   ├── latency.png
│   ├── retransmissions.png
│   └── bandwidth-utilization.png
│
├── protocol-comparison/
│   └── arq-performance-comparison.png
│
└── report/
    └── computer-networking-assignment.pdf
```

This is a recommended structure, not a claim that every listed file is
already included. Add only the folders and artifacts that exist in your
repository. If you have a Packet Tracer project file or simulation
source code, include it in the relevant folder.

## How to Explore the Project

1.  Review the network topology and address-allocation table to
    understand the network layout.
2.  Inspect the Wireshark screenshots or capture files to examine IPv4
    packet fields.
3.  Review the Traceroute output to understand hop-by-hop routing
    behavior.
4.  Study the fragmentation example to see how IPv4 handles packets that
    exceed an MTU.
5.  Follow the Selective Repeat event log and performance graphs to
    understand acknowledgments, packet loss, retransmissions, and window
    movement.
6.  Read the protocol comparison to understand the trade-offs among
    Stop-and-Wait, Go-Back-N, and Selective Repeat.

The assignment report is the primary reference for the documented
topology, examples, simulation settings, and observations.

## Key Learning Outcomes

By completing this project, a learner can develop practical
understanding of:

-   Designing a segmented network for multiple departments and remote
    offices.
-   Planning IPv4 subnets and host address ranges.
-   Understanding the responsibilities of the OSI layers.
-   Explaining the roles of DHCP and DNS in network communication.
-   Reading and interpreting IPv4 packet headers in Wireshark.
-   Understanding TTL-based routing discovery with Traceroute.
-   Explaining IPv4 fragmentation and destination-side reassembly.
-   Understanding sliding-window transmission and selective
    retransmission.
-   Interpreting throughput, latency, retransmission, and
    bandwidth-utilization graphs.
-   Comparing reliable data-transfer strategies and their trade-offs.

## Possible Future Improvements

The report proposes several improvements to the Selective Repeat
approach:

1.  **Dynamic Window Size Adjustment** --- adapt the window size based
    on changing network conditions, such as packet loss and round-trip
    time.
2.  **Enhanced Acknowledgment Strategies** --- combine cumulative and
    selective acknowledgments where appropriate to reduce control
    overhead.
3.  **Hybrid Error Control** --- investigate combining Forward Error
    Correction (FEC) with Selective Repeat to recover from some errors
    without retransmission.

Additional possible extensions include:

-   Add the original Packet Tracer project file and document
    device/interface configurations.
-   Include Wireshark `.pcap` or `.pcapng` captures where available.
-   Provide reproducible source code for the ARQ simulation, if source
    code is part of the project.
-   Document the simulation assumptions, random-loss model, timeout
    policy, and metric formulas.
-   Add setup instructions and test cases so another learner can
    reproduce the results.

## Disclaimer

This repository documents an academic course assignment and its learning
outcomes. Network addresses, topology details, packet captures, and
simulation observations are presented for educational purposes. The
server address entries and other configuration details should be checked
against the actual Packet Tracer configuration before being reused in a
live network.

------------------------------------------------------------------------

