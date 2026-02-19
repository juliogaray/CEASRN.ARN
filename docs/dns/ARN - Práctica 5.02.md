![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Amazon Route 53 - Alta disponibilidad y resolución DNS híbrida

En esta práctica veremos otras capacidades del servicio Amazon Route 53, centrándonos en las opciones de alta disponibilidad (con *health checks* y redirección automática en caso de fallo) y resolución de nombres DNS en entornos híbridos (AWS + *on premise).*

## Objetivos
1. Configurar una red híbrida entre AWS y una red local simulada.
2. Implementar resolución DNS cruzada entre ambas redes.
3. Configurar *health checks* de Route 53 para supervisar servicios web.
4. Implementar un *failover DNS* automático basado en disponibilidad.

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- Responder a estas preguntas:
  - ¿Qué sucede si ambos servidores están caídos?
  - ¿Cómo podrías automatizar la recuperación con Lambda o SSM?
  - ¿Cómo implementarías [*weighted routing*](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-weighted.html) para balancear carga?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (nombre del subdominio, registros creados, etcétera).

---

## Parte 1 – Preparación de la red híbrida


### 1.1 Crear la VPC en AWS

1. Accede a la consola de AWS > **VPC** > **Your VPCs** > _Create VPC:_
   - **Name tag**: `VPC-Hibrida`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - Habilita *DNS hostname* y *DNS resolution.*

2. Crea **2 subredes**:
   - Subred pública: `10.0.1.0/24` en la zona permitida por nuestra *landing zone.*
   - Subred privada: `10.0.2.0/24` en la misma zona

3. Crea una **Internet Gateway**:
   - Nómbrala `IGW-Hibrida`
   - Asóciala a la VPC.

4. Crea una **Tabla de rutas** para la subred pública:
   - Asociada a la subred `10.0.1.0/24`
   - Ruta: `0.0.0.0/0` → Target: `Internet Gateway`

5. Deja la subred privada sin acceso a internet (para pruebas híbridas, podríamos usar NAT optativo más adelante).


### 1.2 Lanzar instancia EC2 en la subred pública

1. Ir a **EC2 > Launch instance**:
   - Nombre: `WebPrincipal`
   - AMI: Amazon Linux 2
   - Tipo: t2.micro (gratis)
   - Subred: `10.0.1.0/24`
   - Asignar IP pública automática: **Sí**
   - Grupos de seguridad:
     - HTTP (puerto 80)
     - HTTPS (puerto 443)
     - SSH (puerto 22)

2. Instala un servidor web:
   - Conéctate por SSH:
     ```bash
     ssh -i tu_clave.pem ec2-user@<ip_publica>
     ```
   - Amazon Linux:
     ```bash
     sudo yum install -y httpd
     sudo systemctl start httpd
     sudo systemctl enable httpd
     echo "Servidor Principal" | sudo tee /var/www/html/index.html
     ```

### 1.3 Simular red *on-premise* en VirtualBox con *ipfire*

1. En VirtualBox:
   - Crea una red interna: `intnet`
   - Añade una VM *ipfire* con los siguientes interfaces:
     - Red verde: `intnet` (IP: `10.1.1.1/24`)
     - Red roja: adaptador puente para salir a Internet, con dirección IP fija de tu red doméstica (Ж.Ж.Ж.Ж).
   - VM cliente local (Linux):
     - En `intnet`
     - IP estática: `10.1.1.10`
     - DNS temporal: `8.8.8.8` (lo cambiaremos luego)

2. Desde ipfire, asegúrate de que la máquina cliente tiene acceso a Internet (normalmente es así por defecto).

3. Este esquema requiere que tu servidor ipfire tenga una IP pública accesible desde Internet (y esto no funcionará si en tu casa se usa [CGNAT](https://es.wikipedia.org/wiki/Carrier_Grade_NAT)).  
Para ello, abre los puertos correspondientes en tu router, redirigiéndolos a la dirección IP de tu máquina ipfire (Ж.Ж.Ж.Ж). Los puertos requeridos son:

   - **UDP 500** [(IKE, intercambio de claves)](https://es.wikipedia.org/wiki/Internet_key_exchange)
   - **UDP 4500** [(NAT-Traversal para IPsec)]((https://en.wikipedia.org/wiki/NAT_traversal#IPsec))
   - **ESP (Protocolo 50)**[^1] [(Encapsulación segura de paquetes IPsec)](https://en.wikipedia.org/wiki/IPsec#Encapsulating_Security_Payload) - Normalmente esto no es posible en un router doméstico, y en su lugar se abre el puerto anterior (UDP4500) para permitir el tráfico ESP a través de dicho puerto (así que si no puedes hacerlo, no te preocupes).


### 1.4 Crear VPN *site-to-site* entre ipfire y AWS

#### En AWS:
1. Ir a **VPC > VPN Connections > Create VPN Connection**:
   - Tipo: *Site-to-site VPN*
   - Virtual Private Gateway:
     - Si no tienes una, créala y asóciala a la VPC.
   - Customer Gateway:
     - IP pública de tu ipfire (en realidad la IP pública de tu router de casa —si no estás en CGNAT, recuerda—)
     - Routing: estático
     - CIDR del lado *on-premise:* `10.1.1.0/24`
   - Establece al menos una conexión de túnel

2. Anota:
   - direcciones IP del túnel de AWS *(outside* IP, *inside* IP)
   - Claves precompartidas
   - Enrutamiento a `10.0.0.0/16`

#### En ipfire:
1. Configura la VPN en **IPsec** usando la IP y claves proporcionadas por AWS.
2. Asegúrate de permitir tráfico entre `10.1.1.0/24` y `10.0.0.0/16`.


### 1.5 Configurar resolución DNS híbrida

1. Desde el cliente on-premise (`10.1.1.10`), añade el servidor DNS de AWS en `/etc/resolv.conf`:
   ```bash
   nameserver 10.0.0.2  # Resolver interno de AWS
   ```

2. Desde AWS, asegúrate de que la VPC tiene habilitada la resolución y asignación de _hostnames_ DNS (en la configuración de la VPC).

3. Verifica conectividad:
   - Haz ping de `10.1.1.10` a `10.0.1.x` (servidor EC2)
   - Usa `dig` para probar la resolución (verás esto mejor en el paso 4)


### Resultado de la parte 1

- Tienes una red híbrida funcional entre AWS (`10.0.0.0/16`) y una red simulada local (`10.1.1.0/24`).
- Puedes comunicarte entre instancias y empezar a configurar Route 53.
- La resolución DNS ya puede usar el resolver interno de AWS a través de la VPN.

---

## Parte 2: DNS


### 2.1 Configurar *Health Checks*

1. En Route 53, accede a *Health Checks*.
2. Crea un nuevo *health check* con estas características:
   - Tipo: HTTP
   - IP: IP pública de la EC2 en AWS
   - Puerto: 80
   - Path: `/` (o algún archivo de prueba, como `/status.html`)
3. Optativo: habilita alarmas de _CloudWatch_ para disponer de notificaciones adicionales.


### 2.2: Configurar *Failover Routing*

1. **Lanza una segunda instancia EC2** (backup) en otra subred con su propio servidor web (contenido distinto).
2. En la zona hospedada de Route 53, crear dos registros A (por ejemplo, `web.midominio.net`):
   - **Primary**: Apunta a la IP de la instancia principal.
     - Configurado como *Failover* -> *Primary*
     - Asociado al *health check* anterior.
   - **Secondary**: Apunta a la IP de la instancia de respaldo.
     - Configurado como *Failover* -> *Secondary*

### 2.3: Validar resolución DNS híbrida

1. En la máquina on-premise, configura `/etc/resolv.conf` para que use el *resolver* de la VPC (a través de la VPN).
2. Haz una consulta `dig web.midominio.net` y verifica que se resuelve correctamente.
3. Apaga el servidor principal (o el puerto 80) para simular una caída.
4. Observa que, después del periodo de comprobación (entre 30 s y 2 minutos), Route 53 responde con la IP secundaria.

---

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)

[^1]: Dado que la mayoría de los routers domésticos no permiten redireccionar el protocolo ESP directamente, se necesita NAT-Traversal (UDP 4500), que encapsula ESP en UDP. Esto ya está soportado por _ipfire_ y AWS.

