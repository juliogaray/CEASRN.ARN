![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Conceptos básicos 

(v20241024)

## 1. Infraestructura general de AWS

### Regiones *(regions)*

El servicio de AWS a nivel mundial se divide en **regiones**. Una región se define como un área geográfica independiente que contiene subdivisiones denominadas **zonas de disponibilidad** *(Availability Zones, AZ en inglés).* De media, cada región está compuesta por tres zonas de disponibilidad. Las regiones ofrecen diversos servicios de AWS, como computación, almacenamiento, bases de datos, redes y otros servicios.

El objetivo es permitir a los usuarios implementar recursos dentro del área de cobertura deseada o en ubicaciones geográficas específicas, garantizando:
- Un acceso rápido con una latencia mínima;
- (IMPORTANTE) el cumplimiento de las regulaciones de residencia de datos;
- Redundancia y recuperación ante desastres (por la existencia de múltiples regiones).

![Mapa de regiones de AWS en 2020](https://maturitymodel.security.aws.dev/en/Regions.png)


A marzo de 2024, había 33 regiones de AWS lanzadas en todo el mundo. La elección de la región correcta depende de factores como la latencia, la residencia legal de datos y los requisitos normativos.

Las regiones están conectadas entre sí a través de redes troncales de alto ancho de banda para la transferencia de datos entre ellas.

### Zonas de disponibilidad *(Availability Zones, AZ)*

Cada región geográfica se subdivide en varias zonas de disponibilidad (AZ), que son conjuntos de centros de datos aislados dentro de la misma región. Estas AZ están separadas estratégicamente entre sí para garantizar que cualquier fallo o interrupción en una zona no afecte las operaciones de otras zonas.

![Mapa de regiones de AWS en 2020](https://www.researchgate.net/profile/Ivan_Beres/publication/360541079/figure/fig1/AS:1154856056291328@1652350548135/AWS-Regions-and-Availability-Zones-Scott-nd.ppm)

&nbsp; 

&nbsp; 

&nbsp; 

## 2. Virtual Private Cloud ![Icono de VPC](https://juliogaray.github.io/CEASRN.ARN/docs/imgVPC.svg) 

Una VPC es una sección reservada y **aislada lógicamente** de la nube de AWS, donde se pueden lanzar recursos de AWS en una red virtual definida por nosotros.
- Nos permite **controlar nuestros recursos de red virtual**, como:
    - Selección del intervalo de direcciones IP;
    - Creación de subredes;
    - Configuración de tablas de encaminamiento y puertas de enlace de red;
- Nos permite **personalizar la configuración de red** (dentro de la VPC);
- Nos permite utilizar **varias capas de seguridad**.

Es importante tener claro que una VPC...

1. Se encuentra aislada de forma lógica de otras VPC;
2. Está **dedicada en exclusiva** a una cuenta de AWS;
3. **Pertenece a una única región** de AWS, pero **puede abarcar varias zonas de disponibilidad**.

&nbsp; 

&nbsp; 

&nbsp; 

## 3.  Subred

Todos sabemos lo que es una subred (si no, estamos en el curso equivocado).

Dentro de AWS, una subred...

1. Está aislada de las demás subredes dentro de la misma VPC (y, por supuesto, de cualquier otra VPC);
2. Comprende un intervalo de direcciones IP de una VPC;
3. Pertenece a **una única zona de disponibilidad**;
4. Se puede clasificar como **pública** (con acceso directo a Internet) o **privada** (sin dicho acceso).

&nbsp; 

&nbsp; 

&nbsp; 

## 4. Direccionamiento IP

Cuando creamos una VPC, le asignamos un bloque IPv4 de CIDR (un rango de direcciones IPv4 privadas). Algunos factores que tenemos que tener en cuenta son:
- **No se puede cambiar el rango de direcciones** después de crear la VPC.
- El tamaño máximo de bloque de CIDR IPv4 es /16 (65 536 direcciones).
- El tamaño mínimo de bloque de CIDR IPv4 es /28 (16 direcciones).
- También se admite IPv6 (con un límite de tamaño de bloque diferente).
- Aunque sea una obviedad, los bloques de CIDR de las subredes dentro de una VPC **no pueden superponerse**.
- Una subred puede ocupar todo el rango de direcciones IP de una VPC. Pero también puede ocupar sólo una parte, haciendo *subnetting.*

### 4.1 Direcciones reservadas

Dentro de toda subred creada en una VPC hay siempre **5 direcciones reservadas**:

1. La primera direcciónm que es la dirección de red;
2. La segunda dirección, para encaminamiento y comunicación interna (puerta de enlace);
3. La tercera dirección, para el servicio DNS;
4. La cuarta dirección, para uso futuro;
5. La última dirección del rango de la subred, como es lógico, es la dirección de difusión *(broadcast).*

### 4.2 Direcciones IP públicas

#### Direcciones IPv4 públicas dinámicas
Toda máquina virtual creada en una subred dada obtiene automáticamente (por un mecanismo asimilable a DHCP) una dirección IP privada de su rango.

Si la subred es **pública**, se le puede asignar una dirección IPv4 pública a una máquina virtual a través de la configuración de asignación automática de direcciones IP públicas en el nivel de subred.

#### Direcciones IP *elásticas*

Una dirección IP ***elástica*** es una dirección PÚBLICA FIJA que se asocia a una cuenta AWS.
- Se puede asignar y reasignar en cualquier momento.
- Tiene un coste adicional.
- Se puede asociar a un interfaz de red en lugar de a una máquina virtual. De ese modo, si una máquina accesible al público falla, podemos mover todo su interfaz de red virtual (ver a continuación) a otra igual (una copia en *stand-by,* por ejemplo) y hacer que todo funcione de nuevo muy rápidamente.

### 4.3 Interfaces de red elásticos

Un interfaz de red elástico es un **interfaz de red virtual** que se puede:
    - Asociar a una instancia;
    - Desconectar de la instancia y conectarlo a otra para redirigir el tráfico de red;
- Cuando se vuelve a asociar a una nueva instancia, **sus atributos también se asocian**;
- Cada instancia de una VPC tiene un **interfaz de red predeterminado** al que se asigna una dirección IPv4 privada del intervalo de direcciones de la VPC.
- Cuando se crea una máquina virtual, incluye un interfaz de red primario, al que se le asigna una dirección IP del rango de la subred. **Este interfaz no es elástico, y no se puede disociar de la máquina**. 
- Los interfaces elásticos son, pues, siempre interfaces secundarios. Una máquina puede tener varios, dependiendo de su configuración.

### 4.4 Tablas de encaminamiento

Las subredes que creamos en una VPC pueden disponer de sus propias tablas de encaminamiento (TE).

De forma predeterminada, una tabla siempre tiene una entrada *local* asociada al bloque CIDR de la VPC, para encaminar todo el tráfico dirigido a la VPC local. Esta ruta no se puede cambiar ni eliminar. Pero se pueden añadir todas las demás rutas que deseemos.

La VPC cuenta con una **tabla de encaminamiento maestra**, que sirve a todas las subredes que no tengan definida una TE propia.

Varias subredes pueden compartir una misma TE, pero cada subred sólo puede disponer de una única TE, lógicamente.

&nbsp; 

&nbsp; 

&nbsp; 

## 5. Gestión de redes entre VPC

En esta sección vamos a ver cómo gestionar el tráfico fuera de una VPC.

### 5.1 Puerta de enlace a Internet *(Internet Gateway)* ![Icono de Internet Gateway](https://juliogaray.github.io/CEASRN.ARN/docs/imgVpcInternetGateway.svg)

Una puerta de enlace a Internet es un componente de VPC escalable y redundante, de alta disponibilidad, que permite la comunicación entre las máquinas virtuales de una subred pública e Internet. De este modo podemos:
- Permitir la comunicación desde las máquinas virtuales de la subred pública hacia Internet;
- Permitir el acceso desde Internet a las máquinas virtuales con direcciones IP públicas, usando [NAT](https://es.wikipedia.org/wiki/Traducci%C3%B3n_de_direcciones_de_red).

Para hacer una subred pública, añadimos una *puerta de enlace a Internet* a nuestra VPC, y añadimos una entrada a la tabla de encaminamiento de la subred para dirigir el tráfico hacia Internet.

#### Puerta de enlace NAT a Internet *(Internet NAT Gateway)* ![Icono de Internet NAT Gateway](https://juliogaray.github.io/CEASRN.ARN/docs/imgVpcNatGateway.svg)

Podemos usar una *puerta de enlace NAT a Internet (NAT Gateway)* para permitir que una subred privada se conecte a Internet. La subred privada debe tener una ruta en la tabla de encaminamiento correspondiente a la *NAT Gateway* (que debe encontrarse en una subred pública). Esto permite a las máquinas virtuales de la subred privada acceder a Internet (por ejemplo, para buscar actualizaciones), pero impide iniciar tráfico en sentido inverso, desde Internet hacia la subred privada.

Una *NAT Gateway* debe estar asociada a una dirección IP elástica.

(INACABADO)

&nbsp; 

&nbsp; 

&nbsp; 

## Referencias

[Curso AWS Academy Cloud Foundations](https://aws.amazon.com/es/training/awsacademy/). AWS Academy.

A. Worx (2024). [Discovering the AWS World: Regions, Zones, and Beyond!](https://ascribeworx.hashnode.dev/aws-regions-and-zones).

