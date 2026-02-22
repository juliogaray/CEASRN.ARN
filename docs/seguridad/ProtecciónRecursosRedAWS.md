![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----
# Protección de recursos en la red de AWS: Un enfoque integral

(v20241130)

La protección de recursos en AWS es un componente fundamental de cualquier estrategia de seguridad en la nube. AWS proporciona una amplia gama de herramientas diseñadas para proteger las aplicaciones, datos y redes de los usuarios frente a amenazas internas y externas. Entre estas herramientas destacan **AWS Network Firewall**, **Security Groups** y **Listas de Control de Acceso a la Red (NACL, por sus siglas en inglés)**, cada una desempeñando un rol específico dentro de un enfoque de seguridad en capas.

---

## 1. AWS Network Firewall: Protección avanzada a nivel de red ﻿![Icono de AWS Network Firewall](../img/AWSNetworkFirewall.svg)

*AWS Network Firewall* es un servicio completamente administrado que permite implementar políticas de seguridad avanzadas para proteger el tráfico de red. Diseñado para usuarios que requieren control granular sobre su tráfico y protección frente a amenazas modernas, AWS Network Firewall combina reglas de filtrado personalizadas con funciones avanzadas de inspección.

 ﻿![Esquema de red que incorpora AWS Network Firewall](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2021/05/28/Screen-Shot-2021-04-02-at-2.30.28-PM.png)

### Características clave:
- **Filtrado avanzado de tráfico:**
  - Permite configurar reglas basadas en IP, puertos, protocolos y patrones específicos.
  - Ofrece capacidades de [inspección profunda de paquetes (DPI)](https://es.wikipedia.org/wiki/Inspecci%C3%B3n_profunda_de_paquete) para identificar tráfico sospechoso o malicioso.

- **Protección contra amenazas avanzadas:**
  - Integración con firmas conocidas de amenazas, como malware o intentos de intrusión.
  - Compatibilidad con reglas basadas en [**Suricata**](https://en.wikipedia.org/wiki/Suricata_(software)), un [motor de detección de intrusos](https://es.wikipedia.org/wiki/Sistema_de_detecci%C3%B3n_de_intrusos) ampliamente utilizado.

- **Escalabilidad y alta disponibilidad:**
  - AWS Network Firewall se escala automáticamente para manejar grandes volúmenes de tráfico.
  - Se implementa en zonas de disponibilidad para garantizar redundancia.

- **Monitorización y visibilidad:**
  - Integración nativa con [**Amazon CloudWatch**](http://julio.iespacomolla.es/CEARSN.ARN/CloudWatch.md), **AWS CloudTrail** y [**Amazon VPC Flow Logs**]((http://julio.iespacomolla.es/CEARSN.ARN/FlowLogs.md)) para monitorización, auditoría y análisis de tráfico.

- **Conexión a través de *Firewall Endpoint:***
  - Dentro de una Amazon VPC podemos definir un ***Firewall Endpoint***, un punto a de acceso que permite a los usuarios conectar su red al servicio *AWS Network Firewall.* Este endpoint actúa como un punto de control para dirigir el tráfico de red hacia las reglas configuradas en *Network Firewall,* proporcionando un mecanismo de inspección y protección avanzada del tráfico dentro de la VPC.

### Casos de uso:
1. **Protección de aplicaciones web:** implementación de reglas específicas para bloquear tráfico no autorizado hacia servidores *backend.*
2. [**Prevención de intrusiones**](https://es.wikipedia.org/wiki/Sistema_de_prevenci%C3%B3n_de_intrusos)**:** identificación y bloqueo de patrones de tráfico que coincidan con amenazas conocidas.
3. **Cumplimiento normativo:** aplicación de políticas que garanticen el cumplimiento de normativas como [GDPR](https://es.wikipedia.org/wiki/Reglamento_General_de_Protecci%C3%B3n_de_Datos) o [PCI DSS](https://es.wikipedia.org/wiki/PCI_DSS).

---

## 2. Security Groups: Control de tráfico a nivel de instancia

Los **Security Groups** son un componente fundamental para controlar el tráfico de red hacia y desde las instancias EC2 y otros servicios compatibles. Actúan como un cortafuegos virtual que define reglas de entrada y salida basadas en características específicas del tráfico.

 ﻿![Esquema de red en AWS con Security Groups y NACL](../img/SecurityGroupsYNACL.svg)

### Características principales:
- **Estado *(stateful):***
  - Si se permite un tipo de tráfico entrante, el tráfico de respuesta saliente correspondiente se permite automáticamente, y viceversa.
  
- **Configuración de reglas:**
  - Las reglas se pueden definir por:
    - Dirección (entrante o saliente).
    - Puertos.
    - Protocolo.
    - Direcciones IP o grupos de seguridad.

- **Facilidad de uso:**
  - Los *Security Groups* son fáciles de configurar y administrar a través de la consola de AWS o mediante herramientas como AWS CLI y SDK.

### Casos de uso:
1. **Definición de zonas de confianza:** permitir el tráfico únicamente entre grupos de seguridad específicos, como instancias de una aplicación y su base de datos.
2. **Control granular:** restringir acceso a ciertas direcciones IP, como permitir solo administradores desde una red corporativa.

### Limitaciones:
Aunque efectivos, los *Security Groups* no inspeccionan el contenido del tráfico ni ofrecen capacidades avanzadas como detección de amenazas.

---

## 3. Listas de Control de Acceso a la Red (NACL): filtrado adicional a nivel de subred

Las **NACL** proporcionan un nivel adicional de control al filtrar el tráfico entrante y saliente en una subred de Amazon VPC. A diferencia de los *Security Groups,* las NACL funcionan sin estado (*stateless*), lo que significa que cada regla debe especificarse explícitamente para ambos sentidos del tráfico.

### Características principales:
- **Control detallado:**
  - Define reglas numeradas que se evalúan en orden secuencial.
  - Permite o deniega tráfico basado en IP, puertos y protocolos.

- ***Estateless:***
  - Las reglas de entrada y salida deben configurarse por separado.

- **Cobertura:**
  - Se aplican automáticamente a todas las instancias dentro de la subred asociada.

### Casos de uso:
1. **Bloqueo global:** denegar acceso desde ciertas IP maliciosas para todas las instancias en una subred.
2. **Seguridad adicional:** agregar una capa de protección a instancias que ya usan *Security Groups.*

### Limitaciones:
- La configuración de NACL puede volverse compleja en entornos grandes debido a la necesidad de gestionar reglas en ambos sentidos del tráfico.
- No ofrece capacidades de inspección avanzada ni integración con servicios de monitoreo.

---

## Comparativa de enfoques

| **Herramienta**         | **Nivel de aplicación** | **Estado**  | **Casos de uso principal**                          | **Capacidades avanzadas**             |
|--------------------------|-------------------------|-------------|----------------------------------------------------|---------------------------------------|
| AWS Network Firewall     | Red                    | *Stateful*     | Protección avanzada contra amenazas               | DPI, detección de intrusiones         |
| Security Groups          | Instancia/Servicio     | *Stateful*     | Control granular del tráfico de red               | No                                    |
| NACL                     | Subred                 | *Stateless*    | Reglas básicas de denegación/permiso en subredes  | No                                    |

---

## [Mejores prácticas](https://es.wikipedia.org/wiki/Buenas_pr%C3%A1cticas) para proteger recursos en AWS

1. **Defensa en profundidad:**
   - Combina NACL, *Security Groups* y *AWS Network Firewall* para múltiples capas de seguridad.
   - Usa *Network Firewall* para protección avanzada y reglas específicas para amenazas conocidas.

2. **Principio de privilegios mínimos:**
   - Configura reglas restrictivas por defecto, permitiendo solo el tráfico necesario.
   - Mantén las reglas de *Security Groups* y NACL lo más específicas posible.

3. **Monitoreo continuo:**
   - Activa *VPC Flow Logs* para analizar patrones de tráfico.
   - Usa *Amazon CloudWatch* para detectar anomalías y eventos sospechosos.

4. **Automatización y escalabilidad:**
   - Usa *AWS Firewall Manager* (ver más abajo) para gestionar reglas de seguridad a escala en múltiples cuentas o regiones.
   - Implementa herramientas como AWS Config para auditar continuamente el cumplimiento de políticas.

5. **Capacitación y revisiones periódicas:**
   - Actualiza regularmente las reglas de seguridad para adaptarte a las nuevas amenazas.
   - Realiza pruebas de penetración para verificar la efectividad de tus configuraciones.

---

## Otras herramientas útiles para el filtrado de tráfico

### AWS Firewall Manager ﻿![Icono de AWS Network Firewall](../img/FirewallManager.svg)

**AWS Firewall Manager** facilita la **administración centralizada** de reglas de seguridad en múltiples cuentas y regiones dentro de una organización de AWS.
#### Características principales:
- **Gestión centralizada:** implementación y auditoría de reglas para AWS WAF, AWS Shield Advanced y AWS Network Firewall.
- **Cumplimiento normativo:** configuración de políticas que se aplican automáticamente a recursos nuevos o existentes.
- **Alertas:** monitorización para identificar recursos no protegidos o configuraciones no conformes.

#### **Casos de uso:**
  - Empresas con arquitecturas multi-región y multi-cuenta.
  - Simplificación de la gestión de reglas en entornos grandes.

### AWS Shield ﻿![Icono de AWS Network Firewall](../img/AWSShield.svg)

**AWS Shield** es un servicio administrado de protección contra ataques DDoS (Distributed Denial of Service). Proporciona dos niveles de protección: **Shield Standard** (gratuito) y **Shield Advanced** (servicio de pago).

#### Características principales:
- **Shield Standard:** protección automática contra ataques DDoS comunes en todos los servicios de AWS, como Amazon EC2, *Elastic Load Balancing* y *Amazon Route 53* (servicio que veremos más adelante en este curso).
- **Shield Advanced:** protección mejorada con mitigación específica de ataques sofisticados; monitorización continua por el equipo de *AWS DDoS Response Team (DRT);* cobertura de costes relacionados con picos de tráfico DDoS.

#### Casos de uso:
- Empresas que necesitan protección garantizada contra grandes ataques DDoS.
- Servicios críticos que requieren alta disponibilidad durante eventos maliciosos.

### AWS WAF (Web Application Firewall) ﻿![Icono de AWS Network Firewall](../img/AWSWAF.svg)

Anque no forma parte del contenido de este módulo, debes saber que otro servicio de seguridad a tu disposición es **AWS WAF**, un cortafuegos de aplicaciones web que protege las aplicaciones web contra amenazas comunes, como [ataques de inyección de SQL](https://es.wikipedia.org/wiki/Inyecci%C3%B3n_SQL) y [scripts entre sitios (XSS)](https://es.wikipedia.org/wiki/Cross-site_scripting). Mientras que *AWS Network Firewall* se enfoca en la seguridad de red, AWS WAF está diseñado específicamente para proteger aplicaciones web.

#### Características principales:
- **Protección basada en reglas:** filtra tráfico HTTP(S) mediante reglas que permiten o bloquean solicitudes basadas en patrones predefinidos o personalizados.
- **Listas de control:** soporte para listas de IP permitidas y bloqueadas, detección de bots y control de acceso por geolocalización.
- **Integración:** compatible con servicios como *Amazon CloudFront, Application Load Balancer (ALB)* y *API Gateway.*
  
#### **Casos de uso:**
- Protección contra ataques DDoS a nivel de aplicación.
- Aplicación de políticas personalizadas para tráfico web.
- Cumplimiento normativo al filtrar contenido malicioso.

---

## **Conclusión**

La protección de recursos en AWS requiere una estrategia que combine herramientas básicas como *Security Groups* y NACL con soluciones avanzadas como *AWS Network Firewall.* Al implementar estas herramientas en conjunto, las organizaciones pueden construir un entorno seguro, resiliente y capaz de responder a las amenazas modernas. La clave está en aplicar políticas adecuadas, monitorizar continuamente y adaptarse a un panorama de amenazas en constante evolución.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
