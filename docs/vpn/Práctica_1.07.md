![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Monitorización de la red con VPC Flow Logs

## Objetivos
Aprender a configurar y usar **VPC Flow Logs** para monitorizar el tráfico de red dentro de una **VPC** en AWS:
- Habilitar los *logs*
- interpretar su contenido
- crear métricas personalizadas
- analizar datos usando herramientas básicas de **CloudWatch**.

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- Responder a estas preguntas:
  - ¿Qué diferencia existe entre los *logs* de tráfico aceptado y rechazado en los **VPC Flow Logs**?
  - ¿Qué ventajas ofrecen las métricas personalizadas para monitorizar patrones específicos en el tráfico de red?
  - ¿Cómo podrías optimizar las reglas de seguridad basándote en los datos de los *VPC Flow Logs?*
  - Si observases un aumento repentino en el tráfico rechazado, ¿qué pasos tomarías para investigar y solucionar el problema?
  - ¿Qué limitaciones tienen los *VPC Flow Logs* para el análisis de redes y cómo podrías complementarlos con otras herramientas de AWS?
  - **Pregunta bonus**: ¿qué podría aportar a tu análisis de datos el uso de **Amazon CloudWatch Logs Insights**? En esta práctica hemos prescindido de ampliar el contenido con esta herramienta, pero en el futuro seguramente la ampliaremos con su uso.

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

---

## Paso 1: configurar el entorno

1. **Crear una VPC personalizada:**
   - Accede a la consola de **VPC**.
   - Crea una nueva **VPC** con las siguientes configuraciones:
     - **Nombre:** `Mi_VPC` (o el nombre que quieras)
     - **CIDR:** `10.0.0.0/16`
   - Crea una **subred pública** (por ejemplo, `MiVPCSubredPub`) y una **subred privada** (por ejemplo, `MiVPCSubredPriv`).

2. **Lanzar dos instancias EC2:**
   - Lanza dos instancias EC2 (usa *Amazon Linux* por simplicidad) en la VPC creada:
     - **Instancia A:** En la subred pública.
     - **Instancia B:** En la subred privada.
   - Asigna nombres para identificarlas fácilmente (por ejemplo, `InstanciaA` y `InstanciaB`).
   - Configura el acceso SSH para la instancia en la subred pública.

3. **Configurar el tráfico de red:**
   - Asegúrate de que las instancias puedan comunicarse entre sí:
     - Configura una **tabla de rutas** para permitir el tráfico entre subredes.
     - Ajusta los **grupos de seguridad** para permitir tráfico ICMP (ping) y SSH entre las instancias.


## Paso 2: habilitar *VPC Flow Logs*

1. Ve a la consola de **VPC** y selecciona la VPC `Mi_VPC`.
2. En la pestaña **Flow Logs**, haz clic en **Create Flow Log**:
   - **Filter:** Selecciona `All` para capturar todo el tráfico.
   - **Destination log group:** Selecciona **Send to CloudWatch Logs** y elige un nombre descriptivo como `GrupoVPCFlowLogs`.
   - **IAM Role:** Crea o selecciona un rol de IAM con permisos para enviar *logs* a CloudWatch (ya sabes las limitaciones que tenemos en el Learner Lab al respecto).

3. Verifica que los *logs* comiencen a aparecer en **CloudWatch Logs**:
   - Ve a la consola de CloudWatch.
   - Busca el **Log Group** `GrupoVPCFlowLogs` y confirma que los registros están llegando.


## Paso 3: interpretar los *PC Flow Logs*

1. **Explorar el contenido de los logs:**
   - Accede al grupo de *logs* en **CloudWatch Logs**.
   - Inspecciona algunas entradas de *log.* Cada registro tiene el siguiente formato:
     ```lua
     version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status
     ```

2. **Responder preguntas clave:**
   - ¿Qué direcciones IP están generando la mayor parte del tráfico?
   - ¿Hay tráfico rechazado (`action="REJECT"`)?


## Paso 4: crear métricas personalizadas

1. Configura un **filtro de métricas** en el grupo de logs `GrupoVPCFlowLogs` para capturar eventos relevantes.

   **Ejemplo 1: métrica para tráfico rechazado**
   - Ve al grupo de logs y selecciona **Create Metric Filter**.
   - Usa el siguiente patrón para capturar tráfico rechazado:
     ```lua
     [version, account-id, interface-id, srcaddr, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action="REJECT", log-status]
     ```
   - Asigna un nombre a la métrica, como `TraficoRechazado`.

   **Ejemplo 2: métrica para tráfico por puerto 22 (SSH)**
   - Configura un filtro para capturar tráfico hacia el puerto 22:
     ```lua
     [version, account-id, interface-id, srcaddr, dstaddr, srcport, dstport=22, protocol, packets, bytes, start, end, action, log-status]
     ```

2. Verifica las métricas en **CloudWatch Metrics**:
   - Ve a la sección de métricas en CloudWatch.
   - Encuentra las métricas personalizadas bajo el *namespace* que configuraste.


## Paso 5: crear una alarma para tráfico anómalo

1. Configura una alarma basada en la métrica `TraficoRechazado`:
   - Ve a **CloudWatch > Alarms > Create Alarm**.
   - Selecciona la métrica `TraficoRechazado`.
   - Configura la alarma para que se active si el tráfico rechazado supera cierto umbral (por ejemplo, 10 eventos en 5 minutos).

2. Configura una acción para la alarma:
   - Usa un tema de **SNS** para enviar notificaciones por correo electrónico.


## Paso 6: generar tráfico de red y analizar resultados

1. Conéctate a la instancia pública (`InstanciaA`) usando SSH.
2. Genera tráfico hacia la instancia privada (`InstanciaB`) usando comandos como `ping` o `curl`.
3. Intenta realizar acciones que generen tráfico rechazado, como conectar a un puerto bloqueado por las reglas de seguridad.


## Paso 7: crear un *Dashboard* de CloudWatch

1. Ve a la consola de **CloudWatch > Dashboards**.
2. Crea un Dashboard llamado `MonitorizacionVPC`.
3. Añade *widgets:*
   - Gráficos para las métricas personalizadas creadas (por ejemplo, `TraficoRechazado`).
   - Gráficos de métricas estándar como **NetworkIn** y **NetworkOut** para las instancias EC2.

Haz una captura bonita para la entrega. :wink:

## Paso 8: responde a las preguntas...

... de la sección «Entrega» :wink:

¡Y las del paso 3, ojo!

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
