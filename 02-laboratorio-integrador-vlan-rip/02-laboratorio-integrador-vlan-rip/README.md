# Laboratorio integrador: VLAN, enrutamiento inter-VLAN y RIP

## Descripción

Este laboratorio fue desarrollado en Cisco Packet Tracer como parte de mi aprendizaje de CCNA2.

El objetivo es implementar una red con múltiples VLAN, configurar el enrutamiento entre ellas y permitir la comunicación entre redes remotas utilizando RIP.

## Tecnologías utilizadas

- VLAN y puertos de acceso
- Trunking IEEE 802.1Q
- Router-on-a-Stick
- Switch multicapa e interfaces SVI
- RIP
- Port Security

## Topología

La red está formada por:

- 2 routers Cisco 1841.
- 2 switches Cisco 2960.
- 1 switch multicapa Cisco 3560.
- 6 computadoras distribuidas en diferentes VLAN.

## Direccionamiento

| VLAN | Red | Gateway |
|---|---|---|
| 10 | 192.168.10.0/24 | 192.168.10.1 |
| 20 | 192.168.20.0/24 | 192.168.20.1 |
| 30 | 192.168.30.0/24 | 192.168.30.1 |
| 40 | 192.168.40.0/24 | 192.168.40.1 |
| 50 | 192.168.50.0/24 | 192.168.50.1 |
| 60 | 192.168.60.0/24 | 192.168.60.1 |
| 80 | 192.168.80.0/24 | Red de tránsito |
| 99 | 192.168.99.0/24 | 192.168.99.1 |
| 199 | 192.168.199.0/24 | 192.168.199.1 |

La VLAN 70 se utiliza como VLAN nativa en el enlace troncal entre SWMC y SW2.

## Implementación

### 1. Configuración de VLAN

Se separaron las computadoras en diferentes VLAN para organizar los dominios de broadcast.

### 2. Enrutamiento inter-VLAN

En Router0 se implementó Router-on-a-Stick mediante subinterfaces.

En SWMC se utilizaron interfaces SVI y se habilitó el enrutamiento IP.

### 3. Enrutamiento RIP

Se configuró RIP para intercambiar información de enrutamiento entre Router0, Router1 y SWMC.

Se utilizaron interfaces pasivas para evitar enviar actualizaciones innecesarias hacia redes de usuarios.

### 4. Seguridad de puertos

El laboratorio requiere limitar a una dirección MAC dinámica los puertos conectados a las PC y utilizar el modo de violación `shutdown`.

La verificación final de Port Security está pendiente de documentar.

## Problemas encontrados

### Falla de conectividad entre routers

**Síntoma:** las interfaces FastEthernet0/1 mostraban `up/down`.

**Diagnóstico:** se verificaron las interfaces con `show ip interface brief` y `show interfaces fastEthernet 0/1`.

**Solución:** se reemplazó el cable Ethernet directo por un cable Copper Cross-Over.

**Resultado:** se estableció comunicación entre los routers mediante ping.

### Falta de una red en RIP

**Síntoma:** SWMC no aprendía las rutas esperadas.

**Diagnóstico:** se utilizaron `show ip route` y `show ip protocols`.

**Solución:** se incorporó la red de tránsito 192.168.80.0 al proceso RIP.

## Comandos de verificación

```bash
show ip interface brief
show vlan brief
show interfaces trunk
show ip route
show ip protocols
show port-security
ping
```

## Conclusiones

Este laboratorio permitió comprender las diferencias entre enrutamiento mediante subinterfaces y mediante interfaces SVI, el funcionamiento de los enlaces troncales y la importancia de los protocolos de enrutamiento.

También permitió practicar el diagnóstico de problemas de conectividad, una habilidad fundamental para operaciones de redes y soporte NOC.

## Herramienta

Cisco Packet Tracer.
