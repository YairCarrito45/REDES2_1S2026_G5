# Práctica 2 - Redes de Computadoras 2

### Grupo 5
* Estiben Yair Lopez Leveron 	202204578
* Johan Moises Cardona Rosales	202201405
* Giovanni Saul Concoha Cax     202100229

------------------------------



## CONFIGURACIÓN PISO 1

## 1. Configuración Multilayer Switch (3560)

### Activación de enrutamiento y creación de VLANs

```cisco
enable
configure terminal

vlan 15
name ADMIN

vlan 25
name ESTUDIANTES

ip routing
```

### Configuración de interfaces VLAN (SVI)

```cisco
interface vlan 15
ip address 192.198.15.66 255.255.255.240
no shutdown

interface vlan 25
ip address 192.198.15.2 255.255.255.192
no shutdown
```

### Configuración de enlace troncal hacia switch de acceso

```cisco
interface fa0/1
switchport trunk encapsulation dot1q
switchport mode trunk
no shutdown
```

### Configuración de enlaces capa 3 hacia routers

```cisco
interface gig0/1
no switchport
ip address 10.2.5.1 255.255.255.252
no shutdown

interface gig0/2
no switchport
ip address 10.2.5.5 255.255.255.252
no shutdown
```

### Configuración de EIGRP

```cisco
router eigrp 5
no auto-summary
network 10.2.5.0 0.0.0.255
network 192.198.15.0 0.0.0.255
```

### Guardar configuración

```cisco
end
write memory
```

---

## 2. Configuración Switch de Acceso (2960)

### Creación de VLANs

```cisco
enable
configure terminal

vlan 15
name ADMIN

vlan 25
name ESTUDIANTES
```

### Asignación de puertos a VLANs

```cisco
interface fa0/10
switchport mode access
switchport access vlan 15

interface fa0/11
switchport mode access
switchport access vlan 25
```

### Configuración de trunk hacia multilayer

```cisco
interface gig0/1
switchport mode trunk
no shutdown
```

### Guardar configuración

```cisco
end
write memory
```

---

## 3. Configuración Router1

### Activación de interfaz

```cisco
enable
configure terminal

interface gig0/0
ip address 10.2.5.2 255.255.255.252
no shutdown
```

### Configuración EIGRP

```cisco
router eigrp 5
no auto-summary
network 10.2.5.0 0.0.0.255
```

### Guardar configuración

```cisco
end
write memory
```

---

## 4. Configuración Router0

### Activación de interfaz

```cisco
enable
configure terminal

interface gig0/0
ip address 10.2.5.6 255.255.255.252
no shutdown
```

### Configuración EIGRP

```cisco
router eigrp 5
no auto-summary
network 10.2.5.0 0.0.0.255
```

### Guardar configuración

```cisco
end
write memory
```

---

## 5. Configuración de PCs (pruebas)

### VLAN 15 (ADMIN)
- **IP:** 192.198.15.70
- **Máscara:** 255.255.255.240
- **Gateway:** 192.198.15.66

### VLAN 25 (ESTUDIANTES)
- **IP:** 192.198.15.10
- **Máscara:** 255.255.255.192
- **Gateway:** 192.198.15.2

---

## 6. Comandos de verificación

### Multilayer

```cisco
show ip interface brief
show interfaces trunk
show ip eigrp neighbors
show ip route
```

### Routers

```cisco
show ip eigrp neighbors
show ip route
```


# CONFIGURACIÓN DATA CENTER (BIBLIOTECA CENTRAL)

## 1. Configuración de VLANs en Switch3 (2960)

Se crearon las VLANs para segmentar los servidores:

```cisco
enable
configure terminal

vlan 35
name WEB_SERVERS

vlan 45
name DHCP_SERVERS

end
write memory
```

---

## 2. Asignación de puertos a VLANs

Se asignaron los servidores a sus respectivas VLANs:

```cisco
interface fa0/2
switchport mode access
switchport access vlan 35

interface fa0/3
switchport mode access
switchport access vlan 45

end
write memory
```

---

## 3. Configuración de trunk hacia routers

Se configuraron los enlaces hacia Router2 y Router3 como trunk para permitir múltiples VLANs:

```cisco
interface gig0/1
switchport mode trunk
no shutdown

interface gig0/2
switchport mode trunk
no shutdown

end
write memory
```

---

## 4. Configuración Router-on-a-Stick (Router2)

Se configuraron subinterfaces para enrutar VLANs:

```cisco
enable
configure terminal

interface gig0/0
no shutdown

interface gig0/0.35
encapsulation dot1Q 35
ip address 192.198.35.2 255.255.255.0

interface gig0/0.45
encapsulation dot1Q 45
ip address 192.198.45.2 255.255.255.0

end
write memory
```

---

## 5. Configuración Router-on-a-Stick (Router3)

