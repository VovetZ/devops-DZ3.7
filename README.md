# devops-DZ3.7



>1.    Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
### Ответ
Для Linux
- ifconfig (считается устаревшим) 
```
vk@vk-desktop:~$ ifconfig -a
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:4e:47:1d:48  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eno1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.106  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::f7b0:95f2:c528:a9bb  prefixlen 64  scopeid 0x20<link>
        ether 00:22:4d:9a:2c:aa  txqueuelen 1000  (Ethernet)
        RX packets 633948  bytes 575506181 (575.5 MB)
        RX errors 0  dropped 14  overruns 0  frame 0
        TX packets 162568  bytes 24836289 (24.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 20  memory 0xfb300000-fb320000  

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 3795  bytes 1347204 (1.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3795  bytes 1347204 (1.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```
-  ip -br link show 
```
vk@vk-desktop:~$ ip -br link show 
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP> 
eno1             UP             00:22:4d:9a:2c:aa <BROADCAST,MULTICAST,UP,LOWER_UP> 
docker0          DOWN           02:42:4e:47:1d:48 <NO-CARRIER,BROADCAST,MULTICAST,UP> 
```
- nmcli device status
```
vk@vk-desktop:~$ nmcli device status
DEVICE   TYPE      STATE                   CONNECTION         
eno1     ethernet  connected               Wired connection 1 
docker0  bridge    connected (externally)  docker0            
lo       loopback  unmanaged               --     
```
- netstat -i
```
vk@vk-desktop:~$ netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
docker0   1500        0      0      0 0             0      0      0      0 BMU
eno1      1500   634501      0     14 0        163040      0      0      0 BMRU
lo       65536     3833      0      0 0          3833      0      0      0 LRU
```
- В файле /proc/net/dev тоже содержится список всех сетевых интерфейсов, а также статистика их использования:
```
vk@vk-desktop:~$ cat /proc/net/dev
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo: 1355572    3819    0    0    0     0          0         0  1355572    3819    0    0    0     0       0          0
  eno1: 575578719  634288    0   14    0     0          0     12317 24878050  162861    0    0    0     0       0          0
docker0:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
```
- networkctl list
```
vk@vk-desktop:~$ networkctl list
WARNING: systemd-networkd is not running, output will be incomplete.

IDX LINK    TYPE     OPERATIONAL SETUP    
  1 lo      loopback n/a         unmanaged
  2 eno1    ether    n/a         unmanaged
  3 docker0 bridge   n/a         unmanaged

3 links listed.
```

Для Windows мне известна только команда`ipconfig /all`

>2.    Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
### Ответ
Используется протокол Link Layer Discovery Protocol (LLDP) — протокол канального уровня, позволяющий сетевому оборудованию оповещать локальную сеть о своем существовании и характеристиках, а также собирать такие же оповещения, поступающие от соседнего оборудования. Протокол формально утвержден как IEEE standard 802.1AB-2009, в сентябре 2009 года, и является независимой от производителей сетевого оборудования заменой их патентованным протоколам, таким как Cisco Discovery Protocol, Extreme Discovery Protocol, Foundry Discovery Protocol и Nortel Discovery Protocol (последний также известен как SONMP).

Правда, ничего интересного на домашней Ubuntu 22.04 я не обнаружил (имя пакета и команды приведены ниже)
```bash
vk@vk-desktop:~$ sudo apt install lldpd
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  snmpd
The following NEW packages will be installed:
  lldpd
0 upgraded, 1 newly installed, 0 to remove and 50 not upgraded.
Need to get 193 kB of archives.
After this operation, 558 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu jammy/universe amd64 lldpd amd64 1.0.13-1 [193 kB]
Fetched 193 kB in 1s (213 kB/s) 
Selecting previously unselected package lldpd.
(Reading database ... 356397 files and directories currently installed.)
Preparing to unpack .../lldpd_1.0.13-1_amd64.deb ...
Unpacking lldpd (1.0.13-1) ...
Setting up lldpd (1.0.13-1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/lldpd.service → /lib/systemd/system/lldpd.service.
Processing triggers for libc-bin (2.35-0ubuntu3) ...
Processing triggers for man-db (2.10.2-1) ...
vk@vk-desktop:~$ systemctl enable lldpd
Synchronizing state of lldpd.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable lldpd
Failed to enable unit: Connection timed out
vk@vk-desktop:~$ systemctl enable lldpd
Synchronizing state of lldpd.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable lldpd
vk@vk-desktop:~$ systemctl start lldpd
vk@vk-desktop:~$ lldpctl
-------------------------------------------------------------------------------
LLDP neighbors:
-------------------------------------------------------------------------------
```

