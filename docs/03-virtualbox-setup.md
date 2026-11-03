# 03 — VirtualBox Lab Environment

## Overview

I created two virtual machines in VirtualBox:

- **Kali Linux** — used as the client and testing machine.
- **Ubuntu Server** — hosts Apache, MySQL, DVWA and the SafeLine installation.

Both machines are attached to the same VirtualBox NAT Network, named `NatNetwork`. This gives the VMs addresses on the same private subnet so Kali can communicate with Ubuntu.

## Network configuration

| Item | Value |
|---|---|
| VirtualBox NAT Network | `NatNetwork` |
| Network | `10.0.2.0/24` |
| Gateway | `10.0.2.1` |
| Kali Linux | `10.0.2.3` |
| Ubuntu Server | `10.0.2.4` |

Ubuntu also shows these additional network addresses:

| Interface/network purpose | Address |
|---|---|
| SafeLine-related bridge | `192.168.0.1` |
| Docker bridge | `172.17.0.1` |

The bridge addresses are not the Kali/Ubuntu NAT Network addresses. For the basic VM-to-VM path used in this lab, Kali communicates with Ubuntu at `10.0.2.4`.

## Why this lab uses NAT Network instead of Bridged Adapter

The PDF's Section 3.4 describes Bridged Adapter mode. In this lab, both VMs were instead connected to a VirtualBox NAT Network called `NatNetwork`.

A NAT Network allows connected VMs to communicate with one another and provides network access through VirtualBox's NAT. Bridged mode is different: it places the VM more directly on the physical network to which the host is connected.

The actual lab setup is therefore documented as NAT Network. It would be inaccurate to say that Bridged Adapter was configured.

## VM resource adjustment

Ubuntu Server's RAM was increased from 4 GB to 6 GB. The display settings were adjusted to VMSVGA, 128 MB video memory and disabled 3D acceleration after earlier system crash/problem notifications and VirtualBox graphics warnings.

These adjustments are recorded as troubleshooting steps only. The issue should not be described as permanently resolved without further evidence.

## Basic connectivity checks

The following commands are useful for checking the VM's network configuration. They are included as reference commands; only the results explicitly described below are treated as verified.

On Kali or Ubuntu:

```bash
ip addr
```

Displays network interfaces and their assigned IP addresses.

```bash
ip route
```

Displays the routing table, including the default route.

From Kali, test reachability to Ubuntu:

```bash
ping -c 4 10.0.2.4
```

The lab's recorded result is that Kali could ping Ubuntu successfully.

## Current verified state

- Both VMs are connected to `NatNetwork`.
- Kali uses `10.0.2.3`.
- Ubuntu uses `10.0.2.4`.
- Both addresses belong to `10.0.2.0/24`.
- Kali can reach Ubuntu over the lab network.

## Notes for reproducing the setup

VirtualBox may assign different IP addresses in another environment. If that happens, update the IP references in the local `/etc/hosts` files and in later application configuration steps to match the actual Ubuntu address. Do not blindly reuse the addresses in this document on a different network.

## Reference

*Safeline WAF Guide.pdf*, Section 3. The network mode in this lab differs from the guide's Bridged Adapter example.

