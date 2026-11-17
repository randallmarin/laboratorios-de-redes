# Laboratorio de Enrutamiento Inter-VLAN y RIP

## 1. Descripción

Este laboratorio fue desarrollado en Cisco Packet Tracer como parte de mi formación en CCNA2: Switching, Routing, and Wireless Essentials.

El objetivo principal fue implementar una red con múltiples VLAN, utilizando dos métodos de enrutamiento inter-VLAN: Router-on-a-Stick mediante un router Cisco y enrutamiento mediante interfaces virtuales (SVI) en un switch multicapa.

Además, se configuró el protocolo RIP para permitir el intercambio de información de enrutamiento entre los dispositivos de capa 3 y facilitar la comunicación entre redes remotas.

Durante el desarrollo se realizaron pruebas de conectividad y diagnóstico para identificar y solucionar problemas de comunicación.

## 2. Tecnologías utilizadas

- VLAN (Virtual Local Area Network)
- Puertos de acceso (Access)
- Enlaces troncales (Trunk)
- IEEE 802.1Q
- Router-on-a-Stick
- Interfaces virtuales SVI
- Switch multicapa (capa 3)
- RIP (Routing Information Protocol)
- ICMP (Ping)
- Direccionamiento IPv4
- Cisco IOS

## 3. Topología de red

El laboratorio está compuesto por:

| Dispositivo | Cantidad | Modelo |
|---|---|---|
| Router | 2 | Cisco 1841 |
| Switch capa 2 | 2 | Cisco 2960 |
| Switch multicapa | 1 | Cisco 3560 |
| Computadoras | 6 | PC-PT |

### Funcionamiento general

La red se divide en dos segmentos principales.

**Segmento izquierdo:**

Está compuesto por un router Cisco 1841, un switch Cisco 2960 y tres computadoras distribuidas en las VLAN 10, 20 y 30.

El enrutamiento entre estas VLAN se realiza mediante Router-on-a-Stick, utilizando subinterfaces en el router.

**Segmento derecho:**

Está compuesto por un switch multicapa Cisco 3560, un switch Cisco 2960 y tres computadoras distribuidas en las VLAN 40, 50 y 60.

El enrutamiento entre estas VLAN se realiza mediante interfaces SVI configuradas en el switch multicapa.

Ambos segmentos se comunican a través de Router0 y Router1, utilizando RIP para intercambiar información de enrutamiento.

## 4. Direccionamiento IP

### Redes VLAN

| VLAN | Dirección de red | Gateway |
|---|---|---|
| 10 | 192.168.10.0/24 | 192.168.10.1 |
| 20 | 192.168.20.0/24 | 192.168.20.1 |
| 30 | 192.168.30.0/24 | 192.168.30.1 |
| 40 | 192.168.40.0/24 | 192.168.40.1 |
| 50 | 192.168.50.0/24 | 192.168.50.1 |
| 60 | 192.168.60.0/24 | 192.168.60.1 |
| 99 | 192.168.99.0/24 | 192.168.99.1 |
| 199 | 192.168.199.0/24 | 192.168.199.1 |

La VLAN 70 se utiliza como VLAN nativa en el enlace troncal entre SWMC y SW2.

### Redes de interconexión

| Dispositivo | Interfaz | Dirección IP |
|---|---|---|
| Router0 | Fa0/1 | 200.10.10.1/24 |
| Router1 | Fa0/1 | 200.10.10.2/24 |
| Router1 | Fa0/0 | 192.168.80.1/24 |
| SWMC | VLAN 80 | 192.168.80.2/24 |

La VLAN 80 fue utilizada como red de tránsito entre Router1 y SWMC.

## 5. Implementación

### 5.1. Configuración de VLAN

Se crearon distintas VLAN para separar lógicamente las computadoras.

En el switch izquierdo se utilizaron las VLAN 10, 20 y 30.

En el switch derecho se utilizaron las VLAN 40, 50 y 60.

Las VLAN permiten dividir la red en diferentes dominios de broadcast, facilitando su organización y administración.

### 5.2. Configuración de enlaces troncales

Se configuraron enlaces troncales estáticos para transportar múltiples VLAN mediante IEEE 802.1Q.

En el enlace entre SWMC y SW2 se estableció la VLAN 70 como nativa.

Se utilizó el siguiente comando para verificar los enlaces:

```bash
show interfaces trunk
```

### 5.3. Enrutamiento mediante Router-on-a-Stick

En Router0 se utilizaron subinterfaces asociadas a las VLAN 10, 20, 30 y 99.

Cada subinterfaz cuenta con una dirección IP que funciona como puerta de enlace de su respectiva red.

Este método permite que dispositivos pertenecientes a diferentes VLAN puedan comunicarse utilizando un mismo enlace físico entre el router y el switch.

### 5.4. Enrutamiento mediante switch multicapa

