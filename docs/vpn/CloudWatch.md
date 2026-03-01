![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Monitorización con *CloudWatch*

(v20241117)

***Amazon CloudWatch*** es un servicio de monitorización integral diseñado para proporcionar observabilidad y control sobre los recursos de AWS y aplicaciones tanto en la nube como [*on-premises*](https://blog.softexpert.com/es/on-premise-vs-cloud/). Vamos a ver sus características avanzadas y cómo utilizarlas para optimizar nuestros sistemas.

﻿![Vista de un panel general del AWS CloudWatch](https://docs.aws.amazon.com/images/AmazonCloudWatch/latest/monitoring/images/CW-default-dashboard-update.png)

---

## 1. Conceptos básicos
- **Métricas**: representan datos sobre el rendimiento de los recursos. P. ej.: uso de CPU en una instancia EC2;
- ***Logs**:* registros de eventos de aplicaciones y sistemas;
- **Alarmas**: permiten reaccionar automáticamente a eventos como un aumento en la latencia o un uso elevado de memoria;
- ***Dashboards**:* visualización centralizada de métricas y *logs;*
- **Eventos *(EventBridge)***: permite responder a cambios en los recursos de manera automatizada.


## 2. Capacidades avanzadas

### 2.1 Métricas personalizadas
Además de las métricas predeterminadas, podemos definir métricas personalizadas para rastrear datos específicos de nuestras aplicaciones o sistemas.  
**Ejemplo**: «número de usuarios activos o tiempo de respuesta en milisegundos».  
**Implementación**:
1. Usa el [SDK](https://es.wikipedia.org/wiki/Kit_de_desarrollo_de_software) de AWS para enviar métricas personalizadas con `put-metric-data`.
2. Configura alarmas basadas en estas métricas.

### 2.2 *Logs Insights*
**CloudWatch Logs Insights** es una potente herramienta para analizar grandes volúmenes de *logs* en tiempo real usando un lenguaje de consulta similar a SQL.

﻿![Vsta de una consulta de Logs Insights](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2018/11/28/ci_dlg_qo_ec2_1-759x630.png)

**Casos de uso**:
- Identificar errores específicos;
- Analizar patrones de acceso o tendencias de uso;  
**Ejemplo de consulta**:
```plaintext
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

> **¿Qué crees que hace esta consulta?**

### 2.3 Alarmas compuestas
Las alarmas compuestas permiten combinar varias alarmas estándar utilizando operadores lógicos como `AND`, `OR` o `NOT`.  
**Ventajas**:
- Reducir la cantidad de alertas falsas;
- Crear condiciones más complejas para el monitorización;  
**Ejemplo**: «configurar una alarma que se active sólo si tanto el uso de CPU como la latencia exceden ciertos límites».

### 2.4 Integración con **AWS Lambda**
Puedes automatizar respuestas a eventos críticos mediante AWS Lambda.  
**Ejemplo**: «reiniciar automáticamente una instancia EC2 si se detecta un fallo recurrente».


## 3. Observabilidad integral con *CloudWatch ServiceLens*
**CloudWatch ServiceLens** proporciona una vista unificada de métricas, logs y trazas distribuidas, especialmente útil en [aplicaciones de microservicios](https://es.wikipedia.org/wiki/Arquitectura_de_microservicios).

﻿![Ejemplo de panel de AWS CloudWatch ServiceLens](https://docs.aws.amazon.com/images/AmazonCloudWatch/latest/monitoring/images/ServiceMap.png)

**Características**:
- **monitorización de latencia**: detecta cuellos de botella;
- **Mapas de dependencias**: muestra las interacciones entre servicios;
- **Trazas distribuidas**: identifica cuellos de botella usando el servicio integrado **AWS X-Ray** (un servicio de monitorización y depuración de AWS que permite analizar y depurar aplicaciones distribuidas, como las que están basadas en arquitecturas de microservicios).


## 4. Optimización continua
- ***Dashboards* personalizados**: diseña vistas específicas para equipos o aplicaciones;
- **Análisis predictivo**: configura alarmas con predicción basada en patrones históricos (p. ej., detección de anomalías);
- ***CloudWatch Evidently**:* realiza [experimentos A/B](https://en.wikipedia.org/wiki/A/B_testing) para probar nuevas configuraciones o características.


## 5. Ejemplo práctico: configuración avanzada
**Caso**: monitorización de un clúster de aplicaciones críticas.
1. Configura alarmas compuestas:
   - Condición: «CPU > 80 % **y** errores 5xx > 5 en 5 minutos».
2. Usa *Logs Insights* para investigar rápidamente cualquier alerta.
3. Automatiza la respuesta con [Lambda](https://es.wikipedia.org/wiki/AWS_Lambda):
   - Envía notificaciones mediante [SNS](https://en.wikipedia.org/wiki/Amazon_Simple_Notification_Service).
   - Escala el clúster automáticamente usando *Auto Scaling.*


## 6. Consideraciones finales
- **Costes**: CloudWatch cobra por el uso de métricas, *logs* y *dashboards.* Optimiza las métricas personalizadas y efectúa retención de *logs* para minimizar costos;
- **Seguridad**: usa [IAM](https://es.wikipedia.org/wiki/Administraci%C3%B3n_de_identidades) para restringir el acceso a métricas y *logs* sensibles;
- **Prácticas recomendadas**:
  - Configurar alarmas para métricas clave desde el inicio;
  - Centralizar los *logs* en una cuenta para facilitar la gestión.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
