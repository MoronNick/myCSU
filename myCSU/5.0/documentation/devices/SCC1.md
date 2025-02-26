# SCC1

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management SSH](#management-ssh)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [Enable Password](#enable-password)
  - [AAA Authorization](#aaa-authorization)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
  - [Logging](#logging)
  - [SNMP](#snmp)
  - [Event Handler](#event-handler)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Port-Channel Interfaces](#port-channel-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [Virtual Router MAC Address](#virtual-router-mac-address)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [ACL](#acl)
  - [IP Access-lists](#ip-access-lists)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Virtual Source NAT](#virtual-source-nat)
  - [Virtual Source NAT Summary](#virtual-source-nat-summary)
  - [Virtual Source NAT Configuration](#virtual-source-nat-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management0 | OOB_MANAGEMENT | oob | mgmt | 192.168.100.204/24 | 192.168.100.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management0 | OOB_MANAGEMENT | oob | mgmt | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management0
   description OOB_MANAGEMENT
   no shutdown
   vrf mgmt
   ip address 192.168.100.204/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 8.8.8.8 | mgmt | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf mgmt 8.8.8.8
```

### NTP

#### NTP Summary

##### NTP Local Interface

| Interface | VRF |
| --------- | --- |
| Management0 | mgmt |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 132.163.96.3 | mgmt | - | - | - | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf mgmt Management0
ntp server vrf mgmt 132.163.96.3
```

### Management SSH

#### IPv4 ACL

| IPv4 ACL | VRF |
| -------- | --- |
| mgmt-acl | mgmt |

#### SSH Timeout and Management

| Idle Timeout | SSH Management |
| ------------ | -------------- |
| default | Enabled |

#### Max number of SSH sessions limit and per-host limit

| Connection Limit | Max from a single Host |
| ---------------- | ---------------------- |
| - | - |

#### Ciphers and Algorithms

| Ciphers | Key-exchange methods | MAC algorithms | Hostkey server algorithms |
|---------|----------------------|----------------|---------------------------|
| default | default | default | default |


#### Management SSH Device Configuration

```eos
!
management ssh
   ip access-group mgmt-acl vrf mgmt in
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| mgmt | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf mgmt
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |
| nmoore | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin secret sha512 <removed>
username nmoore privilege 15 role network-admin secret sha512 <removed>
```

### Enable Password

Enable password has been disabled

### AAA Authorization

#### AAA Authorization Summary

| Type | User Stores |
| ---- | ----------- |
| Exec | local |

Authorization for configuration commands is disabled.

#### AAA Authorization Device Configuration

```eos
aaa authorization exec default local
!
```

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | apiserver.cv-staging.corp.arista.io:443 | mgmt | token-secure,/tmp/cv-onboarding-token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=apiserver.cv-staging.corp.arista.io:443 -cvauth=token-secure,/tmp/cv-onboarding-token -cvvrf=mgmt -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
```

### Logging

#### Logging Servers and Features Summary

| Type | Level |
| -----| ----- |

| VRF | Source Interface |
| --- | ---------------- |
| mgmt | Management0 |

| VRF | Hosts | Ports | Protocol |
| --- | ----- | ----- | -------- |
| mgmt | 129.82.111.172 | Default | UDP |

#### Logging Servers and Features Device Configuration

```eos
!
logging vrf mgmt host 129.82.111.172
logging vrf mgmt source-interface Management0
```

### SNMP

#### SNMP Configuration Summary

| Contact | Location | SNMP Traps | State |
| ------- | -------- | ---------- | ----- |
| noc@colostate.edu | - | All | Disabled |

#### SNMP Hosts Configuration

| Host | VRF | Community | Username | Authentication level | SNMP Version |
| ---- |---- | --------- | -------- | -------------------- | ------------ |
| 10.100.201.199 | mgmt | <removed> | - | - | 2c |

#### SNMP Communities

| Community | Access | Access List IPv4 | Access List IPv6 | View |
| --------- | ------ | ---------------- | ---------------- | ---- |
| <removed> | ro | - | - | - |

#### SNMP Device Configuration

```eos
!
snmp-server contact noc@colostate.edu
snmp-server community <removed> ro
snmp-server host 10.100.201.199 vrf mgmt version 2c <removed>
```

### Event Handler

#### Event Handler Summary

| Handler | Actions | Trigger | Trigger Config |
| ------- | ------- | ------- | -------------- |
| renameInterfaces | bash <code>bash /mnt/flash/renameInterfaces.sh</code> | on-boot | - |

#### Event Handler Device Configuration

```eos
!
event-handler renameInterfaces
   trigger on-boot
   action bash bash /mnt/flash/renameInterfaces.sh
   delay 30
```

## MLAG

### MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| DC1_L3_LEAF2 | Vlan4094 | 10.255.1.69 | Port-Channel11 |

Dual primary detection is disabled.

### MLAG Device Configuration

```eos
!
mlag configuration
   domain-id DC1_L3_LEAF2
   local-interface Vlan4094
   peer-address 10.255.1.69
   peer-link Port-Channel11
   reload-delay mlag 300
   reload-delay non-mlag 330
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

#### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 4096 |

#### Global Spanning-Tree Settings

- Spanning Tree disabled for VLANs: **4093-4094**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
no spanning-tree vlan-id 4093-4094
spanning-tree mst 0 priority 4096
```

## Internal VLAN Allocation Policy

### Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

### Internal VLAN Allocation Policy Device Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 2 | BGP_to_Palo | - |
| 8 | West_Campus_Remote_Public | - |
| 26 | CVMBS_RBL_Public | - |
| 48 | COE_ATS_Public | - |
| 50 | Drone_Pub | - |
| 84 | vendor_foothills | - |
| 99 | Wifi | - |
| 108 | ACRC-CIRA_Public | - |
| 168 | Aruba_ctrlr_public | - |
| 214 | CVMBS_ARBL_Public | - |
| 232 | ERC_Public | - |
| 233 | ERC_Private | - |
| 248 | CSFS_Public | - |
| 313 | csu-visitor | - |
| 314 | IoT_Wireless | - |
| 315 | NOC_Systems | - |
| 320 | WCC_PA_PtP | - |
| 322 | West_Campus_Remote_Private | - |
| 323 | West_Campus_Remote_Utility | - |
| 331 | CSFS_Public | - |
| 332 | ARBL_Utility | - |
| 351 | AIDL_Private | - |
| 352 | AIDL_Utility | - |
| 375 | IDA_Private | - |
| 376 | IDA_Utility | - |
| 384 | HHS_Aggie_Labs_Private | - |
| 386 | ERL_Private | - |
| 387 | CVMBS_Equine_Center_Private | - |
| 388 | Equine_Center_Utility | - |
| 395 | ACRC-CIRA_Utility | - |
| 397 | ERC_Utility | - |
| 401 | Atmos_Private | - |
| 409 | ABL_Utility | - |
| 421 | Aggie_Labs_Private | - |
| 422 | Aggie_Labs_Utility | - |
| 426 | RBL_Utility | - |
| 428 | ATS_Utility | - |
| 472 | SimLab_Public | - |
| 512 | SimLab_Private | - |
| 513 | SimLab_Utility | - |
| 525 | ATS_Chem_Utility | - |
| 540 | Beam_Lab_Private | - |
| 584 | WCC_Public_Non_CSU | - |
| 600 | WCC_ENDR_DREN_L2_link | - |
| 620 | Heating_2_Chill | - |
| 634 | Engr_Wifi | - |
| 653 | AECOM_Private | - |
| 804 | Cardkey | - |
| 814 | EMS | - |
| 815 | IDRC_EMS | - |
| 817 | GMP_EMS | - |
| 1068 | Aruba_ctrlr_private | - |
| 1107 | Temple_Grandin_Util | - |
| 1108 | WCC_Public_Non_CSU | - |
| 1112 | Engines_Lab_Priv2 | - |
| 1208 | ACRC-CIRA_Static_Private | - |
| 1226 | CVMBS_RBL_Private | - |
| 1300 | Temple_Grandin_Priv | - |
| 2020 | ARUBA_000 | - |
| 2023 | ARUBA_192 | - |
| 2326 | VOIP_RIC | - |
| 2670 | VOIP_ARBL | - |
| 2672 | VOIP_RBL | - |
| 2676 | VOIP_ERL | - |
| 2677 | VOIP_Aggie_Labs | - |
| 2680 | VOIP_BEAMLAB | - |
| 2684 | VOIP_IDA | - |
| 2689 | VOIP_CSFS | - |
| 2691 | VOIP_AIDL | - |
| 2694 | VOIP_AECOM | - |
| 2698 | VOIP_ACRC-CIRA | - |
| 2699 | VOIP_ERC | - |
| 2712 | VOIP_FOOTHILLS_CATCHALL | - |
| 2716 | VOIP_ATS_Main | - |
| 2717 | VOIP_ATS_CMMAP | - |
| 2718 | VOIP_ATS_Chem | - |
| 2719 | VOIP_ATS_Annex | - |
| 2720 | VOIP_SimLab | - |
| 3009 | MLAG_L3_VRF_campus | MLAG |
| 3019 | MLAG_L3_VRF_wifi | MLAG |
| 3300 | ACNS_SW-MGMT_WC | - |
| 3301 | Wireless_ACRC | - |
| 3302 | Wireless_ABL | - |
| 3303 | Wireless_AECOM | - |
| 3304 | Wireless_Aggie_Labs | - |
| 3305 | Wireless_AIDL | - |
| 3306 | Wireless_ARBL | - |
| 3307 | Wireless_Atmospheric_Science | - |
| 3308 | Wireless_CSFS | - |
| 3309 | Wireless_ERC | - |
| 3310 | Wireless_ERL | - |
| 3311 | Wireless_IDA | - |
| 3312 | Wireless_RBL | - |
| 3313 | Wireless_Temple_Grandin | - |
| 3349 | ACNS_UPS-MGMT_WC | - |
| 3500 | csu-net | - |
| 3714 | .1x_foothills | - |
| 3785 | aruba_guest | - |
| 3900 | Wireless-1083_0 | - |
| 3903 | Wireless-1083_64 | - |
| 4093 | MLAG_L3 | MLAG |
| 4094 | MLAG | MLAG |

### VLANs Device Configuration

```eos
!
vlan 2
   name BGP_to_Palo
!
vlan 8
   name West_Campus_Remote_Public
!
vlan 26
   name CVMBS_RBL_Public
!
vlan 48
   name COE_ATS_Public
!
vlan 50
   name Drone_Pub
!
vlan 84
   name vendor_foothills
!
vlan 99
   name Wifi
!
vlan 108
   name ACRC-CIRA_Public
!
vlan 168
   name Aruba_ctrlr_public
!
vlan 214
   name CVMBS_ARBL_Public
!
vlan 232
   name ERC_Public
!
vlan 233
   name ERC_Private
!
vlan 248
   name CSFS_Public
!
vlan 313
   name csu-visitor
!
vlan 314
   name IoT_Wireless
!
vlan 315
   name NOC_Systems
!
vlan 320
   name WCC_PA_PtP
!
vlan 322
   name West_Campus_Remote_Private
!
vlan 323
   name West_Campus_Remote_Utility
!
vlan 331
   name CSFS_Public
!
vlan 332
   name ARBL_Utility
!
vlan 351
   name AIDL_Private
!
vlan 352
   name AIDL_Utility
!
vlan 375
   name IDA_Private
!
vlan 376
   name IDA_Utility
!
vlan 384
   name HHS_Aggie_Labs_Private
!
vlan 386
   name ERL_Private
!
vlan 387
   name CVMBS_Equine_Center_Private
!
vlan 388
   name Equine_Center_Utility
!
vlan 395
   name ACRC-CIRA_Utility
!
vlan 397
   name ERC_Utility
!
vlan 401
   name Atmos_Private
!
vlan 409
   name ABL_Utility
!
vlan 421
   name Aggie_Labs_Private
!
vlan 422
   name Aggie_Labs_Utility
!
vlan 426
   name RBL_Utility
!
vlan 428
   name ATS_Utility
!
vlan 472
   name SimLab_Public
!
vlan 512
   name SimLab_Private
!
vlan 513
   name SimLab_Utility
!
vlan 525
   name ATS_Chem_Utility
!
vlan 540
   name Beam_Lab_Private
!
vlan 584
   name WCC_Public_Non_CSU
!
vlan 600
   name WCC_ENDR_DREN_L2_link
!
vlan 620
   name Heating_2_Chill
!
vlan 634
   name Engr_Wifi
!
vlan 653
   name AECOM_Private
!
vlan 804
   name Cardkey
!
vlan 814
   name EMS
!
vlan 815
   name IDRC_EMS
!
vlan 817
   name GMP_EMS
!
vlan 1068
   name Aruba_ctrlr_private
!
vlan 1107
   name Temple_Grandin_Util
!
vlan 1108
   name WCC_Public_Non_CSU
!
vlan 1112
   name Engines_Lab_Priv2
!
vlan 1208
   name ACRC-CIRA_Static_Private
!
vlan 1226
   name CVMBS_RBL_Private
!
vlan 1300
   name Temple_Grandin_Priv
!
vlan 2020
   name ARUBA_000
!
vlan 2023
   name ARUBA_192
!
vlan 2326
   name VOIP_RIC
!
vlan 2670
   name VOIP_ARBL
!
vlan 2672
   name VOIP_RBL
!
vlan 2676
   name VOIP_ERL
!
vlan 2677
   name VOIP_Aggie_Labs
!
vlan 2680
   name VOIP_BEAMLAB
!
vlan 2684
   name VOIP_IDA
!
vlan 2689
   name VOIP_CSFS
!
vlan 2691
   name VOIP_AIDL
!
vlan 2694
   name VOIP_AECOM
!
vlan 2698
   name VOIP_ACRC-CIRA
!
vlan 2699
   name VOIP_ERC
!
vlan 2712
   name VOIP_FOOTHILLS_CATCHALL
!
vlan 2716
   name VOIP_ATS_Main
!
vlan 2717
   name VOIP_ATS_CMMAP
!
vlan 2718
   name VOIP_ATS_Chem
!
vlan 2719
   name VOIP_ATS_Annex
!
vlan 2720
   name VOIP_SimLab
!
vlan 3009
   name MLAG_L3_VRF_campus
   trunk group MLAG
!
vlan 3019
   name MLAG_L3_VRF_wifi
   trunk group MLAG
!
vlan 3300
   name ACNS_SW-MGMT_WC
!
vlan 3301
   name Wireless_ACRC
!
vlan 3302
   name Wireless_ABL
!
vlan 3303
   name Wireless_AECOM
!
vlan 3304
   name Wireless_Aggie_Labs
!
vlan 3305
   name Wireless_AIDL
!
vlan 3306
   name Wireless_ARBL
!
vlan 3307
   name Wireless_Atmospheric_Science
!
vlan 3308
   name Wireless_CSFS
!
vlan 3309
   name Wireless_ERC
!
vlan 3310
   name Wireless_ERL
!
vlan 3311
   name Wireless_IDA
!
vlan 3312
   name Wireless_RBL
!
vlan 3313
   name Wireless_Temple_Grandin
!
vlan 3349
   name ACNS_UPS-MGMT_WC
!
vlan 3500
   name csu-net
!
vlan 3714
   name .1x_foothills
!
vlan 3785
   name aruba_guest
!
vlan 3900
   name Wireless-1083_0
!
vlan 3903
   name Wireless-1083_64
!
vlan 4093
   name MLAG_L3
   trunk group MLAG
!
vlan 4094
   name MLAG
   trunk group MLAG
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet11 | MLAG_SCC2_Ethernet11 | *trunk | *- | *- | *MLAG | 11 |
| Ethernet12 | MLAG_SCC2_Ethernet12 | *trunk | *- | *- | *MLAG | 11 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet7 | P2P_Spine1_Ethernet3 | - | 10.255.255.9/31 | default | 1500 | False | - | - |
| Ethernet8 | P2P_Spine2_Ethernet3 | - | 10.255.255.11/31 | default | 1500 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet7
   description P2P_Spine1_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.9/31
!
interface Ethernet8
   description P2P_Spine2_Ethernet3
   no shutdown
   mtu 1500
   no switchport
   ip address 10.255.255.11/31
!
interface Ethernet11
   description MLAG_SCC2_Ethernet11
   no shutdown
   channel-group 11 mode active
!
interface Ethernet12
   description MLAG_SCC2_Ethernet12
   no shutdown
   channel-group 11 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel11 | MLAG_SCC2_Port-Channel11 | trunk | - | - | MLAG | - | - | - | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel11
   description MLAG_SCC2_Port-Channel11
   no shutdown
   switchport mode trunk
   switchport trunk group MLAG
   switchport
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | ROUTER_ID | default | 10.255.0.5/32 |
| Loopback1 | VXLAN_TUNNEL_SOURCE | default | 10.255.1.5/32 |
| Loopback10 | DIAG_VRF_campus | campus | 10.255.10.5/32 |
| Loopback11 | DIAG_VRF_wifi | wifi | 10.255.11.5/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | ROUTER_ID | default | - |
| Loopback1 | VXLAN_TUNNEL_SOURCE | default | - |
| Loopback10 | DIAG_VRF_campus | campus | - |
| Loopback11 | DIAG_VRF_wifi | wifi | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description ROUTER_ID
   no shutdown
   ip address 10.255.0.5/32
!
interface Loopback1
   description VXLAN_TUNNEL_SOURCE
   no shutdown
   ip address 10.255.1.5/32
!
interface Loopback10
   description DIAG_VRF_campus
   no shutdown
   vrf campus
   ip address 10.255.10.5/32
!
interface Loopback11
   description DIAG_VRF_wifi
   no shutdown
   vrf wifi
   ip address 10.255.11.5/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan2 | BGP_to_Palo | campus | - | False |
| Vlan8 | West_Campus_Remote_Public | campus | - | False |
| Vlan26 | CVMBS_RBL_Public | campus | - | False |
| Vlan48 | COE_ATS_Public | campus | - | False |
| Vlan50 | Drone_Pub | campus | - | False |
| Vlan84 | vendor_foothills | campus | - | False |
| Vlan99 | Wifi | wifi | - | False |
| Vlan108 | ACRC-CIRA_Public | campus | - | False |
| Vlan214 | CVMBS_ARBL_Public | campus | - | False |
| Vlan232 | ERC_Public | campus | - | False |
| Vlan233 | ERC_Private | campus | - | False |
| Vlan248 | CSFS_Public | campus | - | False |
| Vlan320 | WCC_PA_PtP | campus | - | False |
| Vlan322 | West_Campus_Remote_Private | campus | - | False |
| Vlan323 | West_Campus_Remote_Utility | campus | - | False |
| Vlan331 | CSFS_Public | campus | - | False |
| Vlan332 | ARBL_Utility | campus | - | False |
| Vlan351 | AIDL_Private | campus | - | False |
| Vlan352 | AIDL_Utility | campus | - | False |
| Vlan375 | IDA_Private | campus | - | False |
| Vlan376 | IDA_Utility | campus | - | False |
| Vlan384 | HHS_Aggie_Labs_Private | campus | - | False |
| Vlan386 | ERL_Private | campus | - | False |
| Vlan387 | CVMBS_Equine_Center_Private | campus | - | False |
| Vlan388 | Equine_Center_Utility | campus | - | False |
| Vlan395 | ACRC-CIRA_Utility | campus | - | False |
| Vlan397 | ERC_Utility | campus | - | False |
| Vlan401 | Atmos_Private | campus | - | False |
| Vlan409 | ABL_Utility | campus | - | False |
| Vlan421 | Aggie_Labs_Private | campus | - | False |
| Vlan422 | Aggie_Labs_Utility | campus | - | False |
| Vlan426 | RBL_Utility | campus | - | False |
| Vlan428 | ATS_Utility | campus | - | False |
| Vlan472 | SimLab_Public | campus | - | False |
| Vlan512 | SimLab_Private | campus | - | False |
| Vlan513 | SimLab_Utility | campus | - | False |
| Vlan525 | ATS_Chem_Utility | campus | - | False |
| Vlan540 | Beam_Lab_Private | campus | - | False |
| Vlan584 | WCC_Public_Non_CSU | campus | - | False |
| Vlan600 | WCC_ENDR_DREN_L2_link | campus | - | False |
| Vlan653 | AECOM_Private | campus | - | False |
| Vlan1107 | Temple_Grandin_Util | campus | - | False |
| Vlan1108 | WCC_Public_Non_CSU | campus | - | False |
| Vlan1112 | Engines_Lab_Priv2 | campus | - | False |
| Vlan1208 | ACRC-CIRA_Static_Private | campus | - | False |
| Vlan1226 | CVMBS_RBL_Private | campus | - | False |
| Vlan1300 | Temple_Grandin_Priv | campus | - | False |
| Vlan2326 | VOIP_RIC | campus | - | False |
| Vlan2670 | VOIP_ARBL | campus | - | False |
| Vlan2672 | VOIP_RBL | campus | - | False |
| Vlan2676 | VOIP_ERL | campus | - | False |
| Vlan2677 | VOIP_Aggie_Labs | campus | - | False |
| Vlan2680 | VOIP_BEAMLAB | campus | - | False |
| Vlan2684 | VOIP_IDA | campus | - | False |
| Vlan2689 | VOIP_CSFS | campus | - | False |
| Vlan2691 | VOIP_AIDL | campus | - | False |
| Vlan2694 | VOIP_AECOM | campus | - | False |
| Vlan2698 | VOIP_ACRC-CIRA | campus | - | False |
| Vlan2699 | VOIP_ERC | campus | - | False |
| Vlan2712 | VOIP_FOOTHILLS_CATCHALL | campus | - | False |
| Vlan2716 | VOIP_ATS_Main | campus | - | False |
| Vlan2717 | VOIP_ATS_CMMAP | campus | - | False |
| Vlan2718 | VOIP_ATS_Chem | campus | - | False |
| Vlan2719 | VOIP_ATS_Annex | campus | - | False |
| Vlan2720 | VOIP_SimLab | campus | - | False |
| Vlan3009 | MLAG_L3_VRF_campus | campus | 1500 | False |
| Vlan3019 | MLAG_L3_VRF_wifi | wifi | 1500 | False |
| Vlan3300 | ACNS_SW-MGMT_WC | campus | - | False |
| Vlan3301 | Wireless_ACRC | campus | - | False |
| Vlan3302 | Wireless_ABL | campus | - | False |
| Vlan3303 | Wireless_AECOM | campus | - | False |
| Vlan3304 | Wireless_Aggie_Labs | campus | - | False |
| Vlan3305 | Wireless_AIDL | campus | - | False |
| Vlan3306 | Wireless_ARBL | campus | - | False |
| Vlan3307 | Wireless_Atmospheric_Science | campus | - | False |
| Vlan3308 | Wireless_CSFS | campus | - | False |
| Vlan3309 | Wireless_ERC | campus | - | False |
| Vlan3310 | Wireless_ERL | campus | - | False |
| Vlan3311 | Wireless_IDA | campus | - | False |
| Vlan3312 | Wireless_RBL | campus | - | False |
| Vlan3313 | Wireless_Temple_Grandin | campus | - | False |
| Vlan3349 | ACNS_UPS-MGMT_WC | campus | - | False |
| Vlan4093 | MLAG_L3 | default | 1500 | False |
| Vlan4094 | MLAG | default | 1500 | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ------ | ------- |
| Vlan2 |  campus  |  -  |  -  |  -  |  -  |  -  |
| Vlan8 |  campus  |  -  |  129.82.8.1/23  |  -  |  -  |  -  |
| Vlan26 |  campus  |  -  |  129.82.26.1/23  |  -  |  -  |  -  |
| Vlan48 |  campus  |  -  |  129.82.48.1/24  |  -  |  -  |  -  |
| Vlan50 |  campus  |  -  |  129.82.5.193/26  |  -  |  -  |  -  |
| Vlan84 |  campus  |  -  |  129.82.84.129/26  |  -  |  -  |  -  |
| Vlan99 |  wifi  |  -  |  129.82.99.1/24  |  -  |  -  |  -  |
| Vlan108 |  campus  |  -  |  129.82.108.1/23  |  -  |  -  |  -  |
| Vlan214 |  campus  |  -  |  129.82.214.1/24  |  -  |  -  |  -  |
| Vlan232 |  campus  |  -  |  129.82.232.1/23  |  -  |  -  |  -  |
| Vlan233 |  campus  |  -  |  10.1.232.1/23  |  -  |  -  |  -  |
| Vlan248 |  campus  |  -  |  129.82.248.1/24  |  -  |  -  |  -  |
| Vlan320 |  campus  |  -  |  129.82.1.138/29  |  -  |  -  |  -  |
| Vlan322 |  campus  |  -  |  10.1.8.1/23  |  -  |  -  |  -  |
| Vlan323 |  campus  |  -  |  10.11.8.1/23  |  -  |  -  |  -  |
| Vlan331 |  campus  |  -  |  10.1.214.1/24  |  -  |  -  |  -  |
| Vlan332 |  campus  |  -  |  10.11.214.1/24  |  -  |  -  |  -  |
| Vlan351 |  campus  |  -  |  10.1.178.1/24  |  -  |  -  |  -  |
| Vlan352 |  campus  |  -  |  10.11.178.1/24  |  -  |  -  |  -  |
| Vlan375 |  campus  |  -  |  10.1.179.1/24  |  -  |  -  |  -  |
| Vlan376 |  campus  |  -  |  10.11.179.1/24  |  -  |  -  |  -  |
| Vlan384 |  campus  |  -  |  10.3.152.1/24  |  -  |  -  |  -  |
| Vlan386 |  campus  |  -  |  10.2.216.1/23  |  -  |  -  |  -  |
| Vlan387 |  campus  |  -  |  10.2.218.1/23  |  -  |  -  |  -  |
| Vlan388 |  campus  |  -  |  10.11.218.1/24  |  -  |  -  |  -  |
| Vlan395 |  campus  |  -  |  10.11.108.1/24  |  -  |  -  |  -  |
| Vlan397 |  campus  |  -  |  10.11.232.1/23  |  -  |  -  |  -  |
| Vlan401 |  campus  |  -  |  10.1.48.1/23  |  -  |  -  |  -  |
| Vlan409 |  campus  |  -  |  10.11.228.1/24  |  -  |  -  |  -  |
| Vlan421 |  campus  |  -  |  10.2.255.1/24  |  -  |  -  |  -  |
| Vlan422 |  campus  |  -  |  10.12.255.1/24  |  -  |  -  |  -  |
| Vlan426 |  campus  |  -  |  10.11.26.1/23  |  -  |  -  |  -  |
| Vlan428 |  campus  |  -  |  10.11.48.1/24  |  -  |  -  |  -  |
| Vlan472 |  campus  |  -  |  129.82.4.129/25  |  -  |  -  |  -  |
| Vlan512 |  campus  |  -  |  10.1.4.129/25  |  -  |  -  |  -  |
| Vlan513 |  campus  |  -  |  10.11.4.129/25  |  -  |  -  |  -  |
| Vlan525 |  campus  |  -  |  10.11.49.1/24  |  -  |  -  |  -  |
| Vlan540 |  campus  |  -  |  10.1.228.1/24  |  -  |  -  |  -  |
| Vlan584 |  campus  |  -  |  129.82.18.129/25  |  -  |  -  |  -  |
| Vlan600 |  campus  |  -  |  129.82.1.72/28  |  -  |  -  |  -  |
| Vlan653 |  campus  |  -  |  10.2.228.1/24  |  -  |  -  |  -  |
| Vlan1107 |  campus  |  -  |  10.11.107.1/24  |  -  |  -  |  -  |
| Vlan1108 |  campus  |  -  |  10.1.108.1/23  |  -  |  -  |  -  |
| Vlan1112 |  campus  |  -  |  10.3.106.1/24  |  -  |  -  |  -  |
| Vlan1208 |  campus  |  -  |  10.2.108.1/23  |  -  |  -  |  -  |
| Vlan1226 |  campus  |  -  |  10.1.26.1/23  |  -  |  -  |  -  |
| Vlan1300 |  campus  |  -  |  10.1.15.1/24  |  -  |  -  |  -  |
| Vlan2326 |  campus  |  -  |  10.174.26.1/23  |  -  |  -  |  -  |
| Vlan2670 |  campus  |  -  |  10.20.138.1/23  |  -  |  -  |  -  |
| Vlan2672 |  campus  |  -  |  10.20.142.1/23  |  -  |  -  |  -  |
| Vlan2676 |  campus  |  -  |  10.20.150.1/23  |  -  |  -  |  -  |
| Vlan2677 |  campus  |  -  |  10.20.152.1/23  |  -  |  -  |  -  |
| Vlan2680 |  campus  |  -  |  10.20.158.1/23  |  -  |  -  |  -  |
| Vlan2684 |  campus  |  -  |  10.20.166.1/23  |  -  |  -  |  -  |
| Vlan2689 |  campus  |  -  |  10.20.176.1/23  |  -  |  -  |  -  |
| Vlan2691 |  campus  |  -  |  10.20.180.1/23  |  -  |  -  |  -  |
| Vlan2694 |  campus  |  -  |  10.20.186.1/23  |  -  |  -  |  -  |
| Vlan2698 |  campus  |  -  |  10.20.194.1/23  |  -  |  -  |  -  |
| Vlan2699 |  campus  |  -  |  10.20.196.1/23  |  -  |  -  |  -  |
| Vlan2712 |  campus  |  -  |  10.20.222.1/23  |  -  |  -  |  -  |
| Vlan2716 |  campus  |  -  |  10.20.230.1/23  |  -  |  -  |  -  |
| Vlan2717 |  campus  |  -  |  10.20.232.1/23  |  -  |  -  |  -  |
| Vlan2718 |  campus  |  -  |  10.20.234.1/23  |  -  |  -  |  -  |
| Vlan2719 |  campus  |  -  |  10.20.236.1/23  |  -  |  -  |  -  |
| Vlan2720 |  campus  |  -  |  10.20.238.1/23  |  -  |  -  |  -  |
| Vlan3009 |  campus  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |
| Vlan3019 |  wifi  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |
| Vlan3300 |  campus  |  -  |  10.111.0.1/23  |  -  |  -  |  -  |
| Vlan3301 |  campus  |  -  |  10.111.108.1/24  |  -  |  -  |  -  |
| Vlan3302 |  campus  |  -  |  10.111.228.1/24  |  -  |  -  |  -  |
| Vlan3303 |  campus  |  -  |  10.111.226.1/24  |  -  |  -  |  -  |
| Vlan3304 |  campus  |  -  |  10.111.255.1/24  |  -  |  -  |  -  |
| Vlan3305 |  campus  |  -  |  10.111.178.1/24  |  -  |  -  |  -  |
| Vlan3306 |  campus  |  -  |  10.111.214.1/24  |  -  |  -  |  -  |
| Vlan3307 |  campus  |  -  |  10.111.48.1/24  |  -  |  -  |  -  |
| Vlan3308 |  campus  |  -  |  10.111.248.1/24  |  -  |  -  |  -  |
| Vlan3309 |  campus  |  -  |  10.111.232.1/24  |  -  |  -  |  -  |
| Vlan3310 |  campus  |  -  |  10.111.216.1/24  |  -  |  -  |  -  |
| Vlan3311 |  campus  |  -  |  10.111.179.1/24  |  -  |  -  |  -  |
| Vlan3312 |  campus  |  -  |  10.111.26.1/24  |  -  |  -  |  -  |
| Vlan3313 |  campus  |  -  |  10.111.227.1/24  |  -  |  -  |  -  |
| Vlan3349 |  campus  |  -  |  10.111.2.1/23  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.255.1.100/31  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.255.1.68/31  |  -  |  -  |  -  |  -  |

##### IPv6

| Interface | VRF | IPv6 Address | IPv6 Virtual Addresses | Virtual Router Addresses | ND RA Disabled | Managed Config Flag | Other Config Flag | IPv6 ACL In | IPv6 ACL Out |
| --------- | --- | ------------ | ---------------------- | ------------------------ | -------------- | ------------------- | ----------------- | ----------- | ------------ |
| Vlan8 | campus | - | fec0:0:8::1/96 | - | - | - | - | - | - |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan2
   description BGP_to_Palo
   no shutdown
   vrf campus
!
interface Vlan8
   description West_Campus_Remote_Public
   no shutdown
   vrf campus
   ipv6 enable
   ip address virtual 129.82.8.1/23
   ipv6 address virtual fec0:0:8::1/96
!
interface Vlan26
   description CVMBS_RBL_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.26.1/23
!
interface Vlan48
   description COE_ATS_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.48.1/24
!
interface Vlan50
   description Drone_Pub
   no shutdown
   vrf campus
   ip address virtual 129.82.5.193/26
!
interface Vlan84
   description vendor_foothills
   no shutdown
   vrf campus
   ip address virtual 129.82.84.129/26
!
interface Vlan99
   description Wifi
   no shutdown
   vrf wifi
   ip address virtual 129.82.99.1/24
!
interface Vlan108
   description ACRC-CIRA_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.108.1/23
!
interface Vlan214
   description CVMBS_ARBL_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.214.1/24
!
interface Vlan232
   description ERC_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.232.1/23
!
interface Vlan233
   description ERC_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.232.1/23
!
interface Vlan248
   description CSFS_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.248.1/24
!
interface Vlan320
   description WCC_PA_PtP
   no shutdown
   vrf campus
   ip address virtual 129.82.1.138/29
!
interface Vlan322
   description West_Campus_Remote_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.8.1/23
!
interface Vlan323
   description West_Campus_Remote_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.8.1/23
!
interface Vlan331
   description CSFS_Public
   no shutdown
   vrf campus
   ip address virtual 10.1.214.1/24
!
interface Vlan332
   description ARBL_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.214.1/24
!
interface Vlan351
   description AIDL_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.178.1/24
!
interface Vlan352
   description AIDL_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.178.1/24
!
interface Vlan375
   description IDA_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.179.1/24
!
interface Vlan376
   description IDA_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.179.1/24
!
interface Vlan384
   description HHS_Aggie_Labs_Private
   no shutdown
   vrf campus
   ip address virtual 10.3.152.1/24
!
interface Vlan386
   description ERL_Private
   no shutdown
   vrf campus
   ip address virtual 10.2.216.1/23
!
interface Vlan387
   description CVMBS_Equine_Center_Private
   no shutdown
   vrf campus
   ip address virtual 10.2.218.1/23
!
interface Vlan388
   description Equine_Center_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.218.1/24
!
interface Vlan395
   description ACRC-CIRA_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.108.1/24
!
interface Vlan397
   description ERC_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.232.1/23
!
interface Vlan401
   description Atmos_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.48.1/23
!
interface Vlan409
   description ABL_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.228.1/24
!
interface Vlan421
   description Aggie_Labs_Private
   no shutdown
   vrf campus
   ip address virtual 10.2.255.1/24
!
interface Vlan422
   description Aggie_Labs_Utility
   no shutdown
   vrf campus
   ip address virtual 10.12.255.1/24
!
interface Vlan426
   description RBL_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.26.1/23
!
interface Vlan428
   description ATS_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.48.1/24
!
interface Vlan472
   description SimLab_Public
   no shutdown
   vrf campus
   ip address virtual 129.82.4.129/25
!
interface Vlan512
   description SimLab_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.4.129/25
!
interface Vlan513
   description SimLab_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.4.129/25
!
interface Vlan525
   description ATS_Chem_Utility
   no shutdown
   vrf campus
   ip address virtual 10.11.49.1/24
!
interface Vlan540
   description Beam_Lab_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.228.1/24
!
interface Vlan584
   description WCC_Public_Non_CSU
   no shutdown
   vrf campus
   ip address virtual 129.82.18.129/25
!
interface Vlan600
   description WCC_ENDR_DREN_L2_link
   no shutdown
   vrf campus
   ip address virtual 129.82.1.72/28
!
interface Vlan653
   description AECOM_Private
   no shutdown
   vrf campus
   ip address virtual 10.2.228.1/24
!
interface Vlan1107
   description Temple_Grandin_Util
   no shutdown
   vrf campus
   ip address virtual 10.11.107.1/24
!
interface Vlan1108
   description WCC_Public_Non_CSU
   no shutdown
   vrf campus
   ip address virtual 10.1.108.1/23
!
interface Vlan1112
   description Engines_Lab_Priv2
   no shutdown
   vrf campus
   ip address virtual 10.3.106.1/24
!
interface Vlan1208
   description ACRC-CIRA_Static_Private
   no shutdown
   vrf campus
   ip address virtual 10.2.108.1/23
!
interface Vlan1226
   description CVMBS_RBL_Private
   no shutdown
   vrf campus
   ip address virtual 10.1.26.1/23
!
interface Vlan1300
   description Temple_Grandin_Priv
   no shutdown
   vrf campus
   ip address virtual 10.1.15.1/24
!
interface Vlan2326
   description VOIP_RIC
   no shutdown
   vrf campus
   ip address virtual 10.174.26.1/23
!
interface Vlan2670
   description VOIP_ARBL
   no shutdown
   vrf campus
   ip address virtual 10.20.138.1/23
!
interface Vlan2672
   description VOIP_RBL
   no shutdown
   vrf campus
   ip address virtual 10.20.142.1/23
!
interface Vlan2676
   description VOIP_ERL
   no shutdown
   vrf campus
   ip address virtual 10.20.150.1/23
!
interface Vlan2677
   description VOIP_Aggie_Labs
   no shutdown
   vrf campus
   ip address virtual 10.20.152.1/23
!
interface Vlan2680
   description VOIP_BEAMLAB
   no shutdown
   vrf campus
   ip address virtual 10.20.158.1/23
!
interface Vlan2684
   description VOIP_IDA
   no shutdown
   vrf campus
   ip address virtual 10.20.166.1/23
!
interface Vlan2689
   description VOIP_CSFS
   no shutdown
   vrf campus
   ip address virtual 10.20.176.1/23
!
interface Vlan2691
   description VOIP_AIDL
   no shutdown
   vrf campus
   ip address virtual 10.20.180.1/23
!
interface Vlan2694
   description VOIP_AECOM
   no shutdown
   vrf campus
   ip address virtual 10.20.186.1/23
!
interface Vlan2698
   description VOIP_ACRC-CIRA
   no shutdown
   vrf campus
   ip address virtual 10.20.194.1/23
!
interface Vlan2699
   description VOIP_ERC
   no shutdown
   vrf campus
   ip address virtual 10.20.196.1/23
!
interface Vlan2712
   description VOIP_FOOTHILLS_CATCHALL
   no shutdown
   vrf campus
   ip address virtual 10.20.222.1/23
!
interface Vlan2716
   description VOIP_ATS_Main
   no shutdown
   vrf campus
   ip address virtual 10.20.230.1/23
!
interface Vlan2717
   description VOIP_ATS_CMMAP
   no shutdown
   vrf campus
   ip address virtual 10.20.232.1/23
!
interface Vlan2718
   description VOIP_ATS_Chem
   no shutdown
   vrf campus
   ip address virtual 10.20.234.1/23
!
interface Vlan2719
   description VOIP_ATS_Annex
   no shutdown
   vrf campus
   ip address virtual 10.20.236.1/23
!
interface Vlan2720
   description VOIP_SimLab
   no shutdown
   vrf campus
   ip address virtual 10.20.238.1/23
!
interface Vlan3009
   description MLAG_L3_VRF_campus
   no shutdown
   mtu 1500
   vrf campus
   ip address 10.255.1.100/31
!
interface Vlan3019
   description MLAG_L3_VRF_wifi
   no shutdown
   mtu 1500
   vrf wifi
   ip address 10.255.1.100/31
!
interface Vlan3300
   description ACNS_SW-MGMT_WC
   no shutdown
   vrf campus
   ip address virtual 10.111.0.1/23
!
interface Vlan3301
   description Wireless_ACRC
   no shutdown
   vrf campus
   ip address virtual 10.111.108.1/24
!
interface Vlan3302
   description Wireless_ABL
   no shutdown
   vrf campus
   ip address virtual 10.111.228.1/24
!
interface Vlan3303
   description Wireless_AECOM
   no shutdown
   vrf campus
   ip address virtual 10.111.226.1/24
!
interface Vlan3304
   description Wireless_Aggie_Labs
   no shutdown
   vrf campus
   ip address virtual 10.111.255.1/24
!
interface Vlan3305
   description Wireless_AIDL
   no shutdown
   vrf campus
   ip address virtual 10.111.178.1/24
!
interface Vlan3306
   description Wireless_ARBL
   no shutdown
   vrf campus
   ip address virtual 10.111.214.1/24
!
interface Vlan3307
   description Wireless_Atmospheric_Science
   no shutdown
   vrf campus
   ip address virtual 10.111.48.1/24
!
interface Vlan3308
   description Wireless_CSFS
   no shutdown
   vrf campus
   ip address virtual 10.111.248.1/24
!
interface Vlan3309
   description Wireless_ERC
   no shutdown
   vrf campus
   ip address virtual 10.111.232.1/24
!
interface Vlan3310
   description Wireless_ERL
   no shutdown
   vrf campus
   ip address virtual 10.111.216.1/24
!
interface Vlan3311
   description Wireless_IDA
   no shutdown
   vrf campus
   ip address virtual 10.111.179.1/24
!
interface Vlan3312
   description Wireless_RBL
   no shutdown
   vrf campus
   ip address virtual 10.111.26.1/24
!
interface Vlan3313
   description Wireless_Temple_Grandin
   no shutdown
   vrf campus
   ip address virtual 10.111.227.1/24
!
interface Vlan3349
   description ACNS_UPS-MGMT_WC
   no shutdown
   vrf campus
   ip address virtual 10.111.2.1/23
!
interface Vlan4093
   description MLAG_L3
   no shutdown
   mtu 1500
   ip address 10.255.1.100/31
!
interface Vlan4094
   description MLAG
   no shutdown
   mtu 1500
   no autostate
   ip address 10.255.1.68/31
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Loopback1 |
| UDP port | 4789 |
| EVPN MLAG Shared Router MAC | mlag-system-id |

##### VLAN to VNI, Flood List and Multicast Group Mappings

| VLAN | VNI | Flood List | Multicast Group |
| ---- | --- | ---------- | --------------- |
| 2 | 10002 | - | - |
| 8 | 10008 | - | - |
| 26 | 10026 | - | - |
| 48 | 10048 | - | - |
| 50 | 10050 | - | - |
| 84 | 10084 | - | - |
| 99 | 10099 | - | - |
| 108 | 10108 | - | - |
| 168 | 10168 | - | - |
| 214 | 10214 | - | - |
| 232 | 10232 | - | - |
| 233 | 10233 | - | - |
| 248 | 10248 | - | - |
| 313 | 10313 | - | - |
| 314 | 10314 | - | - |
| 315 | 10315 | - | - |
| 320 | 10320 | - | - |
| 322 | 10322 | - | - |
| 323 | 10323 | - | - |
| 331 | 10331 | - | - |
| 332 | 10332 | - | - |
| 351 | 10351 | - | - |
| 352 | 10352 | - | - |
| 375 | 10375 | - | - |
| 376 | 10376 | - | - |
| 384 | 10384 | - | - |
| 386 | 10386 | - | - |
| 387 | 10387 | - | - |
| 388 | 10388 | - | - |
| 395 | 10395 | - | - |
| 397 | 10397 | - | - |
| 401 | 10401 | - | - |
| 409 | 10409 | - | - |
| 421 | 10421 | - | - |
| 422 | 10422 | - | - |
| 426 | 10426 | - | - |
| 428 | 10428 | - | - |
| 472 | 10472 | - | - |
| 512 | 10512 | - | - |
| 513 | 10513 | - | - |
| 525 | 10525 | - | - |
| 540 | 10540 | - | - |
| 584 | 10584 | - | - |
| 600 | 10600 | - | - |
| 620 | 10620 | - | - |
| 634 | 10634 | - | - |
| 653 | 10653 | - | - |
| 804 | 10804 | - | - |
| 814 | 10814 | - | - |
| 815 | 10815 | - | - |
| 817 | 10817 | - | - |
| 1068 | 11068 | - | - |
| 1107 | 11107 | - | - |
| 1108 | 11108 | - | - |
| 1112 | 11112 | - | - |
| 1208 | 11208 | - | - |
| 1226 | 11226 | - | - |
| 1300 | 11300 | - | - |
| 2020 | 12020 | - | - |
| 2023 | 12023 | - | - |
| 2326 | 12326 | - | - |
| 2670 | 12670 | - | - |
| 2672 | 12672 | - | - |
| 2676 | 12676 | - | - |
| 2677 | 12677 | - | - |
| 2680 | 12680 | - | - |
| 2684 | 12684 | - | - |
| 2689 | 12689 | - | - |
| 2691 | 12691 | - | - |
| 2694 | 12694 | - | - |
| 2698 | 12698 | - | - |
| 2699 | 12699 | - | - |
| 2712 | 12712 | - | - |
| 2716 | 12716 | - | - |
| 2717 | 12717 | - | - |
| 2718 | 12718 | - | - |
| 2719 | 12719 | - | - |
| 2720 | 12720 | - | - |
| 3300 | 13300 | - | - |
| 3301 | 13301 | - | - |
| 3302 | 13302 | - | - |
| 3303 | 13303 | - | - |
| 3304 | 13304 | - | - |
| 3305 | 13305 | - | - |
| 3306 | 13306 | - | - |
| 3307 | 13307 | - | - |
| 3308 | 13308 | - | - |
| 3309 | 13309 | - | - |
| 3310 | 13310 | - | - |
| 3311 | 13311 | - | - |
| 3312 | 13312 | - | - |
| 3313 | 13313 | - | - |
| 3349 | 13349 | - | - |
| 3500 | 13500 | - | - |
| 3714 | 13714 | - | - |
| 3785 | 13785 | - | - |
| 3900 | 13900 | - | - |
| 3903 | 13903 | - | - |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| campus | 10 | - |
| wifi | 20 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description SCC1_VTEP
   vxlan source-interface Loopback1
   vxlan virtual-router encapsulation mac-address mlag-system-id
   vxlan udp-port 4789
   vxlan vlan 2 vni 10002
   vxlan vlan 8 vni 10008
   vxlan vlan 26 vni 10026
   vxlan vlan 48 vni 10048
   vxlan vlan 50 vni 10050
   vxlan vlan 84 vni 10084
   vxlan vlan 99 vni 10099
   vxlan vlan 108 vni 10108
   vxlan vlan 168 vni 10168
   vxlan vlan 214 vni 10214
   vxlan vlan 232 vni 10232
   vxlan vlan 233 vni 10233
   vxlan vlan 248 vni 10248
   vxlan vlan 313 vni 10313
   vxlan vlan 314 vni 10314
   vxlan vlan 315 vni 10315
   vxlan vlan 320 vni 10320
   vxlan vlan 322 vni 10322
   vxlan vlan 323 vni 10323
   vxlan vlan 331 vni 10331
   vxlan vlan 332 vni 10332
   vxlan vlan 351 vni 10351
   vxlan vlan 352 vni 10352
   vxlan vlan 375 vni 10375
   vxlan vlan 376 vni 10376
   vxlan vlan 384 vni 10384
   vxlan vlan 386 vni 10386
   vxlan vlan 387 vni 10387
   vxlan vlan 388 vni 10388
   vxlan vlan 395 vni 10395
   vxlan vlan 397 vni 10397
   vxlan vlan 401 vni 10401
   vxlan vlan 409 vni 10409
   vxlan vlan 421 vni 10421
   vxlan vlan 422 vni 10422
   vxlan vlan 426 vni 10426
   vxlan vlan 428 vni 10428
   vxlan vlan 472 vni 10472
   vxlan vlan 512 vni 10512
   vxlan vlan 513 vni 10513
   vxlan vlan 525 vni 10525
   vxlan vlan 540 vni 10540
   vxlan vlan 584 vni 10584
   vxlan vlan 600 vni 10600
   vxlan vlan 620 vni 10620
   vxlan vlan 634 vni 10634
   vxlan vlan 653 vni 10653
   vxlan vlan 804 vni 10804
   vxlan vlan 814 vni 10814
   vxlan vlan 815 vni 10815
   vxlan vlan 817 vni 10817
   vxlan vlan 1068 vni 11068
   vxlan vlan 1107 vni 11107
   vxlan vlan 1108 vni 11108
   vxlan vlan 1112 vni 11112
   vxlan vlan 1208 vni 11208
   vxlan vlan 1226 vni 11226
   vxlan vlan 1300 vni 11300
   vxlan vlan 2020 vni 12020
   vxlan vlan 2023 vni 12023
   vxlan vlan 2326 vni 12326
   vxlan vlan 2670 vni 12670
   vxlan vlan 2672 vni 12672
   vxlan vlan 2676 vni 12676
   vxlan vlan 2677 vni 12677
   vxlan vlan 2680 vni 12680
   vxlan vlan 2684 vni 12684
   vxlan vlan 2689 vni 12689
   vxlan vlan 2691 vni 12691
   vxlan vlan 2694 vni 12694
   vxlan vlan 2698 vni 12698
   vxlan vlan 2699 vni 12699
   vxlan vlan 2712 vni 12712
   vxlan vlan 2716 vni 12716
   vxlan vlan 2717 vni 12717
   vxlan vlan 2718 vni 12718
   vxlan vlan 2719 vni 12719
   vxlan vlan 2720 vni 12720
   vxlan vlan 3300 vni 13300
   vxlan vlan 3301 vni 13301
   vxlan vlan 3302 vni 13302
   vxlan vlan 3303 vni 13303
   vxlan vlan 3304 vni 13304
   vxlan vlan 3305 vni 13305
   vxlan vlan 3306 vni 13306
   vxlan vlan 3307 vni 13307
   vxlan vlan 3308 vni 13308
   vxlan vlan 3309 vni 13309
   vxlan vlan 3310 vni 13310
   vxlan vlan 3311 vni 13311
   vxlan vlan 3312 vni 13312
   vxlan vlan 3313 vni 13313
   vxlan vlan 3349 vni 13349
   vxlan vlan 3500 vni 13500
   vxlan vlan 3714 vni 13714
   vxlan vlan 3785 vni 13785
   vxlan vlan 3900 vni 13900
   vxlan vlan 3903 vni 13903
   vxlan vrf campus vni 10
   vxlan vrf wifi vni 20
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### Virtual Router MAC Address

#### Virtual Router MAC Address Summary

Virtual Router MAC Address: 00:1c:73:00:00:99

#### Virtual Router MAC Address Device Configuration

```eos
!
ip virtual-router mac-address 00:1c:73:00:00:99
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| campus | True |
| mgmt | True |
| wifi | True |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf campus
ip routing vrf mgmt
ip routing vrf wifi
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| campus | true |
| mgmt | false |
| wifi | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| mgmt | 0.0.0.0/0 | 192.168.100.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf mgmt 0.0.0.0/0 192.168.100.1
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65102 | 10.255.0.5 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

##### EVPN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | evpn |
| Source | Loopback0 |
| BFD | True |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

##### MLAG-IPv4-UNDERLAY-PEER

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Remote AS | 65102 |
| Next-hop self | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.0.1 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.0.2 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | default | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.255.8 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.255.10 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | campus | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.1.101 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | wifi | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| EVPN-OVERLAY-PEERS | True | default |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 2 | 10.255.0.5:10002 | 10002:10002 | - | - | learned |
| 8 | 10.255.0.5:10008 | 10008:10008 | - | - | learned |
| 26 | 10.255.0.5:10026 | 10026:10026 | - | - | learned |
| 48 | 10.255.0.5:10048 | 10048:10048 | - | - | learned |
| 50 | 10.255.0.5:10050 | 10050:10050 | - | - | learned |
| 84 | 10.255.0.5:10084 | 10084:10084 | - | - | learned |
| 99 | 10.255.0.5:10099 | 10099:10099 | - | - | learned |
| 108 | 10.255.0.5:10108 | 10108:10108 | - | - | learned |
| 168 | 10.255.0.5:10168 | 10168:10168 | - | - | learned |
| 214 | 10.255.0.5:10214 | 10214:10214 | - | - | learned |
| 232 | 10.255.0.5:10232 | 10232:10232 | - | - | learned |
| 233 | 10.255.0.5:10233 | 10233:10233 | - | - | learned |
| 248 | 10.255.0.5:10248 | 10248:10248 | - | - | learned |
| 313 | 10.255.0.5:10313 | 10313:10313 | - | - | learned |
| 314 | 10.255.0.5:10314 | 10314:10314 | - | - | learned |
| 315 | 10.255.0.5:10315 | 10315:10315 | - | - | learned |
| 320 | 10.255.0.5:10320 | 10320:10320 | - | - | learned |
| 322 | 10.255.0.5:10322 | 10322:10322 | - | - | learned |
| 323 | 10.255.0.5:10323 | 10323:10323 | - | - | learned |
| 331 | 10.255.0.5:10331 | 10331:10331 | - | - | learned |
| 332 | 10.255.0.5:10332 | 10332:10332 | - | - | learned |
| 351 | 10.255.0.5:10351 | 10351:10351 | - | - | learned |
| 352 | 10.255.0.5:10352 | 10352:10352 | - | - | learned |
| 375 | 10.255.0.5:10375 | 10375:10375 | - | - | learned |
| 376 | 10.255.0.5:10376 | 10376:10376 | - | - | learned |
| 384 | 10.255.0.5:10384 | 10384:10384 | - | - | learned |
| 386 | 10.255.0.5:10386 | 10386:10386 | - | - | learned |
| 387 | 10.255.0.5:10387 | 10387:10387 | - | - | learned |
| 388 | 10.255.0.5:10388 | 10388:10388 | - | - | learned |
| 395 | 10.255.0.5:10395 | 10395:10395 | - | - | learned |
| 397 | 10.255.0.5:10397 | 10397:10397 | - | - | learned |
| 401 | 10.255.0.5:10401 | 10401:10401 | - | - | learned |
| 409 | 10.255.0.5:10409 | 10409:10409 | - | - | learned |
| 421 | 10.255.0.5:10421 | 10421:10421 | - | - | learned |
| 422 | 10.255.0.5:10422 | 10422:10422 | - | - | learned |
| 426 | 10.255.0.5:10426 | 10426:10426 | - | - | learned |
| 428 | 10.255.0.5:10428 | 10428:10428 | - | - | learned |
| 472 | 10.255.0.5:10472 | 10472:10472 | - | - | learned |
| 512 | 10.255.0.5:10512 | 10512:10512 | - | - | learned |
| 513 | 10.255.0.5:10513 | 10513:10513 | - | - | learned |
| 525 | 10.255.0.5:10525 | 10525:10525 | - | - | learned |
| 540 | 10.255.0.5:10540 | 10540:10540 | - | - | learned |
| 584 | 10.255.0.5:10584 | 10584:10584 | - | - | learned |
| 600 | 10.255.0.5:10600 | 10600:10600 | - | - | learned |
| 620 | 10.255.0.5:10620 | 10620:10620 | - | - | learned |
| 634 | 10.255.0.5:10634 | 10634:10634 | - | - | learned |
| 653 | 10.255.0.5:10653 | 10653:10653 | - | - | learned |
| 804 | 10.255.0.5:10804 | 10804:10804 | - | - | learned |
| 814 | 10.255.0.5:10814 | 10814:10814 | - | - | learned |
| 815 | 10.255.0.5:10815 | 10815:10815 | - | - | learned |
| 817 | 10.255.0.5:10817 | 10817:10817 | - | - | learned |
| 1068 | 10.255.0.5:11068 | 11068:11068 | - | - | learned |
| 1107 | 10.255.0.5:11107 | 11107:11107 | - | - | learned |
| 1108 | 10.255.0.5:11108 | 11108:11108 | - | - | learned |
| 1112 | 10.255.0.5:11112 | 11112:11112 | - | - | learned |
| 1208 | 10.255.0.5:11208 | 11208:11208 | - | - | learned |
| 1226 | 10.255.0.5:11226 | 11226:11226 | - | - | learned |
| 1300 | 10.255.0.5:11300 | 11300:11300 | - | - | learned |
| 2020 | 10.255.0.5:12020 | 12020:12020 | - | - | learned |
| 2023 | 10.255.0.5:12023 | 12023:12023 | - | - | learned |
| 2326 | 10.255.0.5:12326 | 12326:12326 | - | - | learned |
| 2670 | 10.255.0.5:12670 | 12670:12670 | - | - | learned |
| 2672 | 10.255.0.5:12672 | 12672:12672 | - | - | learned |
| 2676 | 10.255.0.5:12676 | 12676:12676 | - | - | learned |
| 2677 | 10.255.0.5:12677 | 12677:12677 | - | - | learned |
| 2680 | 10.255.0.5:12680 | 12680:12680 | - | - | learned |
| 2684 | 10.255.0.5:12684 | 12684:12684 | - | - | learned |
| 2689 | 10.255.0.5:12689 | 12689:12689 | - | - | learned |
| 2691 | 10.255.0.5:12691 | 12691:12691 | - | - | learned |
| 2694 | 10.255.0.5:12694 | 12694:12694 | - | - | learned |
| 2698 | 10.255.0.5:12698 | 12698:12698 | - | - | learned |
| 2699 | 10.255.0.5:12699 | 12699:12699 | - | - | learned |
| 2712 | 10.255.0.5:12712 | 12712:12712 | - | - | learned |
| 2716 | 10.255.0.5:12716 | 12716:12716 | - | - | learned |
| 2717 | 10.255.0.5:12717 | 12717:12717 | - | - | learned |
| 2718 | 10.255.0.5:12718 | 12718:12718 | - | - | learned |
| 2719 | 10.255.0.5:12719 | 12719:12719 | - | - | learned |
| 2720 | 10.255.0.5:12720 | 12720:12720 | - | - | learned |
| 3300 | 10.255.0.5:13300 | 13300:13300 | - | - | learned |
| 3301 | 10.255.0.5:13301 | 13301:13301 | - | - | learned |
| 3302 | 10.255.0.5:13302 | 13302:13302 | - | - | learned |
| 3303 | 10.255.0.5:13303 | 13303:13303 | - | - | learned |
| 3304 | 10.255.0.5:13304 | 13304:13304 | - | - | learned |
| 3305 | 10.255.0.5:13305 | 13305:13305 | - | - | learned |
| 3306 | 10.255.0.5:13306 | 13306:13306 | - | - | learned |
| 3307 | 10.255.0.5:13307 | 13307:13307 | - | - | learned |
| 3308 | 10.255.0.5:13308 | 13308:13308 | - | - | learned |
| 3309 | 10.255.0.5:13309 | 13309:13309 | - | - | learned |
| 3310 | 10.255.0.5:13310 | 13310:13310 | - | - | learned |
| 3311 | 10.255.0.5:13311 | 13311:13311 | - | - | learned |
| 3312 | 10.255.0.5:13312 | 13312:13312 | - | - | learned |
| 3313 | 10.255.0.5:13313 | 13313:13313 | - | - | learned |
| 3349 | 10.255.0.5:13349 | 13349:13349 | - | - | learned |
| 3500 | 10.255.0.5:13500 | 13500:13500 | - | - | learned |
| 3714 | 10.255.0.5:13714 | 13714:13714 | - | - | learned |
| 3785 | 10.255.0.5:13785 | 13785:13785 | - | - | learned |
| 3900 | 10.255.0.5:13900 | 13900:13900 | - | - | learned |
| 3903 | 10.255.0.5:13903 | 13903:13903 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| campus | 10.255.0.5:10 | connected |
| wifi | 10.255.0.5:20 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65102
   router-id 10.255.0.5
   no bgp default ipv4-unicast
   maximum-paths 4 ecmp 4
   neighbor EVPN-OVERLAY-PEERS peer group
   neighbor EVPN-OVERLAY-PEERS update-source Loopback0
   neighbor EVPN-OVERLAY-PEERS bfd
   neighbor EVPN-OVERLAY-PEERS ebgp-multihop 3
   neighbor EVPN-OVERLAY-PEERS password 7 <removed>
   neighbor EVPN-OVERLAY-PEERS send-community
   neighbor EVPN-OVERLAY-PEERS maximum-routes 0
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS password 7 <removed>
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER peer group
   neighbor MLAG-IPv4-UNDERLAY-PEER remote-as 65102
   neighbor MLAG-IPv4-UNDERLAY-PEER next-hop-self
   neighbor MLAG-IPv4-UNDERLAY-PEER description SCC2
   neighbor MLAG-IPv4-UNDERLAY-PEER route-map RM-MLAG-PEER-IN in
   neighbor MLAG-IPv4-UNDERLAY-PEER password 7 <removed>
   neighbor MLAG-IPv4-UNDERLAY-PEER send-community
   neighbor MLAG-IPv4-UNDERLAY-PEER maximum-routes 12000
   neighbor 10.255.0.1 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.1 remote-as 65100
   neighbor 10.255.0.1 description Spine1_Loopback0
   neighbor 10.255.0.2 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.2 remote-as 65100
   neighbor 10.255.0.2 description Spine2_Loopback0
   neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
   neighbor 10.255.1.101 description SCC2_Vlan4093
   neighbor 10.255.255.8 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.8 remote-as 65100
   neighbor 10.255.255.8 description Spine1_Ethernet3
   neighbor 10.255.255.10 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.10 remote-as 65100
   neighbor 10.255.255.10 description Spine2_Ethernet3
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 2
      rd 10.255.0.5:10002
      route-target both 10002:10002
      redistribute learned
   !
   vlan 8
      rd 10.255.0.5:10008
      route-target both 10008:10008
      redistribute learned
   !
   vlan 26
      rd 10.255.0.5:10026
      route-target both 10026:10026
      redistribute learned
   !
   vlan 48
      rd 10.255.0.5:10048
      route-target both 10048:10048
      redistribute learned
   !
   vlan 50
      rd 10.255.0.5:10050
      route-target both 10050:10050
      redistribute learned
   !
   vlan 84
      rd 10.255.0.5:10084
      route-target both 10084:10084
      redistribute learned
   !
   vlan 99
      rd 10.255.0.5:10099
      route-target both 10099:10099
      redistribute learned
   !
   vlan 108
      rd 10.255.0.5:10108
      route-target both 10108:10108
      redistribute learned
   !
   vlan 168
      rd 10.255.0.5:10168
      route-target both 10168:10168
      redistribute learned
   !
   vlan 214
      rd 10.255.0.5:10214
      route-target both 10214:10214
      redistribute learned
   !
   vlan 232
      rd 10.255.0.5:10232
      route-target both 10232:10232
      redistribute learned
   !
   vlan 233
      rd 10.255.0.5:10233
      route-target both 10233:10233
      redistribute learned
   !
   vlan 248
      rd 10.255.0.5:10248
      route-target both 10248:10248
      redistribute learned
   !
   vlan 313
      rd 10.255.0.5:10313
      route-target both 10313:10313
      redistribute learned
   !
   vlan 314
      rd 10.255.0.5:10314
      route-target both 10314:10314
      redistribute learned
   !
   vlan 315
      rd 10.255.0.5:10315
      route-target both 10315:10315
      redistribute learned
   !
   vlan 320
      rd 10.255.0.5:10320
      route-target both 10320:10320
      redistribute learned
   !
   vlan 322
      rd 10.255.0.5:10322
      route-target both 10322:10322
      redistribute learned
   !
   vlan 323
      rd 10.255.0.5:10323
      route-target both 10323:10323
      redistribute learned
   !
   vlan 331
      rd 10.255.0.5:10331
      route-target both 10331:10331
      redistribute learned
   !
   vlan 332
      rd 10.255.0.5:10332
      route-target both 10332:10332
      redistribute learned
   !
   vlan 351
      rd 10.255.0.5:10351
      route-target both 10351:10351
      redistribute learned
   !
   vlan 352
      rd 10.255.0.5:10352
      route-target both 10352:10352
      redistribute learned
   !
   vlan 375
      rd 10.255.0.5:10375
      route-target both 10375:10375
      redistribute learned
   !
   vlan 376
      rd 10.255.0.5:10376
      route-target both 10376:10376
      redistribute learned
   !
   vlan 384
      rd 10.255.0.5:10384
      route-target both 10384:10384
      redistribute learned
   !
   vlan 386
      rd 10.255.0.5:10386
      route-target both 10386:10386
      redistribute learned
   !
   vlan 387
      rd 10.255.0.5:10387
      route-target both 10387:10387
      redistribute learned
   !
   vlan 388
      rd 10.255.0.5:10388
      route-target both 10388:10388
      redistribute learned
   !
   vlan 395
      rd 10.255.0.5:10395
      route-target both 10395:10395
      redistribute learned
   !
   vlan 397
      rd 10.255.0.5:10397
      route-target both 10397:10397
      redistribute learned
   !
   vlan 401
      rd 10.255.0.5:10401
      route-target both 10401:10401
      redistribute learned
   !
   vlan 409
      rd 10.255.0.5:10409
      route-target both 10409:10409
      redistribute learned
   !
   vlan 421
      rd 10.255.0.5:10421
      route-target both 10421:10421
      redistribute learned
   !
   vlan 422
      rd 10.255.0.5:10422
      route-target both 10422:10422
      redistribute learned
   !
   vlan 426
      rd 10.255.0.5:10426
      route-target both 10426:10426
      redistribute learned
   !
   vlan 428
      rd 10.255.0.5:10428
      route-target both 10428:10428
      redistribute learned
   !
   vlan 472
      rd 10.255.0.5:10472
      route-target both 10472:10472
      redistribute learned
   !
   vlan 512
      rd 10.255.0.5:10512
      route-target both 10512:10512
      redistribute learned
   !
   vlan 513
      rd 10.255.0.5:10513
      route-target both 10513:10513
      redistribute learned
   !
   vlan 525
      rd 10.255.0.5:10525
      route-target both 10525:10525
      redistribute learned
   !
   vlan 540
      rd 10.255.0.5:10540
      route-target both 10540:10540
      redistribute learned
   !
   vlan 584
      rd 10.255.0.5:10584
      route-target both 10584:10584
      redistribute learned
   !
   vlan 600
      rd 10.255.0.5:10600
      route-target both 10600:10600
      redistribute learned
   !
   vlan 620
      rd 10.255.0.5:10620
      route-target both 10620:10620
      redistribute learned
   !
   vlan 634
      rd 10.255.0.5:10634
      route-target both 10634:10634
      redistribute learned
   !
   vlan 653
      rd 10.255.0.5:10653
      route-target both 10653:10653
      redistribute learned
   !
   vlan 804
      rd 10.255.0.5:10804
      route-target both 10804:10804
      redistribute learned
   !
   vlan 814
      rd 10.255.0.5:10814
      route-target both 10814:10814
      redistribute learned
   !
   vlan 815
      rd 10.255.0.5:10815
      route-target both 10815:10815
      redistribute learned
   !
   vlan 817
      rd 10.255.0.5:10817
      route-target both 10817:10817
      redistribute learned
   !
   vlan 1068
      rd 10.255.0.5:11068
      route-target both 11068:11068
      redistribute learned
   !
   vlan 1107
      rd 10.255.0.5:11107
      route-target both 11107:11107
      redistribute learned
   !
   vlan 1108
      rd 10.255.0.5:11108
      route-target both 11108:11108
      redistribute learned
   !
   vlan 1112
      rd 10.255.0.5:11112
      route-target both 11112:11112
      redistribute learned
   !
   vlan 1208
      rd 10.255.0.5:11208
      route-target both 11208:11208
      redistribute learned
   !
   vlan 1226
      rd 10.255.0.5:11226
      route-target both 11226:11226
      redistribute learned
   !
   vlan 1300
      rd 10.255.0.5:11300
      route-target both 11300:11300
      redistribute learned
   !
   vlan 2020
      rd 10.255.0.5:12020
      route-target both 12020:12020
      redistribute learned
   !
   vlan 2023
      rd 10.255.0.5:12023
      route-target both 12023:12023
      redistribute learned
   !
   vlan 2326
      rd 10.255.0.5:12326
      route-target both 12326:12326
      redistribute learned
   !
   vlan 2670
      rd 10.255.0.5:12670
      route-target both 12670:12670
      redistribute learned
   !
   vlan 2672
      rd 10.255.0.5:12672
      route-target both 12672:12672
      redistribute learned
   !
   vlan 2676
      rd 10.255.0.5:12676
      route-target both 12676:12676
      redistribute learned
   !
   vlan 2677
      rd 10.255.0.5:12677
      route-target both 12677:12677
      redistribute learned
   !
   vlan 2680
      rd 10.255.0.5:12680
      route-target both 12680:12680
      redistribute learned
   !
   vlan 2684
      rd 10.255.0.5:12684
      route-target both 12684:12684
      redistribute learned
   !
   vlan 2689
      rd 10.255.0.5:12689
      route-target both 12689:12689
      redistribute learned
   !
   vlan 2691
      rd 10.255.0.5:12691
      route-target both 12691:12691
      redistribute learned
   !
   vlan 2694
      rd 10.255.0.5:12694
      route-target both 12694:12694
      redistribute learned
   !
   vlan 2698
      rd 10.255.0.5:12698
      route-target both 12698:12698
      redistribute learned
   !
   vlan 2699
      rd 10.255.0.5:12699
      route-target both 12699:12699
      redistribute learned
   !
   vlan 2712
      rd 10.255.0.5:12712
      route-target both 12712:12712
      redistribute learned
   !
   vlan 2716
      rd 10.255.0.5:12716
      route-target both 12716:12716
      redistribute learned
   !
   vlan 2717
      rd 10.255.0.5:12717
      route-target both 12717:12717
      redistribute learned
   !
   vlan 2718
      rd 10.255.0.5:12718
      route-target both 12718:12718
      redistribute learned
   !
   vlan 2719
      rd 10.255.0.5:12719
      route-target both 12719:12719
      redistribute learned
   !
   vlan 2720
      rd 10.255.0.5:12720
      route-target both 12720:12720
      redistribute learned
   !
   vlan 3300
      rd 10.255.0.5:13300
      route-target both 13300:13300
      redistribute learned
   !
   vlan 3301
      rd 10.255.0.5:13301
      route-target both 13301:13301
      redistribute learned
   !
   vlan 3302
      rd 10.255.0.5:13302
      route-target both 13302:13302
      redistribute learned
   !
   vlan 3303
      rd 10.255.0.5:13303
      route-target both 13303:13303
      redistribute learned
   !
   vlan 3304
      rd 10.255.0.5:13304
      route-target both 13304:13304
      redistribute learned
   !
   vlan 3305
      rd 10.255.0.5:13305
      route-target both 13305:13305
      redistribute learned
   !
   vlan 3306
      rd 10.255.0.5:13306
      route-target both 13306:13306
      redistribute learned
   !
   vlan 3307
      rd 10.255.0.5:13307
      route-target both 13307:13307
      redistribute learned
   !
   vlan 3308
      rd 10.255.0.5:13308
      route-target both 13308:13308
      redistribute learned
   !
   vlan 3309
      rd 10.255.0.5:13309
      route-target both 13309:13309
      redistribute learned
   !
   vlan 3310
      rd 10.255.0.5:13310
      route-target both 13310:13310
      redistribute learned
   !
   vlan 3311
      rd 10.255.0.5:13311
      route-target both 13311:13311
      redistribute learned
   !
   vlan 3312
      rd 10.255.0.5:13312
      route-target both 13312:13312
      redistribute learned
   !
   vlan 3313
      rd 10.255.0.5:13313
      route-target both 13313:13313
      redistribute learned
   !
   vlan 3349
      rd 10.255.0.5:13349
      route-target both 13349:13349
      redistribute learned
   !
   vlan 3500
      rd 10.255.0.5:13500
      route-target both 13500:13500
      redistribute learned
   !
   vlan 3714
      rd 10.255.0.5:13714
      route-target both 13714:13714
      redistribute learned
   !
   vlan 3785
      rd 10.255.0.5:13785
      route-target both 13785:13785
      redistribute learned
   !
   vlan 3900
      rd 10.255.0.5:13900
      route-target both 13900:13900
      redistribute learned
   !
   vlan 3903
      rd 10.255.0.5:13903
      route-target both 13903:13903
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY-PEERS activate
   !
   address-family ipv4
      no neighbor EVPN-OVERLAY-PEERS activate
      neighbor IPv4-UNDERLAY-PEERS activate
      neighbor MLAG-IPv4-UNDERLAY-PEER activate
   !
   vrf campus
      rd 10.255.0.5:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.255.0.5
      neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
      neighbor 10.255.1.101 description SCC2_Vlan3009
      redistribute connected route-map RM-CONN-2-BGP-VRFS
   !
   vrf wifi
      rd 10.255.0.5:20
      route-target import evpn 20:20
      route-target export evpn 20:20
      router-id 10.255.0.5
      neighbor 10.255.1.101 peer group MLAG-IPv4-UNDERLAY-PEER
      neighbor 10.255.1.101 description SCC2_Vlan3019
      redistribute connected route-map RM-CONN-2-BGP-VRFS
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 300 min-rx 300 multiplier 3
```

## Multicast

### IP IGMP Snooping

#### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

#### IP IGMP Snooping Device Configuration

```eos
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.0.0/27 eq 32 |
| 20 | permit 10.255.1.0/27 eq 32 |

##### PL-MLAG-PEER-VRFS

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.1.100/31 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.255.0.0/27 eq 32
   seq 20 permit 10.255.1.0/27 eq 32
!
ip prefix-list PL-MLAG-PEER-VRFS
   seq 10 permit 10.255.1.100/31
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |

##### RM-CONN-2-BGP-VRFS

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | deny | ip address prefix-list PL-MLAG-PEER-VRFS | - | - | - |
| 20 | permit | - | - | - | - |

##### RM-MLAG-PEER-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | origin incomplete | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-CONN-2-BGP-VRFS deny 10
   match ip address prefix-list PL-MLAG-PEER-VRFS
!
route-map RM-CONN-2-BGP-VRFS permit 20
!
route-map RM-MLAG-PEER-IN permit 10
   description Make routes learned over MLAG Peer-link less preferred on spines to ensure optimal routing
   set origin incomplete
```

## ACL

### IP Access-lists

#### IP Access-lists Device Configuration

```eos
!
ip access-list mgmt-acl
   10 permit ip 192.168.1.0/24 any
   20 permit ip 192.168.3.0/24 any
   30 permit ip 192.168.100.0/24 any
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| campus | enabled |
| mgmt | enabled |
| wifi | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance campus
!
vrf instance mgmt
!
vrf instance wifi
```

## Virtual Source NAT

### Virtual Source NAT Summary

| Source NAT VRF | Source NAT IP Address |
| -------------- | --------------------- |
| campus | 10.255.10.5 |
| wifi | 10.255.11.5 |

### Virtual Source NAT Configuration

```eos
!
ip address virtual source-nat vrf campus address 10.255.10.5
ip address virtual source-nat vrf wifi address 10.255.11.5
```
