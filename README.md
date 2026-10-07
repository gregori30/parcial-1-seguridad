# parcial-1-seguridad
#  Proyecto de Seguridad Perimetral con FortiGate, Cisco y VPN IPsec

**Matrícula:** 2024-1462  
**Plataforma:** EVE-NG  
**Firewall:** FortiGate / FortiOS 7.0.x  
**Switch:** Cisco IOSvL2  
**VPN:** IPsec Site-to-Site FortiGate ↔ Cisco  
**Servidores:** Ubuntu Linux  
**Base de datos:** MariaDB  
**Web Server:** Apache2  

---

##  Video demostrativo

> El video demuestra el funcionamiento completo de la infraestructura, incluyendo VLAN, DHCP, acceso a Internet, NAT, Web Filter, VPN IPsec, servidor HTTP, MariaDB y políticas de seguridad.

**Enlace del video:**

https://youtu.be/W9iFdr1aJyQ

---

# 📑 Índice

1. [Descripción del proyecto](#-descripción-del-proyecto)
2. [Objetivos](#-objetivos)
3. [Tecnologías utilizadas](#-tecnologías-utilizadas)
4. [Topología](#-topología)
5. [Direccionamiento IP](#-direccionamiento-ip)
6. [VLAN 10 - Usuarios](#-vlan-10---usuarios)
7. [VLAN 20 - Administrativos](#-vlan-20---administrativos)
8. [Trunk 802.1Q](#-trunk-8021q)
9. [Servidor DHCP](#-servidor-dhcp)
10. [Salida a Internet](#-salida-a-internet)
11. [NAT](#-nat)
12. [Web Server](#-web-server)
13. [DB Server](#-db-server)
14. [VPN IPsec](#-vpn-ipsec)
15. [Web Filter](#-web-filter)
16. [Política HTTP](#-política-http)
17. [Configuración del switch](#-configuración-del-switch)
18. [Pruebas realizadas](#-pruebas-realizadas)
19. [Evidencias](#-evidencias)
20. [Conclusiones](#-conclusiones)

---

# 📌 Descripción del proyecto

Este proyecto implementa una infraestructura de red empresarial simulada utilizando EVE-NG.

El objetivo principal es implementar mecanismos de seguridad perimetral mediante un firewall FortiGate, segmentación lógica mediante VLAN, traducción de direcciones mediante NAT, filtrado de contenido web y comunicación segura entre dos ubicaciones mediante una VPN IPsec Site-to-Site.

La infraestructura está compuesta por:

- FortiGate con FortiOS 7.0.x.
- Router Cisco utilizado como ISP.
- Router Cisco utilizado como router de sucursal.
- Switch Cisco IOSvL2.
- VLAN 10 para usuarios.
- VLAN 20 para usuarios administrativos.
- Servidor Web Ubuntu.
- Servidor de Base de Datos Ubuntu.
- MariaDB.
- Apache HTTP Server.
- PCs virtuales.
- EVE-NG como plataforma de virtualización.

---

#  Objetivos

Los principales objetivos son:

- Implementar segmentación mediante VLAN.
- Configurar trunking IEEE 802.1Q.
- Proporcionar direccionamiento dinámico mediante DHCP.
- Implementar salida a Internet.
- Configurar NAT.
- Implementar políticas de firewall.
- Permitir únicamente HTTP hacia el Web Server desde la red de usuarios.
- Registrar tráfico bloqueado.
- Implementar filtrado web.
- Bloquear el acceso de VLAN 10 a `/inventario`.
- Implementar una VPN IPsec Site-to-Site.
- Proteger la comunicación entre la sede principal y la sucursal.
- Implementar un servidor HTTP.
- Implementar MariaDB.
- Documentar y demostrar cada requisito.

---

#  Tecnologías utilizadas

| Tecnología | Función |
|---|---|
| EVE-NG | Virtualización de la infraestructura |
| FortiGate | Firewall principal |
| FortiOS 7.0.x | Sistema operativo del firewall |
| Cisco IOS | Routing y VPN |
| Cisco IOSvL2 | Switching |
| IEEE 802.1Q | Trunk de VLAN |
| DHCP | Asignación automática de IP |
| NAT/PAT | Acceso a Internet |
| IPsec | VPN Site-to-Site |
| IKE | Negociación de VPN |
| Apache2 | Servidor HTTP |
| MariaDB | Base de datos |
| Ubuntu Server | Sistema operativo de servidores |
| GitHub | Documentación y evidencias |

---

#  Topología

La infraestructura implementada tiene la siguiente estructura:

```text
                          INTERNET
                             |
                          Cloud0
                             |
                           e0/0
                          R-ISP
                        /       \
                     e0/1       e0/2
                       |          |
                    port1        e0/0
                  FortiGate === R-SUCURSAL
                    port2       VPN IPsec
                      |
                   802.1Q
                      |
                   SW-CORE
                   /     \
                VLAN10   VLAN20
                  |        |
               PC-USR    PC-ADM


R-SUCURSAL
     |
10.62.30.0/28
   /       \
 WEB       DB
Server    Server
```

## Diagrama gráfico

<img width="2487" height="1388" alt="Screenshot 2026-10-07 130207" src="https://github.com/user-attachments/assets/9f128b27-0273-44ba-9d22-f225b94134fd" />


---

#  Direccionamiento IP

El direccionamiento utilizado está basado en la matrícula **2024-1462**, utilizando el número **62**.

| Dispositivo/Red | Dirección |
|---|---|
| VLAN 10 | `10.62.10.0/24` |
| Gateway VLAN 10 | `10.62.10.1` |
| DHCP VLAN 10 | `10.62.10.100 - 10.62.10.200` |
| VLAN 20 | `10.62.20.0/24` |
| Gateway VLAN 20 | `10.62.20.1` |
| Red servidores | `10.62.30.0/28` |
| Gateway servidores | `10.62.30.1` |
| Web Server | `10.62.30.2/28` |
| DB Server | `10.62.30.3/28` |
| Red sucursal | `10.62.40.0/24` |
| Gateway sucursal | `10.62.40.1` |
| FortiGate WAN | `203.0.113.2/30` |
| ISP → FortiGate | `203.0.113.1/30` |
| Router sucursal WAN | `198.51.100.2/30` |
| ISP → Sucursal | `198.51.100.1/30` |

---

#  VLAN 10 - Usuarios

La VLAN 10 corresponde a los usuarios de la organización.

```text
VLAN ID: 10
Nombre: USUARIOS
Red: 10.62.10.0/24
Gateway: 10.62.10.1
DHCP: Sí
```

La puerta de enlace se encuentra configurada en una subinterfaz VLAN del FortiGate.

### Evidencia

<img width="392" height="544" alt="Screenshot 2026-10-07 130455" src="https://github.com/user-attachments/assets/dfecb9bb-3421-4448-8309-450a49c73bf1" />

---

# 👨‍💼 VLAN 20 - Administrativos

La VLAN 20 corresponde a los usuarios administrativos.

```text
VLAN ID: 20
Nombre: ADMINISTRATIVOS
Red: 10.62.20.0/24
Gateway: 10.62.20.1
```

Esta VLAN permite mantener separados los usuarios administrativos de los usuarios convencionales.

### Evidencia

<img width="382" height="260" alt="Screenshot 2026-10-07 130657" src="https://github.com/user-attachments/assets/98a5ddc7-2fa1-4575-aeb2-5a71dc3555c3" />


---

# 🔗 Trunk 802.1Q

La conexión entre el FortiGate y el switch Cisco utiliza un enlace trunk IEEE 802.1Q.

El trunk transporta:

```text
VLAN 10
VLAN 20
```

La estructura es:

```text
FortiGate port2
       |
       | 802.1Q
       |
SW-CORE e0/0
```

En Cisco se verifica mediante:

```bash
show interfaces trunk
```

---

#  Servidor DHCP

FortiGate funciona como servidor DHCP para VLAN 10.

Rango configurado:

```text
10.62.10.100
        -
10.62.10.200
```

Gateway:

```text
10.62.10.1
```

El cliente obtiene automáticamente:

- Dirección IPv4.
- Máscara.
- Gateway.
- DNS.

### Evidencia

<img width="392" height="544" alt="Screenshot 2026-10-07 130455" src="https://github.com/user-attachments/assets/1b2c96b5-c790-4c54-9f41-5619056198ab" />


---

#  Salida a Internet

La infraestructura proporciona acceso a Internet mediante:

```text
PC VLAN10
    |
SW-CORE
    |
FortiGate
    |
R-ISP
    |
Cloud0
    |
Internet
```

El router ISP recibe una dirección mediante DHCP desde Cloud0.

El FortiGate utiliza como siguiente salto:

```text
203.0.113.1
```

## Prueba

```bash
ping 8.8.8.8
```

### Evidencia

<img width="522" height="164" alt="Screenshot 2026-10-07 130847" src="https://github.com/user-attachments/assets/ddcd5dc5-85fc-421d-8bce-ad28fcb01cb5" />


También se utiliza:

```bash
traceroute 8.8.8.8
```

o en Windows:

```powershell
tracert 8.8.8.8
```

### Evidencia

<img width="532" height="271" alt="Screenshot 2026-10-07 131011" src="https://github.com/user-attachments/assets/185f2763-94f0-4b52-bd95-650be6b72755" />


---

#  NAT

El FortiGate realiza NAT para permitir que los equipos internos tengan acceso hacia Internet.

Conceptualmente:

```text
10.62.10.x
      |
      | NAT
      ↓
203.0.113.2
      |
    R-ISP
      |
   Internet
```

El router ISP también realiza NAT/PAT hacia la interfaz conectada a Cloud0.

---

#  Web Server

El servidor Web utiliza Ubuntu Linux y Apache2.

Configuración:

```text
IP: 10.62.30.2
Máscara: /28
Gateway: 10.62.30.1
Servicio: HTTP
Puerto: TCP/80
```

Apache se verifica mediante:

```bash
systemctl status apache2
```

Y:

```bash
ss -lntp | grep :80
```

La página principal puede accederse mediante:

```text
http://10.62.30.2/
```


---

#  Sección /inventario

Dentro del servidor Web se creó:

```text
/inventario
```

Disponible localmente mediante:

```text
http://10.62.30.2/inventario/
```

Esta sección se utiliza para demostrar el funcionamiento del Web Filter.

Los usuarios de VLAN 10 no deben poder acceder a esta sección.

---

#  DB Server

El servidor de base de datos utiliza:

```text
Sistema: Ubuntu Linux
IP: 10.62.30.3
Máscara: /28
Gateway: 10.62.30.1
Servicio: MariaDB
Puerto: TCP/3306
```

El servicio puede verificarse mediante:

```bash
systemctl status mariadb
```

Y:

```bash
ss -lntp | grep 3306
```

---

#  VPN IPsec

Se implementó una VPN IPsec Site-to-Site entre:

```text
FortiGate
203.0.113.2

        ↕

VPN IPsec

        ↕

Cisco R-SUCURSAL
198.51.100.2
```

Las redes protegidas son:

```text
Sede principal:
10.62.10.0/24

Sucursal/servidores:
10.62.30.0/28
```

El objetivo es permitir:

```text
PC VLAN10
    |
10.62.10.0/24
    |
FortiGate
    |
======= IPsec =======
    |
R-SUCURSAL
    |
10.62.30.0/28
    |
Web Server
10.62.30.2
```

---

#  Verificación de VPN

En FortiGate:

```bash
get vpn ipsec tunnel summary
```

También:

```bash
diagnose vpn tunnel list
```

En Cisco:

```bash
show crypto isakmp sa
show crypto ipsec sa
```

### VPN activa

<img width="702" height="144" alt="Screenshot 2026-10-07 131120" src="https://github.com/user-attachments/assets/9f5799dd-afd9-4aca-93cd-4c186ade92f9" />


---

#  Traceroute mediante VPN

Desde VLAN 10 se realiza:

```bash
traceroute 10.62.30.2
```

o:

```powershell
tracert 10.62.30.2
```
---

Con la VPN activa:

```text
PC VLAN10
      ↓
FortiGate
      ↓
VPN
      ↓
R-SUCURSAL
      ↓
WEB SERVER

COMUNICACIÓN: OK
```

Al desactivar el túnel:

```text
PC VLAN10
      ↓
FortiGate
      X
      X VPN DOWN
      X
WEB SERVER

COMUNICACIÓN: FALLIDA
```

---

#  Web Filter

FortiGate implementa un perfil Web Filter para restringir el acceso desde VLAN 10 a:

```text
/inventario
```

El usuario puede acceder al sitio principal:

```text
http://10.62.30.2/
```

pero no debe poder acceder a:

```text
http://10.62.30.2/inventario/
```

Al intentar acceder, FortiGate muestra la página de violación/bloqueo de política.


#  Log del Web Filter

El evento de bloqueo se registra en FortiGate.

El registro permite comprobar:

- IP origen.
- IP destino.
- URL.
- Acción.
- Perfil Web Filter.
- Política aplicada.
---

#  Política HTTP

VLAN 10 tiene permitido acceder al Web Server mediante:

```text
TCP/80
HTTP
```

Flujo permitido:

```text
VLAN10
   |
   | TCP/80
   ↓
FortiGate
   |
   | VPN
   ↓
WEB SERVER
```

### Evidencia

<img width="492" height="652" alt="Screenshot 2026-10-07 131358" src="https://github.com/user-attachments/assets/3491a6d9-03c5-42e4-b346-9f533d9a2963" />


---

# Bloqueo de otros servicios

Los usuarios de VLAN 10 no tienen permitido utilizar otros servicios hacia el Web Server.

Por ejemplo, un intento hacia un servicio no autorizado debe ser rechazado por la política del firewall.

El evento debe quedar registrado en:

```text
Log & Report
    ↓
Forward Traffic
```


# 🔑 Configuración del switch

El switch Cisco tiene configurado:

- Hostname.
- VLAN 10.
- VLAN 20.
- Trunk 802.1Q.
- Usuario local.
- Contraseña almacenada mediante el mecanismo solicitado en la práctica.
- Banner de advertencia.
- DNS lookup deshabilitado.
- Acceso administrativo.

Ejemplo de verificación:

```bash
show running-config
```

Usuario:

```bash
show running-config | include username
```

VLAN:

```bash
show vlan brief
```

Trunk:

```bash
show interfaces trunk
```

### Evidencia
<img width="579" height="393" alt="Screenshot 2026-10-07 131742" src="https://github.com/user-attachments/assets/5a7e62af-80c5-492b-bd27-8ed3574c5ad6" />

<img width="575" height="396" alt="image" src="https://github.com/user-attachments/assets/511cf6b7-f469-46d2-8399-9d53700dd56d" />
<img width="579" height="396" alt="Screenshot 2026-10-07 131819" src="https://github.com/user-attachments/assets/02c07c9c-8fe9-43aa-ab42-f41f1099c98d" />

<img width="565" height="390" alt="Screenshot 2026-10-07 131919" src="https://github.com/user-attachments/assets/5d26b765-ea82-4f4d-90e4-090ba9580f97" />

---

# 🧪 Pruebas realizadas

Durante la implementación se realizaron pruebas de:

| Prueba | Resultado esperado |
|---|---|
| DHCP VLAN 10 | Exitoso |
| Ping VLAN10 → Gateway | Exitoso |
| Ping FortiGate → ISP | Exitoso |
| Ping Internet | Exitoso |
| Traceroute Internet | Exitoso |
| HTTP Web Server | Exitoso |
| MariaDB TCP/3306 | Escuchando |
| VPN FortiGate ↔ Cisco | Establecida |
| VLAN10 → Web mediante VPN | Exitoso |
| VPN desactivada → Web | Fallido |
| HTTP/80 → Web | Permitido |
| Otro servicio → Web | Denegado |
| `/inventario` desde VLAN10 | Bloqueado |
| Web Filter log | Generado |
| Forward Traffic log | Generado |


#  Archivos de configuración

Las configuraciones de los dispositivos se encuentran en:

```text
/configs
```

Archivos:

```text
R-ISP.cfg
SW-CORE.cfg
FGT-2024-1462.conf
R-SUCURSAL.cfg
```

Cada archivo contiene la configuración correspondiente al dispositivo.

---

#  Comandos principales de verificación

## FortiGate

```bash
show system interface
show system dhcp server
show firewall policy
get router info routing-table all
get vpn ipsec tunnel summary
diagnose vpn tunnel list
```

## Cisco ISP

```bash
show ip interface brief
show ip route
show ip nat translations
show ip nat statistics
```

## Cisco Switch

```bash
show vlan brief
show interfaces trunk
show running-config
```

## Cisco Sucursal

```bash
show ip interface brief
show ip route
show crypto isakmp sa
show crypto ipsec sa
```

## Ubuntu Web Server

```bash
ip addr
ip route
systemctl status apache2
ss -lntp | grep :80
```

## Ubuntu DB Server

```bash
ip addr
ip route
systemctl status mariadb
ss -lntp | grep 3306
```

---

#  Consideraciones de seguridad

El proyecto aplica diferentes controles de seguridad:

1. Segmentación mediante VLAN.
2. Separación entre usuarios y administrativos.
3. Políticas de firewall.
4. Principio de mínimo privilegio para acceso al Web Server.
5. Filtrado de contenido mediante Web Filter.
6. Registro de tráfico permitido y bloqueado.
7. VPN IPsec para proteger comunicaciones entre redes.
8. NAT para evitar exposición directa de las redes internas.
9. Credenciales locales para administración del switch.
10. Banner de advertencia para acceso administrativo.

---

# Seguridad del repositorio

Las contraseñas, PSK de VPN y otras credenciales reales no deben publicarse directamente en un repositorio público.

Ejemplo:

```text
PSK=<REDACTED>
PASSWORD=<REDACTED>
```

Las configuraciones publicadas deben utilizar valores sanitizados cuando contengan información sensible.

---

#  Conclusión

La implementación permitió construir una infraestructura empresarial virtual segmentada y protegida mediante FortiGate y dispositivos Cisco.

La VLAN 10 proporciona conectividad a los usuarios mediante DHCP, mientras que VLAN 20 mantiene separados los dispositivos administrativos.

FortiGate funciona como punto central de seguridad, proporcionando enrutamiento, NAT, políticas de firewall, filtrado web y conectividad VPN.

La VPN IPsec Site-to-Site proporciona comunicación entre la sede principal y la red remota donde se encuentra el Web Server. Las pruebas realizadas con el túnel activo e inactivo permiten comprobar que el acceso entre ambas redes depende de la VPN.

El Web Filter restringe el acceso de los usuarios a contenido específico del servidor Web y genera registros que permiten demostrar la aplicación de la política.

Finalmente, Apache y MariaDB proporcionan los servicios requeridos de HTTP y base de datos, completando una infraestructura que integra switching, routing, firewall, VPN y servicios Linux dentro de EVE-NG.

---

## Proyecto

**Proyecto de Seguridad de Redes con FortiGate y Cisco**

**Matrícula:** 2024-1462

**Plataforma:** EVE-NG