>3.    Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
### Ответ
Используется технология VLAN (Virtual Local Area Network, виртуальная локальная сеть) — это функция в роутерах и коммутаторах, позволяющая на одном физическом сетевом интерфейсе (Ethernet, Wi-Fi интерфейсе) создать несколько виртуальных локальных сетей. VLAN используют для создания логической топологии сети, которая никак не зависит от физической топологии.
Пакет "vlan" используется для настройки VLAN-ов в Linux. Данный пакет будет содержать в себе утилиту "vconfig" для настройки VLAN.

Netplan — это новая утилита сетевых настроек с помощью командной строки, установленный начиная с Ubuntu 17.10 для легкого управления и сетевых настроек в системах Ubuntu. Она позволяет настроить сетевой интерфейс с использованием абстракции YAML. Он работает совместно с сетевыми демонами NetworkManager и systemd-networkd  в качестве интерфейсов к ядру.
Он считывает сетевую конфигурацию, описанную в файле /etc/netplan/*.yaml

Пример:
```bash
vlans:
  vlan50:
    id: 50
    link: eth0
    dhcp4: no
    addresses: [192.168.1.2/24]
    gateway: 192.168.1.1
    routes:
        - to: 192.168.1.2/24
         via: 192.168.1.1
         on-link: true
```
где

- vlans: — объявляем блок настройки vlan.
- vlan10: — произвольное имя vlan интерфейса.
- id: — тег нашего vlan.
- link: — интерфейс через который vlan будет доступен.
- routes: — объявляем блок описания маршрутов.
- — to: — задаем адрес/подсеть до которой необходим маршрут.
- via: — указываем шлюз через которой будет доступна наша подсеть.
- on-link: — указываем что прописывать маршруты всегда при поднятии линка.


>4.    Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
### Ответ
Агрегирование каналов (англ. link aggregation) — технологии объединения нескольких параллельных каналов передачи данных в сетях Ethernet в один логический, позволяющие увеличить пропускную способность и повысить надёжность.
Типы LAG:
- статический (на Cisco mode on);
- динамический – LACP протокол (на Cisco mode active).


Типы агрегации (объединения) интерфейсов в Linux
- mode=0 (balance-rr)
Этот режим используется по-умолчанию, если в настройках не указано другое. balance-rr обеспечивает балансировку нагрузки и отказоустойчивость. В данном режиме пакеты отправляются "по кругу" от первого интерфейса к последнему и сначала. Если выходит из строя один из интерфейсов, пакеты отправляются на остальные оставшиеся.При подключении портов к разным коммутаторам, требует их настройки.

- mode=1 (active-backup)
При active-backup один интерфейс работает в активном режиме, остальные в ожидающем. Если активный падает, управление передается одному из ожидающих. Не требует поддержки данной функциональности от коммутатора.

- mode=2 (balance-xor)
Передача пакетов распределяется между объединенными интерфейсами по формуле ((MAC-адрес источника) XOR (MAC-адрес получателя)) % число интерфейсов. Один и тот же интерфейс работает с определённым получателем. Режим даёт балансировку нагрузки и отказоустойчивость.

- mode=3 (broadcast)
Происходит передача во все объединенные интерфейсы, обеспечивая отказоустойчивость.

- mode=4 (802.3ad)
Это динамическое объединение портов. В данном режиме можно получить значительное увеличение пропускной способности как входящего так и исходящего трафика, используя все объединенные интерфейсы. Требует поддержки режима от коммутатора, а так же (иногда) дополнительную настройку коммутатора.

- mode=5 (balance-tlb)
Адаптивная балансировка нагрузки. При balance-tlb входящий трафик получается только активным интерфейсом, исходящий - распределяется в зависимости от текущей загрузки каждого интерфейса. Обеспечивается отказоустойчивость и распределение нагрузки исходящего трафика. Не требует специальной поддержки коммутатора.

- mode=6 (balance-alb)
Адаптивная балансировка нагрузки (более совершенная). Обеспечивает балансировку нагрузки как исходящего (TLB, transmit load balancing), так и входящего трафика (для IPv4 через ARP). Не требует специальной поддержки коммутатором, но требует возможности изменять MAC-адрес устройства.

Файлы конфигурации сети для Ubuntu 20.10 и выше находятся по пути /etc/netplan/имяфайла.yaml Для bondidng приводим его к виду:
```
bonds:
    bond0:
      dhcp4: no
      interfaces: [enp0s3, enp0s4]
      parameters: 
        mode: 802.3ad
        mii-monitor-interval: 1

```

>5.    Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
### Ответ
Воспользуемся утилитой `ipcalc`
```
vk@vk-desktop:/etc/netplan$ ipcalc 10.10.10.0/29
Address:   10.10.10.0           00001010.00001010.00001010.00000 000
Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
Wildcard:  0.0.0.7              00000000.00000000.00000000.00000 111
=>
Network:   10.10.10.0/29        00001010.00001010.00001010.00000 000
HostMin:   10.10.10.1           00001010.00001010.00001010.00000 001
HostMax:   10.10.10.6           00001010.00001010.00001010.00000 110
Broadcast: 10.10.10.7           00001010.00001010.00001010.00000 111
Hosts/Net: 6                     Class A, Private Internet
```

Всего этой подсети 8 адресов - 6 на адреса хостов + адрес подсети + бродкаст.

Дале, для  10.10.10.0/24:
```
vk@vk-desktop:/etc/netplan$ ipcalc 10.10.10.0/24
Address:   10.10.10.0           00001010.00001010.00001010. 00000000
Netmask:   255.255.255.0 = 24   11111111.11111111.11111111. 00000000
Wildcard:  0.0.0.255            00000000.00000000.00000000. 11111111
=>
Network:   10.10.10.0/24        00001010.00001010.00001010. 00000000
HostMin:   10.10.10.1           00001010.00001010.00001010. 00000001
HostMax:   10.10.10.254         00001010.00001010.00001010. 11111110
Broadcast: 10.10.10.255         00001010.00001010.00001010. 11111111
Hosts/Net: 254                   Class A, Private Internet
```

В /24 подсети 256 адресов, значит, подсеть 10.10.10.0/24 можно разделить на 32 подсети 10.10.10.0/29 
Подсети будут такими: 
- 10.10.10.0/29 
- 10.10.10.8/29
- 10.10.10.16/29 и т.д. до 
- 10.10.10.248/29

>6.    Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
### Ответ
Согласно статье в Википедии [Частный IP-адрес](https://ru.wikipedia.org/wiki/%D0%A7%D0%B0%D1%81%D1%82%D0%BD%D1%8B%D0%B9_IP-%D0%B0%D0%B4%D1%80%D0%B5%D1%81) существует диапазон 100.64.0.0 — 100.127.255.255 с маской 255.192.0.0 (/10) - подсеть рекомендована согласно RFC 6598 для использования в качестве адресов для CGN (Carrier-Grade NAT). Нам необходимы подсети размером 50 хостов, считаем следующим образом:
```
vk@vk-desktop:/etc/netplan$ ipcalc 100.64.0.0/10 -s 50
Address:   100.64.0.0           01100100.01 000000.00000000.00000000
Netmask:   255.192.0.0 = 10     11111111.11 000000.00000000.00000000
Wildcard:  0.63.255.255         00000000.00 111111.11111111.11111111
=>
Network:   100.64.0.0/10        01100100.01 000000.00000000.00000000
HostMin:   100.64.0.1           01100100.01 000000.00000000.00000001
HostMax:   100.127.255.254      01100100.01 111111.11111111.11111110
Broadcast: 100.127.255.255      01100100.01 111111.11111111.11111111
Hosts/Net: 4194302               Class A

1. Requested size: 50 hosts
Netmask:   255.255.255.224 = 27 11111111.11111111.11111111.111 00000
Network:   100.64.0.0/27        01100100.01000000.00000000.000 00000
HostMin:   100.64.0.1           01100100.01000000.00000000.000 00001
HostMax:   100.64.0.30          01100100.01000000.00000000.000 11110
Broadcast: 100.64.0.31          01100100.01000000.00000000.000 11111
Hosts/Net: 30                    Class A

Needed size:  64 addresses.
Used network: 100.64.0.0/26
Unused:
100.64.0.64/26
100.64.0.128/25
100.64.1.0/24
100.64.2.0/23
100.64.4.0/22
100.64.8.0/21
100.64.16.0/20
100.64.32.0/19
100.64.64.0/18
100.64.128.0/17
100.65.0.0/16
100.66.0.0/15
100.68.0.0/14
100.72.0.0/13
100.80.0.0/12
100.96.0.0/11
```
Вывод - оптимальным решением будут подсети /26 по 64 хоста.

>7.    Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
### Ответ
**Linux**

- Для очистки всей таблицы ARP
`ip -s -s neigh flush all`

- Удалить запись ARP для определенного адреса
`arp -d <ip-address>`

- Для просмотра таблицы ARP в Linux.
`arp -n`

**Windows**
- просмотреть таблицу ARP-кэша, просто введите следующую команду.
`arp -a`
- удалить всю таблицу ARP
`netsh interface IP delete arpcache`
или 
`arp -d`
- удалить какую-либо конкретную запись в кэше, а не всю таблицу.
`arp -d <ip-address>`
