# Rendu TP-5

## Préparation du lab

### 2. Préparation des routeur cisco

J'assigne à r1 l'IP 10.5.1.254.  
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





