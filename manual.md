# Manual

## Topologia

![Topologia](imagnes/topologia.PNG)

### Configuracion modo acceso o troncal
**ESW1**
```
conf t
interface range f 1/0 - 4
switchport mode trunk
switchport trunk allowed vlan 1,10,20,1002-1005
end
```

Verificar con:
![1](imagnes/1.PNG)

**ESW2**
```
conf t
interface range f1/0 - 1
switchport mode trunk
switchport trunk allowed vlan 1,10,20,1002-1005
end

conf t
interface range f 1/4 - 7
switchport mode trunk
switchport trunk allowed vlan 1,10,20,1002-1005
end
```

Verificar con:
```
sh int status
```
![2](imagnes/2.PNG)

**ESW3**
```
conf t
interface range f1/2 - 7
switchport mode trunk
switchport trunk allowed vlan 1,10,20,1002-1005
end
```
Verificar con:
```
sh int status
```
![3](imagnes/3.PNG)

### Configurar VTP:

**ESW1**
```
conf t 
vtp domain redes1_201612383
vtp password redes1_201612383
vtp mode server
end
```

**ESW2**
```
conf t
vtp password redes1_201612383
vtp mode client
```

**ESW3**
```
conf t
vtp password redes1_201612383
vtp mode client
```

![4](imagnes/4.PNG)

### Configurar VLANS

Solo se configuran en el ESW1 porque ese es el servidor

```
conf t
vlan 10
name VENTAS
end 

conf t
vlan 20
name CONTABILIDAD
end
```

Verificar:
```
sh vlan-sw
```

![4](imagnes/7.PNG)

### Configurar port-channel

**ESW1 == ESW2**
ESW1
```
conf t 
interface range  f 1/0 - 1
channel-group 1 mode on
end
```

ESW2
```
conf t 
interface range  f 1/0 - 1
channel-group 1 mode on
end
```

Verficar en ambos switches:
```
sh et summ
```

**ESW1 == ESW3**
ESW1
```
conf t 
interface range  f 1/2 - 3
channel-group 2 mode on
end
```

ESW3
```
conf t 
interface range  f 1/2 - 3
channel-group 2 mode on
end
```

Verficar en ambos switches:
```
sh et summ
```

**ESW2 == ESW3**
ESW2
```
conf t 
interface range  f 1/4 - 5
channel-group 3 mode on
end
```

ESW3
```
conf t 
interface range  f 1/4 - 5
channel-group 3 mode on
end
```

Verficar en ambos switches:
```
sh et summ
```

### Configurar el router:

Configurar las subinterfaces del router con la direccion Gateway de los hosts de cada red proporcionada mas adelante

Activacion del puerto
```
conf t
int fa 0/0
not shut
exit
```

Configuracion de las subinterfaces
```
int fa 0/0.10
encapsulation dot1q 10
ip add 192.168.13.254 255.255.255.0
exit
```

```
int fa 0/0.20
encapsulation dot1q 20
ip add 192.168.23.254 255.255.255.0
exit
```

### Creacion DHCP
```
conf t
int fa 0/0.10
ip dhcp pool ventas
network 192.168.13.0 255.255.255.0
default-route 192.168.13.254
exit
```

```
int fa 0/0.20
ip dhcp pool contabilidad
network 192.168.23.0 255.255.255.0
default-route 192.168.23.254
exit
```

| VLAN | Direcciond de Red  | Primera Direccion Asignable | Ultima direccion Asignable  | Direccion de Broadcas  |
|-------------:|------:|------------:| -------------:| -------------:|
| 10           | 192.168.13.0/24 | 192.168.13.1 | 192.168.13.253 | 192.168.13.254
| 20           | 192.168.23.0/24 | 192.168.23.1 | 192.168.23.253 | 192.168.23.254
