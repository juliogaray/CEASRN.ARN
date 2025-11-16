![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)


# Gestión de Direcciones IP con ~~IPAM~~ *Amazon VPC IP Address Manager*

> **NOTA:** Dentro de AWS Academy y, concretamente, dentro de un *Learner Lab*, no está disponible el servicio IPAM. Vamos a hacer esta práctica adaptada con el servicio *Amazon VPC IP Address Manager*, que es «parecido».

## Objetivos
1. Crear y configurar un **IPAM** (IP Address Manager) en AWS.
2. Configurar ***pools* de direcciones IP** para una red VPC.
3. Asignar direcciones IP a subredes dentro de una VPC.
4. Monitorear y visualizar el uso de direcciones IP a través de la consola de IPAM.

**Debes entregar** en esta práctica un archivo PDF donde expliques el proceso de creación de todos los elementos requeridos. **No es necesario que el documento contenga ninguna captura de pantalla.**

Indica en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

## 1. Crear un IPAM

1. **Accede a la consola de AWS** en nuestro *Learner Lab.*
2. En el panel de búsqueda, escribe **IPAM** y selecciona **Amazon VPC IP Address Manager**.
3. Haz clic en **Create IPAM**:
    - Marca la casilla que «permite a *Amazon VPC IP Address Manager* replicar los datos de las cuentas de los miembros en la cuenta de administrador delegado y de las regiones operativas en la región de origen».
    - **Nivel de IPAM**: «Nivel avanzado» (con el gratuito el servicio está muy limitado y no es posible continuar la práctica);
    - **Name**: ipam-*tus_iniciales* (por ejemplo, en mi caso, ipam-jgg);
    - **Scope**: Selecciona **Private** (ya que trabajaremos con direcciones IP privadas).
    - **Regions**: Elige la región (por ejemplo, *us-east-1).*
4. Revisa la configuración y haz clic en **Create IPAM**. Esto creará un administrador de direcciones IP centralizado.
5. Anota en tu documento de respuesta el IP de la IPAM, y los identificadores de alcance público y privado.

## 2. Crear un *pool* de direcciones IP

1. En el menú lateral de IPAM, selecciona **Pools** o **Grupos** y luego haz clic en **Create pool(Crear grupo)**.
2. Completa los siguientes datos:
   - **Alcance de IPAM**: Selecciona el IPAM **privado** que acabas de crear.
   - **Etiqueta de nombre**: «Pool-VPC-Privado-jgg» (sustituye «jgg» por tus iniciales).
   - **Familia de direcciones**: Selecciona **IPv4** (esto no es posible con el nivel gratuito de IPAM).
   - **Planificación de recursos**: «dentro del alcance».
   - **Configuración regional**: «us-east-1» (debe ser la misma donde crearemos después la VPC).
3. En la sección de **CIDR**, introduce el rango de direcciones IP 10.ЖЖ.0.0/16, donde ЖЖ es tu número de PC en clase.
4. Haz clic en **Create** para crear el pool.
5. Anota en tu documento de respuesta el IP del *pool* creado.
---

## 3. Crear una VPC utilizando el *pool* de IPAM

1. En el menú de la consola, ve a **VPC** y selecciona **Sus VPC**.
2. Haz clic en **Crear VPC**.
   - Asegúrate de que estás en la región «Norte de Virginia» en la barra de navegación, arriba del todo.
   - Marca la opción **Solo la VPC**.
   - **Nombre**: «jgg-vpc-ipam» (sustituye «jgg» por tus iniciales).
   - **CIDR block**: Selecciona la opción de **Bloque de CIDR IPv4 asignado por IPAM**.
   - Elige el pool "Pool-VPC-Privado" que creaste antes (probablemente esté ya seleccionado).
   - La **CIDR** será seleccionada automáticamente desde el pool de direcciones.
3. Haz clic en **Create**.

## 4. Crear subredes y asignar direcciones IP automáticamente

1. Ve a **Subredes** en la consola de **VPC** y haz clic en **Crear subred**.
   - **VPC**: Selecciona «jgg-vpc-ipam» (sustituye «jgg» por tus iniciales).
   - **Nombre de la subred**: «jgg-vpc-ipam-subred-1» (sustituye «jgg» por tus iniciales).
   - **Availability Zone**: Elige una de las AZ disponibles.
   - **Bloque de CIDR de VPC IPv4**: Asigna un rango de direcciones IP dentro del disponible para la VPC.
2. Repite este proceso para crear una segunda subred ("Subred-2") en otra Availability Zone.

### 5. Verificar y monitorizar el uso de direcciones IP

1. Regresa a la consola de **Amazon VPC IP Address Manager**.
2. Haz clic en **Grupos** y selecciona "Pool-VPC-Privado-jgg" (pero, ya sabes, sustituye «jgg» por tus iniciales).
3. Verás una lista con el **estado de las asignaciones de IPs** y qué bloques CIDR han sido asignados a las subredes dentro de la VPC.
4. Observa el uso de direcciones IP, identificando cuántas direcciones están en uso y cuántas están disponibles en el *pool.*

**Anota en tu documento de solución de la práctica** los datos vistos en los puntos 3 y 4.

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
