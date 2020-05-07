## Развертывание коммутируемой сети с резервными каналами


![](https://github.com/Vl-linux/network/blob/master/stp/topology2.png)


### Создание сети и настройка основных параметров устройства

#### Настройка базовых параметров каждого коммутатора

Отключение поиска DNS
Присвоение имени устройствам в соответствии с топологией
logging synchronous - запрещает вывод консольных сообщений, которые могут прервать ввод команд в консольном режиме (по умолчанию не включена)
copy running-config startup-config - копирование файла конфигурации в NVRAM, позволяет обеспечить сохранение сделанных настроек при перезагрузке системы

```
Switch>en
Switch#conf t  
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname s1
s1(config)#no ip  domain-lookup 
s1(config)#line console 0
s1(config-line)#logging synchronous 
s1(config-line)#exit
s1(config)#interface vlan 1
s1(config-if)#ip address 192.168.1.1 255.255.255.0 
s1(config-if)#no shutdown 
s1(config-if)#exit
s1(config)#exit
s1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
s1#
```

```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname s2
s2(config)#no ip domain-lookup 
s2(config)#line console 0
s2(config-line)#logging synchronous 
s2(config-line)#exit
s2(config)#interface vlan 1
s2(config-if)#ip address 192.168.1.2 255.255.255.0
s2(config-if)#no shutdown 
s2(config-if)#exit
s2(config)#exit
s2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
s2#
```

```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname s3
s3(config)#no ip  domain-lookup 
s3(config)#line console 0
s3(config-line)#logging synchronous 
s3(config-line)#exit
s3(config)#interface vlan 1
s3(config-if)#ip address 192.168.1.3 255.255.255.0
s3(config-if)#no shutdown 
s3(config-if)#exit
s3(config)#exit
s3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
s3#

```

##### Проверка

```
s1#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/2/8 ms
s1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/3/9 ms
```

```
s2#ping 192.168.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
s2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/3/4 ms
s2#
```

```
s3#ping 192.168.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/8 ms
s3#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/4 ms
```

Эхо-запрос от всех коммутаторов выполняется успешно!


### Определение корневого моста

#### Отключение всех портов на коммутаторах. Настройка подключенных портов в качестве транковых.

```
s1(config)#interface range fastEthernet 0/1 - 24
s1(config-if-range)#shutdown 
s1(config-if-range)#switchport mode trunk 
```

```
s2(config)#interface range fastEthernet 0/1 - 24
s2(config-if-range)#shutdown 
s2(config-if-range)#exit
s2(config)#interface range fastEthernet 0/1 - 4
s2(config-if-range)#switchport mode trunk 
```

```
s3(config)#interface range fastEthernet 0/1 - 24
s3(config-if-range)#shutdown 
s3(config-if-range)#exit
s3(config)#interface range fastEthernet 0/1 - 4 
s3(config-if-range)#switchport mode trunk 
```

#### Включение портов F0/2 и F0/4 на всех коммутаторах.

```
s1(config)#interface fastEthernet 0/2
s1(config-if)#no shutdown 
*Mar  1 01:31:02.879: %LINK-3-UPDOWN: Interface FastEthernet0/2, changed state to up
s1(config-if)#exit
*Mar  1 01:31:06.461: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up
s1(config)#interface fastEthernet 0/4
s1(config-if)#no shutdown 
*Mar  1 01:31:16.041: %LINK-3-UPDOWN: Interface FastEthernet0/4, changed state to up
s1(config-if)#
```

```
s2(config)#interface fastEthernet 0/2
s2(config-if)#no shutdown 
01:34:43: %LINK-3-UPDOWN: Interface FastEthernet0/2, changed state to up
s2(config-if)#exit
01:34:46: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up
s2(config)#interface fastEthernet 0/4
s2(config-if)#no shutdown               
s2(config-if)#exit
01:35:00: %LINK-3-UPDOWN: Interface FastEthernet0/4, changed state to up
01:35:04: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to up
```

```
s3(config)#interface range fastEthernet 0/2    
s3(config-if-range)#no shutdown 
01:46:55: %LINK-3-UPDOWN: Interface FastEthernet0/2, changed state to up
01:46:58: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up
s3(config-if-range)#exit                            
s3(config)#interface fastEthernet 0/4      
s3(config-if)#no shutdown 
01:47:34: %LINK-3-UPDOWN: Interface FastEthernet0/4, changed state to up
01:47:38: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to up
```

#### Отображение данных протокола spanning-tree. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.

```
s1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0008.302d.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p 
s1#
```

##### Определите коммутатор с заблокированным портом.

```
s2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s2#
```

```
s3# show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s3#
```

Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. 
(Расширенным идентификатором системы является номер сети VLAN)
В данный момент все три коммутатора имеют равные значения приоритета идентификатора моста поэтому коммутатор с самым низким значением MAC-адреса становится корневым мостом
- Исходя из данных коммутаторов, коммутатор S1 является корневым мостом.
- Корневыми портами являются порты Fa0/4 на коммутаторах S2 и S3.
- Назначенные порты: Fa0/2 и Fa0/4 на комутаторе S1 и порт Fa0/2 а комутаторе S3.
- На коммутаторе S2 порт Fa0/2 является альтернативным и в настоящее время заблокирован.


#### Измените стоимость порта

```
s2(config)#interface fastEthernet 0/4
s2(config-if)#spanning-tree cost 18
```

#### Просмотр изменения протокола spanning-tree

```
s2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        18
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 15 
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg LRN 19        128.2    P2p 
Fa0/4            Root FWD 18        128.4    P2p 
s2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        18
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/4            Root FWD 18        128.4    P2p 
s2#
```

```
s3#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s3#
```

При изменении стоимости порта Fa0/4 на комутаторе S2, cтоимость корневого пути меняется и протокол блокирует порт Fa0/2 уже на комутаторе S3.


#### Удаление изменения стоимости порта

```
s2(config)#interface fastEthernet 0/4
s2(config-if)#no spanning-tree cost 18  
s2(config-if)#exit
s2#show spanning-tree 
02:09:02: %SYS-5-CONFIG_I: Configured from console by console
s2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 15 
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s2#
```

```
s3#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 15 
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/4            Root FWD 19        128.4    P2p 
s3#
```

#### Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

```
s1(config)#interface fastEthernet 0/1
s1(config-if)#no shutdown         
*Mar  1 02:13:29.971: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to down
s1(config-if)#exit
s1(config)#interface fastEthernet 0/3
s1(config-if)#no shutdown               
s1(config-if)#exit                      
*Mar  1 02:13:51.764: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to down
*Mar  1 02:14:40.469: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to up
*Mar  1 02:14:42.473: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
*Mar  1 02:15:26.170: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
*Mar  1 02:15:28.191: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
s1(config)#exit
s1#
*Mar  1 02:17:47.795: %SYS-5-CONFIG_I: Configured from console by console
s1#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0008.302d.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/1               Desg FWD 19        128.1    P2p 
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/3               Desg FWD 19        128.3    P2p 
Fa0/4               Desg FWD 19        128.4    P2p 
s1#
```


```
s2(config)#interface fastEthernet 0/1
s2(config-if)#no shutdown 
02:14:10: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to down
s2(config-if)#exit
s2(config)#interface fastEthernet 0/3
s2(config-if)#no shutdown 
s2(config-if)#exit
02:14:30: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to up
02:14:34: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
02:14:59: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
02:15:01: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
s2(config)#exit
02:16:44: %SYS-5-CONFIG_I: Configured from console by console
s2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        3 (FastEthernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0015.fac1.4500
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Altn BLK 19        128.1    P2p 
Fa0/2            Altn BLK 19        128.2    P2p 
Fa0/3            Root FWD 19        128.3    P2p 
Fa0/4            Altn BLK 19        128.4    P2p 
s2#
```


```
s3(config)#interface fastEthernet 0/1
s3(config-if)#no shutdown 
s3(config-if)#exit
02:22:28: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
s3(config)#exit
02:22:32: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
s3(config)#interface fastEthernet 0/3
s3(config-if)#no shutdown 
s3(config-if)#exit
02:22:46: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to up
s3(config)#
02:22:50: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
s3(config)#exit
s3#
02:24:44: %SYS-5-CONFIG_I: Configured from console by console
s3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0008.302d.4580
             Cost        19
             Port        3 (FastEthernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0011.2142.4580
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time 300
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Desg FWD 19        128.1    P2p 
Fa0/2            Desg FWD 19        128.2    P2p 
Fa0/3            Root FWD 19        128.3    P2p 
Fa0/4            Altn BLK 19        128.4    P2p 
s3#
```

- Протоколом STP в качестве порта корневого моста выбраны порты Fa0/3 на S2 и S3 коммутаторах некорневого моста.
- Состояния портов определяются по стоимости пути к корневому мосту. 
- Если стоимости портов равны, процесс сравнивает BID. 
- Если BID равны, для определения корневого моста используются приоритеты портов.
