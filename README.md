# Cisco Nexus Cheet Sheet

## VRF
```
vrf context secure
  ip route 0.0.0.0/0 10.35.204.1

! Configure physical interface as trunk
interface Ethernet1/48
  no shutdown
  no switchport
  mtu 1500
  ! no IP here — only subinterfaces carry IPs

! Subinterface for secure VRF
interface Ethernet1/48.204
  encapsulation dot1Q 204
  vrf member secure
  ip address 10.35.204.2/30
```

## OSPF 
### Setup OSPF instance and associate it with a VRF
```
router ospf 204
  vrf secure
    router-id 35.0.0.204
    passive-interface default
```

### Configure uplink interface
```
! Subinterface for secure VRF
interface Ethernet1/48.204
  encapsulation dot1Q 204
  vrf member secure
  ip address 10.35.204.2/30
  mtu 1500
  no shutdown
  ip ospf network point-to-point
  ip router ospf 204 area 0.0.0.0
  no ip ospf passive-interface
```

### OSPF internal gateway virtual interface, enable dhcp relay
```
interface Vlan140
  vrf member secure
  ip address 10.35.140.254/24
  ip dhcp relay address 10.35.204.1
  mtu 1500
  no shutdown
  ip router ospf 204 area 35.0.0.204

interface Vlan148
  description secure-nfs
  vrf member secure
  ip address 10.35.148.254/24
  ip dhcp relay address 10.35.204.1
  mtu 9216
  no shutdown
  ip router ospf 204 area 35.0.0.204
```

### Verify
```
show ip ospf 204 vrf secure
show running-config interface Vlan140
show ip dhcp relay
```
