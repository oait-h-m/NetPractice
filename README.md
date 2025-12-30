# NetPractice - 42 Network Project

A practical introduction to networking concepts through hands-on configuration exercises.

## 📚 Table of Contents

- [Overview](#overview)
- [Key Concepts](#key-concepts)
- [IP Addressing](#ip-addressing)
- [Subnet Masks](#subnet-masks)
- [Subnetting Guide](#subnetting-guide)
- [Routing](#routing)
- [Common Issues](#common-issues)
- [Tips & Tricks](#tips--tricks)

---

## 🎯 Overview

NetPractice is a 42 project that teaches fundamental networking concepts through interactive exercises. You'll configure networks by setting IP addresses, subnet masks, and routing tables to ensure proper communication between devices.

## 🔑 Key Concepts

### What is an IP Address?

An IP address is a unique identifier for devices on a network, consisting of 4 octets (32 bits total).

**Format:** `XXX.XXX.XXX.XXX` (each XXX is 0-255)

**Example:** `192.168.1.10`

### Network Classes

| Class | First Octet Range | Default Mask | CIDR | Example |
|-------|------------------|--------------|------|---------|
| A | 1 - 126 | 255.0.0.0 | /8 | 10.0.0.0 |
| B | 128 - 191 | 255.255.0.0 | /16 | 172.16.0.0 |
| C | 192 - 223 | 255.255.255.0 | /24 | 192.168.1.0 |

**Note:** 127.x.x.x is reserved for loopback (localhost)

### Special IP Addresses

- **Network Address**: First IP in a subnet (all host bits = 0)
- **Broadcast Address**: Last IP in a subnet (all host bits = 1)
- **Usable IPs**: Everything between network and broadcast addresses

---

## 📡 IP Addressing

### Private IP Ranges (RFC 1918)

These are reserved for internal networks:

- **Class A**: 10.0.0.0 - 10.255.255.255
- **Class B**: 172.16.0.0 - 172.31.255.255
- **Class C**: 192.168.0.0 - 192.168.255.255

### Calculating Usable IPs

For IP **192.168.1.10/24**:

1. **Network Address**: 192.168.1.0
2. **Broadcast Address**: 192.168.1.255
3. **First Usable**: 192.168.1.1
4. **Last Usable**: 192.168.1.254
5. **Total Usable Hosts**: 254

**Formula:** 2^(host bits) - 2 = usable hosts

---

## 🎭 Subnet Masks

### What is a Subnet Mask?

A subnet mask divides an IP address into:
- **Network portion** (identifies the subnet)
- **Host portion** (identifies the device)

### Common Subnet Masks

| CIDR | Subnet Mask | Hosts | Notation |
|------|-------------|-------|----------|
| /24 | 255.255.255.0 | 254 | Class C default |
| /25 | 255.255.255.128 | 126 | Half of /24 |
| /26 | 255.255.255.192 | 62 | Quarter of /24 |
| /27 | 255.255.255.224 | 30 | Eighth of /24 |
| /28 | 255.255.255.240 | 14 | Sixteenth of /24 |
| /30 | 255.255.255.252 | 2 | Point-to-point links |

### CIDR Notation

**/24** means the first 24 bits are the network portion.

**Example:** 192.168.1.0/24
- Network bits: 24
- Host bits: 32 - 24 = 8
- Possible hosts: 2^8 - 2 = 254

---

## 🔧 Subnetting Guide

### How to Subnet a Network

**Goal:** Divide 192.168.1.0/24 into 4 subnets

**Step 1:** Calculate bits needed
- 2^n ≥ number of subnets
- 2^2 = 4 ✓
- Need to borrow 2 bits

**Step 2:** New prefix length
- /24 + 2 = /26

**Step 3:** Calculate subnet size
- Host bits left: 32 - 26 = 6
- Hosts per subnet: 2^6 - 2 = 62
- Subnet size: 2^6 = 64

**Step 4:** List the subnets

| Subnet | Network Address | First Usable | Last Usable | Broadcast |
|--------|----------------|--------------|-------------|-----------|
| 1 | 192.168.1.0 | 192.168.1.1 | 192.168.1.62 | 192.168.1.63 |
| 2 | 192.168.1.64 | 192.168.1.65 | 192.168.1.126 | 192.168.1.127 |
| 3 | 192.168.1.128 | 192.168.1.129 | 192.168.1.190 | 192.168.1.191 |
| 4 | 192.168.1.192 | 192.168.1.193 | 192.168.1.254 | 192.168.1.255 |

### Quick Subnet Cheat Sheet

```
/30 = 255.255.255.252 = 2 hosts (point-to-point)
/29 = 255.255.255.248 = 6 hosts
/28 = 255.255.255.240 = 14 hosts
/27 = 255.255.255.224 = 30 hosts
/26 = 255.255.255.192 = 62 hosts
/25 = 255.255.255.128 = 126 hosts
/24 = 255.255.255.0 = 254 hosts
```

---

## 🛣️ Routing

### Routing Table Basics

A routing table tells a device where to send packets.

**Components:**
- **Destination**: Target network
- **Next Hop**: Where to send the packet
- **Interface**: Which network interface to use

### Default Route

**0.0.0.0/0** or **default** = Send all unknown traffic here (usually to a gateway/router)

### Example Routing Table

| Destination | Mask | Next Hop | Interface |
|-------------|------|----------|-----------|
| 192.168.1.0 | /24 | 0.0.0.0 | eth0 |
| 10.0.0.0 | /8 | 192.168.1.1 | eth0 |
| 0.0.0.0 | /0 | 192.168.1.254 | eth0 |

**Interpretation:**
- Traffic to 192.168.1.x: Direct delivery via eth0
- Traffic to 10.x.x.x: Send to 192.168.1.1
- Everything else: Send to default gateway 192.168.1.254

---

## ⚠️ Common Issues

### 1. Devices Can't Communicate

**Check:**
- Are they on the same subnet?
- Do their subnet masks match?
- Are IPs in the usable range (not network/broadcast)?

### 2. Overlapping Subnets

**Problem:** Two interfaces on the same device can't be in the same subnet

**Solution:** Use different subnets for each interface

### 3. Invalid IP Configurations

**Avoid:**
- Using network address (e.g., 192.168.1.0/24)
- Using broadcast address (e.g., 192.168.1.255/24)
- Mismatched subnet masks on same network

### 4. Routing Issues

**Check:**
- Does a route exist to the destination?
- Is the next hop reachable?
- Is the default route configured correctly?

---

## 💡 Tips & Tricks

### For NetPractice Exercises

1. **Start with the constraints**: Work with fixed values first
2. **Check subnet compatibility**: Devices must be in the same subnet to communicate directly
3. **Use /30 for point-to-point**: Links between routers typically use /30 (2 usable IPs)
4. **Plan your subnets**: Don't waste IP space - use appropriate subnet sizes
5. **Verify routing**: Every network needs a path back (return route)

### Quick Calculations

**To find network address:**
- Apply the subnet mask (bitwise AND)
- OR just remember: network address has all host bits = 0

**To find broadcast address:**
- Set all host bits to 1

**To find number of hosts:**
- Count host bits: 2^(32 - prefix) - 2

### Memorization Tricks

**Powers of 2:**
```
2^1 = 2      2^5 = 32
2^2 = 4      2^6 = 64
2^3 = 8      2^7 = 128
2^4 = 16     2^8 = 256
```

**Subnet Mask Pattern:**
- Each bit borrowed cuts available hosts in half
- /24 → /25 → /26 → /27 → /28 → /29 → /30
- 254 → 126 → 62 → 30 → 14 → 6 → 2

---