En SWMC se crearon interfaces SVI para las VLAN 40, 50, 60, 80 y 199.

Las interfaces SVI permiten asignar direcciones IP a las VLAN y realizar el enrutamiento entre redes.

Para habilitar el enrutamiento IP en el switch multicapa se utilizó:

```bash
ip routing
```

A diferencia del método Router-on-a-Stick, el switch multicapa puede realizar el enrutamiento entre sus VLAN directamente.

### 5.5. Configuración de RIP

Se configuró RIP en Router0, Router1 y SWMC para intercambiar información sobre las redes conectadas.

Se utilizaron comandos como:

```bash
router rip
network 200.10.10.0
```

Además, se estudiaron las interfaces pasivas:

```bash
passive-interface default
no passive-interface fastEthernet 0/1
```

Las interfaces pasivas permiten evitar el envío de actualizaciones RIP hacia redes donde no es necesario intercambiar información de enrutamiento.

En SWMC se utilizó VLAN 80 como interfaz de capa 3 para comunicarse con Router1.

## 6. Verificación de conectividad

Para comprobar el funcionamiento de los enlaces se utilizó el comando `ping`.

Se realizaron pruebas de conectividad entre:

- Router0 y Router1.
- Router1 y SWMC.
- SWMC y SW2.

Durante las pruebas se confirmó que los dispositivos podían comunicarse mediante sus direcciones IP correspondientes.

También se utilizaron comandos de diagnóstico para revisar el estado de las interfaces y las tablas de enrutamiento.

### Comandos de verificación

```bash
show ip interface brief
show interfaces trunk
show vlan brief
show ip route
show ip protocols
ping
```

## 7. Problemas encontrados y soluciones

### 7.1. Falla de conectividad entre Router0 y Router1

**Problema:**

Las interfaces FastEthernet0/1 de ambos routers mostraban el estado `up/down`.

Aunque las direcciones IP estaban correctamente configuradas, no se establecía comunicación.

**Diagnóstico:**

Se utilizaron los siguientes comandos:

```bash
show ip interface brief
show interfaces fastEthernet 0/1
```

Se identificó que las interfaces físicas estaban activas, pero el protocolo de línea permanecía deshabilitado.

**Solución:**

Se reemplazó el cable Copper Straight-Through por un cable Copper Cross-Over.

**Resultado:**

Las interfaces pasaron al estado `up/up` y se logró comunicación entre los routers mediante ping.

### 7.2. Falta de comunicación entre SWMC y SW2

**Problema:**

El ping desde SWMC hacia la dirección administrativa de SW2 no respondía.

**Diagnóstico:**

Se revisó la configuración IP de la interfaz VLAN 199.

**Solución:**

Se configuraron las siguientes direcciones:

- SWMC: 192.168.199.1/24
- SW2: 192.168.199.20/24

**Resultado:**

Se consiguió comunicación entre SWMC y SW2.

### 7.3. Falta de rutas aprendidas mediante RIP

**Problema:**

SWMC mostraba únicamente rutas conectadas directamente en su tabla de enrutamiento.

**Diagnóstico:**

Se utilizaron los comandos:

```bash
show ip route
show ip protocols
```

Se detectó que faltaba incluir la red de tránsito 192.168.80.0 en la configuración RIP de SWMC.

**Solución:**

Se agregó la red al proceso RIP:

```bash
router rip
network 192.168.80.0
```

**Resultado:**

Se corrigió la configuración de participación de RIP en la red de tránsito y se continuaron las pruebas de conectividad.

## 8. Conceptos aprendidos

Durante este laboratorio se reforzaron los siguientes conocimientos:

- Diferencia entre VLAN y subred IP.
- Funcionamiento de los puertos Access y Trunk.
- Uso de la encapsulación IEEE 802.1Q.
- Diferencia entre subinterfaces y SVI.
- Funcionamiento de Router-on-a-Stick.
- Enrutamiento mediante switches multicapa.
- Uso de RIP para intercambiar información de rutas.
- Función de `passive-interface`.
- Interpretación de los estados `up/up`, `up/down` y `down/down`.
- Diagnóstico de conectividad mediante comandos Cisco IOS.

## 9. Conclusiones

Este laboratorio permitió comprender cómo se realiza la comunicación entre dispositivos pertenecientes a diferentes VLAN y redes IP.

Se implementaron dos métodos de enrutamiento inter-VLAN y se utilizó RIP para comunicar dispositivos de capa 3.

También se reforzó la importancia del diagnóstico de fallas, especialmente al identificar problemas de conectividad relacionados con el cableado, las interfaces y la configuración del protocolo de enrutamiento.

Los conocimientos desarrollados son aplicables a la administración de redes empresariales, soporte de infraestructura y operaciones de redes (NOC).

## 10. Herramienta utilizada

**Cisco Packet Tracer**

Laboratorio práctico de CCNA2 — Switching, Routing, and Wireless Essentials.
