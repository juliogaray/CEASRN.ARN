![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)


# Gestión de Direcciones IP con AWS IPAM

## Objetivos
1. Crear y configurar un **IPAM** (IP Address Manager) en AWS.
2. Configurar ***pools* de direcciones IP** para una red VPC.
3. Asignar direcciones IP a subredes dentro de una VPC.
4. Monitorear y visualizar el uso de direcciones IP a través de la consola de IPAM.

**Debes entregar** en esta práctica un archivo PDF donde expliques el proceso de creación de todos los elementos requeridos. **No es necesario que el documento contenga ninguna captura de pantalla.**

Indica en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

## 1. Crear un IPAM

1. **Accede a la consola de AWS** en nuestro *Learner Lab.*
2. En el panel de búsqueda, escribe **IPAM** y selecciona **IP Address Manager**.
3. Haz clic en **Create IPAM**:
   - **Name**: "Práctica-IPAM" (puedes elegir cualquier nombre).
   - **Scope**: Selecciona **Private** (ya que trabajaremos con direcciones IP privadas).
   - **Regions**: Elige la región (por ejemplo, *us-east-1).*
4. Revisa la configuración y haz clic en **Create IPAM**. Esto creará un administrador de direcciones IP centralizado.

## 2. Crear un *pool* de direcciones IP

1. En el menú lateral de IPAM, selecciona **Pools** y luego haz clic en **Create pool**.
2. Completa los siguientes datos:
   - **IPAM scope**: Selecciona el IPAM que acabas de crear.
   - **Name**: "Pool-VPC-Privado".
   - **IP address family**: Selecciona **IPv4**.
3. En la sección de **CIDR**, introduce el rango de direcciones IP 10.ЖЖ.0.0/16, donde ЖЖ es tu número de PC en clase.
4. Haz clic en **Create** para crear el pool.

---

## 3. Crear una VPC utilizando el *pool* de IPAM

1. En el menú de la consola, ve a **VPC** y selecciona **Your VPCs**.
2. Haz clic en **Create VPC**.
   - **Name**: "VPC-Práctica-IPAM".
   - **CIDR block**: Selecciona la opción de **Use IPAM pool**.
   - Elige el pool "Pool-VPC-Privado" que creaste antes.
   - La **CIDR** será seleccionada automáticamente desde el pool de direcciones.
3. Haz clic en **Create**.

## 4. Crear subredes y asignar direcciones IP automáticamente

1. Ve a **Subnets** en la consola de **VPC** y haz clic en **Create subnet**.
   - **Name**: "Subred-1".
   - **VPC**: Selecciona "VPC-Práctica".
   - **Availability Zone**: Elige una de las AZ disponibles.
   - **Use IPAM**: Activa la opción para que se asigne un bloque CIDR automáticamente desde el pool.
2. Repite este proceso para crear una segunda subred ("Subred-2") en otra Availability Zone.
3. AWS IPAM asignará automáticamente rangos de direcciones IP para ambas subredes desde el pool configurado.

### 5. Verificar y monitorizar el uso de direcciones IP

1. Regresa a la consola de **IPAM**.
2. Haz clic en **Pools** y selecciona "Pool-VPC-Privado".
3. Verás una lista con el **estado de las asignaciones de IPs** y qué bloques CIDR han sido asignados a las subredes dentro de la VPC.
4. Observa el uso de direcciones IP, identificando cuántas direcciones están en uso y cuántas están disponibles en el *pool.*

**Anota en tu documento de solución de la práctica** los datos vistos en los puntos 3 y 4..

### 6. Limpiar los recursos (punto optativo)

1. Elimina las subredes y la VPC creadas si ya no las necesitas para evitar costos adicionales.
2. En **IPAM**, también puedes eliminar el *pool* y el IPAM creado para este ejercicio.

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
