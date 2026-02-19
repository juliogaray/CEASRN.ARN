![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)


# Creación de una arquitectura Multi-VPC con *AWS Transit Gateway*


## Objetivos
Configurar AWS Transit Gateway para conectar tres VPC, simulando un entorno de red empresarial con varios departamentos (por ejemplo, Contabilidad, Recursos Humanos y Desarrollo), cada uno en una VPC diferente. Configuraremos reglas de enrutamiento y acceso para que los recursos de cada VPC puedan comunicarse entre sí de manera controlada.

Esta práctica te ayudará a comprender cómo facilita *AWS Transit Gateway* la interconexión de redes en la nube a gran escala, y cómo proporciona una infraestructura de red simplificada y escalable en AWS.

>**NOTA:** esta práctica es, en principio, de desarrollo individual. Pero si algunos/as de vosotros/as deseáis hacerla en grupo, hacédselo saber a vuestro/a profesor(a).

### Entrega
Deberás:
- Documentar el proceso seguido, **incluyendo capturas de pantalla.**
- Verificar y explicar los resultados de tus pruebas de conectividad.
- Responder a estas preguntas:
  - ¿Cómo se comportan las rutas en Transit Gateway si se agregan nuevas VPC al diseño?
  - ¿Qué reglas de seguridad deben modificarse para garantizar la comunicación entre VPCs en Transit Gateway?
  - ¿Cuál es la ventaja de utilizar Transit Gateway frente a conexiones directas de VPC Peering en una arquitectura como esta?

Elabora y entrega un único archivo PDF con todo lo especificado, incluidas las preguntas y sus respuestas correspondientes.  
No olvides indicar en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera). Indica también los datos necesarios de tu compañero/a de práctica, incluyendo su ID de cuenta y el identificador de su PVC.

---
## Paso 1: Configuración inicial de las VPC

Ante todo, puedes usar la configuración del laboratorio que tenías en las prácticas anteriores. Elimina la conexión *VPC Peering* que tenías con tu compañero/a pero deja tu(s) VPC intactas, así como las subredes y las instancias EC2. A continuación crea el resto de los recursos que se indican en este punto.

1. **Crea tres VPC** en diferentes bloques CIDR (por ejemplo, 10.41.0.0/16, 10.71.0.0/16, y 10.35.0.0/16) para los departamentos de Contabilidad, Recursos Humanos y Desarrollo. **Sustituye los números 41, 71 y 35 por (1) tu número de PC, (2) tu número de PC + 100, (3) tu número de PC más 200.**
2. En cada VPC, **crea dos subredes** (una pública y una privada) y asocia las subredes públicas a una **tabla de encaminamiento pública** con una *Internet Gateway*.
3. En cada VPC, **crea una instancia EC2** en la subred pública (configurando reglas de seguridad para SSH y tráfico ICMP/ping). Usaremos estas instancias para verificar la conectividad entre VPC diferentes más adelante.

## Paso 2: Creación y configuración de AWS Transit Gateway
1. En el servicio de **AWS Transit Gateway**, crea un nuevo **Transit Gateway**. Recuerda anotar el ID del recurso.
2. Conecta cada VPC al Transit Gateway:
   - En el menú de VPC Peering o en el de «Attachments» de la Transit Gateway, selecciona «Crear un Attachment de Transit Gateway».
   - Configura cada VPC con su propio *Attachment* a la Transit Gateway y activa el encaminamiento.
3. Configura las **tablas de encaminamiento de Transit Gateway** para definir el tráfico entre las VPC:
   - Crea reglas que permitan el tráfico desde y hacia cada VPC, agregando las rutas a los bloques CIDR correspondientes.
   - Asigna las VPC a la tabla de encaminamiento de Transit Gateway para que puedan comunicarse entre sí.

## Paso 3: Configuración de tablas de encaminamiento en cada VPC
1. Modifica las tablas de encaminamiento de cada VPC para incluir las rutas hacia las otras VPC a través de la Transit Gateway.
2. Verifica que las **tablas de enrutamiento de las subredes públicas** de cada VPC tienen rutas configuradas hacia las demás VPC usando el Attachment de Transit Gateway.

## Paso 4: Prueba de conectividad
1. **Conecta las instancias EC2** desde cada VPC utilizando SSH para confirmar que pueden comunicarse a través de la Transit Gateway.
2. Realiza pruebas de ping para verificar que cada instancia en la VPC Contabilidad, por ejemplo, puede conectarse a las instancias en las VPC de Recursos Humanos y Desarrollo.

## Paso 5: Limpieza (optativo)

Para evitar costos innecesarios, cada alumno debe:
- Eliminar la AWS Transit Gateway.
- ~~Eliminar las **instancias EC2** y las **VPCs** creadas para esta práctica.~~ No vamos a hacer esto, porque estos recursos nos servirán para prácticas futuras.

## Paso 6: Responde a las preguntas...

... de la sección «Entrega» :wink:

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
