# Laboratorio 01 · Enrutamiento entre VLAN

## Método 1 · Dos enlaces de acceso

Comuniqué PC0, de la VLAN 10, con PC2, de la VLAN 20. Configuré dos
puertos de acceso en el switch: Fa0/3 para la VLAN 10 y Fa0/4 para la
VLAN 20. Cada enlace llega a una interfaz física distinta del router.

![Topología del método 1](topologia.png)

Comprobé la comunicación con un ping de PC0 a PC2. El router pudo
reenviar el paquete porque tenía ambas subredes directamente conectadas.

[Descargar la práctica de Packet Tracer](01-enrutamiento-entre-vlan.pkt)
