![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Servicios de resolución de nombres. DNS

(v20250409)

## Servicios de nombres

### Introducción
La forma de identificar un computador dentro de una red es mediante una dirección IP. Si pensamos en redes de cientos de computadores, el hecho de recordar la dirección de cada uno de los equipos se hace para una persona una tarea casi imposible. Por eso con la implementación de la red ARPANET donde se comenzó con un listado de direcciones IP donde se almacenaban las direcciones de los pocos equipos que estaban conectados a esta red.
Cuando la cantidad de máquinas que estaban conectadas a ARPANET creció, se decidió crear un archivo que contuviera una traducción de direcciones IP a nombres que las personas pudieran recordar. A medida que la cantidad de equipos que se conectaban a esta red aumentaba, se hizo necesario implementar un servicio distribuido que se encargará de obtener las direcciones IP cuando recibía un nombre identificador de un equipo.
Un servicio de resolución de nombres proporciona independencia del nombre de dominio respecto a la dirección IP. Así un dominio puede cambiar de dirección IP de manera transparente para los usuarios del dominio. Incluso es usual que un dominio se identifique con más de una dirección IP como medida de redundancia contra la caída del sistema o como balanceo de cargas.

### DNS
El servicio de resolución de nombres fundamental en el funcionamiento de Internet es DNS (Domain Name Service), que hoy en día es un servicio crítico para el funcionamiento de la mayor parte de los computadores que trabajan dentro de una red.
En 1986 Paul Mockapetris creó el protocolo DNS, con el cual se crean direcciones de dominio (también llamadas URL o URI), que son cadenas de texto del estilo www.debian.org, a las cuales se les asocia una dirección IP, así los equipos de una red además de identificarse por un número, también se identificarán por un nombre, y el protocolo DNS será el encargado de pasar de uno a otro automáticamente, según se necesite.
Lo habitual será usar el protocolo DNS para pasar de un nombre a un número, porque las personas recordarán los nombres de los equipos y no sus números, pero en determinadas circunstancias, también se necesitará lo contrario, es decir, pasar de un número a un nombre, y el DNS (DNS inverso) también se encargará de esto.
El DNS se utiliza para diferentes propósitos. Los más comunes son: Resolución de nombres: Dado el nombre completo de un host (por ejemplo biblioteca.iespacomolla.es), obtener su dirección IP (en este caso, 89.29.212.141).
Resolución inversa de direcciones: Es el mecanismo inverso al anterior. Consiste en, dada una dirección IP, obtener el nombre asociado a esta.
Resolución de servidores de correo: Dado un nombre de dominio (por ejemplo gmail.com) obtener el servidor a través del cual debe realizarse el envío del correo electrónico (en este caso, gmail-smtp-in.l.google.com).
Por tratarse de un sistema muy flexible, es utilizado también para muchas otras funciones, como la obtención de claves públicas de cifrado asimétrico y la validación de envío de e-mails (a través de mecanismos como SPF de protección de falsificación de correo).

### WINS (sólo como curiosidad)

WINS resolvía nombres NetBIOS a direcciones IP en redes Windows, útil en los tiempos de Windows 95/98, NT y hasta principios de los 2000, cuando aún era común el uso de NetBIOS para compartir recursos en redes locales.

- Hoy en día no se recomienda para nuevas implementaciones.

- Puede seguir en uso en redes antiguas donde aún existen dispositivos o aplicaciones que dependen de NetBIOS.

- Ha sido reemplazado por DNS para la resolución de nombres y por otras tecnologías más modernas (como Active Directory con DNS integrado).

- En redes pequeñas las alternativas más conocidas son [mDNS](https://en.wikipedia.org/wiki/Multicast_DNS) y [LLMNR](https://en.wikipedia.org/wiki/Link-Local_Multicast_Name_Resolution) (aunque este último también está en proceso de desaparición).

---

## Aspectos técnicos del DNS

### Estructura de árbol en DNS

En un sistema de nombres jerárquico, cada nodo del árbol se identifica mediante un texto (el nombre de dominio) que no puede repetirse en el mismo nivel, pero sí en otros lugares del árbol del espacio de nombres. Lo mismo ocurre con los archivos: no pueden existir dos archivos con el mismo nombre en el mismo directorio, pero sí en ubicaciones diferentes.

Un dominio está formado por el propio nodo y el resto del árbol que depende de este nodo. Piensen en el ejemplo de un directorio: si se quiere copiar un directorio, se entiende que está formado por el mismo directorio y todos los subdirectorios que contiene.

Llamamos **espacio de nombres** al conjunto de todos los dominios que forman el árbol de nombres DNS. Continuando con la analogía con el sistema de archivos, diríamos que el espacio de nombres es equivalente a todo el sistema de archivos y directorios.

El sistema de nombres de dominio de Internet DNS está formado por los siguientes elementos:

- **Espacio de nombres**: el conjunto de todos los nombres de dominio de Internet (todo el árbol).
- **Dominio**: texto identificativo de un dominio (un nodo y todos sus descendientes).
- **FQDN**: nombre de dominio absoluto comenzando por el nodo y terminando en la raíz.
- **Dominio absoluto**: es un sinónimo de FQDN. Es la ruta completa que va del nodo a la raíz. Los dominios absolutos acaban en punto.
- **Dominio relativo**: nombre de dominio sin calificar (no acaba en punto). Se entiende que un nombre de dominio es relativo al dominio al que pertenece.
- **Dominio raíz**: dominio del cual derivan todos los demás. Se indica con un punto o con la cadena vacía.

La estructura de árbol (jerárquica) del espacio de nombres proporciona un mecanismo de identificación único de un dominio. No puede existir ningún dominio que tenga exactamente el mismo nombre absoluto o FQDN (Fully Qualified Domain Name o nombre de dominio completo). Los dominios se leen desde el nodo hasta la raíz. Así, un dominio que corresponda al departamento de informática del instituto dentro del dominio `.es` se identifica, por ejemplo, como `info.iespacomolla.es.`. Si nos fijamos en el dominio anterior, veremos que acaba en punto: es una manera de indicar el dominio raíz. El dominio raíz se define como un dominio sin etiqueta o, mejor dicho, con la cadena vacía como etiqueta. Esto provoca que los dominios que se indican de manera absoluta acaben con el carácter punto.

Un dominio absoluto o FQDN es el que incluye todos los nodos desde el dominio hasta la raíz (incluida en forma de punto final). Un dominio relativo no incluye la raíz y puede ser relativo al dominio actual. Por ejemplo, dentro del dominio del instituto, el dominio cpd (centro de procesamiento de datos) es un nombre relativo que hace referencia al nombre absoluto `cpd.iespacomolla.es.`.

---

### Dominios raíz

Un ***root name server**,* es decir, un **servidor raíz de nombres de dominio** (también conocido como *DNS root server* o *root server* por su nomenclatura en inglés) es un servidor que ejerce una función esencial en la traducción de los nombres de dominio en direcciones IP: este responde a las solicitudes de los clientes *(**requests**)* en la zona raíz del sistema de nombres de dominio (la zona raíz señala el nivel más alto en el espacio de nombres del DNS). En este sentido, este tipo de servidores raíz de nombres de dominio no se encargan por sí mismos de la resolución de nombres de dominio, sino que proporcionan información a los clientes sobre los servidores DNS de los cuales pueden recibir datos sobre la dirección IP solicitada.

Esto tiene lugar por medio del llamado archivo de la zona raíz, que es un componente esencial de todo *DNS root server* y cuya capacidad es de unos escasos 2 MB. A pesar de ello, este contiene todos los nombres y direcciones IP de cualquier servidor de nombres de dominio de todos los dominios de nivel superior (**TLD** o ***Top Level Domain*** por su nomenclatura en inglés). A estos datos les corresponde una función muy importante: un *root server* recurre a ellos al nombrar al cliente de aquel servidor de nombres de dominio que cuenta con la información necesaria sobre su solicitud.

No obstante, incluso en el caso de que solo transmitan solicitudes, los servidores raíz de nombres de dominio son indispensables para la resolución de los nombres, y es que sin ellos el sistema de nombres de dominio (**DNS**) no funcionaría en su forma actual. Un **root server** opera en la raíz (**root**) del **Domain Name System** y se convierte así, en cierta manera, en el punto de intermediación más importante de las direcciones de Internet.

Los trece principales servidores DNS son los siguientes:

- **a.root-servers.net**: Tiene la dirección IPv4 **198.41.0.4** y IPv6 **2001:503:ba3e::2:30**. Está gestionado por **VeriSign, Inc.**
- **b.root-servers.net**: Tiene la dirección IPv4 **199.9.14.201** y IPv6 **2001:500:200::b**. Está gestionado por **University of Southern California (ISI)**.
- **c.root-servers.net**: Tiene la dirección IPv4 **192.33.4.12** y IPv6 **2001:500:2::c**. Está gestionado por **Cogent Communications**.
- **d.root-servers.net**: Tiene la dirección IPv4 **199.7.91.13** y IPv6 **2001:500:2d::d**. Está gestionado por **University of Maryland**.
- **e.root-servers.net**: Tiene la dirección IPv4 **192.203.230.10** y IPv6 **2001:500:a8::e**. Está gestionado por **NASA (Ames Research Center)**.
- **f.root-servers.net**: Tiene la dirección IPv4 **192.5.5.241** y IPv6 **2001:500:2f::f**. Está gestionado por **Internet Systems Consortium, Inc.**
- **g.root-servers.net**: Tiene la dirección IPv4 **192.112.36.4** y IPv6 **2001:500:12::d0d**. Está gestionado por **US Department of Defense (NIC)**.
- **h.root-servers.net**: Tiene la dirección IPv4 **198.97.190.53** y IPv6 **2001:500:1::53**. Está gestionado por **US Army (Research Lab)**.
- **i.root-servers.net**: Tiene la dirección IPv4 **192.36.148.17** y IPv6 **2001:7fe::53**. Está gestionado por **Netnod**.
- **j.root-servers.net**: Tiene la dirección IPv4 **192.58.128.30** y IPv6 **2001:503:c27::2:30**. Está gestionado por **VeriSign, Inc.**
- **k.root-servers.net**: Tiene la dirección IPv4 **193.0.14.129** y IPv6 **2001:7fd::1**. Está gestionado por **RIPE NCC**.
- **l.root-servers.net**: Tiene la dirección IPv4 **199.7.83.42** y IPv6 **2001:500:9f::42**. Está gestionado por **ICANN**.
- **m.root-servers.net**: Tiene la dirección IPv4 **202.12.27.33** y IPv6 **2001:dc3::35**. Está gestionado por **WIDE Project**.

Cada servidor raíz DNS tiene una copia idéntica del archivo de la zona raíz, que se actualiza regularmente para que se muestren los mismos datos.

---

#### Dominios de nivel superior TLD

El nodo raíz está dividido en un conjunto de subdominios llamados **TLD** *(**Top Level Domains*** o dominios de nivel superior). Estos dominios eran, inicialmente, **com**, **edu**, **gov**, **mil**, **org**, **net** e **int**. Posteriormente se añadieron otros como **name**, **biz**, **info**, **pro**, **aero**, **coop** y **museum**. El objetivo era organizar los dominios por funcionalidad, poniendo las empresas en los **.com**, las organizaciones en los **.org**, etc.

Hay tres tipos de **TLD**:

1. **gTLD** – **Generic Top-Level Domains** (Dominios de más alto nivel genéricos)
2. **sTLD** – **Sponsored Top-Level Domains** (Dominios de más alto nivel patrocinados)
3. **ccTLD** – **Country Code Top-Level Domains** (Dominios de más alto nivel por países)

Los responsables de la administración de estos dominios son la **ICANN** (**Internet Corporation for Assigned Names and Numbers**), que es una organización sin fines lucrativos que es responsable de administrar los **TLD** a través de su división **IANA** (**Internet Assigned Numbers Authority**).

Adicionalmente, la **ICANN/IANA** delega la responsabilidad de algunos de sus **TLD** en otras organizaciones. Se puede comprobar la información de estos dominios en el siguiente sitio web: [Información de dominios de la IANA](https://www.iana.org/domains/root/db).

##### **Generic Top-Level Domains (gTLD)**

La categoría **gTLD** contiene todos los **TLD** más conocidos:

- **.com**
- **.org**
- **.net**

Más allá de estos nombres tan conocidos, se pueden encontrar otras opciones relativamente populares como:

- **.xyz**
- **.biz**
- **.info**

Alrededor del 2011, la **ICANN** abrió la puerta al hecho de que las compañías y organizaciones pudieran tener sus propios **gTLD**, lo que amplió ampliamente la lista de **gTLD** y explica la razón por la cual ahora tenemos **gTLD** como:

- **.abarth**
- **.aws**
- **.oracle**
- **.azure**

##### **sTLD – Sponsored Top-Level Domains**

El grupo de los **sTLD** contiene **TLD** que son patrocinados por una entidad específica, como empresas, el gobierno (de EE.UU., se entiende), u otros.

Algunos ejemplos son:

- **.gov** – Para uso del gobierno de los Estados Unidos.
- **.edu** – Para instituciones de enseñanza superior acreditadas por el Departamento de Educación de los Estados Unidos.
- **.mil** – Para el uso del ejército de los Estados Unidos.
- **.museum** – Reservado para museos.
- **.jobs** – Reservado para los administradores de recursos humanos y administradores de la Sociedad de Recursos Humanos.
- **.post** – Patrocinado por la Unión Universal de Correos.
- **.travel** – Reservado para agencias de viajes y negocios similares.

##### **ccTLD – Country Code Top-Level Domains**

Los **ccTLD** son dominios de alto nivel que representan países específicos. Aquí aparece una lista de ejemplos comunes:

- **.us** – Estados Unidos
- **.uk** – Reino Unido
- **.eu** – Unión Europea
- **.de** – Alemania
- **.fr** – Francia
- **.cn** – China
- **.es** – España
- **.ru** – Rusia (y, curiosamente, también [**.su**](https://es.wikipedia.org/wiki/.su))
- **.ca** – Canadá
- **.nl** – Países Bajos
- **.in** – India
- **.ch** – Suiza
- **.jp** – Japón


### Dominios de nivel inferior

Se denominan **dominios de nivel inferior** a los identificadores de las zonas que conforman un identificador completo, es decir, las ramas que forman el camino que identifica completamente una dirección DNS. Al registrar un dominio, se utilizan agencias registradoras que tienen la autoridad. Por ejemplo, en España, el *ccTLD* **.es** fue concedido por la ICANN en 1988 y gestionado en los primeros años por RedIRIS. Desde el año 2000, la gestión está a cargo de la entidad pública empresarial **Red.es**, adscrita al Ministerio de Industria, Turismo y Comercio a través de la Secretaría de Estado de Telecomunicaciones y para la Sociedad de la Información.

Los **dominios de nivel inferior** serán identificadores separados por un punto que se insertan a la izquierda de los dominios de nivel superior y que conforman la ruta completa. Así, por ejemplo, si hemos contratado el dominio **iespacomolla.es**, será la organización encargada de la gestión del servidor de nombres la que permitirá, en la configuración, crear subdominios como **informatica.iespacomolla.es**. Estos subdominios o dominios de nivel inferior pueden encadenarse añadiendo a la izquierda los identificadores de los subdominios.

### FQDN

La estructura de árbol (jerárquica) del espacio de nombres proporciona un mecanismo de identificación único para un dominio. No puede existir ningún dominio que tenga exactamente el mismo nombre absoluto o **FQDN** (**Fully Qualified Domain Name** o nombre de dominio completo). Los dominios se leen desde el nodo hasta la raíz. Así, un dominio que corresponda al departamento de informática del instituto dentro del dominio `.es` se identifica, por ejemplo, como `informatica.iespacomolla.es.`. Si nos fijamos en el dominio anterior, veremos que acaba en punto: es una manera de indicar el dominio raíz. El dominio raíz se define como un dominio sin etiqueta o, mejor dicho, con la cadena vacía como etiqueta. Esto provoca que los dominios que se indican de manera absoluta acaben con el carácter punto.

Un dominio absoluto o **FQDN** es aquel que incluye todos los nodos desde el dominio hasta la raíz (incluida en forma de punto final). Un dominio relativo no incluye la raíz y puede ser relativo al dominio actual. Por ejemplo, dentro del dominio del instituto, el dominio `informatica` (del departamento de informática) es un nombre relativo que hace referencia al nombre absoluto `informatica.iespacomolla.es`.

La longitud máxima permitida para un **FQDN** (**RFC 1035**, **RFC 1123**, **RFC 2181**) es de 255 caracteres, con una restricción adicional de 63 bytes por etiqueta dentro de un nombre de dominio. Las etiquetas **FQDN** están restringidas a un conjunto de caracteres limitado: letras **a-z**, dígitos **0-9** y el carácter guion (`-`), y no distinguen mayúsculas de minúsculas. 

#### Punycode

**Punycode** es un sistema de codificación utilizado en **DNS** para representar nombres de dominio que contienen caracteres fuera del conjunto ASCII básico (letras A-Z, dígitos 0-9 y guiones). Esto permite que los **nombres de dominio internacionalizados** (IDN, *Internationalized Domain Names*) como `español.com` o `müller.de` puedan ser usados en Internet. Entró en vigor en 2003, con los RFC 3490 y 3492.

En España, Red.es comenzó en 2007 a permitir el registro de dominios con caracteres particulares de los idiomas nacionales, como la `ñ`, `ç` o vocales acentuadas (`á`, `é`, `í`, `ó`, `ú` y `ü`), usando esta técnica.

##### ¿Cómo funciona *punycode?*

1. El dominio Unicode se convierte en **Punycode** sólo en las partes que contienen caracteres no ASCII.
2. La parte codificada se antepone con el prefijo especial `xn--`, que **identifica que se trata de un nombre Punycode**.
3. Esta conversión permite que los resolutores DNS, que sólo entienden ASCII, puedan seguir funcionando correctamente.

###### Ejemplos

| Dominio Unicode        | Dominio Punycode                  |
|------------------------|-----------------------------------|
| `müller.de`            | `xn--mller-kva.de`                |
| `español.com`          | `xn--espaol-zwa.com`              |
| `例子.测试`             | `xn--fsq.xn--0zwm56d`              |

Solo se codifican las etiquetas (las partes entre puntos) que contienen caracteres no ASCII. Así, `www.例子.测试` se convierte en `www.xn--fsq.xn--0zwm56d`.

Punycode usa una técnica de codificación llamada [**Bootstring**](https://es.wikipedia.org/wiki/Punycode#Codificaci%C3%B3n_de_inserci%C3%B3n_de_caracteres_distintos_al_ASCII_y_c%C3%B3digos_num%C3%A9ricos), que:
- Preserva los caracteres ASCII normales tal cual.
- Codifica los caracteres Unicode no ASCII al final de la cadena.
- Utiliza un separador `-` para separar la parte normal de la parte codificada.

Por ejemplo, en `xn--mller-kva`:
- `mller` son los caracteres ASCII (eliminando la `ü`, que se codificará después del guion).
- `kva` representa la `ü` codificada como parte del sufijo Bootstring.



---

### Zonas DNS

Una **zona** es la parte del espacio de nombres de dominio gestionada por uno o más servidores DNS. Los servidores que gestionan la zona tienen información completa sobre ella y se dice que tienen autoridad sobre ella. Podríamos pensar que un servidor DNS gestiona un dominio y que una zona es lo mismo que un dominio, pero esto no es necesariamente así. Un dominio se divide en subdominios para facilitar su administración. Cada parte gestionada por uno o más servidores DNS es una zona. El dominio es el árbol del espacio de nombres (él y sus descendientes), y la zona es la parte del árbol gestionada por un servidor de nombres de dominio concreto.

En general, podemos decir que una zona contiene la información completa de los equipos que forman el dominio correspondiente a la zona y de los equipos de los subdominios que no se han delegado. Esta información se almacena en la base de datos de la zona.

Delegar la administración de un subdominio no es más que transferir la autoridad sobre ese subdominio a otra entidad (a otros servidores DNS). Esta entidad es la responsable de la administración de la zona delegada. Tiene toda la autoridad para hacer y deshacer a su criterio. La zona padre pierde el control administrativo de la zona delegada y simplemente apunta a los servidores de nombres de la zona delegada para obtener información cuando la requiera.

El estándar que define el DNS establece que hay que configurar dos o más servidores autorizados para cada zona, llamados **servidor primario** y **servidor secundario**. El motivo es proporcionar un mecanismo de redundancia, robustez, rendimiento y copia de seguridad. Si el servidor de nombres falla y es único, posiblemente la red caerá, será inoperativa.

Los servidores primario (o maestro) y secundario/s (o esclavo/s) son autoridad. Solo el primario tiene los archivos de zona con los datos in situ. Los servidores secundarios obtienen una copia de los datos mediante transferencia.

### Registros de recursos (RR o *resource records)*

Un **Resource Record (RR)**, o registro de recursos, es una estructura de datos fundamental en el sistema de nombres de dominio (DNS). Los registros de recursos contienen información específica sobre un dominio o una zona DNS, y son utilizados para resolver nombres de dominio en direcciones IP u otros tipos de datos útiles. Cada RR está formado por varios campos que describen su propósito y contenido.

#### Estructura de un RR
Un RR típico tiene los siguientes campos:

1. **Nombre (Name):**  
   Es el nombre del dominio al que se aplica el registro. Por ejemplo, `www.ejemplo.com`.

2. **Tipo (Type):**  
   Define el tipo de información que contiene el registro. Algunos tipos comunes incluyen:
   - **A:** Asocia un nombre de dominio con una dirección IPv4.
   - **AAAA:** Asocia un nombre de dominio con una dirección IPv6.
   - **CNAME:** Define un alias para otro nombre de dominio.
   - **MX:** Especifica servidores de correo para el dominio.
   - **NS:** Indica los servidores de nombres autorizados para la zona.
   - **TXT:** Permite almacenar texto arbitrario, a menudo usado para verificar la propiedad del dominio o configurar políticas de seguridad como SPF o DKIM.
   - **SOA:** Contiene información administrativa sobre la zona, como el servidor primario y parámetros de tiempo.

3. **Clase (Class):**  
   Casi siempre será `IN` (Internet). Existen otras clases, pero han quedado obsoletas o son muy poco usadas.

4. **TTL (Time to Live):**  
   Especifica cuánto tiempo (en segundos) los servidores DNS deben cachear el registro antes de consultarlo nuevamente. Un TTL bajo permite actualizaciones rápidas, mientras que uno alto reduce la carga en los servidores DNS.

5. **Datos (Data):**  
   Contiene la información específica del registro según su tipo. Por ejemplo, para un registro A, este campo contendrá la dirección IPv4 correspondiente.

#### Ejemplo de un Resource Record
Supongamos que queremos definir un registro A para el dominio `www.ejemplo.com`. El RR podría tener este aspecto:

```
www.ejemplo.com.    3600    IN    A    192.168.1.1
```

- **Nombre:** `www.ejemplo.com.` (el dominio).
- **TTL:** `3600` segundos (1 hora).
- **Clase:** `IN` (Internet).
- **Tipo:** `A` (registro de dirección IPv4).
- **Datos:** `192.168.1.1` (la dirección IP asociada).

---

### Función de los RR en DNS
Los RR son esenciales para el funcionamiento del DNS porque permiten traducir nombres humanamente legibles (como `www.ejemplo.com`) en direcciones IP u otros datos necesarios para la comunicación en redes. Además, los RR también se utilizan para delegar zonas DNS, configurar servidores de correo, implementar políticas de seguridad y más.

En resumen, un **Resource Record** es una unidad básica de información en el DNS que define cómo se debe interpretar o manejar un nombre de dominio específico.


## El protocolo DNS

El protocolo **DNS *(Domain Name System)*** tiene varias funciones; la más importante es la de traducir nombres comprensibles para las personas en números, que es lo que son las direcciones IP con las que se identifican los ordenadores dentro de las redes. Para llevar a cabo todas sus funciones usa por defecto el **puerto 53** a nivel de servidor, aunque algún software DNS (por ejemplo BIND) permiten cambiarlo.

Para mejorar su rendimiento, puesto que es un protocolo muy usado, las consultas utilizan como protocolo de transporte **UDP**, y además se ha limitado a 512 bytes el tamaño de los datagramas UDP.

El protocolo TCP puede ser negociado entre los extremos para consultas concretas, pero debido a la sobrecarga que aporta la confiabilidad de ese protocolo, el uso de TCP como transporte se queda normalmente como una capacidad teórica, y prácticamente nunca se usa. A pesar de que el protocolo IPv6 utiliza direcciones más largas (128 bits en comparación con 32 bits) y de la incorporación del DNS seguro (DNSSEC), que han hecho que las transacciones DNS utilicen ahora más bytes, se han creado técnicas para que todo pueda seguir funcionando con datagramas UDP, y así no usar TCP.

## Resolución de nombres 

### El *resolver*

Un equipo cliente que desea resolver un nombre de host tiene diferentes maneras de hacerlo. Se puede hacer **localmente mediante un archivo de hosts** (típicamente `/etc/hosts`) o de forma distribuida usando DNS. De hecho, se pueden aplicar ambos métodos conjuntamente indicando su precedencia en algún archivo de configuración del sistema (en sistemas GNU/Linux, el archivo es `/etc/nsswitch.conf`). En Windows, el archivo hosts está situado en %SYSTEMROOT%\system32\drivers\etc\hosts y el *resolver* se configurará en el centro de redes y recursos compartidos cuando se configura la interfaz de red.

El ***resolver*** es la parte cliente del sistema de nombres de dominios DNS, es decir, el servicio que efectúa las consultas DNS a los servidores. Cada *resolver* implementa sus opciones, pero algunas son suficientemente genéricas para describirlas aquí. En la mayoría de sistemas GNU/Linux, estas opciones se definen en el archivo `/etc/resolv.conf`.
Además, estos sistemas pueden almacenar las consultas recurrentes en una caché para poder responder a las consultas más utilizadas en lugar de realizar una nueva consulta a los servidores configurados.

### Proceso de resolución

El proceso de consulta a los servidores se puede resumir en los siguientes pasos:

1. Un equipo de un usuario realiza la consulta del nombre `biblioteca.iespacomolla.es` en el DNS que tiene configurado.
2. Lo normal es que el servidor DNS no tenga respuesta autoritativa, por lo que (el servidor dns) recurre a uno de los 13 servidores DNS raíz.
3. El servidor raíz le responde con una referencia al servidor o servidores del TLD de su consulta (`.es.`).
4. El equipo del usuario le envía la consulta (`biblioteca.iespacomolla.es.`) al servidor del TLD.
5. El servidor del TLD responde con una referencia al servidor del nombre de dominio de su consulta (`.iespacomolla.es.`).
6. El equipo del usuario le envía la consulta (`biblioteca.iespacomolla.es`) al servidor del dominio *.iespacomolla.es.*
7. El servidor del dominio envía al equipo del usuario una respuesta autoritativa de su consulta.

Estos pasos pueden verse en la siguiente imagen:

﻿![Ilustración del proceso de resolución DNS](https://image.slidesharecdn.com/procesoderesolucindenombresdedominiodns-130930110721-phpapp02/95/proceso-de-resolucin-de-nombres-de-dominio-dns-7-638.jpg)


En el proceso de respuesta a una consulta DNS, tienen mucha importancia las diferentes *cachés* que intervienen, porque reducen mucho el tiempo de respuesta. Las caché DNS son almacenes temporales de las últimas respuestas a consultas DNS; cuando se realiza una consulta lo primero que se hace es ver si la respuesta está en la caché y si es así, la respuesta se suministra directamente.

A las respuestas que se almacenan en las caché, se les asigna un tiempo, denominado tiempo de vida *(TTL, Time To Live);* si transcurrido ese tiempo, la respuesta no es utilizada de nuevo, ésta se elimina de la caché, liberando así espacio para almacenar otras respuestas.

El protocolo DNS define tres tipos de consultas:

- **Recursivas:** el cliente (por ejemplo, un navegador o un *resolver* DNS local) solicita a un servidor DNS que le proporcione la respuesta completa, y este servidor se encarga de realizar todas las consultas necesarias hasta obtenerla.
- **Iterativas** o no recursivas: el servidor DNS responde al cliente con la mejor información que tiene, pero no realiza consultas adicionales por su cuenta. Si no tiene la respuesta, deriva al cliente a otro servidor DNS. El cliente tiene que consultar a todos los servidores DNS por su cuenta para alcanzar el resultado final.
- **Inversas:** se utiliza para encontrar un nombre de dominio asociado a una dirección IP (lo contrario de una consulta DNS tradicional).

Por lo general las consultas son recursivas desde el punto de vista de los *resolvers*, e iterativas entre servidores (como hemos visto arriba, el servidor DNS realiza una consulta iterativa para obtener una dirección IP correspondiente a un FQDN).

### Búsqueda inversa

La búsqueda inversa *(reverse looking)* o *mapping* inverso consiste en obtener el nombre FQDN a partir de una IP particular. Esto es necesario para tareas de diagnóstico o, muy corriente hoy en día, para rastrear a un *hacker* o *spammer.*

﻿![Esquema de nombres de dominio, incluyendo el TLP .arpa](https://docs.db.ripe.net/assets/img/forwardreversedns.759452a2.png)

Para realizar las búsquedas inversas utilizando para ello consultas recursivas o iterativas, se crearon los dominios especiales `.IN-ADDR.ARPA` (puede escribirse en minúscula) para IPv4 e `IP6.ARPA` para IPv6. Estos dominios son una buena idea para resolver el problema utilizando la técnica ya descrita.

Puesto que el mecanismo original de DNS nos permite buscar sólo FQDN, cuando pretendemos resolver (inversamente) una dirección IP al FQDN correspondiente tenemos que transformar la IP en un nombre FQDN virtual, y para ello lo que se hace es invertir el orden de la dirección IP y construir la jerarquía utilizando el sTLD `.IN-ADDR` y el TLD `.ARPA`, creados exclusivamente para las búsquedas recursivas inversas.

El motivo de invertir la IP es que, al ser también una estructura jerárquica red-*host,* está escrita al revés que un FQDN, es decir, la parte más significativa, la red, está a la izquierda, y la parte secundaria, el *host,* está a la derecha; si le damos la vuelta, se integra en una estructura jerárquica de mayor (derecha) a menor (izquierda).
La siguiente imagen muestra la estructura jerárquica de los nombres de dominio de Internet:


Con todo esto, a partir de una IP 192.168.1.12/24 crearíamos el nombre de dominio .1.168.192.in-ADDR.ARPA y 12 sería un host dentro de ese dominio.

## Archivo de zona


Un archivo de zona es un archivo de texto con un formato estandarizado (RFC 1035) cuyo contenido está formado por los siguientes elementos:

- **Comentarios:** Comienzan con un punto y coma (;) y terminan con el final de la línea. Pueden colocarse en cualquier lugar.
- **Directivas:** Todas las directivas comienzan con el signo de dólar ($) y controlan ciertos aspectos del archivo de zona.
- **Registros de recursos (RR):** ocupan una línea cada uno, aunque se pueden definir entradas entre paréntesis, que pueden ocupar varias líneas. Sirven para definir características, propiedades y entidades del dominio.
- **Separadores de campos:** pueden ser espacios y/o tabuladores. Se pueden utilizar tantos como se desee. Su uso fundamental es el de dar al archivo de zona un aspecto claro.

### Registro de recursos

Los RR tienen la siguiente sintaxis general:

```txt
nombre-RR ttl-RR clase-RR tipo-RR datos-específicos-del-tipo-RR
```

El campo **nombre-RR** (nombre del lado izquierdo o *left-hand name)* no debe superar los 255 caracteres y puede tomar uno de los siguientes valores:

- Un nombre FQDN (acabado en punto).
- Un nombre no cualificado, y en tal caso, se aplicará la sustitución de $ORIGIN (ver más abajo).
- El carácter @, que es sustituido por el valor actual de $ORIGIN.
- En blanco (espacios y/o tabuladores), que será sustituido por el nombre-RR del RR anterior. Si fuera el primer RR del archivo de zona y por tanto no existe RR anterior, se sustituirá por $ORIGIN.

Cada fragmento de nombre-RR separado por puntos, denominado **etiqueta**, no puede superar los 63 caracteres; los caracteres que podemos usar en estas etiquetas con seguridad son los siguientes: 

- a-z y A-Z. No se diferencia entre mayúsculas y minúsculas.
- 0-9.
- El guion (-).

El estándar amplió estos rangos a cualquier carácter ASCII, pero caracteres fuera de los especificados aquí arriba pueden dar problemas entre diferentes aplicaciones DNS.

Los nombres no pueden comenzar ni acabar con un guion (`-`).

Otro límite a tener en cuenta es que los paquetes DNS que se transportan con UDP no pueden superar los 512 bytes, y este límite tiene especial importancia cuando las respuestas son de múltiples registros. Un límite práctico para cada etiqueta de nombre-RR puede estar entre 10 y 20 caracteres.

El campo **ttl-RR** es opcional y define el tiempo (segundos, minutos, horas, días o semanas) que un RR puede estar en una caché. Si no se especifica se asume el valor de la directiva $TTL. Este campo es un entero de 32 bits, por lo que el intervalo de valores va desde 0 hasta 2147483647.

El campo **clase-RR** puede ser uno de los siguientes valores: IN (Internet), CH y HS. El valor IN es el único que utilizaremos, los usos de CH y HS son muy marginales.

El campo **tipo-RR** especifica el tipo de registro de recurso (SOA, NS, MX, A, AAAA, etc.).

El campo **datos-específicos-del-tipo-RR** consiste en uno o varios parámetros que dependerán del tipo de RR en cuestión.


#### La directiva $ORIGIN

La directiva $ORIGIN (RFC 1035) define el nombre del dominio que será añadido al final de cualquier nombre que no acabe en punto (nombres relativos o no cualificados) en los RR, para así transformarlos en nombres FQDN *(fully qualified domain name).* Si un nombre acaba en punto, ya se considera un nombre FQDN y no se utiliza $ORIGIN.
el nombre-dominio se escribe en formato FQDN para evitar confusiones. Podría no acabar en punto (y funcionaría todo igual) pero siempre escribiremos el dominio acabado en punto.

```text
$ORIGIN nombre-dominio
```

#### $TTL
El concepto tiempo de vida *(TTL, Time To Live)* de los RR significa el tiempo, medido en segundos, que un RR estará en la caché de los servidores de nombres antes de borrarse. Todos los RR pueden llevar opcionalmente un TTL cuando se crean, pero en caso que no se especifique, su TTL vendrá especificado por el valor de la directiva $TTL (RFC 1035). Por tanto, la directiva $TTL especifica el tiempo de vida por defecto de los RR y es obligatoria.

```text
$TTL tiempo-en-segundos
```

#### $INCLUDE
La directiva $INCLUDE se ha añadido en el servicio DNS para que este servicio continúe con la filosofía actual de los archivos de configuración de todos los servicios (DHCP, HTTP, FTP, etc.). Permite que la configuración del archivo de zona pueda ser distribuida en varios archivos, normalmente temáticos desde algún punto de vista, y después incluirlos todos en el archivo de zona. Esta manera de trabajar es más importante cuanto mayor es el archivo de zona. Es una directiva opcional.

La sintaxis es la siguiente:

```text
$INCLUDE nombre-archivo [ nombre-dominio ]
```

Donde `nombre-archivo` puede expresarse con una ruta absoluta o relativa. En este último caso, el directorio de referencia será el directorio del archivo de zona donde se encuentra la directiva.

El argumento opcional `nombre-dominio` establece el valor con el cual inicialmente entraría la directiva $ORIGIN en el archivo que se incluye. Si no se especifica, $ORIGIN entrará con el valor que tenga la directiva en el punto de $INCLUDE. El ámbito de este valor para $ORIGIN es el archivo que se incluye, porque después de la directiva $INCLUDE se restablece el valor de $ORIGIN al que tenía previamente a $INCLUDE.

#### SOA

El RR **SOA** *(Start Of Authority,* RFC 1035, RFC 2308) describe las propiedades globales de la zona o dominio. Solo debe haber un registro SOA en un archivo de zona y debe ser el primer RR. Su sintaxis es la siguiente:

```text
nom-domini  ttl-RR  IN  SOA nombre-servidor-maestro correo-electrónico-administrador(
numero-serie
t-refresco
t-reintento
t-expiración
nx
)
```

En `nombre-dominio` suele ponerse el símbolo `@`, que representa el valor de $ORIGIN. Como es el primer RR del archivo, esta directiva suele tener el nombre del dominio que describe el archivo de zona.

El significado de los campos es el siguiente:

- **nombre-servidor-maestro**: dspecifica un servidor DNS maestro del dominio, pero este significado de maestro es solo en el contexto del DNS dinámico (DDNS), indicando cuál es el servidor que podrá ser actualizado vía transacciones DDNS. Si no se ha configurado el DDNS, y por tanto no se usa, podemos poner cualquier servidor autoritativo para el dominio, maestro o esclavo. Este servidor puede ser interno al dominio o externo, pero siempre debe llevar un RR NS. Es normal escribirlo en formato FQDN, pero esto solo es obligatorio en el caso que sea un servidor externo.

- **correo-electrónico-administrador**: se pone la dirección de correo electrónico (interna al dominio o externa) de la persona responsable de la zona. Se aconseja (RFC 2412) que sea una cuenta de correo exclusiva para este menester. En el ejemplo, el e-mail especificado es hostmaster@iespacomolla.es, pero como el símbolo @ tiene un significado especial, se sustituye por un punto: hostmaster.iespacomolla.es. Es habitual escribirlo en formato FQDN, pero solo es obligatorio para direcciones de correo externas al dominio.

- **numero-serie**: valor entero de 32 bits sin signo que va desde 1 hasta 4294967295. Este número lo utilizan los servidores DNS esclavos para ver si se han producido cambios en el archivo de zona y así actualizarse. Debemos incrementar este número cada vez que se hagan modificaciones sobre el archivo de zona. Se aconseja que se utilice un número con el siguiente formato basado en la fecha de la modificación: yyyyymmddnn, donde nn es un número que comienza por 00 y es la parte que se incrementará cuando se hagan varias modificaciones en el mismo día, porque el resto del número no cambia.

- **t-refresco**: indica el intervalo de tiempo después del cual un servidor esclavo intentará actualizarse con la zona del servidor maestro. El RFC 1912 recomienda utilizar valores comprendidos entre 1200 segundos (20 minutos) y 43200 segundos (12 horas) según lo volátiles que sean los RR. Si se usan los mensajes NOTIFY para la actualización, se pueden poner valores más elevados. Valor entero de 32 bits con signo.

- **t-reintento**: indica el intervalo de tiempo entre reintentos del servidor esclavo, para cuando la conexión con el servidor maestro después del tiempo de refresco ha fallado. El valor dependerá del conocimiento que tengamos del estado de la red, pero los valores van de 180 segundos (2 minutos) a 900 segundos (15 minutos). Valor entero de 32 bits con signo.

- **t-expiración**: indica el intervalo de tiempo que el servidor esclavo estará haciendo reintentos de conexión con el servidor maestro si este no contesta. Hasta que no pase este tiempo, aunque el servidor esclavo no se pueda actualizar, responde a las consultas de manera autoritaria; llegado el momento de expiración, deja de contestar a las consultas de la zona que no ha podido actualizarse (por considerar que la información que contiene no está suficientemente actualizada). El RFC 1912 recomienda utilizar valores comprendidos entre 1209600 y 2419200 segundos, de 2 a 4 semanas. Valor entero de 32 bits con signo.

- **nx**: indica el intervalo de tiempo que se mantendrá una consulta no resuelta en la caché de los servidores de nombres; transcurrido este tiempo, se volverá a intentar resolver la consulta (RFC 2308). Por ejemplo, si se hace una consulta del FQDN `cpd.iespacomolla.es`, y no es posible resolverla, el servidor retornará un error de nombre *(Name Error* o *NXDOMAIN).* Durante los siguientes `nx` segundos, si se vuelve a hacer la misma consulta, se responderá de la misma forma; pero pasadas las dos horas, se intentaría de nuevo resolver la consulta fallida. Los valores posibles van de 0 hasta 10800 (3 horas). Anteriormente, este campo era el TTL por defecto de los RR, pero después se introdujo la directiva $TTL como obligatoria y se le dio al campo el actual significado. Valor entero de 32 bits con signo.

#### NS

Los RR **NS** *(Name Server,* RFC 1035) se usan para listar todos los servidores de nombres que responden de manera autoritaria sobre un dominio. Su sintaxis es la siguiente:

```text
nombre-dominio ttl-RR IN NS nombre-servidor-dns
```

No es obligatorio, pero se aconseja disponer de al menos dos servidores DNS autorizados por cada zona para que el servicio no se detenga en el caso que el servidor maestro deje de funcionar. Esto implica que habrá al menos dos RR NS en un archivo de zona.

Lo habitual es definir los registros NS a continuación del registro SOA (aunque pueden ponerse en cualquier lugar del archivo de zona).

Los servidores de los registros NS de un archivo de zona son públicos y por tanto visibles para cualquier usuario.

Los servidores de nombres especificados en el campo nombre-servidor-dns pueden ser internos al dominio o externos. Todos los internos deben tener su correspondiente registro A en el archivo de zona.

#### MX

Los RR **MX** *(Mail Exchange,* RFC 1035) definen los servidores de correo del dominio y la prioridad entre ellos. Su sintaxis es la siguiente:

```text
nombre-dominio ttl-RR IN MX prioridad-RR nombre-servidor-correo
```

El campo `prioridad-RR` puede tomar un valor entero comprendido entre 0 y 65535 y puede repetirse el mismo valor entre registros MX con el objetivo de balanceo de carga. Cuanto más bajo sea el valor, mayor es la prioridad.

No suele asignarse la prioridad 0 al servidor de correo principal: normalmente se le asigna el valor 10 (de esta manera, si introducimos un nuevo servidor con la máxima prioridad, solo habría que crear un registro MX con una prioridad inferior a 10).

Es habitual, por seguridad, tener al menos dos servidores de correo, por lo que en un archivo de zona lo normal es tener al menos dos registros MX. Estos deben llevar aparejados registros A (nunca CNAME) si se especifica en el campo nombre-servidor-correo un servidor interno al dominio; si fuera externo al RR A no se pone.

#### A

El RR **A** *(Address,* RFC 1035) asocia un nombre de equipo a una IPv4. Su sintaxis es la siguiente:

```text
nombre-host ttl-RR  IN  A   IPv4
```

donde el campo `IPv4` es una dirección IP en la versión 4 del protocolo IP. Al ser un número y no un nombre, no hay que finalizarlo con un punto y no se produce la sustitución de $ORIGIN.

Dentro de esta directiva también podemos asociar a una IP más de un nombre DNS y a un nombre DNS varias direcciones IP.

#### AAAA

El RR **AAAA** (RFC 1035) es funcionalmente equivalente al RR A con la diferencia que trabaja con direcciones IPv6. Su sintaxis es la siguiente:

```text
nombre-RR ttl-RR IN AAAA IPv6
```

donde el campo IPv6 es una dirección IP en la versión 6 del protocolo IP.

#### CNAME

El RR **CNAME** *(Canonical Name,* RFC 1035) se utiliza para asignar un alias a un nombre que ya exista, tanto interno como externo al dominio. Su sintaxis es la siguiente:

```text
nombre-alias ttl-RR IN CNAME nombre-real
```

Pensemos en un servidor que tenga varios servicios, y cada servicio está asociado a un nombre particular, pero apuntando a la misma dirección IP. Por ejemplo, `ftp.iespacomolla.es` o `mail.iespacomolla.es` serán alias que referenciarán a una sola dirección IP que puede tener ya asociado un nombre DNS.

No se aconseja que el campo `nombre-real` sea a su vez un alias, porque hace que el proceso de búsqueda de la IP sea más lento y además puede llevarnos al error de crear un bucle, donde se crea un alias a otro alias, y este último, a su vez, hace referencia al primero.

#### TXT

El RR **TXT** *(Text,* RFC 1035) se usa para introducir una descripción arbitraria en texto plano que podrá ser consultada como cualquier otro tipo de RR. Su sintaxis es la siguiente:

```text
nombre-RR IN TXT texto-RR
```

El campo `nombre-RR` puede ser cualquier nombre, habitualmente el nombre del dominio, de un RR NS, MX, A, etcétera, y es el nombre que se utilizará para la consulta.

#### PTR

El RR **PTR** *(Pointer,* RFC 1035) se usa para asociar a una IP un nombre de equipo y de esta manera poder realizar las búsquedas inversas. Puede verse como el registro opuesto al RR A. Sirve tanto para el protocolo IPv4 como el IPv6. Su sintaxis es la siguiente:

```text
ip-host ttl-RR IN PTR nombre-host
```

El campo `ip-host` es un número, la parte de host de una IP, pero se trata como un nombre, por lo que la sustitución de la directiva $ORIGIN tiene lugar si no acaba en punto.

El campo `nombre-host` es el nombre que se le asociará a la IP y debe estar en formato FQDN, es decir, debe acabar en punto porque, de lo contrario, $ORIGIN se sustituirá y obtendremos resultados extraños, ya que el dominio que le añadiríamos no es el adecuado.

### Transferencias de zona


Para simplificar el mantenimiento de múltiples servidores DNS, es útil disponer de un único servidor (maestro) a partir del cual se actualice el resto (esclavos). Este proceso involucra la transferencia de archivos de zona de un servidor maestro a otros esclavos. Todo esto está estandarizado por el protocolo DNS y se realiza por el puerto 53 utilizando TCP como transporte (a diferencia de las consultas DNS que, como recordarás, usan UDP).

Al principio, la actualización de los servidores esclavos se hacía a través de una transferencia completa de los archivos de zona del servidor maestro, lo cual se denominaba **transferencia AXFR**. Con el paso del tiempo, Internet fue creciendo y también los archivos de zona, por lo que cada vez era más lenta la actualización de los servidores DNS debido al volumen de información a transferir. Para evitar este problema se crearon las transferencias incrementales, denominadas **transferencias IXFR**, con las cuales solo se envían los cambios que se han producido en el archivo de zona. Posteriormente, se creó otro tipo de transferencia haciendo uso de los mensajes NOTIFY.

#### AXFR

***Asynchronous Full Transfer Zone*** es el proceso original de actualización de las zonas de los servidores esclavos a partir de las zonas del servidor maestro, consistía en que, pasado el tiempo de refresco especificado en el RR SOA, el servidor esclavo solicitaba el registro SOA al maestro; una vez recibido, el servidor esclavo comparaba el número de serie del RR SOA recibido con el número de serie del RR SOA de la última transferencia, y si el primero era superior al segundo, significaba que la zona en el servidor maestro se había modificado desde la última transferencia, por lo que el esclavo solicitaba una nueva transferencia del archivo de zona completo (**AXFR**).

Es fundamental que la manipulación del número de serie del RR SOA sea correcta, y por tanto, siempre se cambie a un número superior.

#### IXFR

Con el crecimiento de Internet, las transferencias AXFR se hicieron lentas y consumían mucho ancho de banda, además de que no es muy óptimo hacer un simple cambio en el archivo de zona y transferir el archivo completo. Para solucionar este problema se crearon las **transferencias incrementales *(IXFR, Incremental Zone Transfer)***, con las cuales solo se enviaban aquellos RR que habían sido modificados desde la última transferencia.

El proceso IXFR es prácticamente igual que el AXFR, en primer lugar, después del tiempo de refresco, el servidor esclavo solicita al maestro el RR SOA, y si lleva un número de serie superior, el esclavo solicita la transferencia del archivo de zona e indica si es capaz de aceptar transferencias IXFR; si ambos, el maestro y el esclavo, pueden trabajar con estas transferencias, se produce una transferencia IXFR del archivo de zona, en otro caso, la transferencia será AXFR.

Como puede entenderse de lo anterior, IXFR solo afecta a la cantidad de información que se transfiere, pero no al tiempo de actualización, que sigue siendo el tiempo de refresco.

#### NOTIFY

Como se ha dicho en el punto anterior, con las transferencias IXFR se consigue reducir el volumen de información a transferir, pero el tiempo que tardan en propagarse las modificaciones del archivo de zona sigue siendo el tiempo de refresco especificado en el RR SOA, que suelen ser varias horas, de 2 a 12 según la recomendación del RFC 1912, lo cual puede ser inaceptable en algunos casos.

El RFC 1996 introdujo los mensajes **NOTIFY**, que pueden ser enviados (tanto por servidores maestros como esclavos) a los servidores indicados en los RR NS cuando la zona es cargada. Un servidor esclavo que recibe este mensaje solicita a continuación el RR SOA al servidor maestro, realizándose el proceso ya descrito anteriormente, que podrá acabar con una transferencia AXFR o IXFR.

Como puede verse, con los mensajes NOTIFY sí que se puede reducir el tiempo de propagación de los cambios.

#### DDNS
La nomenclatura ***Dynamic DNS (DynDNS)*** hace referencia a ***Dynamic Domain Name System*** (sistema dinámico de nombres de dominio), también conocido por su abreviatura **DDNS** y sirve para asociar direcciones IP que cambian de forma dinámica a un nombre de dominio fijo. Un servicio DDNS mantiene una base de datos de estos cambios y actualiza los servidores DNS con ellos.

El DDNS puede entenderse como una extensión del DNS. El DNS es un directorio global que conoce la dirección IP de cada dominio registrado, es decir, que puede resolver nombres a direcciones para permitir el acceso a páginas web, correos electrónicos o al protocolo FTP. El DNS «busca» la dirección IP perteneciente al nombre del host (en caso de que esta no pueda encontrarse en la caché del DNS), estableciendo para ello un diálogo permanente con la base de datos del DNS del proveedor de Internet correspondiente. Si todo va bien, responde a la solicitud con la dirección IP correcta. En sentido contrario, las direcciones IP pueden traducirse en los nombres de dominio correspondientes, pero resulta un poco complicado porque el reverse DNS no es un estándar de Internet y el DNS tampoco está optimizado para ello.

El procedimiento de los servicios DDNS secunda, por contra, la asignación explícita de direcciones IP dinámicas a dominios específicos, automatizando así la coordinación con las bases de datos DNS del proveedor de Internet.

El DDNS se ocupa, en este sentido, de detectar las modificaciones llevadas a cabo en la dirección IP y de actualizar los registros DNS en consecuencia. Por ello, se puede establecer una conexión con un equipo cuya dirección IP varíe de forma dinámica.


&nbsp;

&nbsp;

&nbsp;

# Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 R. Cantó, M. A. Tomás, [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)



