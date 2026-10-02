# Secure Enterprise Network & SOC Lab

A practical **Secure Enterprise Network + SOC monitoring lab** built in **EVE-NG** using Cisco IOS devices, a Windows 7 Syslog server running Kiwi Syslog Server, and Kali Linux for security validation.

## Project Overview
This project simulates a segmented enterprise network with redundant Layer-3 core infrastructure and multiple security controls.

### Implemented
- VLAN segmentation and inter-VLAN routing
- HSRP gateway redundancy
- OSPF dynamic routing
- LACP EtherChannel
- Rapid-PVST
- Centralized DHCP
- NTP
- SSH
- Port Security
- DHCP Snooping
- Dynamic ARP Inspection (DAI)
- Guest VLAN ACL isolation
- IDS
- Centralized Syslog with Kiwi Syslog Server

## Architecture

```text
                         INTERNET
                            |
                           ISP
                            |
                           EDGE
                     ______/  \______
                    /                \
                 CORE1  ========  CORE2
                    |   LACP Po1     |
              ______|________________|______
             |       |        |       |
            SW1     SW2      SW3     SW4
           /  \      |      /   \      |
          HR SALES   IT    SOC  SYSLOG  GUEST
                              |      |
                         SERVER-RTR  Windows 7
                         DHCP/NTP    Kiwi Syslog
```

## VLAN Plan

| VLAN | Name | Network | Gateway |
|---:|---|---|---|
| 10 | HR | 10.10.10.0/24 | 10.10.10.1 |
| 20 | SALES | 10.10.20.0/24 | 10.10.20.1 |
| 30 | IT | 10.10.30.0/24 | 10.10.30.1 |
| 40 | SERVER | 10.10.40.0/24 | 10.10.40.1 |
| 50 | GUEST | 10.10.50.0/24 | 10.10.50.1 |
| 60 | SOC | 10.10.60.0/24 | 10.10.60.1 |
| 99 | MANAGEMENT | 10.10.99.0/24 | 10.10.99.1 |

## Infrastructure

| Link/Device | Address |
|---|---|
| ISP Gi0/0 | 203.0.113.1/30 |
| EDGE Gi0/0 | 203.0.113.2/30 |
| EDGE Gi0/1 | 10.255.0.1/30 |
| CORE1 Gi0/0 | 10.255.0.2/30 |
| EDGE Gi0/2 | 10.255.0.5/30 |
| CORE2 Gi0/0 | 10.255.0.6/30 |
| CORE1 Po1 | 10.255.0.9/30 |
| CORE2 Po1 | 10.255.0.10/30 |
| SERVER-RTR | 10.10.40.10/24 |
| Windows 7 / Kiwi Syslog | 10.10.40.20/24 |

## Security Workflow

```text
Traffic
  ↓
Security Controls / IDS
  ↓
Detection Event
  ↓
Cisco Syslog
  ↓ UDP 514
Kiwi Syslog Server
  ↓
SOC Investigation
```

## Security Controls

| Control | Purpose |
|---|---|
| Port Security | Restricts unauthorized MAC addresses on access ports |
| DHCP Snooping | Helps prevent rogue DHCP servers and builds bindings |
| Dynamic ARP Inspection | Validates ARP using DHCP Snooping bindings |
| BPDU Guard + PortFast | Protects end-device ports from unexpected STP BPDUs |
| Guest ACL | Blocks Guest access to protected internal VLANs |
| IDS | Detects configured suspicious/security traffic |
| Centralized Syslog | Collects Cisco events in one monitoring location |
| SSH | Secure remote administration on designated devices |

## Routing & Redundancy
- OSPF Area 0
- EDGE router ID: `3.3.3.3`
- CORE1 router ID: `1.1.1.1`
- CORE2 router ID: `2.2.2.2`
- HSRP virtual gateways
- LACP EtherChannel between CORE1 and CORE2
- Rapid-PVST
- CORE1 STP root primary; CORE2 secondary

## Services
- DHCP: SERVER-RTR
- NTP master: SERVER-RTR
- Centralized Syslog: Windows 7 / Kiwi Syslog
- SSH: ISP and EDGE
- IDS: configured and validated

## Validation
All major components were tested successfully: inter-VLAN connectivity, HSRP, OSPF, EtherChannel, DHCP, NTP, Port Security, DHCP Snooping, DAI, Guest isolation, centralized Syslog, and IDS.

## Useful Commands

```cisco
show ip interface brief
show ip ospf neighbor
show ip route ospf
show standby brief
show etherchannel summary
show spanning-tree
show ip dhcp binding
show ip dhcp pool
show ntp status
show logging
show port-security
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection statistics
show ip ips configuration
show ip ips interfaces
show ip ips statistics
```

**Important:** remove passwords, private keys, tokens and other secrets before publishing configurations.


## License

This project is licensed under the [MIT License](LICENSE).
