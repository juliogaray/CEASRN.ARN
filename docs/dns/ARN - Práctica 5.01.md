![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Amazon Route 53 - Gestión de DNS con Route 53 + Seguridad

En esta práctica probaremos el servicio Amazon Route 53, reservando un dominio para las prácticas, gestionándolo, y estableciendo la configuración básica de seguridad.

## Objetivos
1. Registrar y configurar un dominio en Amazon Route 53.
2. Crear y gestionar una zona hospedada (hosted zone).
3. Definir registros DNS (A, CNAME, MX, TXT).
4. Configurar *Route 53 Resolver DNS Firewall*.
5. Crear reglas de firewall para proteger las consultas DNS.
6. Asociar un nombre de dominio a un servidor web montado en EC2.

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.** Particularmente:
  - Captura de pantalla de la **Hosted Zone** en Route 53 con los registros A y CNAME.
  - Evidencia del **bloqueo de DNS Firewall** (log de consulta denegada).
- Responder a estas preguntas:
  - Cuando definimos, en la parte 3, los registros de la zona, especificamos un registro de tipo MX. Ya sabes que eso indica a qué máquina se debe enviar el correo electrónico dirigido a cualquier cuenta del dominio. ¿Qué haría falta añadir en la zona para que esto funcionase?
  - ¿Qué ventajas ofrece el uso de Route 53 frente a un proveedor DNS tradicional?
  - ¿Qué implicaciones de seguridad tendría permitir consultas DNS sin restricciones dentro de una red en la nube?
  - ¿Por qué es importante proteger las consultas DNS mediante un cortafuegos como el de *Route 53 Resolver?*
  - ¿Qué retos o problemas pueden surgir al asociar un dominio personalizado con una instancia EC2?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (nombre del subdominio, registros creados, etcétera).

---

## Parte 1 – Registro de un dominio y configuración en Route 53

Para esta parte necesitaremos registrar un dominio. Esto no se puede hacer gratuitamente, ahora que [Freenom](https://www.freenom.com/), la única empresa que lo permitía, ha dejado de ofrecer este servicio.

Así que disponemos de dos alternativas: registrar un dominio económico, o registrar un dominio directamente en *Route 53.* En este momento, en la *Landing Zone* con la que trabajamos, no es posible. Por ello vamos a plantear la práctica con la segunda opción, lo que nos supondrá un coste de casi un euro. 

1. Accede a un proveedor de dominios. Te recomendamos usar [*namecheap*](https://www.namecheap.com/). Dispone de una promoción de dominios por 99 centavos de dólar, o sea, 87 céntimos de euro, usando el código `99SPECIAL` en [esta página](https://www.namecheap.com/promos/99-cent-domain-names/).
2. Crea una cuenta, elige un dominio a tu gusto que no te cueste más de lo razonable, y adquiérelo. Aquí vamos a usar el ejemplo `cloudpaco.space`.
3. Accede a la configuración DNS del dominio en *namecheap* y establece los servidores de nombres *(nameservers)* proporcionados por Amazon Route 53 (ver Parte 2).
4. Verifica que la propagación DNS se ha completado correctamente (puede tardar unos minutos… o incluso horas).

---

## Parte 2: Creación de la zona hospedada

1. En Route 53, ve a **"Hosted Zones" > "Create Hosted Zone"**.
2. Introduce el nombre del dominio registrado.
3. Tipo: **Public hosted zone**.
4. Crea la zona.

Amazon creará automáticamente registros NS y SOA.

---

## Parte 3: Gestión de registros DNS

Dentro de la zona hospedada:

1. Crear un **registro A**:
   - Nombre: `www`
   - Tipo: A
   - Valor: IP pública de la instancia EC2 (ver Parte 6)

2. Crear un **registro CNAME**:
   - Nombre: `blog`
   - Tipo: CNAME
   - Valor: `www.cloudpaco.space` (usa tu propio dominio, ya sabes)

3. Crear un **registro MX**:
   - Nombre: (vacío)
   - Tipo: MX
   - Valor: `10 mail.cloudpaco.space`

4. Crear un **registro TXT** (para SPF o verificaciones):
   - Nombre: `_amazonses`
   - Tipo: TXT
   - Valor: "v=spf1 include:amazonses.com ~all"

   Este registro se utiliza habitualmente para configurar políticas de envío de correo electrónico mediante **Amazon SES (Simple Email Service)**. Sirve para indicar a los servidores de destino que el dominio permite a Amazon SES enviar correos en su nombre, lo cual ayuda a evitar que esos correos sean considerados como spam o suplantación de identidad (phishing). También es útil para verificar el dominio en algunos servicios en la nube.

---

## Parte 4: Activar y configurar *Route 53 Resolver DNS Firewall*

1. Ve a **Route 53 > DNS Firewall**.
2. Crea un **Firewall rule group**:
   - Nombre: `BloqueoMalware`
   - Descripción: Bloqueo de dominios maliciosos conocidos

3. Añade reglas:
   - Regla 1: bloquear `*.malwaredomain.com`
   - Regla 2: bloquear `*.phishingtest.net`
   - Acción: **BLOCK** con respuesta `NXDOMAIN`

> **NOTA:** Esta regla especifica que cuando un cliente intente resolver un nombre de dominio que coincida con el patrón definido (por ejemplo, `*.malwaredomain.com`), el firewall DNS devolverá la respuesta `NXDOMAIN`, indicando que el dominio no existe. Esto impide que el cliente llegue a contactar con el dominio, lo cual es útil para bloquear sitios maliciosos conocidos o de prueba. `NXDOMAIN` es una respuesta estándar del sistema DNS que comunica que el nombre de dominio solicitado no tiene una dirección asociada válida en la zona consultada.

4. Añade una regla más:  
   - **Lista predefinida**: Selecciona `AWSManagedDomainsMalwareDomainList`.  
   - **Acción**: **BLOCK** con respuesta `NXDOMAIN`

5. Guarda el grupo de reglas.

---

## Parte 5: Asociar el firewall a una VPC

1. Ve a **"Associations"** dentro del grupo de reglas creado.
2. Asocia con la VPC donde estén las instancias o servicios que realizan consultas DNS.
3. Confirma que la VPC está usando *Route 53 Resolver* (por defecto lo hace).

---

## Parte 6: Montaje de un servidor web en EC2 y asociación con el dominio

1. Lanza una nueva instancia EC2:
   - AMI: Amazon Linux 2 (o Ubuntu Server)
   - Tipo: t2.micro (dentro del Free Tier)
   - Seguridad: permite tráfico HTTP (puerto 80), HTTPS (puerto 443) y SSH (puerto 22)

2. Conéctate a la instancia vía SSH:
   ```bash
   ssh -i clave.pem ec2-user@IP_PUBLICA
   ```

3. Instala un servidor web (ejemplo con Apache en Amazon Linux 2):
   ```bash
   sudo yum update -y
   sudo yum install -y httpd
   sudo systemctl start httpd
   sudo systemctl enable httpd
   echo "<h1>Servidor web de prueba en EC2</h1>" | sudo tee /var/www/html/index.html
   ```

4. Asocia la IP pública al registro A correspondiente en Route 53 (ver **Parte 3**).

5. Accede desde el navegador a… (modifica el dominio para usar el tuyo propio):
   ```
   http://www.cloudpaco.space
   ```
   Debería mostrarse la página HTML servida desde EC2.

---

## Parte 7: Pruebas

Desde una instancia EC2 o desde tu máquina local:

1. Comprueba que se puede resolver `www.cloudpaco.space` (usando tu dominio):
   ```bash
   dig www.cloudpaco.space
   ```

2. Intenta resolver un dominio bloqueado:
   ```bash
   dig test.malwaredomain.com
   ```
   - Debería devolver `NXDOMAIN` o sencillamenteo no resolver.

3. Accede al sitio web desde el navegador para verificar que el dominio responde correctamente.

---

## Recursos adicionales

- [Documentación oficial de Route 53](https://docs.aws.amazon.com/route53/)
- [Lista de dominios maliciosos de prueba](https://www.eicar.org/)
- [Guía de DNS Firewall Resolver](https://docs.aws.amazon.com/route53/latest/DeveloperGuide/resolver-dns-firewall.html)
- [Guía para instalar Apache en EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html)

---

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)

