![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Configuración de un VPN *Site-to-Site* con AWS

En esta práctica implementaremos un enlace VPN de tipo *Site-to-Site* entre una red local (que simularemos dentro de VirtualBox) y una VPC de AWS.

## Objetivo
Configurar una conexión **site-to-site VPN** entre una red local en VirtualBox y una VPC en AWS. Usaremos una máquina [***pfSense***](https://es.wikipedia.org/wiki/PfSense) como *Customer Gateway (CGW)* en el extremo del enlace VPN que corresponde a la red local física del cliente *(on-premises).* Esto permitirá la comunicación segura entre ambas redes.

> **NOTA:** la configuración de VPN *site-to-site* requiere que **el extremo del VPN que corresponde a la localización del cliente disponga de una IP pública**. Esto significa que esta práctica no se puede hacer desde la red del IES, porque no podemos disponer de dicha dirección IP pública. La solución que proponemos en esta práctica es **realizar la implementación en casa**, usando vuestra red doméstica, si es posible.  
Si no es posible, se puede considerar la alternativa de crear una red virtual en otra región de AWS que simule la red local, pero el ejercicio queda un tanto desvirtuado. La primera opción es muy preferible, y es para la que se proporcionan instrucciones. Si necesitas hacer uso de la otra alternativa, discútelo con tu profesor(a).

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- Responder a estas preguntas:
  - ¿Qué ventajas ofrece un VPN site-to-site frente a un VPN de acceso remoto?
  - ¿Cómo influye el cifrado en el rendimiento de la conexión VPN?
  - ¿Por qué es importante definir correctamente las rutas estáticas en ambos extremos del VPN?
  - ¿Qué medidas de seguridad adicionales podrían implementarse para reforzar la protección del VPN?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

---
## Infraestructura

﻿![Gráfico de infraestructura de la práctica](https://juliogaray.github.io/CEASRN.ARN/docs/imgARN-Práctica3.02.svg)

### En VirtualBox:
Configuraremos dos máquinas:
1. **pfSense (Customer Gateway)**
   - **Interfaz WAN:** Adaptador puente, con una dirección IP fija del rango de la red doméstica: Ж.Ж.Ж.Ж
   - **Interfaz LAN:** Red interna «intnet» (10.1.1.1/24)
2. **Servidor Interno**
   - **Interfaz LAN:** Red interna «intnet» (10.1.1.10/24, con el equipo pfSense —dirección del Interfaz LAN— como puerta de enlace)

### En la red doméstica donde realicemos la práctica:
Para que AWS pueda establecer el VPN con la *Customer Gateway (CGW),* **la CGW debe tener una dirección IP públicamente accesible**. Si hacemos la práctica detrás de un router, es necesario **redirigir los siguientes puertos** en dicho router hacia la IP —del Interfaz WAN— de la máquina virtual pfSense:
   - **UDP 500** [(IKE, intercambio de claves)](https://es.wikipedia.org/wiki/Internet_key_exchange)
   - **UDP 4500** [(NAT-Traversal para IPsec)]((https://en.wikipedia.org/wiki/NAT_traversal#IPsec))
   - **ESP (Protocolo 50)**[^1] [(Encapsulación segura de paquetes IPsec)](https://en.wikipedia.org/wiki/IPsec#Encapsulating_Security_Payload)  

   Pasos para implementar la redirección:
   1. **Accede a la configuración del router** y buscar la opción de redirección de puertos o *"port forwarding".*
   2. **Redirige los puertos 500 y 4500 UDP** al equipo donde has instalado pfSense (IP Ж.Ж.Ж.Ж).
   3. **Verifica la IP pública del router** (por ejemplo, en [https://whatismyipaddress.com/](https://whatismyipaddress.com/)) y úsala al configurar el Customer Gateway en AWS.

   Debes asegurarte de **que tu router tenga una dirección IP pública hacia el exterior** (y no, por ejemplo, una [dirección CGNAT](https://es.wikipedia.org/wiki/Carrier_Grade_NAT)) y de que tu IP pública no cambie con frecuencia (si no es estática, puede que necesites usar [DDNS](https://es.wikipedia.org/wiki/DNS_din%C3%A1mico)).

### En AWS:
1. **VPC** con CIDR `10.0.0.0/16`
2. **Subred privada** `10.0.4.0/24`
3. **Instancia EC2** ejecutando Amazon Linux, del tamaño mínimo, simplemente para probar la conexión. IP: `10.0.4.10`; puerta de enlace `10.0.4.1`
4. **Virtual Private Gateway (VGW)**
5. **Customer Gateway (CGW)** con la IP pública de pfSense (en esta práctica, la IP pública de tu casa)
6. **Site-to-Site VPN** configurado entre VGW y CGW

---
## Paso 1: instalación de pfSense en VirtualBox
1. Descarga la ISO de pfSense desde https://www.pfsense.org/download/
2. Crea una nueva máquina virtual en VirtualBox:
   - **Memoria:** 1 GB
   - **Disco:** 8 GB
   - **Red:**
     - **Adaptador 1:** Adaptador puente (WAN)
     - **Adaptador 2:** Red Interna (LAN).
3. Instala pfSense con configuración por defecto (recuerda que la dirección IP WAN debe ser una dirección fija de tu red doméstica; la dirección IP LAN debe ser 10.0.1.1/24))
4. Una vez instalado, accede a la interfaz web desde el servidor interno (`https://10.0.1.1`)

## Paso 2: configuración del VPN *Site-to-Site* en AWS
1. **Crear la VPC**
   - Ve a **AWS Console > VPC > Your VPCs**.
   - Haz clic en **Create VPC**.
   - Ingresa un nombre y el rango CIDR `10.0.0.0/16`.
   - Asegúrate de tener al menos una subred privada con el rango `10.0.4.0/24`.
   - Guarda los cambios.
2. **Crear la Virtual Private Gateway (VGW)**
   - Ve a **AWS Console > VPC > Virtual Private Gateways** (o *puerta de enlace privada virtual).*
   - Haz clic en **Create Virtual Private Gateway**.
   - Asigna un nombre y mantén la configuración predeterminada.
   - Una vez creada, asocia la VGW creada a la VPC (`10.0.0.0/16`).
3. **Crear el Customer Gateway (CGW)**
   - Ve a **AWS Console > VPC > Customer Gateways** (o *Gateways de cliente).*
   - Haz clic en **Create Customer Gateway**.
   - Asigna un nombre.
   - Introduce la IP pública de tu red doméstica, para conectar con la máquina *pfSense.*
   - Guarda los cambios.
4. **Crear el VPN Site-to-Site**
   - Ve a **AWS Console > VPC > Site-to-Site VPN Connections** (o *Conexiones de VPN sitio a sitio).*
   - Haz clic en **Create VPN Connection**.
   - Selecciona el VGW creado anteriormente.
   - Selecciona el Customer Gateway (CGW) previamente creado.
   - Mantén la configuración predeterminada para **Routing Options** *(static* o *BGP* según el caso).
   - Introduce la red `10.1.1.0/24` en *Static Routes (CIDR de red IPv4 remota)* si es necesario.
   - Guarda los cambios y espera a que el estado pase a **Available**.
5. **Descargar la configuración generada para pfSense**
   - En la sección **Site-to-Site VPN Connections**, selecciona el VPN creado.
   - Haz clic en **Download Configuration**.
   - Selecciona el proveedor **Generic** y la versión de IKE **ikev2**.
   - Descarga y guarda el archivo para configurar pfSense.

---
## 5. Configuración del VPN en pfSense
1. Ve a **VPN > IPsec > Tunnels** y agrega un nuevo túnel.
2. Configura los parámetros según la configuración descargada de AWS.
3. En **Phase 1**,[^2] establece:
   - **Remote Gateway:** IP de uno de los dos túneles que provee AWS para cada VPN
   - **Authentication Method:** Pre-Shared Key
   - **Key Exchange Version:** IKEv2
   - **Encryption:** AES-256
4. En **Phase 2**,[^3] agrega:
   - **Local Network:** `10.0.1.0/24`
   - **Remote Network:** `10.0.2.0/24`
5. Guarda y aplica los cambios.

---
## 6. Pruebas de conectividad
1. Desde el servidor interno en VirtualBox, prueba conectividad con:
   ```bash
   ping 10.0.2.10
   ```
2. Desde AWS (una instancia en la subred privada), prueba conectividad con:
   ```bash
   ping 10.0.1.10
   ```
3. Revisa los logs en pfSense si hay problemas: **Status > System Logs > IPsec**




&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)

---
### Notas
[^1]: Dado que la mayoría de los routers domésticos no permiten redireccionar el protocolo ESP directamente, se necesita NAT-Traversal (UDP 4500), que encapsula ESP en UDP. Esto ya está soportado por pfSense y AWS.
[^2]: **Phase 1 (fase 1)** establece los parámetros de autenticación y cifrado entre los dos extremos del VPN antes de permitir la transmisión de datos.
[^3]: **Phase 2 (fase 2)** define los protocolos y redes que se protegerán dentro del túnel VPN, incluyendo los rangos de direcciones IP que pueden comunicarse entre sí.
