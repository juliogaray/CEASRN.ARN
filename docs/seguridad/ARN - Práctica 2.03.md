![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](http://julio.iespacomolla.es/Recursos-Comunes/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Conceptos básicos de AWS Network Firewall

Para esta práctica realizaremos el laboratorio *AWS Network Firewall Fundamentals*, a través de la plataforma [*Skill Builder*](https://skillbuilder.aws/) de Amazon. Por defecto el laboratorio aparecerá en inglés, pero es posible configurarlo en español (de Latinoamérica). Ten en cuenta, si optas por usar la traducción al español, que los enlaces y encabezados en el curso aparecerán en español, pero no coincidirán siempre con las traducciones usadas en el enunciado de desarrollo de la práctica (si puedes hacerlo en inglés, problablemente sea la mejor opción).

El laboratorio está pensado para poder hacerlo en una hora y 15 minutos. Esta estimación puede resultar un poco optimista.

## Objetivos
1. Desarrollar un recorrido práctico de la configuración de un entorno de AWS que utiliza AWS Network Firewall correctamente. 
2. Desarrollar las 4 tareas, subdivididas en pasos, para aprender los detalles de lo que se necesita para tener la infraestructura de red adecuada a fin de encaminar y bloquear el tráfico con AWS Network Firewall.
3. Partiendo de única VPC con dos subredes, añadir las subredes adicionales necesarias, el cortafuegos, y un enlace NAT filtrado.
4. **Desarrollar el punto final** que, etiquertado como «Evaluación de conocimientos», sugiere **modificar las reglas para permitir el tráfico y, luego, comprobar que puede conectarse**.

Se utilizan dos instancias de EC2 en este laboratorio para ayudar a probar la conectividad de red.

### Entrega
Deberás elaborar y entregar un único archivo PDF con todo lo especificado a continuación, incluidos los 3 esquemas de red:[^1]
- Desarrollar las 4 tareas, explicando en dos o tres párrafos, lo que haces en cada una de ellas (incluye, como siempre los ID de los elementos creados, como subredes, tablas de encaminamiento, etcétera);
- **Al final de las tareas 1, 2 y 3**, incluye en tu trabajo un **esquema de red** que refleje la situación del laboratorio en ese momento (para las tareas 2, 3 deberán reflejarse los cambios operados en la red durante la ejecución de la tarea);
- Desarrolla el punto final de «Evaluación de conocimientos» (modificar las reglas para permitir el tráfico y, luego, comprobar que puede conectarse). En la entrega, simplemente describe en un par de párrafos lo que haces para implementarlo.

[^1]: Para elaborar los esquemas de red de AWS, Amazon sugiere varias herramientas en la página donde ofrece sus iconos para este fin ([aws.amazon.com/es/architecture/icons/](https://aws.amazon.com/es/architecture/icons/)). Echa una ojeada en dicha página y elige la herramienta que prefieras para tus esquemas.

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2025 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
