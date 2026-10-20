
---

## 2. `configs/GW-MAIN-01.md`

```markdown
# GW-MAIN-01 — Router-PT (эмуляция Cisco ISR)

**Роль:** граничный маршрутизатор, Router-on-a-stick (4 подынтерфейса),
DHCP-сервер для всех VLAN, расширенные ACL 120/140

## Что настроено

- Подынтерфейсы `Gi0/0.10`, `Gi0/0.20`, `Gi0/0.30`, `Gi0/0.40` — шлюзы VLAN 10/20/30/40
  с инкапсуляцией `dot1Q`, `ip nat inside`
- DHCP-пулы для четырёх подсетей, исключены адреса `.1`–`.10`
- Расширенные ACL:
  - **120** на `Gi0/0.30` — блокирует гостям доступ в VLAN 10/20/40, разрешает any
  - **140** на `Gi0/0.40` — блокирует IoT доступ в VLAN 10/20, разрешает any
- NAT inside на всех подынтерфейсах

## Конфигурация

```cisco
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname GW-MAIN-01
!
!
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 ip nat inside
!
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 ip access-group 120 in
 ip nat inside
!
interface GigabitEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
 ip access-group 140 in
 ip nat inside
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
ip nat inside source list 1 interface GigabitEthernet0/1 overload
ip classless
ip route 0.0.0.0 0.0.0.0 203.0.113.1
!
ip flow-export version 9
!
!
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.40.1 192.168.40.10
!
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN40
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 8.8.8.8
!
!
!
!
access-list 120 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 120 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 120 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
access-list 120 permit ip 192.168.30.0 0.0.0.255 any
access-list 140 deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 140 deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 140 permit ip 192.168.40.0 0.0.0.255 any
!
!
!
!
!
!
!
!
!
!
!
!
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
