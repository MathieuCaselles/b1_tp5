# Rendu TP-5

## I. Préparation du lab

### 2. Préparation des routeur cisco

J'assigne à r1 l'IP 10.5.1.254 pour net1.  
Pour cela il faut dans la console de r1 taper conf t puis interface Ethernet0/0 puis ip address 10.5.1.254 255.255.255.0  
  
Je verifie en tapant show ip int br :  

    R1#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.5.1.254      YES NVRAM  up                    up
    Ethernet0/1                unassigned      YES NVRAM  administratively down down
    Ethernet0/2                unassigned      YES NVRAM  administratively down down
    Ethernet0/3                unassigned      YES NVRAM  administratively down down

Idem pour r2 mais sur Ethernet0/1 :
  
    R2#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                unassigned      YES unset  administratively down down
    Ethernet0/1                10.5.2.254      YES manual up                    up
    Ethernet0/2                unassigned      YES unset  administratively down down
    Ethernet0/3                unassigned      YES unset  administratively down down

Je fais aussi pareil pour net12 mais avec un /30 car on a besoin que de 2 machine sur le réseau, les 2 routeur.:   

    
    R1#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.5.1.254      YES NVRAM  up                    up
    Ethernet0/1                10.1.0.1        YES manual up                    up
    Ethernet0/2                unassigned      YES NVRAM  administratively down down
    Ethernet0/3                unassigned      YES NVRAM  administratively down down

    
    R2#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.1.0.2        YES manual up                    up
    Ethernet0/1                10.5.2.254      YES manual up                    up
    Ethernet0/2                unassigned      YES unset  administratively down down
    Ethernet0/3                unassigned      YES unset  administratively down down

## II. Lancement et configuration du lab

### Checklist IP VMs

La connexion SSH est fonctionnelle pour toutes mes machines avec les nom de domaine mis précédemment:  

    PS C:\WINDOWS\system32> ssh root@192.168.233.5
    The authenticity of host '192.168.233.5 (192.168.233.5)' can't be established.
    ECDSA key fingerprint is SHA256:0bw8l4FgAF5dSJSe5CYUZtLxNh75AysSQ1NNEbMIYNc.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '192.168.233.5' (ECDSA) to the list of known hosts.
    root@192.168.233.5's password:
    Last login: Tue Jan 22 11:02:10 2019
    [root@client1 ~]#

    PS C:\WINDOWS\system32> ssh root@192.168.233.3
    root@192.168.233.3's password:
    Last login: Mon Feb 25 20:45:35 2019 from 192.168.233.1
    [root@server ~]#

    PS C:\WINDOWS\system32> ssh root@192.168.233.4
    root@192.168.233.4's password:
    Last login: Mon Feb 25 20:47:33 2019 from 192.168.233.1
    [root@client2 ~]#

### Checklist IP Routeurs

Je l'avais déja fait lors du grand I. MAis je vérifie quand même en tapant show ip int br:  

    R1#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.5.1.254      YES NVRAM  up                    up
    Ethernet0/1                10.1.0.1        YES NVRAM  up                    up
    Ethernet0/2                unassigned      YES NVRAM  administratively down down
    Ethernet0/3                unassigned      YES NVRAM  administratively down down
    R1#

    
    R2#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.1.0.2        YES NVRAM  up                    up
    Ethernet0/1                10.5.2.254      YES NVRAM  up                    up
    Ethernet0/2                unassigned      YES NVRAM  administratively down down
    Ethernet0/3                unassigned      YES NVRAM  administratively down down
    R2#

Je définis ensuite mon nom de domaine en écrivant 'conf t' puis  'hostname <HOSTNAME>' :  

    
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R1(config)#hostname routeur1
    routeur1(config)#exit
    routeur1#
    *Mar  1 01:05:08.199: %SYS-5-CONFIG_I: Configured from console by console
    routeur1#


    R2#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R2(config)#hostname routeur2
    routeur2(config)#exit
    routeur2#
    *Mar  1 01:07:02.971: %SYS-5-CONFIG_I: Configured from console by console
    routeur2#

J'oublie pas a la fin de rajouter la commande ci-dossous pour ne pas perdre les changement au redémarrage.  

    # copy running-config startup-config


### Checklist routes

