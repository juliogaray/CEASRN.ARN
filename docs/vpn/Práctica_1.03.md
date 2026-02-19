![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)


# Comunicación desde una red privada a Internet

Hemos visto que, convencionalmente, una subred privada y una pública se diferencian en que la primera no tiene acceso a Internet.

Obviamente puede ser necesario establecer un contacto limitado desde una subred privada a Internet para ciertos fines, como instalar o actualizar software, por ejemplo.

En esta práctica NO GUIADA tenéis que crear una máquina virtual EC2 (un simple servidor con *Amazon Linux,* del tipo más pequeño posible) en vuestra subred privada (la que creasteis en la práctica anterior), y tenéis que conseguir actualizar (```sudo dnf update -y```) la máquina.


## Objetivos
1. Crear y configurar una subred pública y una privada en una VPC (podéis usar las de la práctica anterior).
2. Crear un servidor ***Amazon Linux**** EC2 en la subred privada, con dirección IP fija. Pregunta: ¿se puede usar *Amazon VPC IP Address Manager* para asignarle la dirección IP al servidor?
3. Configurar un método de acceso a Internet para la subred privada (preferiblemente una *gateway NAT* en modo zonal).
4. Actualizar la máquina del punto 2.

\* *Usamos* Amazon Linux *en lugar de Debian para simplificar la práctica, porque esta versión de GNU/Linux ya incorpora el [agente SSM](https://docs.aws.amazon.com/es_es/systems-manager/latest/userguide/ssm-agent.html), lo que nos permite acceder al sistema desde la consola de AWS, sin necesidad de usar un servidor bastión u otras soluciones.*


**Debes entregar** en esta práctica un archivo PDF donde expliques el proceso de creación de todos los elementos requeridos. **Incluye al menos una captura de pantalla del servidor actualizándose correctamente.**

Como de costumbre, indica en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características.

&nbsp;

&nbsp;

&nbsp;

## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)
