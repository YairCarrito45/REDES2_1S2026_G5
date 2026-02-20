# edificio Derecho

## switch 16
```Cisco
enable
	config terminal
		hostname SW16_G5

		# creacion de SW server - vtp
		vtp mode server
		vtp version 2
		vtp domain G5

		# creacion de vlan 
		vlan 85
			name bachillerato_der
			exit

		vlan 75 
			name basicos_der
			exit

		vlan 65
			name primaria_der
			exit

		exit

	write

```


Confirmacion del SW server:

show vtp status`:

```bash
SW16_G5#show vtp status 
VTP Version capable             : 1 to 2
VTP version running             : 2
VTP Domain Name                 : G5
VTP Pruning Mode                : Disabled
VTP Traps Generation            : Disabled
Device ID                       : 000A.F320.7C00
Configuration last modified by 0.0.0.0 at 3-2-93 12:22:36
Local updater ID is 0.0.0.0 (no valid interface found)

Feature VLAN : 
--------------
VTP Operating Mode                : Server
Maximum VLANs supported locally   : 255
Number of existing VLANs          : 8
Configuration Revision            : 6
MD5 digest                        : 0xAA 0x6B 0x64 0xB4 0xBE 0x61 0x11 0x82 
                                    0x21 0x60 0xF5 0x51 0x0D 0x77 0x8A 0xC3 
SW16_G5#


```


confirmacion de la creacion de vlans:
show vlan brief:

```bash
SW16_G5#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
65   primaria_der                     active    
75   basicos_der                      active    
85   bachillerato_der                 active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
SW16_G5#

```