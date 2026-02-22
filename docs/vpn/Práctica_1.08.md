![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Implementación de AWS PrivateLink

En esta práctica configuraremos **AWS PrivateLink** utilizando un **Network Load Balancer (NLB)** para exponer un servicio privado. Al finalizar, habremos creado un servicio básico accesible mediante un **Interface VPC Endpoint** desde otra VPC.

## Objetivos
1. Configurar un **Network Load Balancer (NLB)** para un servicio privado.
2. Crear un **Endpoint Service** asociado al NLB.
3. Configurar un **Interface VPC Endpoint** en otra VPC para acceder al servicio.
4. Verificar la comunicación privada mediante AWS PrivateLink.

![Esquema de la pŕactica 1.08](https://juliogaray.github.io/CEASRN.ARN/docs/imgPráctica-AWS-PrivateLink.svg)

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- **Explica en cada paso lo que estás haciendo y por qué es necesario o para qué sirve.**
- Responder a estas preguntas:
  - ¿Qué significa que el NLB sea «interno» y cómo afecta esto a la seguridad de la arquitectura?
  - ¿Cuál es la función de un *Endpoint Service* y por qué es necesario para exponer un servicio privado?
  - ¿Cómo difiere un *Interface VPC Endpoint* de un *Gateway VPC Endpoint?* ¿En qué situaciones usarías cada uno?
  - Cuando configuras un NLB y creas un *Target Group,* ¿qué sucede si la instancia EC2 registrada en el *target group* falla?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

---

## Paso 1: configurar el servicio *backend*

1. **Crear una VPC para el proveedor de servicios:**
   - En la consola de AWS, ve a **VPC** > **Your VPCs** > **Create VPC**.
     - **Nombre:** `Proveedor-VPC`
     - **CIDR block:** `10.0.0.0/16`
     - **Subnets:** Crea dos subredes públicas en diferentes AZs.
   - **Nota:** Asegúrate de habilitar una **Internet Gateway** para que las instancias puedan descargar software durante la configuración.

2. **Lanzar una instancia EC2:**
   - Ve a **EC2** > **Launch Instances**.
     - **Nombre:** `Instancia-Backend`
     - **AMI:** Amazon Linux 2.
     - **Tipo de instancia:** `t2.micro` (suficiente para este laboratorio).
     - **Red:** Usa la **Proveedor-VPC** y una de sus subredes públicas.
     - **Grupo de seguridad:** Permite tráfico en el puerto `80` desde `0.0.0.0/0`.

3. **Configurar un servidor web simple:**
   - Conéctate a la instancia mediante SSH.
   - Instala un servidor web:
     ```bash
     sudo yum update -y
     sudo yum install httpd -y
     sudo systemctl start httpd
     sudo systemctl enable httpd
     echo "Hola desde el servicio privado en AWS PrivateLink" | sudo tee /var/www/html/index.html
     ```
   - Verifica el servidor accediendo a la dirección pública de la instancia en un navegador (`http://<IP-Pública>`).


## Paso 2: configurar el *Network Load Balancer*

1. **Crear un NLB:**
   - Ve a **EC2** > **Load Balancers** > **Create Load Balancer**.
   - Selecciona **Network Load Balancer**.
   - Configura:
     - **Nombre:** `NLB-PrivateLink`
     - **Esquema:** Interno.
     - **VPC:** `Proveedor-VPC`.
     - **Subnets:** Selecciona las dos subredes públicas creadas anteriormente.

2. **Configurar un *listener:***
   - **Protocolo:** TCP.
   - **Puerto:** 80.

3. **Crear un *target group:***
   - Ve a **Target Groups** > **Create Target Group**.
     - **Tipo de destino:** Instance.
     - **Nombre:** `Backend-Targets`.
     - **Protocolo:** TCP.
     - **VPC:** `Proveedor-VPC`.
   - **Registrar instancias:**
     - Selecciona la instancia `Instancia-Backend` configurada anteriormente.

4. **Asociar el *target group* al NLB:**
   - Vuelve a **Load Balancers** y selecciona el NLB.
   - En **Listeners**, edita el listener TCP en el puerto `80` y asocia el target group `Backend-Targets`.

5. **Verifica el NLB:**
   - Obtén la dirección privada del NLB (aparece en la configuración).
   - Desde otra instancia en la misma VPC, realiza una prueba:
     ```bash
     curl <Dirección-IP-Privada-NLB>
     ```
   - Deberías ver el mensaje: `Hola desde el servicio privado en AWS PrivateLink`.


## Paso 3: crear el *Endpoint Service*

1. **Crear un Endpoint Service:**
   - Ve a **VPC** > **Endpoint Services** > **Create Endpoint Service**.
   - Configura:
     - **Nombre:** `EpS-PrivateLink`.
     - **NLB asociado:** Selecciona `NLB-PrivateLink`.
     - **Aceptación de solicitudes:** Habilita **Require acceptance** para probar permisos.

2. **Compartir el servicio:**
   - Comparte el *Endpoint Service* con otra cuenta o utiliza tu misma cuenta.
   - Copia el nombre del *Endpoint Service;* lo necesitarás más adelante.

## Paso 4: crear el *Interface VPC Endpoint*

1. **Crear una VPC para el consumidor:**
   - Sigue las instrucciones del Paso 1 (punto 1), pero usa el nombre `Consumidor-VPC` y un rango `10.1.0.0/16`.

2. **Crear el *Interface VPC Endpoint:***
   - Ve a **VPC** > **Endpoints** > **Create Endpoint**.
   - Configura:
     - **Servicio:** Busca el nombre del Endpoint Service creado anteriormente.
     - **VPC:** `Consumidor-VPC`.
     - **Subnets:** Selecciona una subred privada de la `Consumidor-VPC`.
     - **Seguridad:** Usa un grupo de seguridad que permita tráfico en el puerto `80`.

3. **Aceptar la solicitud del consumidor (si se requiere):**
   - Ve a **Endpoint Services**, selecciona tu servicio y aprueba la solicitud.

## Paso 5: probar la configuración

1. **Lanzar una instancia en la Consumidor-VPC:**
   - Llámala `Instancia-Consumidor`
   - Conéctate mediante SSH y realiza una prueba hacia la dirección privada del *Interface VPC Endpoint:*
     ```bash
     curl <IP-del-Interface-Endpoint>
     ```
   - Deberías ver el mensaje: `Hola desde el servicio privado en AWS PrivateLink`.

---

## Monitorización

- Usa herramientas como **VPC Flow Logs** para observar el tráfico.
- Optativo: Configura métricas personalizadas en **CloudWatch** para monitorizar el uso del Endpoint Service.
- Haz una captura bonita para la entrega. :wink:

## Paso 8: responde a las preguntas...

... de la sección «Entrega» :wink:

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