Configuración redundante para alta disponibilidad:

```cisco
enable
configure terminal

interface gig0/0
no shutdown

interface gig0/0.35
encapsulation dot1Q 35
ip address 192.198.35.3 255.255.255.0

interface gig0/0.45
encapsulation dot1Q 45
ip address 192.198.45.3 255.255.255.0

end
write memory
```

---

## 6. Configuración HSRP (Alta disponibilidad)

### Router2 (Activo)

```cisco
interface gig0/0.35
standby 35 ip 192.198.35.1
standby 35 priority 110
standby 35 preempt

interface gig0/0.45
standby 45 ip 192.198.45.1
standby 45 priority 110
standby 45 preempt
```

### Router3 (Respaldo)

```cisco
interface gig0/0.35
standby 35 ip 192.198.35.1
standby 35 priority 100
standby 35 preempt

interface gig0/0.45
standby 45 ip 192.198.45.1
standby 45 priority 100
standby 45 preempt
```

---

## 7. Configuración EIGRP en routers

### Router2

```cisco
router eigrp 5
no auto-summary
network 10.2.6.0 0.0.0.3
network 192.198.35.0 0.0.0.255
network 192.198.45.0 0.0.0.255
```

### Router3

```cisco
router eigrp 5
no auto-summary
network 10.2.6.4 0.0.0.3
network 192.198.35.0 0.0.0.255
network 192.198.45.0 0.0.0.255
```

---

## 8. Ajuste en Multilayer Switch (Data Center)

Se eliminaron las interfaces VLAN para evitar conflicto con HSRP:

```cisco
enable
configure terminal

interface vlan 35
no ip address
shutdown

interface vlan 45
no ip address
shutdown

end
write memory
```

---

## 9. Configuración de servidores

### ServerWeb
- **IP:** 192.198.35.10
- **Máscara:** 255.255.255.0
- **Gateway:** 192.198.35.1

### ServerDHCP
- **IP:** 192.198.45.10
- **Máscara:** 255.255.255.0
- **Gateway:** 192.198.45.1

---

## 10. Verificaciones realizadas

### HSRP

```cisco
show standby brief
```

### EIGRP

```cisco
show ip eigrp neighbors
show ip route
```

### Trunks

```cisco
show interfaces trunk
```


# Implementación de Enlace entre Piso 1 y Data Center mediante EtherChannel y EIGRP

## 1. Descripción general

Se implementó un enlace redundante y de alta disponibilidad entre el MultilayerSwitch4 (Piso 1) y el MultilayerSwitch2 (Data Center) mediante:

- EtherChannel con LACP
- Enlace de Capa 3 (Layer 3)
- Enrutamiento dinámico con EIGRP

El objetivo fue permitir la comunicación entre las VLANs del Piso 1 y los servicios ubicados en el Data Center.

---

## 2. Configuración del EtherChannel (LACP – Capa 3)

Se agruparon cuatro interfaces físicas en cada switch para formar un canal lógico (Port-channel1).

### Características:

- **Protocolo:** LACP (mode active)
- **Tipo:** Capa 3 (no switchport)
- **IPs asignadas:**
  - Piso 1 → `10.2.7.1/30`
  - Data Center → `10.2.7.2/30`

### Configuración aplicada

#### MultilayerSwitch4 (Piso 1)

```cisco
interface range fa0/1 - 4
no switchport
channel-group 1 mode active
no shutdown

interface port-channel 1
no switchport
ip address 10.2.7.1 255.255.255.252
no shutdown
```

#### MultilayerSwitch2 (Data Center)

```cisco
interface range fa0/1 - 4
no switchport
channel-group 1 mode active
no shutdown

interface port-channel 1
no switchport
ip address 10.2.7.2 255.255.255.252
no shutdown
```

---

## 3. Configuración de Enrutamiento Dinámico (EIGRP)

Se habilitó EIGRP (AS 5) para intercambiar rutas entre ambas capas.

### Redes anunciadas

#### Piso 1

```cisco
router eigrp 5
network 10.2.7.0 0.0.0.3
network 192.198.15.0 0.0.0.255
```

#### Data Center

```cisco
router eigrp 5
network 10.2.7.0 0.0.0.3
network 192.198.35.0 0.0.0.255
network 192.198.45.0 0.0.0.255
```

---

## 4. Verificación de conectividad

### Estado del Port-channel

- **Port-channel1 → up/up**
  - Indica enlace activo y funcional

### Vecinos EIGRP detectados

- **Piso 1 ↔ Data Center:**
  - `10.2.7.1`
  - `10.2.7.2`

### Rutas aprendidas correctamente

#### En Piso 1:

- `192.198.35.0/24` (WEB)
- `192.198.45.0/24` (DHCP)

#### En Data Center:

- `192.198.15.0/26` (VLAN 25)
- `192.198.15.64/28` (VLAN 15)