# Cisco Discovery Day Lab - Inter-VLAN Routing Setup

This documentation outlines the configuration completed during a technical interview Discovery Day with classmates. We implemented a basic inter-VLAN routing setup using a switch and router.

## Network Topology

Our setup consists of:
- One Cisco switch (SW01)
- One Cisco router (RT01) configured as "Router on a Stick"
- Two client PCs in separate VLANs

![Network Topology](/images/topology.png)

## IP Addressing Scheme

| Device | Interface | IP Address | VLAN |
|--------|-----------|------------|------|
| PC1 | Network Adapter | 10.10.10.2/24 | 10 |
| PC2 | Network Adapter | 10.10.20.2/24 | 20 |
| Router (RT01) | GigabitEthernet0/0/0.10 | 10.10.10.1/24 | 10 |
| Router (RT01) | GigabitEthernet0/0/0.20 | 10.10.20.1/24 | 20 |

## Switch Configuration (SW01)

```cisco
! Enter privileged EXEC mode
enable

! Enter configuration mode
configure terminal

! Create and name VLAN 10
vlan 10
name LT1_VLAN
exit

! Create and name VLAN 20
vlan 20
name LT2_VLAN
exit

! Configure port for PC1 (VLAN 10)
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit

! Configure port for PC2 (VLAN 20)
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit

! Configure trunk port to router
interface fastEthernet 0/3
switchport mode trunk
switchport trunk encapsulation dot1q
switchport trunk allowed vlan 10,20
exit

! Save configuration
end
write memory
```

## Router Configuration (RT01)

```cisco
! Enter privileged EXEC mode
enable

! Enter configuration mode
configure terminal

! Enable the physical interface
interface GigabitEthernet0/0/0
no shutdown
exit

! Configure subinterface for VLAN 10
interface GigabitEthernet0/0/0.10
encapsulation dot1Q 10
ip address 10.10.10.1 255.255.255.0
exit

! Configure subinterface for VLAN 20
interface GigabitEthernet0/0/0.20
encapsulation dot1Q 20
ip address 10.10.20.1 255.255.255.0
exit

! Save configuration
end
write memory
```

## Verification Tests

| Source | Destination | Command | Expected Result |
|--------|-------------|---------|----------------|
| PC1 | PC1 | ping 10.10.10.2 | Success |
| PC1 | Router VLAN10 | ping 10.10.10.1 | Success |
| PC1 | PC2 | ping 10.10.20.2 | Success |
| PC2 | PC2 | ping 10.10.20.2 | Success |
| PC2 | Router VLAN20 | ping 10.10.20.1 | Success |
| PC2 | PC1 | ping 10.10.10.2 | Success |

## Key Learning Points

1. VLANs allow logical separation of networks on a single physical switch
2. "Router on a Stick" configuration enables inter-VLAN communication through a single router interface
3. Proper encapsulation (dot1q) is required for VLAN traffic to traverse trunk links
4. Default gateways must be properly configured on client devices to enable cross-VLAN communication
