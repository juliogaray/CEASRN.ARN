![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Balanceo de cargas: *Network Load Balancer (NLB)*

(v20241124)

## ¿Qué es un Network Load Balancer (NLB)? ![Icono de Network Load Balancer](https://raw.githubusercontent.com/juliogaray/CEASRN.ARN/docs/img/NLB.svg)

Un **Network Load Balancer (NLB)** es un servicio de AWS que distribuye el tráfico entrante entre varias instancias o servicios [backend](https://es.wikipedia.org/wiki/Front_end_y_back_end) en una red, asegurando alta disponibilidad, escalabilidad y tolerancia a fallos. Está diseñado específicamente para manejar grandes volúmenes de tráfico y ofrecer conexiones de baja latencia a nivel de red (capa 4 del [modelo OSI](https://es.wikipedia.org/wiki/Modelo_OSI)).

## ¿Cómo funciona un NLB?
El NLB actúa como un «puente» que recibe solicitudes desde los clientes y las redirige hacia las instancias *backend* de manera eficiente. Esto lo logra mediante:

1. **Asignación de direcciones IP:**
   - Al crear un NLB, este asigna automáticamente direcciones IP públicas o privadas para que los clientes puedan enviar solicitudes.
   - Si el tráfico es interno (solo dentro de AWS), se usa una IP privada.

2. **Escucha de puertos (Listeners):**
   - Los **listeners** son configuraciones del NLB que especifican en qué puertos y protocolos debe escuchar el tráfico (por ejemplo, TCP o UDP).

3. **Rutas de tráfico a través de *Target Groups:***
   - Los **target groups** son grupos de instancias, contenedores u otros recursos *backend* hacia los cuales el NLB redirige las solicitudes.
   - El NLB verifica la salud de las instancias en el target group mediante controles periódicos (**health checks**) y evita enviar tráfico a las que están inactivas.

4. **Distribución de tráfico:**
   - Distribuye el tráfico según la capacidad y estado de las instancias *backend,* asegurando que ninguna se sobrecargue.

## Características clave del NLB

1. **Alto rendimiento:**
   - Diseñado para manejar millones de solicitudes por segundo, lo que lo hace ideal para aplicaciones que necesitan rendimiento extremo.

2. **Conexión en capa 4 (TCP/UDP):**
   - El NLB trabaja en la capa de transporte, redirigiendo tráfico basado en puertos y protocolos (TCP, UDP, o ambos).
   - No analiza el contenido de las solicitudes (como haría un *Application Load Balancer).*

3. **Dirección IP estática:**
   - Los NLBs pueden asociarse a direcciones IP estáticas o Elastic IPs, lo que es útil para integraciones con sistemas externos.

4. **Integración con AWS PrivateLink:**
   - Es el único tipo de *Load Balancer* que se puede usar con **AWS PrivateLink**, permitiendo exponer servicios de forma privada.

5. **Resistencia a fallos:**
   - El NLB es tolerante a fallos porque distribuye el tráfico entre varias zonas de disponibilidad (AZs) automáticamente.

## Ventajas del NLB

- **Baja latencia:** gracias a su diseño optimizado, puede manejar grandes volúmenes de tráfico con latencia mínima.
- **Escalabilidad automática:** ajusta su capacidad automáticamente en función de la carga.
- **Compatibilidad:** soporta cargas basadas en TCP, UDP y TLS, lo que lo hace ideal para aplicaciones en tiempo real como VoIP, *gaming* o servicios de *streaming.*

## Casos de uso del NLB

1. **Aplicaciones críticas de alto rendimiento:**
   - Sistemas que requieren manejar millones de conexiones simultáneas, como plataformas de juegos en línea o aplicaciones de mensajería en tiempo real.

2. **Exposición de servicios con *AWS PrivateLink:***
   - El NLB es indispensable para habilitar conexiones privadas hacia servicios personalizados mediante *PrivateLink.*

3. **Integración con redes on-premises:**
   - Con IPs estáticas o *Elastic IPs,* el NLB puede integrarse fácilmente con sistemas en redes locales.

4. **Aplicaciones que usan protocolos no compatibles con ALB:**
   - Servicios que necesitan UDP (por ejemplo, DNS o VoIP) o tráfico puro en TCP.

## Ejemplo práctico

Supongamos que tienes una aplicación web que utiliza varios servidores para manejar el tráfico. Sin un balanceador de carga, los clientes enviarían solicitudes directamente a los servidores, y si uno falla, el servicio dejaría de funcionar. Con un NLB:

1. El NLB escucha las solicitudes entrantes en un puerto específico (por ejemplo, 443 para HTTPS).
2. Redirige el tráfico hacia las instancias backend disponibles en el **target group**.
3. Si un servidor falla, el NLB deja de enviar tráfico a ese servidor y lo redirige hacia otros servidores saludables.

De este modo, los clientes siempre tienen una experiencia fluida y el servicio permanece disponible.

## Comparación con otros *Load Balancers* en AWS

| **Característica**         | **Network Load Balancer (NLB)**    | **Application Load Balancer (ALB)** | **Classic Load Balancer (CLB)** |
|----------------------------|------------------------------------|-------------------------------------|---------------------------------|
| **Capa OSI**               | Capa 4 (TCP/UDP)                  | Capa 7 (HTTP/HTTPS)                 | Capa 4 y 7                     |
| **Rendimiento**            | Muy alto (millones de conexiones) | Alto                                | Menos eficiente                |
| **Protocolos soportados**  | TCP, UDP, TLS                     | HTTP, HTTPS, WebSockets             | TCP, HTTP, HTTPS               |
| **PrivateLink compatible** | Sí                                | No                                  | No                             |

## Conclusión

El **Network Load Balancer (NLB)** es una herramienta esencial para manejar tráfico de red de alta demanda, especialmente en aplicaciones críticas donde la latencia y el rendimiento son factores clave. En combinación con AWS PrivateLink, permite construir soluciones privadas, escalables y seguras.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
