![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Comunicaciones seguras con AWS PrivateLink

(v20241124)

En entornos empresariales modernos, garantizar una comunicación segura y privada entre aplicaciones y servicios es crucial, especialmente en la nube. **AWS PrivateLink** es una solución de AWS que permite establecer conexiones privadas y seguras entre servicios alojados en AWS y clientes que necesitan acceder a ellos.

﻿![Esquema de conexión entre VPC usando AWS PrivateLink](https://docs.aws.amazon.com/images/prescriptive-guidance/latest/integrate-third-party-services/images/p1_privatelink.png)

## ¿Qué es AWS PrivateLink? ![Icono de AWS PrivateLink Gateway](http://julio.iespacomolla.es/CEARSN.ARN/imágenes/PrivateLink.svg)
**AWS PrivateLink** es un servicio que permite a las aplicaciones comunicarse entre sí **utilizando la red privada de AWS**, sin necesidad de exponer datos a Internet. Esto se logra mediante el uso de **Interface VPC Endpoints**, que conectan servicios alojados en una VPC (Virtual Private Cloud) con otras VPCs o servicios de AWS.

En lugar de acceder a un servicio a través de su dirección pública (como una API en Internet), **AWS PrivateLink** redirige todo el tráfico a través de la infraestructura privada de AWS. Esto ofrece mayor seguridad, privacidad y rendimiento.


## Componentes clave de AWS PrivateLink

Para entender AWS PrivateLink, es importante conocer sus componentes principales:

1. **Endpoint Service**:
   - Este es el servicio que un proveedor expone de manera privada a través de AWS PrivateLink.
   - Se configura vinculándolo a un balanceador de carga (ver componente n.º 3), que distribuye el tráfico entrante hacia las instancias o servicios *backend* del proveedor.
   - Los proveedores pueden compartir su *Endpoint Service* con cuentas específicas de AWS o incluso hacerlo público para cualquier cliente dentro de AWS.

2. **Interface VPC Endpoint**:
   - Este es el recurso que los clientes (consumidores del servicio) crean en su propia VPC para conectarse al *Endpoint Service* del proveedor.
   - Es un interfaz ENI (**Elastic Network Interface**) que proporciona una dirección IP privada en la VPC del cliente.
   - Los clientes usan este punto de acceso para enviar y recibir tráfico hacia el *Endpoint Service,* como si se tratara de un recurso local.

3. **Network Load Balancer (NLB)**:
   - El [**NLB**](http://julio.iespacomolla.es/CEARSN.ARN/NLB.md) es un componente necesario para que el proveedor exponga su servicio mediante *PrivateLink.* Se encarga de recibir el tráfico de los Interface VPC Endpoints y distribuirlo entre las instancias *backend.* Ten en cuenta que no se puede usar AWS PrivateLink sin un NLB. Si no queremos usar un NLB, podemos usar métodos de conexión alternativos a AWS PrivateLink, como *VPC Peering* o *Transit Gateway.* Por supuesto, podemos acceder a servicios gestionados por AWS (como p. ej. S3) sin necesidad de un NLB porque no requieren un Endpoint Service personalizado.

## Funcionamiento de AWS PrivateLink
El flujo básico de PrivateLink puede resumirse en los siguientes pasos:

1. **Proveedor de servicios:**
   - Configura un **Endpoint Service** asociado a un *Network Load Balancer.*
   - Comparte el *Endpoint Service* con los clientes mediante permisos específicos.

2. **Consumidor de servicios:**
   - Configura un **Interface VPC Endpoint** en su propia VPC.
   - Una vez configurado, el cliente puede enviar solicitudes al servicio del proveedor usando direcciones IP privadas.

3. **Comunicación privada:**
   - El tráfico fluye directamente entre el Interface VPC Endpoint del cliente y el Endpoint Service del proveedor a través de la red interna de AWS (o sea, como hemos dicho, sin exponer datos a Internet).

## Ventajas de AWS PrivateLink

1. **Seguridad:**
   - Todo el tráfico permanece dentro de la red privada de AWS, evitando la exposición a Internet.
   - Compatible con configuraciones de políticas de seguridad y control de acceso mediante AWS IAM.

2. **Privacidad:**
   - Se utilizan direcciones IP privadas, lo que asegura que los datos no salgan del entorno privado.

3. **Escalabilidad y rendimiento:**
   - AWS PrivateLink utiliza Network Load Balancers, que son altamente escalables y diseñados para manejar grandes volúmenes de tráfico.

4. **Facilidad de integración:**
   - Compatible con servicios de AWS como *S3, DynamoDB* y servicios personalizados.

## Caso práctico
Imagina que una empresa desarrolla un servicio de análisis de datos y desea que sus clientes puedan enviar datos de manera privada y segura. Usando AWS PrivateLink:

1. **El proveedor de servicios** configura su *Endpoint Service* vinculado a un NLB que redirige las solicitudes hacia su aplicación de análisis.
2. **Los clientes** configuran un Interface *VPC Endpoint* en sus VPC, lo que les permite acceder al servicio como si estuviera dentro de su propia red.

De esta forma, la comunicación es segura y privada, sin necesidad de exponer APIs públicas en Internet.


## Comparación con otras soluciones
*AWS PrivateLink* no es la única forma de conectar recursos en AWS. Sin embargo, es la mejor opción cuando necesitas garantizar:
- **Privacidad total** del tráfico.
- **Comunicación directa** entre VPC o servicios en AWS sin depender de Internet.
- Mayor control sobre quién puede acceder a un servicio ofrecido por la empresa.

AWS PrivateLink es una herramienta fundamental para cualquier administrador de sistemas que trabaje con redes en la nube, especialmente cuando se trata de garantizar la seguridad y la privacidad de los datos.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
