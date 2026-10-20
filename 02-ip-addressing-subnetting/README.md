# IP Addressing and Subnetting

## Overview

This document covers the fundamentals of IP addressing and subnetting. These concepts help explain how devices are identified, how networks are organized, and how IP addresses are divided into smaller networks.

This documentation focuses on IPv4 and basic subnetting calculations.

## 1. What Is an IP Address?

An IP (Internet Protocol) address is a logical address assigned to a network interface for communication over an IP network.

It helps identify the source and destination of IP packets.

### Example

```text
192.168.1.10
```

An IPv4 address contains **32 bits**, divided into four groups called octets. Each octet contains 8 bits and has a decimal value from 0 to 255.

| Octet | Decimal Value | Binary Value |
|---|---:|---|
| 1 | 192 | 11000000 |
| 2 | 168 | 10101000 |
| 3 | 1 | 00000001 |
| 4 | 10 | 00001010 |

IPv4 addresses are commonly written in dotted-decimal format.

## 2. Public and Private IP Addresses

IPv4 addresses can be used in different contexts.

- **Private IP addresses** are used within private networks and are not directly routable across the public internet.
- **Public IP addresses** are globally routable addresses, subject to routing and filtering policies.

### Private IPv4 Ranges

| Range | CIDR | Common Use |
|---|---|---|
| 10.0.0.0 – 10.255.255.255 | `10.0.0.0/8` | Large private networks |
| 172.16.0.0 – 172.31.255.255 | `172.16.0.0/12` | Private organizational networks |
| 192.168.0.0 – 192.168.255.255 | `192.168.0.0/16` | Home and small office networks |

Private IP addresses commonly use Network Address Translation (NAT) when devices need access to the public internet.

## 3. Network Portion and Host Portion

An IP address and its subnet mask work together to identify the network portion and the host portion of an address.

- **Network portion:** identifies the IP network.
- **Host portion:** identifies an interface within that network.

The subnet mask determines how many bits belong to the network prefix.

### Example

```text
IP Address:  192.168.1.10
Subnet Mask: 255.255.255.0
CIDR:        /24
```

In this example:

- Network address: `192.168.1.0`
- Usable host range: `192.168.1.1` – `192.168.1.254`
- Broadcast address: `192.168.1.255`

This example assumes a conventional IPv4 `/24` subnet.

## 4. What Is a Subnet Mask?

A subnet mask indicates which bits of an IPv4 address belong to the network prefix and which bits are available for host addressing.

### Common Subnet Masks

| CIDR Prefix | Subnet Mask | Host Bits | Usable Hosts per Subnet* |
|---|---|---:|---:|
| `/8` | `255.0.0.0` | 24 | 16,777,214 |
| `/16` | `255.255.0.0` | 16 | 65,534 |
| `/24` | `255.255.255.0` | 8 | 254 |
| `/25` | `255.255.255.128` | 7 | 126 |
| `/26` | `255.255.255.192` | 6 | 62 |
| `/27` | `255.255.255.224` | 5 | 30 |
| `/28` | `255.255.255.240` | 4 | 14 |
| `/30` | `255.255.255.252` | 2 | 2 |

\*For conventional IPv4 subnets, the usable-host calculation generally excludes the network and broadcast addresses. Special cases, such as `/31` point-to-point links, follow different rules.

## 5. What Is CIDR?

CIDR stands for Classless Inter-Domain Routing.

CIDR notation represents the network prefix by adding a slash and a number after the IP address.

For example:

```text
192.168.1.10/24
```

The `/24` means that the first 24 bits represent the network prefix. The remaining 8 bits are available for host addressing.

CIDR allows networks to be allocated in different sizes rather than relying only on traditional Class A, B, and C boundaries.

## 6. What Is Subnetting?

Subnetting is the process of dividing a larger IP network into smaller networks called subnets.

Subnetting can help organizations:

- Organize devices into separate network segments.
- Use IP address space more efficiently.
- Reduce unnecessary broadcast traffic.
- Apply network policies between different segments.
- Make network management and troubleshooting easier.

For example, an organization might place employee computers, guest Wi-Fi devices, and servers in separate subnets.

Subnetting alone does not provide security isolation. Appropriate routing rules, access control lists, or firewall policies may also be required.

## 7. Basic Subnetting Calculations

For a conventional IPv4 subnet:

```text
Total Addresses = 2^(Number of Host Bits)

Usable Hosts = 2^(Number of Host Bits) - 2
```

The subtraction of two accounts for the network address and broadcast address.

### Example: A /26 Subnet

Given:

```text
Network:      192.168.1.0/26
Subnet Mask:  255.255.255.192
```

Step 1: Calculate the host bits.

```text
32 - 26 = 6 host bits
```

Step 2: Calculate the total number of addresses.

```text
2^6 = 64 addresses
```

Step 3: Calculate the conventional usable host count.

```text
64 - 2 = 62 usable host addresses
```

Step 4: Identify the address range.

| Item | Address |
|---|---|
| Network address | `192.168.1.0` |
| First usable host | `192.168.1.1` |
| Last usable host | `192.168.1.62` |
| Broadcast address | `192.168.1.63` |

Therefore, `192.168.1.0/26` contains 64 total addresses, with 62 conventionally usable for hosts.

## 8. Understanding Subnet Block Sizes

A `/24` network can be divided into four `/26` subnets.

Each `/26` subnet contains 64 addresses. The block size is 64 in the final octet.

| Subnet | Network Address | Usable Host Range | Broadcast |
|---|---|---|---|
| 1 | `192.168.1.0/26` | `.1 – .62` | `.63` |
| 2 | `192.168.1.64/26` | `.65 – .126` | `.127` |
| 3 | `192.168.1.128/26` | `.129 – .190` | `.191` |
| 4 | `192.168.1.192/26` | `.193 – .254` | `.255` |

The network addresses increase by 64 because each subnet contains 64 total addresses.

## 9. Why IP Addressing Matters in Cybersecurity

Understanding IP addressing and subnetting provides a foundation for:

- Identifying hosts within a network.
- Understanding network boundaries.
- Reading network scan results in authorized labs.
- Understanding which hosts belong to the same subnet.
- Learning network segmentation and access control.
- Troubleshooting basic connectivity problems.

These skills will support my future learning in network security and ethical hacking.

## 10. Practice Exercises

Try answering these questions before checking your results with a subnet calculator or networking reference.

### Exercise 1: Identify the Address Type

Is `192.168.10.25` a private or public IPv4 address?

### Exercise 2: Understand the Prefix

How many host bits are available in a `/27` subnet?

### Exercise 3: Calculate Usable Hosts

How many conventionally usable host addresses are available in a `/28` subnet?

### Exercise 4: Find the Network Range

Given:

```text
IP Address: 192.168.1.70/26
```

Determine:

- The network address.
- The first usable host.
- The last usable host.
- The broadcast address.

### Exercise 5: Understand Network Segmentation

Why might an organization place guest Wi-Fi devices and internal servers in separate subnets?

## Learning Outcome

After studying this topic, I aim to understand the structure of IPv4 addresses, the purpose of subnet masks and CIDR notation, and the basic calculations used to divide networks into smaller subnets.

My next step is to practise subnet calculations and explore how network protocols and ports support communication between devices.

## References

- [Cisco — Configure IP Addresses and Unique Subnets for New Users](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)
- [Cisco Networking Academy — Network Addressing and Basic Troubleshooting](https://www.netacad.com/courses/network-addressing-and-basic-troubleshooting)

---

*Learning networking one concept and one calculation at a time.*