Router1  
J'ajoute la route net 2 en écrivant ''conf t' puis 

    ip route 10.5.2.0 255.255.255.0 10.1.0.2

router2  
Je fais la même chose mais avec net 1

    ip route 10.5.1.0 255.255.255.0 10.1.0.1


server1  

    [root@server ~]# ip route show
    10.5.1.0/24 dev enp0s3 proto kernel scope link src 10.5.1.10 metric 100
    10.5.2.0/24 via 10.5.1.254 dev enp0s3 proto static metric 100
    192.168.233.0/24 dev enp0s8 proto kernel scope link src 192.168.233.3 metric 101

client1    

    [root@client1 ~]# ip route show
    10.5.1.0/24 via 10.5.2.254 dev enp0s3 proto static metric 100
    10.5.2.0/24 dev enp0s3 proto kernel scope link src 10.5.2.10 metric 100
    192.168.233.0/24 dev enp0s8 proto kernel scope link src 192.168.233.5 metric 101

client2  
  
    [root@client2 ~]# ip route show
    10.5.1.0/24 via 10.5.2.254 dev enp0s3 proto static metric 100
    10.5.2.0/24 dev enp0s3 proto kernel scope link src 10.5.2.11 metric 100
    192.168.233.0/24 dev enp0s8 proto kernel scope link src 192.168.233.4 metric 101


Les pings fonctionnens bien !  

server1  

    [root@server ~]# ping 10.5.2.10
    PING 10.5.2.10 (10.5.2.10) 56(84) bytes of data.
    64 bytes from 10.5.2.10: icmp_seq=1 ttl=62 time=40.3 ms
    64 bytes from 10.5.2.10: icmp_seq=2 ttl=62 time=26.9 ms
    ^C
    --- 10.5.2.10 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1002ms
    rtt min/avg/max/mdev = 26.931/33.662/40.394/6.733 ms
    [root@server ~]# ping 10.5.2.11
    PING 10.5.2.11 (10.5.2.11) 56(84) bytes of data.
    64 bytes from 10.5.2.11: icmp_seq=1 ttl=62 time=23.3 ms
    64 bytes from 10.5.2.11: icmp_seq=2 ttl=62 time=31.5 ms
    ^C
    --- 10.5.2.11 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1001ms
    rtt min/avg/max/mdev = 23.312/27.407/31.502/4.095 ms

client1  

    [root@client1 ~]# ping 10.5.1.10
    PING 10.5.1.10 (10.5.1.10) 56(84) bytes of data.
    64 bytes from 10.5.1.10: icmp_seq=3 ttl=62 time=53.0 ms
    64 bytes from 10.5.1.10: icmp_seq=4 ttl=62 time=40.5 ms
    64 bytes from 10.5.1.10: icmp_seq=5 ttl=62 time=37.4 ms
    64 bytes from 10.5.1.10: icmp_seq=6 ttl=62 time=34.3 ms
    64 bytes from 10.5.1.10: icmp_seq=7 ttl=62 time=42.3 ms
    ^C
    --- 10.5.1.10 ping statistics ---
    7 packets transmitted, 5 received, 28% packet loss, time 6007ms
    rtt min/avg/max/mdev = 34.342/41.537/53.082/6.386 ms

client2  

    [root@client2 ~]# ping 10.5.1.10
    PING 10.5.1.10 (10.5.1.10) 56(84) bytes of data.
    64 bytes from 10.5.1.10: icmp_seq=1 ttl=62 time=54.2 ms
    64 bytes from 10.5.1.10: icmp_seq=2 ttl=62 time=41.4 ms
    64 bytes from 10.5.1.10: icmp_seq=3 ttl=62 time=38.4 ms
    ^C
    --- 10.5.1.10 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2003ms
    rtt min/avg/max/mdev = 38.452/44.709/54.241/6.849 ms

## III. DHCP

