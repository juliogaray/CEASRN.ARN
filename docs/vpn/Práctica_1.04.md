![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)


# Configuración de una conexión entre VPCs de distintos alumnos

## Objetivos
Configurar una conexión de *VPC Peering* entre dos instancias EC2 alojadas en diferentes VPCs (pertenecientes a diferentes cuentas usuario). Aprenderemos: 
- Aspectos importantes de la configuración de redes;
- Gestión de permisos de seguridad;
- Encaminamiento en AWS.

Esta práctica debe ayudarte a comprender la configuración de interconexión de redes en AWS, el enrutamiento entre redes privadas y la importancia de la seguridad en entornos de nube.

>**NOTA:** Como es obvio, **esta práctica se debe realizar por parejas**.

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo** —esta vez sí— **capturas de pantalla.**
- Verificar y explicar los resultados de vuestras pruebas de conectividad.
- Responder a estas preguntas:
  - ¿Cuál es la función del *VPC Peering* y en qué situaciones se utiliza?
  - ¿Por qué es importante configurar las tablas de enrutamiento correctamente?
  - ¿Qué otros métodos existen para conectar VPCs en distintas cuentas?

**Cada alumno/a deben entregar su propio trabajo**, independientemente del de su compañero/a de práctica.  
Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera). Indica también los datos necesarios de tu compañero/a de práctica, incluyendo **su nombre**, su ID de cuenta y el identificador de su PVC.

---
## Paso 1: Configuración de las VPCs

>**Atención:** puedes usar los recursos creados en prácticas anteriores. De los pasos que se detallan en este punto, ejecuta sólo los que te sean necesarios.

>**NOTA sobre los rangos IP:** como es lógico, las subredes que vamos a conectar no pueden usar los mismos rangos IP. En principio, si has seguido correctamente las instrucciones de las prácticas anteriores, esto no debería ser problema, ya que hemos usado el n.º de PC de clase como parte del rango IP. En los ejemplos que siguen se usan los rangos 10.41.0.0/16 y 10.71.0.0/16, y subredes de ambos; **sustituye en cada caso el número correspondiente —41 ó 71— por el número de PC que ocupas en clase.**

Completa los siguientes pasos en tu propia cuenta:

1. **Crear una VPC:**
   - Ve a ***VPC Dashboard*** > ***Create VPC***.
   - Asigna un nombre propio a la VPC, preferiblemente usando tus iniciales (por ejemplo, `vpc_jgg`).
   - Selecciona un rango de IP único para cada VPC (por ejemplo, `10.41.0.0/16` para el estudiante 1 y `10.71.0.0/16` para el estudiante 2).

2. **Crea una subred pública** dentro de cada VPC:
   - Asigna una **subred CIDR** que esté dentro del rango de la VPC (por ejemplo, `10.41.1.0/24` y `10.71.1.0/24`).
   - Asegúrate de que cada subred esté asociada con una **tabla de enrutamiento pública** (aunque no es nuestro caso, en otro contexto podría ser necesaria una ruta hacia Internet).

3. **Crea una instancia EC2** dentro de cada subnet:
   - Lanza una **instancia EC2** en la subred recién creada (usa *Amazon Linux,* para disponer libremente del *Agente SSM).*
   - Asegúrate de que el **grupo de seguridad** de la instancia permita la entrada de tráfico en el puerto que usaréis para la conexión (por ejemplo, SSH en el puerto 22 o ICMP para hacer ping).

> **Nota:** Cada estudiante debe anotar la **IP privada** de su instancia EC2 para usarla más adelante.

## Paso 2: Configurar la conexión de *VPC Peering*

1. **Obtén el *Account ID*** de tu compañero/a de práctica.
2. **Obtén el *ID* de la *VPC*** de tu compañero/a de práctica.

2. **Crea la conexión de *VPC Peering:***
   - En el **VPC Dashboard** de uno de los estudiantes, id a **Peering Connections** > **Create Peering Connection**.
   - Seleccionad:
     - La VPC propia del estudiante de ese ordenador como la VPC solicitante.
     - El **Account ID** y el **VPC ID** del otro alumno/a como la VPC aceptante.
   - Solicitad la conexión.

3. **Aceptar la conexión de *VPC Peering*** en la cuenta del otro estudiante:
   - El segundo estudiante debe ir a ***Peering Connections**,* encontrar la solicitud pendiente y **aceptarla**.

## Paso 3: Configuración de encaminamiento

1. **Actualizar las tablas de enrutamiento de ambas VPCs**:
   - Cada alumno debe ir a la tabla de enrutamiento asociada a su VPC y agregar una ruta hacia la otra VPC.
   - Por ejemplo:
     - El estudiante 1 agregará una ruta a la red `10.71.0.0/16` (VPC del estudiante 2) con destino a la conexión de *peering* creada.
     - El estudiante 2 hará lo mismo, añadiendo una ruta a la red `10.41.0.0/16` a través de la conexión de *peering.*

## Paso 4: Configuración de *Grupos de seguridad*

1. **Permitir el tráfico en ambos sentidos**:
   - Cada estudiante debe editar el **grupo de seguridad** de su instancia EC2 para permitir el tráfico desde la red de la VPC del otro estudiante.
   - Por ejemplo, el grupo de seguridad del estudiante 1 debe permitir tráfico entrante desde `10.71.0.0/16` (la VPC del estudiante 2).
   - Configura el grupo de seguridad para permitir tráfico en el puerto deseado. Como mínimo, **ICMP** para ping y **SSH** para pruebas.

## Paso 5: Verificación de la conexión

1. **Probar la conectividad**:
   - Intenta iniciar sesión en tu instancia EC2 y hacer ping a la **IP privada** de la instancia EC2 de tu compañero/a.
   - Intenta establecer una conexión SSH a la otra máquina. Para ello deberéis intercambiar nombres de usuario y contraseñas para hacer la prueba, obviamente.

## Paso 6: Limpieza (optativo)

Para evitar costos innecesarios, cada alumno debe:
- Eliminar la conexión de ***VPC Peering**.*
- Eliminar las **instancias EC2** ~~y las **VPCs** creadas para esta práctica.~~ No vamos a hacer esto, porque las VPC nos servirán para prácticas futuras.

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
