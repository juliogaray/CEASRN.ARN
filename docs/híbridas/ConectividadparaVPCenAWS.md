![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)
# C.E. ADMINISTRACIÓN DE RECURSOS Y SERVICIOS EN LA NUBE
# ADMINISTRACIÓN DE REDES EN LA NUBE
----

# Conectividad para VPC en AWS: opciones y seguridad

(v20250209)

Las **VPC** permiten desplegar infraestructuras de red dentro de AWS con control granular sobre el tráfico y la seguridad. Sin embargo, en muchos escenarios es necesario conectar la VPC con otros entornos, como redes locales empresariales, oficinas remotas o dispositivos individuales.

﻿![Modalidades de acceso remoto a VPN](http://julio.iespacomolla.es/CEARSN.ARN/img/AccesoRemoto.svg)

Las principales opciones de conectividad incluyen:

## 1. Conexiones *Site-to-Site:* túneles VPN empresariales

### **AWS *Site-to-Site* VPN** ![Icono de conexión AWS site-to-site](http://julio.iespacomolla.es/CEARSN.ARN/img/SiteToSiteVPN.svg)
Este tipo de conexión permite **enlazar una red local con una VPC** en AWS a través de un [***túnel VPN IPsec***](https://www.cloudflare.com/es-es/learning/network-layer/what-is-ipsec/). Se configura sobre Internet y ofrece cifrado y autenticación para la protección del tráfico en tránsito.

- **Componentes:**
  - Un **Customer Gateway (CGW)** en la red local, que puede ser un cortafuegos o encaminador compatible con IPsec;
  - Una **Virtual Private Gateway (VGW)** en la VPC de AWS;
  - Túneles redundantes con IPsec para alta disponibilidad.

- **Seguridad:**
  - Cifrado con [*AES-256*](https://es.wikipedia.org/wiki/Advanced_Encryption_Standard);
  - Autenticación mediante [*IKEv1/IKEv2*](https://es.wikipedia.org/wiki/Internet_key_exchange) con [*PSK*](https://es.wikipedia.org/wiki/Clave_precompartida) o [*certificados*](https://es.wikipedia.org/wiki/Certificado_digital);
  - Se recomienda usar [*BGP*](https://es.wikipedia.org/wiki/Border_Gateway_Protocol) para la gestión dinámica de rutas (BGP actualiza las rutas automáticamente, permite definir rutas preferidas también de forma automática, y facilita la redirección de tráfico en caso de fallo de una conexión).

- **Casos de uso:**
  - Conectar redes corporativas con AWS;
  - Acceso seguro a servicios en la nube desde oficinas;
  - Solución rápida sin necesidad de hardware dedicado.

## 2. Conexiones *Host-to-Site:* acceso remoto seguro

### **AWS Client VPN** ![Icono de conexión AWS VPN](http://julio.iespacomolla.es/CEARSN.ARN/img/ClientVPNEndpoint.svg)
Para permitir que usuarios individuales accedan de forma segura a una VPC desde cualquier ubicación, AWS ofrece **Client VPN**, que utiliza el protocolo [***OpenVPN***](https://es.wikipedia.org/wiki/OpenVPN).

- **Características:**
  - No requiere una dirección IP fija en el cliente;
  - Compatible con GNU/Linux, Windows y macOS;
  - Se integra con **AWS IAM** para autenticación basada en credenciales o [*MFA*](https://es.wikipedia.org/wiki/Autenticaci%C3%B3n_de_m%C3%BAltiples_factores);
  - Soporta [*túneles SSL/TLS*](https://www.ssldragon.com/es/blog/que-es-vpn-ssl/) cifrados.

- **Seguridad:**
  - Cifrado [*TLS 1.2*](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte);
  - Autenticación con certificados o IAM;
  - Opcionalmente, se puede integrar con Microsoft Active Directory para acceso basado en grupos.

- **Casos de uso:**
  - Acceso seguro para teletrabajadores o técnicos de soporte;
  - Conexión remota de administradores a instancias en AWS;
  - Alternativa a VPN tradicionales cuando no se requiere una conexión permanente.

## 3. Conexiones dedicadas: *AWS Direct Connect* ![Icono de conexión AWS Direct Connect](http://julio.iespacomolla.es/CEARSN.ARN/img/DirectConnect.svg)
Para empresas que requieren **baja latencia, mayor ancho de banda y seguridad adicional**, AWS ofrece **Direct Connect**, que establece un enlace físico entre el centro de datos del cliente y AWS. Esto es lo que tradicionalmente se denominaba [*línea alquilada*](https://techlib.net/techedu/linea-alquilada/).

- **Características:**
  - Conexión a través de un proveedor de telecomunicaciones;
  - Ancho de banda de 1 Gbps a 100 Gbps;
  - Compatible con VLAN y BGP;
  - No depende de Internet, lo que mejora la estabilidad y la seguridad.

- **Seguridad:**
  - Se recomienda utilizar [***MACsec (Media Access Control Security)***](https://www.ccn.cni.es/es/docman/documentos-publicos/boletines-pytec/470-pildorapytec-abr2021-seguridad-macsec/file) para cifrado a nivel de enlace;
  - Si las exigencias de seguridad son muy elevadas, puede combinarse con una **VPN IPsec** para cifrado de extremo a extremo (para protegerse ante un posible ataque de [*eavesdropping*](https://es.wikipedia.org/wiki/Eavesdropping)).

- **Casos de uso:**
  - Empresas con altos volúmenes de datos que necesitan rendimiento estable;
  - Integración con centros de datos locales para arquitecturas híbridas;
  - Aplicaciones sensibles a la latencia *([trading financiero](https://es.wikipedia.org/wiki/Trading))*, [*streaming*](https://es.wikipedia.org/wiki/Streaming) en tiempo real).

Una línea **AWS Direct Connect** puede terminar en diversos puntos dentro de la infraestructura de AWS, dependiendo de la arquitectura de red y los requisitos del usuario:

### Posibles terminaciones de una conexión *Direct Connect*

- **Directamente en una VPC a través de una Virtual Private Gateway (VGW)** ![Icono de VGW](http://julio.iespacomolla.es/CEARSN.ARN/img/VGW.svg)
   - La conexión *Direct Connect* puede terminar en una **Virtual Private Gateway (VGW)** de una VPC específica.  
   - Es la opción más sencilla, pero está limitada a una única VPC dentro de la misma región.  

- **A través de una Direct Connect Gateway (DXGW)** ![Icono de DXGW](http://julio.iespacomolla.es/CEARSN.ARN/img/DXGW.svg)
   - Permite conectar una única línea *Direct Connect* a **múltiples VPC en diferentes regiones** (dentro de la misma cuenta de AWS).  

- **A través de una Transit Gateway (TGW)** ![Icono de TGW](http://julio.iespacomolla.es/CEARSN.ARN/img/TransitGateway.svg)
   - Si se usa **AWS Transit Gateway (TGW)** junto con *Direct Connect,* el tráfico puede distribuirse a múltiples VPC y redes *on-premises.*
   - En este caso, Direct Connect se conecta a **DXGW**, que luego se asocia a una **TGW**.
   - Permite interconectar varias VPC y redes híbridas de manera más escalable.

Recordemos que AWS Transit Gateway es un servicio diseñado para facilitar la **interconexión a gran escala**, y que funciona como un *hub* central, permitiendo que múltiples VPC, cuentas de AWS e incluso conexiones VPN compartan una única pasarela.

####  **Ejemplos de configuraciones comunes**  

- **Simple: Empresa con una sola VPC**  
  - 🌍 *On-premises* → 📡 **Direct Connect** → 🔗 **VGW** → 🏢 **VPC**  

- **Múltiples VPC en diferentes regiones**  
  - 🌍 *On-premises* → 📡 **Direct Connect** → 🔗 **DXGW** → 🏢 **Varias VPC en diferentes regiones**  

- **Arquitectura escalable con Transit Gateway**  
  - 🌍 *On-premises* → 📡 **Direct Connect** → 🔗 **DXGW** → 🔄 **TGW** → 🏢 **Varias VPCs + VPNs híbridas**  


## 4. Seguridad en el tráfico en tránsito
Para garantizar la seguridad del tráfico en todas estas conexiones, AWS proporciona diferentes mecanismos:

- **Cifrado:**
  - VPNs IPsec para Site-to-Site y Client VPN;
  - TLS para conexiones a servicios de AWS;
  - MACsec en Direct Connect.

- **Autenticación y control de acceso:**
  - IAM para Client VPN;
  - PSK o certificados en VPNs Site-to-Site;
  - Integración con +Active Directory* para autenticación granular.

- **Monitorización y registro:**
  - **AWS CloudWatch** para métricas de red;
  - **AWS VPC Flow Logs** para registrar tráfico aceptado/rechazado;
  - **AWS Network Firewall** y **Security Groups** para filtrado de tráfico.

## **Conclusión**
AWS ofrece múltiples opciones de conectividad para VPCs, adaptándose a distintos escenarios empresariales. La elección entre **VPNs, Client VPN o Direct Connect** dependerá de las necesidades de latencia, seguridad y ancho de banda. Implementar buenas prácticas de cifrado y autenticación es clave para asegurar la integridad de los datos en tránsito.

&nbsp;

&nbsp;

&nbsp;

## Colofón
 
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)

