# csu

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
| csu | l2leaf | e7_0r05c05052051 | 129.82.52.51 | - | Provisioned | - |
| csu | l2leaf | e7_0r05c05052052 | 129.82.52.52 | - | Provisioned | - |
| csu | spine | Spine1 | 129.82.52.20/24 | vEOS-lab | Provisioned | - |
| csu | spine | Spine2 | 129.82.52.21/24 | vEOS-lab | Provisioned | - |
| csu | l3leaf | WCC1 | 129.82.52.30/24 | vEOS-lab | Provisioned | - |
| csu | l3leaf | WCC2 | 129.82.52.31/24 | vEOS-lab | Provisioned | - |

> Provision status is based on Ansible inventory declaration and do not represent real status from CloudVision.

### Fabric Switches with inband Management IP

| POD | Type | Node | Management IP | Inband Interface |
| --- | ---- | ---- | ------------- | ---------------- |

## Fabric Topology

| Type | Node | Node Interface | Peer Type | Peer Node | Peer Interface |
| ---- | ---- | -------------- | --------- | ----------| -------------- |
| spine | Spine1 | Ethernet1/1 | l3leaf | WCC1 | Ethernet53/1 |
| spine | Spine1 | Ethernet2/1 | l3leaf | WCC2 | Ethernet53/1 |
| spine | Spine2 | Ethernet1/1 | l3leaf | WCC1 | Ethernet54/1 |
| spine | Spine2 | Ethernet2/1 | l3leaf | WCC2 | Ethernet54/1 |
| l3leaf | WCC1 | Ethernet49/1 | mlag_peer | WCC2 | Ethernet49/1 |
| l3leaf | WCC1 | Ethernet50/1 | mlag_peer | WCC2 | Ethernet50/1 |

## Fabric IP Allocation

### Fabric Point-To-Point Links

| Uplink IPv4 Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ---------------- | ------------------- | ------------------ | ------------------ |
| 10.255.255.0/26 | 64 | 8 | 12.5 % |

### Point-To-Point Links Node Allocation

| Node | Node Interface | Node IP Address | Peer Node | Peer Interface | Peer IP Address |
| ---- | -------------- | --------------- | --------- | -------------- | --------------- |
| Spine1 | Ethernet1/1 | 10.255.255.0/31 | WCC1 | Ethernet53/1 | 10.255.255.1/31 |
| Spine1 | Ethernet2/1 | 10.255.255.4/31 | WCC2 | Ethernet53/1 | 10.255.255.5/31 |
| Spine2 | Ethernet1/1 | 10.255.255.2/31 | WCC1 | Ethernet54/1 | 10.255.255.3/31 |
| Spine2 | Ethernet2/1 | 10.255.255.6/31 | WCC2 | Ethernet54/1 | 10.255.255.7/31 |

### Loopback Interfaces (BGP EVPN Peering)

| Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------- | ------------------- | ------------------ | ------------------ |
| 10.255.0.0/27 | 32 | 4 | 12.5 % |

### Loopback0 Interfaces Node Allocation

| POD | Node | Loopback0 |
| --- | ---- | --------- |
| csu | Spine1 | 10.255.0.1/32 |
| csu | Spine2 | 10.255.0.2/32 |
| csu | WCC1 | 10.255.0.3/32 |
| csu | WCC2 | 10.255.0.4/32 |

### VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)

| VTEP Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| --------------------- | ------------------- | ------------------ | ------------------ |
| 10.255.1.0/27 | 32 | 2 | 6.25 % |

### VTEP Loopback Node allocation

| POD | Node | Loopback1 |
| --- | ---- | --------- |
| csu | WCC1 | 10.255.1.3/32 |
| csu | WCC2 | 10.255.1.3/32 |