Je fais tout le nécessaire  

    [root@dhcp-net2 ~]# sudo nano /etc/dhcp/dhcpd.conf
    [root@dhcp-net2 ~]# sudo systemctl start dhcpd
    [root@dhcp-net2 ~]# systemctl status dhcpd
    ● dhcpd.service - DHCPv4 Server Daemon
    Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; disabled; vendor preset: disabled)
    Active: active (running) since Tue 2019-02-26 00:00:53 CET; 20s ago
        Docs: man:dhcpd(8)
            man:dhcpd.conf(5)
    Main PID: 3412 (dhcpd)
    Status: "Dispatching packets..."
    CGroup: /system.slice/dhcpd.service
            └─3412 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: No subnet declaration for enp0s8 (192.168.233.4).
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: ** Ignoring requests on enp0s8.  If this is not what
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]:    you want, please write a subnet declaration
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]:    in your dhcpd.conf file for the network segment
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]:    to which interface enp0s8 is attached. **
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: nt
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: Listening on LPF/enp0s3/08:00:27:32:ca:60/10.5.2.0/24
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: Sending on   LPF/enp0s3/08:00:27:32:ca:60/10.5.2.0/24
    Feb 26 00:00:53 dhcp-net2.tp5.b1 dhcpd[3412]: Sending on   Socket/fallback/fallback-net
    Feb 26 00:00:53 dhcp-net2.tp5.b1 systemd[1]: Started DHCPv4 Server Daemon.

### test

J'ai bien repasser client  en dynamique:  

    PS C:\WINDOWS\system32> ssh root@192.168.233.5
    root@192.168.233.5's password:
    Last login: Mon Feb 25 23:31:49 2019 from 192.168.233.1
    [root@client1 ~]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 08:00:27:70:39:7c brd ff:ff:ff:ff:ff:ff
        inet 10.5.2.50/24 brd 10.5.2.255 scope global noprefixroute dynamic enp0s3
        valid_lft 553sec preferred_lft 553sec
        inet6 fe80::a00:27ff:fe70:397c/64 scope link
        valid_lft forever preferred_lft forever
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 08:00:27:bf:fc:6b brd ff:ff:ff:ff:ff:ff
        inet 192.168.233.5/24 brd 192.168.233.255 scope global noprefixroute dynamic enp0s8
        valid_lft 1152sec preferred_lft 1152sec
        inet6 fe80::a00:27ff:febf:fc6b/64 scope link
        valid_lft forever preferred_lft forever

Puis j'utilise dhc client:  

    [root@client1 ~]# sudo dhclient -v
    Internet Systems Consortium DHCP Client 4.2.5
    Copyright 2004-2013 Internet Systems Consortium.
    All rights reserved.
    For info, please visit https://www.isc.org/software/dhcp/

    Listening on LPF/enp0s8/08:00:27:bf:fc:6b
    Sending on   LPF/enp0s8/08:00:27:bf:fc:6b
    Listening on LPF/enp0s3/08:00:27:70:39:7c
    Sending on   LPF/enp0s3/08:00:27:70:39:7c
    Sending on   Socket/fallback
    DHCPDISCOVER on enp0s8 to 255.255.255.255 port 67 interval 5 (xid=0x7edb311d)
    DHCPDISCOVER on enp0s3 to 255.255.255.255 port 67 interval 3 (xid=0x6f26f7bf)
    DHCPREQUEST on enp0s8 to 255.255.255.255 port 67 (xid=0x7edb311d)
    DHCPOFFER from 192.168.233.2
    DHCPACK from 192.168.233.2 (xid=0x7edb311d)
    bound to 192.168.233.5 -- renewal in 490 seconds.

### Explerer dhcp

capture wireshark  

    1	0.000000	PcsCompu_32:ca:60	PcsCompu_70:39:7c	ARP	60	Who has 10.5.2.50? Tell 10.5.2.11
    2	0.000000	PcsCompu_70:39:7c	PcsCompu_32:ca:60	ARP	60	10.5.2.50 is at 08:00:27:70:39:7c
    3	6.379161	10.5.2.50	10.5.2.11	DHCP	342	DHCP Request  - Transaction ID 0x52b47270
    4	6.380137	10.5.2.11	10.5.2.50	ICMP	370	Destination unreachable (Host administratively prohibited)
    5	17.873442	10.5.2.50	10.5.2.11	DHCP	342	DHCP Request  - Transaction ID 0x52b47270
    6	17.874371	10.5.2.11	10.5.2.50	ICMP	370	Destination unreachable (Host administratively prohibited)
    7	17.935858	10.5.2.11	10.5.2.50	DHCP	342	DHCP ACK      - Transaction ID 0x52b47270
