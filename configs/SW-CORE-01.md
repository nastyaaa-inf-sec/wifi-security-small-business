# SW-CORE-01 — Cisco Catalyst 2960-24TT

**Роль:** центральный коммутатор L2, VLAN 10/20/30/40, транки к WLC и точкам доступа


## Что настроено

- 4 VLAN: Management (10), Staff (20), Guests (30), IoT_Devices (40)
- **Транковые порты:**
  - `Fa0/1` — к маршрутизатору `GW-MAIN-01`, native VLAN 1
  - `Fa0/3`, `Fa0/4`, `Fa0/5` — к точкам `AP-OFFICE-01`, `AP-WAREHOUSE-01`, `AP-GUEST-01`, native VLAN 10
- **Access-порты:**
  - `Fa0/2` — WLC `WLC-MAIN-01` (VLAN 10)
  - `Fa0/7` — RADIUS `SRV-RADIUS-01` (VLAN 20)
  - `Fa0/8` — `ADMIN-PC` (VLAN 10)
  - `Fa0/9` — принтер `PRN-OFFICE-01` (VLAN 20)
  - `Fa0/10` — автономная `AP-DEMO-01` (VLAN 20)
  - `Fa0/11` — IP-телефон `VOIP-01` (VLAN 40)
- PortFast на всех access-портах

## Конфигурация

```cisco
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW-CORE-01
!
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan 10
 name Management
!
vlan 20
 name Staff
!
vlan 30
 name Guests
!
vlan 40
 name IoT_Devices
!
interface FastEthernet0/1
 switchport trunk allowed vlan 10,20,30,40
 switchport mode trunk
!
interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/3
 switchport trunk allowed vlan 10,20,30,40
 switchport mode trunk
 switchport trunk native vlan 10
!
interface FastEthernet0/4
 switchport trunk allowed vlan 10,20,30,40
 switchport mode trunk
 switchport trunk native vlan 10
!
interface FastEthernet0/5
 switchport trunk allowed vlan 10,20,30,40
 switchport mode trunk
 switchport trunk native vlan 10
!
interface FastEthernet0/7
 switchport access vlan 20
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/8
 switchport access vlan 10
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/9
 switchport access vlan 20
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/10
 switchport access vlan 20
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/11
 switchport access vlan 40
 switchport mode access
 spanning-tree portfast
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
