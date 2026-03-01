![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# VPC Flow Logs

(v20241118)

**VPC Flow Logs** es un servicio de AWS que captura información sobre el tráfico de red que fluye dentro y fuera de una **VPC**. Esto incluye detalles del tráfico que atraviesa interfaces de red como instancias EC2, gateways, y otras entidades asociadas a la red de la VPC.

﻿![Captura de pantalla de VPC Flow Logs](https://media.amazonwebservices.com/blog/2015/flow_see_a_stream_2.png)

## ¿Para qué sirve?

El principal objetivo de VPC Flow Logs es permitir monitorizar y analizar el tráfico de red en un entorno AWS. Este servicio es útil para:
- **Resolución de problemas**: identificar problemas de conectividad, como fallos en la comunicación entre instancias;
- **Auditoría de seguridad**: rastrear el tráfico rechazado por reglas de seguridad y detectar posibles amenazas;
- **Optimización de red**: analizar patrones de tráfico para ajustar el diseño de la red o las reglas de seguridad;
- **Cumplimiento normativo**: registrar y almacenar datos de tráfico para cumplir con políticas de auditoría.

## ¿Cómo funciona VPC Flow Logs?

Cuando se activa **VPC Flow Logs** en una VPC, subred o interfaz de red específica, AWS genera registros de tráfico que se almacenan en **CloudWatch Logs** o **Amazon S3**. Estos registros contienen información detallada sobre cada conexión de red, organizada en un formato específico.

### Formato de los registros
Un registro típico de VPC Flow Logs incluye los siguientes campos:

```lua
version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status
```

- **version**: versión del formato del log;
- **account-id**: ID de la cuenta de AWS propietaria de la VPC;
- **interface-id**: ID del interfaz de red monitorizado;
- **srcaddr / dstaddr**: direcciones IP de origen y destino del tráfico;
- **srcport / dstport**: puertos de origen y destino;
- **protocol**: protocolo de red utilizado (por ejemplo, TCP o UDP);
- **packets / bytes**: número de paquetes y bytes transferidos;
- **start / end**: marca de tiempo del inicio y fin de la captura del tráfico;
- **action**: resultado de la regla de seguridad (ACCEPT o REJECT);
- **log-status**: estado de la captura (si ha habido errores o no).

## Niveles de captura

Se puede configurar **VPC Flow Logs** para funcionar en diferentes niveles:
1. **VPC**: captura todo el tráfico que cruza la VPC;
2. **Subred**: captura tráfico en una subred específica;
3. **Interfaz de red**: captura tráfico de una instancia o recurso específico.

El nivel seleccionado afecta a la granularidad de los datos capturados.


## Beneficios principales

1. **Transparencia total en la red**
   - Se puede ver tanto el tráfico aceptado como el rechazado, incluyendo puertos, IPs y protocolos.
2. **Facilidad de configuración**
   - La configuración se realiza a través de la consola de AWS, AWS CLI o herramientas como [Terraform](https://es.wikipedia.org/wiki/Terraform_(software)).
3. **Integración con otros servicios**
   - Los registros pueden enviarse a **CloudWatch Logs** para monitorización en tiempo real, **S3** para almacenamiento a largo plazo, o **Logs Insights** para análisis avanzado.
4. **Eficiencia y escalabilidad**
   - AWS maneja automáticamente la recopilación y almacenamiento de los registros sin inerferir con el rendimiento de la red.


## Ejemplo de uso práctico

### Caso: identificar tráfico rechazado
Supongamos que tenemos problemas de conectividad entre dos instancias EC2 en diferentes subredes dentro de nuestra VPC. Activar VPC Flow Logs en la VPC nos permitirá:
1. Verificar si el tráfico está siendo rechazado por alguna regla de seguridad o tabla de rutas.
2. Identificar la dirección IP de origen, destino y los puertos involucrados.
3. Corregir las reglas de seguridad o rutas según sea necesario.

## Limitaciones y consideraciones

1. **Registros de tráfico interno**: los *logs* solo registran tráfico dentro de AWS; no incluyen datos sobre el tráfico externo a Internet que pasa por gateways NAT o IGW.
2. **Costes**: hay costes asociados al almacenamiento de los registros en CloudWatch o S3. Es importante filtrar adecuadamente para evitar recopilar datos innecesarios.
3. **Latencia**: los registros no se generan en tiempo real; puede haber un pequeño retraso en la disponibilidad de los datos.
4. **Privacidad**: los *logs* no capturan el contenido del tráfico, solo los metadatos.

## Cómo habilitar VPC Flow Logs

### Usando la consola de AWS:
1. Desde la consola de **VPC** seleccionamos la VPC, subred o interfaz de red donde queremos habilitar los *logs.*
2. Hacemos clic en la pestaña **Flow Logs** y seleccionamos **Create Flow Log**.
3. Configuramos:
   - **Filter**: seleccionamos `All`, `Accept` o `Reject` según lo que queramos capturar.
   - **Destination**: elegimos **CloudWatch Logs** (podríamos elegir **Amazon S3** para almacenar los datos).
   - **IAM Role**: seleccionamos o creamos un rol de IAM con permisos para enviar *logs.*
4. Hacemos clic en **Create**.

### Usando AWS CLI:
```bash
aws ec2 create-flow-logs \
    --resource-type VPC \
    --resource-ids vpc-12345678 \
    --traffic-type ALL \
    --log-destination arn:aws:logs:region:account-id:log-group:VPCFlowLogsGroup \
    --log-format '${version} ${account-id} ${interface-id} ${srcaddr} ${dstaddr} ${srcport} ${dstport} ${protocol} ${packets} ${bytes} ${start} ${end} ${action} ${log-status}' \
    --deliver-logs-permission-arn arn:aws:iam::account-id:role/FlowLogsRole
```

## Casos de uso avanzado

- **Análisis de tráfico con Logs Insights**: podemos crear consultas para identificar patrones de tráfico, como las principales IP que generan carga.
- **Alarmas en tráfico anómalo**: configuraríamos filtros de métricas en CloudWatch para recibir alertas en tiempo real.
- **Detección de amenazas**: analizando el tráfico rechazado para identificar intentos de acceso no autorizados.

---

***VPC Flow Logs* es una herramienta esencial para cualquier administrador de sistemas o arquitecto en AWS, proporcionando la visibilidad necesaria para mantener una red segura y optimizada.**


&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
