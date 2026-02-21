


# routers:

# Roouter3

```cisco
enable
config termianal

hostname Router3_G5

# INTERFAZ HACIA SWITCH

interface gigabitEthernet0/1
 no shutdown
 exit

! VLAN 85
interface gigabitEthernet0/1.85
 encapsulation dot1Q 85
 ip address 192.178.85.1 255.255.255.0
 no shutdown
 exit

! VLAN 75
interface gigabitEthernet0/1.75
 encapsulation dot1Q 75
 ip address 192.178.75.1 255.255.255.0
 no shutdown
 exit

! VLAN 65
interface gigabitEthernet0/1.65
 encapsulation dot1Q 65
 ip address 192.178.65.1 255.255.255.0
 no shutdown
 exit

# ENLACE HACIA ROUTER2
interface gigabitEthernet0/0
 ip address 10.10.6.2 255.255.255.0
 no shutdown
 exit

# CONFIGURACION OSPF
router ospf 1
 router-id 3.3.3.3
 network 10.10.6.0 0.0.0.255 area 0
 network 192.178.85.0 0.0.0.255 area 0
 network 192.178.75.0 0.0.0.255 area 0
 network 192.178.65.0 0.0.0.255 area 0
 passive-interface gigabitEthernet0/1
 exit

end
write
```



RIP
router rip
version 2
no auto-sumary
networt 10.0.0.0
network 192.168.10.0
network 192.168.20.0
network 192.168.30.0

passive-interface default
no passive-interface gigabitEthernet0/0
end
write


EIGRP
eigrp 100
no auto-summary
network 10.10.30.0 0.0.0.3
network 192.168.40.0 0.0.0.255
network 192.168.50.0 0.0.0.255
network 192.168.60.0 0.0.0.255
pasive-interface default
no passive-interface gigabitEthernert0/0
end
write



 router rip
 version2
 no auto-sumarry
 network 10.0.0.0
 passive-interface default
 no passive-interface gigabitEthernet0/0
 passive-interface gigabitEthernet0/1
 redistribute ospf 1 metric 2
 redistribute eigrp 100 metric 2
 exit


 router ospf 1
 router-id 2.2.2.2
 passive-nterface default
 network 10.10.10.0 0.0.0.0.255 area 0
 redistribute rip subnets
 redistribure eigrp 100 subnets
 exit


eigrp 100
no auto-summary
newtwork 10.10.30.0 0.0.0.3
passive-interface default
no passive-interface gigaEthernet0/1
passive-interface gigbitEthernet0/0
no redistribute rip
no redistribute ospf 1
redistribute rip metric 10000 1000 255 1 1500
redistribute ospf 1 metric 10000 100 255 1 1500
end
