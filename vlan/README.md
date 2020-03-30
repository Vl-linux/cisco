# Настройка VTP Настройка DTP Добавление сетей VLAN и назначение портов Настройка расширенной сети VLAN

## Настройка коммутаторов на использование VTP для обновлений сетей VLAN. sw1 настраивается в качестве сервера.  sw2 и sw3 настраиваются как клиенты.

![](https://github.com/Vl-linux/network/blob/master/vlan/sh1.png)

подробное выполнение в консоли по каждому коммутатору сохранено в log-файлах

#### Настройка sw1 в качестве сервера VTP:

```
sw1(config)#vtp domain ccna
Changing VTP domain name from NULL to ccna
sw1(config)#vtp mode server
Device mode already VTP Server for VLANS.
sw1(config)#vtp password cisco
Setting device VTP password to cisco
sw1(config)#exit
sw1#show vtp status
VTP Version capable             : 1 to 3
VTP version running             : 1
VTP Domain Name                 : ccna
VTP Pruning Mode                : Disabled
VTP Traps Generation            : Disabled
Device ID                       : 0008.302d.4580
Configuration last modified by 0.0.0.0 at 0-0-00 00:00:00
Local updater ID is 0.0.0.0 (no valid interface found)
Feature VLAN:
--------------
VTP Operating Mode                : Server
Maximum VLANs supported locally   : 255
Number of existing VLANs          : 5
Configuration Revision            : 0
MD5 digest                        : 0xB7 0xF1 0x21 0x12 0x5F 0x32 0x9E 0x96 
                                    0x8A 0x0E 0x9E 0xDA 0xB8 0xAF 0xED 0x4C 
*** MD5 digest checksum mismatch on trunk: Fa0/1 ***
*** MD5 digest checksum mismatch on trunk: Fa0/2 ***
```

### Настройка sw2 в качестве клиента VTP:

```
sw2(config)#vtp domain ccna
Domain name already set to ccna.
sw2(config)#vtp mode client 
Setting device to VTP CLIENT mode.
sw2(config)#vtp password cisco
Setting device VLAN database password to cisco
sw2(config)#^Z
sw2#show vtp status
VTP Version                     : 2
Configuration Revision          : 0
Maximum VLANs supported locally : 250
Number of existing VLANs        : 5
VTP Operating Mode              : Client
VTP Domain Name                 : ccna
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0xB7 0xF1 0x21 0x12 0x5F 0x32 0x9E 0x96 
Configuration last modified by 0.0.0.0 at 0-0-00 00:00:00
```

### Настройка sw3 в качестве клиента VTP:

```
sw3(config)#vtp domain ccna
Domain name already set to ccna.
sw3(config)#vtp mode client
Setting device to VTP CLIENT mode.
sw3(config)#vtp pass
sw3(config)#vtp password cisco
Setting device VLAN database password to cisco
sw3#show vtp status
VTP Version                     : 2
Configuration Revision          : 0
Maximum VLANs supported locally : 250
Number of existing VLANs        : 5
VTP Operating Mode              : Client
VTP Domain Name                 : ccna
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0xB7 0xF1 0x21 0x12 0x5F 0x32 0x9E 0x96 
Configuration last modified by 0.0.0.0 at 0-0-00 00:00:00
```

## Настройка динамического протокола транкинга (DTP)


### Настройка динамического магистрального канала между sw1 и sw2 и статического магистрального канал между sw2 и sw3:

```
sw1#show interfaces f0/3 switchport 
Name: Fa0/3
Switchport: Enabled
Administrative Mode: dynamic auto
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1 (default)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk associations: none
Administrative private-vlan trunk mappings: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none

sw1#show interfaces trunk
Port        Mode             Encapsulation  Status        Native vlan
Fa0/1       auto             802.1q         trunking      1
Fa0/2       auto             802.1q         trunking      1
Fa0/3       auto             802.1q         trunking      1
Fa0/4       auto             802.1q         trunking      1
Port        Vlans allowed on trunk
Fa0/1       1-4094
Fa0/2       1-4094
Fa0/3       1-4094
Fa0/4       1-4094
Port        Vlans allowed and active in management domain
Fa0/1       1
Fa0/2       1
Fa0/3       1
Fa0/4       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1
Fa0/2       1
Fa0/3       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/4       1

sw2(config)#interface f0/1
sw2(config-if)#switchport mode trunk 

sw2#show interface trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1
Fa0/2       desirable    802.1q         trunking      1
Fa0/3       desirable    802.1q         trunking      1
Fa0/4       desirable    802.1q         trunking      1
Port      Vlans allowed on trunk
Fa0/1       1-4094
Fa0/2       1-4094
Fa0/3       1-4094
Fa0/4       1-4094
Port        Vlans allowed and active in management domain
Fa0/1       1
Fa0/2       1
Fa0/3       1
Fa0/4       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       none
Fa0/2       none
Fa0/3       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/4       none

sw3#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       desirable    802.1q         trunking      1
Fa0/2       desirable    802.1q         trunking      1
Fa0/3       desirable    802.1q         trunking      1
Fa0/4       desirable    802.1q         trunking      1
Port      Vlans allowed on trunk
Fa0/1       1-4094
Fa0/2       1-4094
Fa0/3       1-4094
Fa0/4       1-4094
Port        Vlans allowed and active in management domain
Fa0/1       1
Fa0/2       1
Fa0/3       1
Fa0/4       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1
Fa0/2       1
Fa0/3       1
Port        Vlans in spanning tree forwarding state and not pruned
Fa0/4       none
```

## Добавление сетей VLAN и назначение портов:

### добавление сеей VLAN выполяетя на коммутаторе sw1:

```
sw1(config)#vlan 10
sw1(config-vlan)#name red
sw1(config-vlan)#vlan 20
sw1(config-vlan)#name blu
sw1(config-vlan)#vlan 30
sw1(config-vlan)#name yel
sw1(config-vlan)#vlan 99
sw1(config-vlan)#name man
sw1(config-vlan)#end

sw1#show vlan brief 
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gi0/1, Gi0/2
10   red                              active    
20   blu                              active    
30   yel                              active    
99   man                              active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
```

### проверка обновления VTP на коммутаторах sw2 и sw3 которые настроены как VTP-клиенты:

```
sw3#show vlan brief
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gi0/1, Gi0/2
10   red                              active    
20   blu                              active    
30   yel                              active    
99   man                              active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

sw2#show vlan brief
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gi0/1, Gi0/2
10   red                              active    
20   blu                              active    
30   yel                              active    
99   man                              active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
```

### Назначение портов сетям VLAN.

#### На коммутаторе sw1 переводим порт F0/15 в режим доступа и назначаем его сети VLAN 20
```
sw1(config)#interface f0/15
sw1(config-if)#switchport mode access 
sw1(config-if)#switchport access vlan 20
```

#### На коммутаторвх sw2 sw3 переводим порт F0/15 в режим доступа и назначаем его сети VLAN 10

```
sw2(config)#interface f0/15
sw2(config-if)#switchport mode access 
sw2(config-if)#switchport access vlan 10

sw3(config)#interface f0/15
sw3(config-if)#switchport mode access 
sw3(config-if)#switchport access vlan 10
```

#### Настройка IP-адреса на коммутаторах для сети VLAN 99

```
sw1(config)#interface vlan 99
sw1(config-if)#ip address 192.168.99.1 255.255.255.0
sw1(config-if)#no shutdown 

sw2(config)#interface vlan 99
sw2(config-if)#ip address 192.168.99.2 255.255.255.0
sw2(config-if)#no shutdown 

sw3(config)#interface vlan 99
sw3(config-if)#ip address 192.168.99.3 255.255.255.0
sw3(config-if)#no shutdown 
```

### Настройка сети VLAN расширенного диапазона:

#### Перевод VTP на коммутаторе sw2 в прозрачный режим

```
sw2(config)#vtp mode transparent 
Setting device to VTP TRANSPARENT mode.
```

#### проверка режима VTP 

```
sw2#sh vtp status
VTP Version                     : 2
Configuration Revision          : 0
Maximum VLANs supported locally : 250
Number of existing VLANs        : 9
VTP Operating Mode              : Transparent
VTP Domain Name                 : ccna
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0xC7 0x94 0x13 0xB0 0x34 0xFB 0x11 0x35 
Configuration last modified by 0.0.0.0 at 3-1-93 01:21:29
sw2#
```

### Создание сети VLAN 2000 расширенного диапазона

```
sw2(config)#vlan 2000
sw2(config-vlan)#end
```

#### проверка

```
sw2#sh vlan brief 
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/16, Fa0/17
                                                Fa0/18, Fa0/19, Fa0/20, Fa0/21
                                                Fa0/22, Fa0/23, Fa0/24, Gi0/1
                                                Gi0/2
10   red                              active    Fa0/15
20   blu                              active    
30   yel                              active    
99   man                              active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
2000 VLAN2000                         active    
```


### Проверка наличия сквозного соединения:

```
sw1>ping 192.168.20.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw1>ping 192.168.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.2, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw1>ping 192.168.10.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.3, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw1>ping 192.168.99.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/4/9 ms
sw1>ping 192.168.99.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/5/9 ms
sw1>


sw2>ping 192.168.20.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw2>ping 192.168.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.2, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw2>ping 192.168.10.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.3, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw2>ping 192.168.99.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/4 ms
sw2>ping 192.168.99.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/4 ms
sw2>


sw3>ping 192.168.20.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw3>ping 192.168.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.2, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw3>ping 192.168.10.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.3, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
sw3>ping 192.168.99.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/8 ms
sw3>ping 192.168.99.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.99.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/4 ms
sw3>
```
![](https://github.com/Vl-linux/network/blob/master/vlan/pc1.png)

![](https://github.com/Vl-linux/network/blob/master/vlan/pc2.png)

![](https://github.com/Vl-linux/network/blob/master/vlan/pc3.png)
