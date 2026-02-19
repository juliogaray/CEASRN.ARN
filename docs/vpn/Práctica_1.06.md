![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Monitorización de la red con AWS CloudWatch

## Objetivos
Aprender a monitorizar el tráfico de red de una instancia EC2 usando CloudWatch, crear métricas personalizadas, configurar alarmas y analizar el comportamiento de la red de la instancia.

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- Responder a estas preguntas:
  - ¿Cómo afecta el tráfico de red en la métrica de `NetworkIn` y `NetworkOut` de CloudWatch?
  - ¿Qué diferencia existe entre las métricas estándar de EC2 (como `NetworkIn` y `NetworkOut`) y las métricas personalizadas que has creado?
  - ¿Qué factores podrías tener en cuenta para establecer un umbral adecuado en una alarma de CloudWatch sobre el tráfico de red? Describe cómo determinarías un valor razonable para el tráfico de red que podría indicar un problema, como una sobrecarga o un ataque de denegación de servicio (DDoS).
  - ¿Cómo puede la integración de CloudWatch con otros servicios de AWS, como [AWS Lambda](https://es.wikipedia.org/wiki/AWS_Lambda) o [SNS](https://en.wikipedia.org/wiki/Amazon_Simple_Notification_Service), ayudarte a automatizar respuestas ante alarmas de tráfico de red? Por ejemplo, ¿qué acción automática podrías definir si el tráfico de red supera el umbral establecido?
  - ¿Qué impacto puede tener en la seguridad de la red el monitoreo proactivo del tráfico con CloudWatch?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

---

## Paso 1: preparar la instancia EC2
1. **Lanza una instancia EC2** en tu VPC.
   - Elige una instancia de tipo `t2.micro` (o cualquier otra que se ajuste a tus necesidades) con Amazon Linux.
   - Configura un **grupo de seguridad** para permitir el tráfico HTTP (puerto 80) y SSH (puerto 22).
   - Asigna una **dirección IP elástica** (si es necesario) para facilitar el acceso.

2. **Accede a tu instancia EC2** a través de SSH:
   ```bash
   ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-ip
   ```

## Paso 2: monitorizar las métricas de red estándar
1. AWS **CloudWatch** ya proporciona algunas métricas predeterminadas de la red para cada instancia EC2. Estas métricas incluyen:
   - **NetworkIn**: El tráfico acumulado de entrada a la instancia (en bytes).
   - **NetworkOut**: El tráfico acumulado de salida desde la instancia (en bytes).

2. **Verifica las métricas de red en CloudWatch**:
   - Ve a la consola de **CloudWatch** > **Metrics** > **EC2** > **Per-Instance Metrics**.
   - Busca las métricas `NetworkIn` y `NetworkOut` asociadas a tu instancia EC2.
   - Observa cómo cambian a medida que interactúas con la instancia.

## Paso 3: crear métricas personalizadas para monitorizar la red
En este paso crearemos una métrica personalizada que refleje el tráfico de red utilizando un script que revise las métricas de tráfico en la red de la instancia EC2.

1. **Instalar en la instancia EC2 `awslogs` para el agente de CloudWatch** (si no está instalado):
   ```bash
   sudo yum install amazon-cloudwatch-agent
   ```

2. **Configurar el agente de CloudWatch** para enviar métricas personalizadas:
   - Crea un archivo [JSON](https://es.wikipedia.org/wiki/JSON) para la configuración del agente:
     ```bash
     sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
     ```
     Ejemplo de contenido:
     ```json
     {
         "metrics": {
             "metrics_collected": {
                 "netstat": {
                     "measurement": [
                         {"name": "bytes_received", "unit": "Bytes"},
                         {"name": "bytes_sent", "unit": "Bytes"}
                     ],
                     "metrics_collection_interval": 60
                 }
             }
         }
     }
     ```
     
      **🔴NOTA**: Se supone que este archivo configurará el agente para recolectar métricas de tráfico de red, como los bytes recibidos (`bytes_received`) y enviados (`bytes_sent`) cada 60 segundos. Sin embargo comprobarás que **este ejemplo de contenido no funciona correctamente**. Contiene etiquetas y métricas obsoletas de Cloudwatch, y es necesario corregirlo para usar los valores con las definiciones actuales. Detalla en tu documento el proceso que sigues para corregir el contenido JSON anterior y hacer que funcione correctamente.

3. **Reiniciar el agente** para aplicar la configuración:
   ```bash
   sudo systemctl restart amazon-cloudwatch-agent
   ```

4. **Verificar las métricas personalizadas en CloudWatch**:
   - En la consola de **CloudWatch**, navega a **Metrics > Custom Metrics** y selecciona la métrica personalizada que has creado (por ejemplo, `bytes_received`, `bytes_sent`).

## Paso 4: crear una alarma para monitorizar el tráfico de red
Configuraremos una alarma de CloudWatch para avisarnos si el tráfico de red excede un umbral determinado (por ejemplo, si el tráfico de entrada excede 1 GB).

1. **Crear la alarma en la consola de CloudWatch**:
   - Ve a **CloudWatch** > **Alarms** > **Create Alarm**.
   - Selecciona la métrica personalizada que configuraste (por ejemplo, `bytes_received`).
   - Establece el umbral para la alarma:
     - Por ejemplo, si el tráfico de entrada supera 1 GB en un periodo de 5 minutos, activa la alarma.
   - Configura las **acciones** de la alarma (enviar una notificación a SNS, correo electrónico, etcétera).

2. **Verifica la alarma**:
   - Revisa si la alarma se activa cuando el tráfico de red supera el umbral configurado.

## Paso 5: generar tráfico de red para activar la alarma
En este paso, generaremos tráfico en la instancia EC2 para simular un aumento en la carga de red y activar la alarma.

1. **Generar tráfico de red (simulando usuarios)**:
   - Usa herramientas como `wget` o `curl` para descargar archivos grandes desde tu servidor o una página web.
   - Ejemplo: Descargar una imagen grande desde un servidor remoto:
     ```bash
     wget https://servidor_de_ejemplo.com/archivo_grandote.zip
     ```
> **NOTA:** el archivo descargado debería ser mayor que el umbral de alarma establecido, obviamente.

2. **Verificar si la alarma se activa**:
   - Mientras se genera el tráfico, verifica en la consola de CloudWatch si la alarma se dispara. Deberías recibir una notificación según lo configurado en el paso anterior.

## Paso 6: análisis
**Revisa las métricas** en CloudWatch:
   - En la consola de CloudWatch, revisa cómo las métricas personalizadas (`bytes_received`, `bytes_sent`) han evolucionado con el tráfico de red.
   - Analiza la forma en que el tráfico de entrada y salida afecta las métricas y si las alarmas se activan correctamente.

## Paso 7: limpieza
Si ya no necesitas las métricas personalizadas, puedes desactivar el agente de CloudWatch y eliminar las alarmas:

```bash
sudo systemctl stop amazon-cloudwatch-agent
```

En la consola de **CloudWatch**, elimina las alarmas y métricas personalizadas que hayas creado.

## Paso 8: Responde a las preguntas...

... de la sección «Entrega» :wink:

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
