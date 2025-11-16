![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Interconexión de VPC

(v20241110)

En estos apuntes vamos a examinar las distintas posibilidades de interconexión de VPC, propias o ajenas. Disponemos para ello fundamentalmente de dos servicios con filosofías y objetivos diferentes:

- **AWS VPC Peering**

Este servicio permite establecer una conexión directa entre **dos** VPCs, permitiendo la comunicación de red privada y aislada entre sus redes. Permite a los recursos en ambas VPC comunicarse como si estuvieran en la misma red, **sin pasar por Internet**. Es una buena opción en configuraciones de red relativamente pequeñas o con pocos emparejamientos, y **cada conexión de *VPC Peering* es independiente**. Esto significa que, en una arquitectura con múltiples VPC que requieran conectarse entre sí, el número de conexiones puede aumentar rápidamente, volviéndose difícil de gestionar y escalando mal.

- **AWS Transit Gateway**

AWS Transit Gateway, en cambio, es un servicio diseñado para facilitar la **interconexión a gran escala**. Funciona como un *hub* central, permitiendo que múltiples VPC, cuentas de AWS e incluso conexiones VPN compartan una única pasarela. Con *Transit Gateway,* en lugar de configurar una conexión de emparejamiento entre cada par de VPC, todas las VPC se conectan al *hub (Transit Gateway),* simplificando la administración y el enrutamiento. Esto lo hace ideal para grandes arquitecturas de red con muchas VPCs, facilitando el enrutamiento y proporcionando mejor escalabilidad.

**¿Cuándo elegir un método o el otro?**

Dependiendo de estos factores:

- **Escalabilidad**: Transit Gateway es mejor para redes de múltiples VPC y grandes arquitecturas. VPC Peering es más adecuado para redes más simples o emparejamientos específicos.
- **Simplicidad en la gestión**: Transit Gateway reduce la complejidad al centralizar las conexiones.
- **Coste**: Transit Gateway generalmente será más costoso que VPC Peering, especialmente en arquitecturas pequeñas.

Estudiémoslas en más detalle.

## AWS VPC Peering ![Icono de VPC Peering Connection](http://julio.iespacomolla.es/CEARSN.ARN/imágenes/VPCPeeringConnection.svg)

**AWS VPC Peering** es un servicio que permite conectar dos *Virtual Private Clouds (VPC)* dentro de la infraestructura de Amazon Web Services, ya sea en la misma región o en regiones distintas. Con este servicio, es posible que dos redes VPC se comuniquen de manera privada, sin necesidad de atravesar la red pública de Internet, lo que aporta mayor seguridad y eficiencia en la transferencia de datos.

### Concepto básico y uso de VPC Peering
Cada VPC en AWS es una red aislada que permite desplegar y gestionar recursos de manera segura. El servicio **VPC Peering** se emplea para que estas redes aisladas puedan establecer una comunicación directa, creando una «conexión de emparejamiento». Esta conexión permite a los recursos en ambas VPCs interactuar como si estuvieran en la misma red local. Esto es ideal para entornos donde es necesario dividir aplicaciones en diferentes VPC por motivos de seguridad o escalabilidad, pero aún así mantener una comunicación fluida entre ellas.

### Ejemplos de aplicación
VPC Peering es ideal en escenarios como:
- **Entornos de producción y desarrollo**: donde se necesitan distintas VPC pero con acceso controlado.
- **División de recursos**: en aplicaciones de múltiples capas, donde diferentes capas (bases de datos, servicios, *frontend)* están aisladas en VPC individuales.
- **Organizaciones multicuenta**: que deben conectar redes de diferentes cuentas de AWS para permitir la colaboración y compartición de datos.

### Ventajas
- **Bajo costo**: Solo se paga por el tráfico de datos, sin costos adicionales por la conexión de emparejamiento.
- **Alta seguridad**: No utiliza Internet; todo el tráfico es privado.
- **Escalabilidad**: Facilita la comunicación entre múltiples VPC y permite gestionar varias redes de manera modular.

### Limitaciones
- **Encaminamiento**: Solo permite comunicación directa entre las VPC emparejadas; no se admite el enrutamiento en cadena, por lo que cada par de VPC requiere su propia conexión.
- **Administración de CIDR**: Los rangos de IP en las VPC no pueden superponerse, por lo que hay que gestionarlos cuidadosamente.

### Implementación básica
Para crear una conexión de VPC Peering, el propietario de una de las VPC debe iniciar la conexión y el otro debe aprobarla. Ambos necesitan configurar sus tablas de enrutamiento para que el tráfico fluya entre las dos redes. En AWS, la configuración de VPC Peering es un proceso guiado, donde se eligen las VPCs a emparejar y luego se añaden rutas en ambas tablas de enrutamiento para habilitar la comunicación.

&nbsp;

&nbsp;

## AWS Transit Gateway ![Icono de AWS Transit Gateway](http://julio.iespacomolla.es/CEARSN.ARN/imágenes/TransitGateway.svg)

Como ya hemos dicho, **AWS Transit Gateway** es un servicio de red que facilita la interconexión de múltiples redes en la nube en una arquitectura centralizada. En lugar de conectar cada par de redes manualmente, AWS Transit Gateway permite configurar un solo *hub* o pasarela que centraliza las conexiones y rutas de varias *Virtual Private Clouds* (VPC), conexiones *VPN*, y otros servicios en AWS.

### Objetivo y funcionalidad principal
El servicio AWS Transit Gateway está diseñado para arquitecturas de red a gran escala, como aquellas con múltiples VPC, redes empresariales híbridas o implementaciones con múltiples cuentas de AWS. En lugar de crear conexiones punto a punto entre cada red, Transit Gateway centraliza el tráfico de cada red a través de un único punto de acceso. Esto simplifica tanto la administración como el diseño de redes complejas, y además mejora la escalabilidad y la seguridad.

### Ventajas clave de AWS Transit Gateway
1. **Centralización y escalabilidad**: AWS Transit Gateway actúa como un *hub* central, permitiendo a las redes conectarse en una arquitectura de estrella, lo cual reduce la complejidad del enrutamiento y permite que las redes escalen a medida que se añaden nuevas VPC o conexiones VPN.
2. **Administración simplificada**: la arquitectura de tipo [*hub-and-spoke*](https://es.wikipedia.org/wiki/Paradigma_de_distribuci%C3%B3n_Spoke-hub) reduce la cantidad de conexiones directas necesarias y, por lo tanto, el tiempo de gestión. Las políticas de encaminamiento y los accesos se pueden gestionar desde un único punto.
3. **Mejora de la seguridad y rendimiento**: al eliminar la necesidad de encaminar el tráfico a través de Internet y centralizarlo en una única conexión, Transit Gateway mejora tanto la seguridad como el rendimiento de la red.

### Casos de uso
- **Conectar múltiples VPC**: Para grandes entornos con muchas VPC o incluso múltiples cuentas de AWS, Transit Gateway permite gestionar el tráfico entre ellas de manera centralizada.
- **Redes híbridas**: Permite conectar VPC con redes [*on-premises*](https://blog.softexpert.com/es/on-premise-vs-cloud/) a través de VPN o *Direct Connect,* proporcionando una solución robusta para redes híbridas.
- **Estructuras de múltiples cuentas**: Las organizaciones que utilizan múltiples cuentas de AWS pueden centralizar sus redes mediante una sola instancia de Transit Gateway, facilitando la colaboración y gestión de redes.

AWS Transit Gateway ofrece un diseño de red flexible y altamente escalable que es ideal para entornos complejos y distribuidos, siendo una excelente herramienta para técnicos que necesitan administrar redes avanzadas y seguras en AWS.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
