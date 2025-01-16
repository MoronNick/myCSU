# CSU

## Table of Contents

- [Fabric Switches and Management IP](#fabric-switches-and-management-ip)
  - [Fabric Switches with inband Management IP](#fabric-switches-with-inband-management-ip)
- [Fabric Topology](#fabric-topology)
- [Fabric IP Allocation](#fabric-ip-allocation)
  - [Fabric Point-To-Point Links](#fabric-point-to-point-links)
  - [Point-To-Point Links Node Allocation](#point-to-point-links-node-allocation)
  - [Loopback Interfaces (BGP EVPN Peering)](#loopback-interfaces-bgp-evpn-peering)
  - [Loopback0 Interfaces Node Allocation](#loopback0-interfaces-node-allocation)
  - [VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)](#vtep-loopback-vxlan-tunnel-source-interfaces-vteps-only)
  - [VTEP Loopback Node allocation](#vtep-loopback-node-allocation)

## Fabric Switches and Management IP

| POD | Type | Node | Management IP | Platform | Provisioned in CloudVision | Serial Number |
| --- | ---- | ---- | ------------- | -------- | -------------------------- | ------------- |
| CSU | l2leaf | e7_0r05c05052051 | 192.168.100.206/24 | - | Provisioned | - |
| CSU | l2leaf | e7_0r05c05052052 | 192.168.100.207/24 | - | Provisioned | - |
| CSU | l3leaf | SCC1 | 192.168.100.204/24 | vEOS-lab | Provisioned | - |
| CSU | l3leaf | SCC2 | 192.168.100.205/24 | vEOS-lab | Provisioned | - |
| CSU | spine | Spine1 | 192.168.100.200/24 | - | Provisioned | - |
| CSU | spine | Spine2 | 192.168.100.201/24 | - | Provisioned | - |
| CSU | l3leaf | WCC1 | 192.168.100.202/24 | vEOS-lab | Provisioned | - |
| CSU | l3leaf | WCC2 | 192.168.100.203/24 | vEOS-lab | Provisioned | - |

> Provision status is based on Ansible inventory declaration and do not represent real status from CloudVision.

### Fabric Switches with inband Management IP

| POD | Type | Node | Management IP | Inband Interface |
| --- | ---- | ---- | ------------- | ---------------- |

## Fabric Topology

| Type | Node | Node Interface | Peer Type | Peer Node | Peer Interface |
| ---- | ---- | -------------- | --------- | ----------| -------------- |
| l2leaf | e7_0r05c05052051 | Ethernet12 | mlag_peer | e7_0r05c05052052 | Ethernet12 |
| l3leaf | SCC1 | Ethernet7 | spine | Spine1 | Ethernet3 |
| l3leaf | SCC1 | Ethernet8 | spine | Spine2 | Ethernet3 |
| l3leaf | SCC1 | Ethernet11 | mlag_peer | SCC2 | Ethernet11 |
| l3leaf | SCC1 | Ethernet12 | mlag_peer | SCC2 | Ethernet12 |
| l3leaf | SCC2 | Ethernet7 | spine | Spine1 | Ethernet4 |
| l3leaf | SCC2 | Ethernet8 | spine | Spine2 | Ethernet4 |
| spine | Spine1 | Ethernet1 | l3leaf | WCC1 | Ethernet7 |
| spine | Spine1 | Ethernet2 | l3leaf | WCC2 | Ethernet7 |
| spine | Spine2 | Ethernet1 | l3leaf | WCC1 | Ethernet8 |
| spine | Spine2 | Ethernet2 | l3leaf | WCC2 | Ethernet8 |
| l3leaf | WCC1 | Ethernet11 | mlag_peer | WCC2 | Ethernet11 |
| l3leaf | WCC1 | Ethernet12 | mlag_peer | WCC2 | Ethernet12 |

## Fabric IP Allocation

### Fabric Point-To-Point Links

| Uplink IPv4 Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ---------------- | ------------------- | ------------------ | ------------------ |
| 10.255.255.0/26 | 64 | 16 | 25.0 % |

### Point-To-Point Links Node Allocation

| Node | Node Interface | Node IP Address | Peer Node | Peer Interface | Peer IP Address |
| ---- | -------------- | --------------- | --------- | -------------- | --------------- |
| SCC1 | Ethernet7 | 10.255.255.9/31 | Spine1 | Ethernet3 | 10.255.255.8/31 |
| SCC1 | Ethernet8 | 10.255.255.11/31 | Spine2 | Ethernet3 | 10.255.255.10/31 |
| SCC2 | Ethernet7 | 10.255.255.13/31 | Spine1 | Ethernet4 | 10.255.255.12/31 |
| SCC2 | Ethernet8 | 10.255.255.15/31 | Spine2 | Ethernet4 | 10.255.255.14/31 |
| Spine1 | Ethernet1 | 10.255.255.0/31 | WCC1 | Ethernet7 | 10.255.255.1/31 |
| Spine1 | Ethernet2 | 10.255.255.4/31 | WCC2 | Ethernet7 | 10.255.255.5/31 |
| Spine2 | Ethernet1 | 10.255.255.2/31 | WCC1 | Ethernet8 | 10.255.255.3/31 |
| Spine2 | Ethernet2 | 10.255.255.6/31 | WCC2 | Ethernet8 | 10.255.255.7/31 |

### Loopback Interfaces (BGP EVPN Peering)

| Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------- | ------------------- | ------------------ | ------------------ |
| 10.255.0.0/27 | 32 | 6 | 18.75 % |

### Loopback0 Interfaces Node Allocation

| POD | Node | Loopback0 |
| --- | ---- | --------- |
| CSU | SCC1 | 10.255.0.5/32 |
| CSU | SCC2 | 10.255.0.6/32 |
| CSU | Spine1 | 10.255.0.1/32 |
| CSU | Spine2 | 10.255.0.2/32 |
| CSU | WCC1 | 10.255.0.3/32 |
| CSU | WCC2 | 10.255.0.4/32 |

### VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)

| VTEP Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------------ | ------------------- | ------------------ | ------------------ |
| 10.255.1.0/27 | 32 | 4 | 12.5 % |

### VTEP Loopback Node allocation

| POD | Node | Loopback1 |
| --- | ---- | --------- |
| CSU | SCC1 | 10.255.1.5/32 |
| CSU | SCC2 | 10.255.1.5/32 |
| CSU | WCC1 | 10.255.1.3/32 |
| CSU | WCC2 | 10.255.1.3/32 |
