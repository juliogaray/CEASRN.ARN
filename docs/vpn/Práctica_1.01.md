![Generalitat Valenciana - CEUO / IES Poeta Paco Mollá (Alicante)](https://raw.githubusercontent.com/juliogaray/recursos/main/img/Cabecera_CEUO_IESPPM_NOFSE_Transparente.svg)

# Creación de una VPC en AWS con una subred pública y una privada


En esta práctica vamos a crear una primera VPC, y dentro de ella definiremos dos subredes (aparte de las que va a traer la VPC por defecto): una pública y una privada. El esquema que buscamos es el siguiente (ignorando las subredes iniciales de la VPC):

![Esquema práctica](../img/ARN-Práctica1.01.svg)


Debes entregar en esta práctica un archivo PDF donde expliques el proceso de creación de todos los elementos requeridos (VPC, subredes). **No es necesario que el documento contenga ninguna captura de pantalla.**

Indica en tu documento todos los datos de cada elemento: nombre (dado por ti), ID de AWS del elemento, y características (rango de direcciones IP, conexiones, etcétera).

----

## 1. Creación de una VPC

Dentro de AWS Academy, en el curso Learner Lab que tu profesor(a) ha creado para estas prácticas, inicia el laboratorio.

1. Configura una VPC con los siguientes datos:
    - Elige la segunda opción: «VPC y más»;
    - Asigna el nombre con el esquema vpc-*tus_iniciales* (por ejemplo, en mi caso, vpc-jgg);
    - Asigna un bloque de CIDR IPv4 con el formato 10.ЖЖ.0.0/16, donde ЖЖ es tu número de PC en clase;
    - Deja la opción «Sin bloque de CIDR IPv6» seleccionada;
    - Zonas de disponibilidad: 2;
    - Cantidad de subredes públicas: 2;
    - Cantidad de subredes privadas: 2;
    - Gateway NAT: «Ninguna» por ahora;
2. Haz clic en «Crear VPC».

Comprueba que se han creado las 4 subredes indicadas (2 públicas y 2 privadas). Examina sus conexiones. ¿A qué está conectada cada subred? ¿Qué son esos elementos? ¿En qué se diferencian?

Recuerda anotar en el PDF de respuesta de la práctica cada subred, sus nombres, ID, rango IPv4, Zona de disponibilidad, etcétera (IPv6 no es necesario).

3. Vamos a crear nuestras dos subredes adicionales, una pública y una privada.
    - Observa cuidadosamente los rangos IP asignados por AWS a las subredes iniciales. Verás que las subredes públicas están en el rango bajo del bloque de la VPC, y las privadas se sitúan a partir de la mitad del rango. 
    - Observa también el tamaño, que será seguramente /20.
    - Crea la primera subred como «Subred 1 (pública)», a continuación de las subredes públicas ya existentes en la VPC. Usa una zona de disponibilidad diferente de las de las otras dos redes.
    - Crea la segunda subred como «Subred 2 (privada)», a continuación de las subredes privads ya existentes en la VPC. Usa la misma zona de disponibilidad que la Subred 1.

4. Modifica la subred 1 para asignarle la misma *tabla de enrutamiento* que tienen las otras dos redes públicas.
5. Modifica la subred 2 para asignarle la *tabla de enrutamiento* de una de las otras dos subredes privadas.
6. Por último, **elimina** las subredes creadas por defecto al principio, de manera que queden únicamente las dos subredes que has creado tú.

7. Comprueba las *tablas de enrutamiento* asociadas a cada subred y las ACL.

Eso es todo. Recuerda finalizar el laboratorio con el botón «End Lab» en la página de AWSAcademy. ¿Qué gasto has generado con todas estas tareas?

Completa el documento con toda la información y la respuesta a las preguntas que has encontrado en este enunciado y entrégalo.

&nbsp;

&nbsp;

&nbsp;


## Colofón
[![CC-BY-NC-SA](https://upload.wikimedia.org/wikipedia/commons/5/55/Cc_by-nc-sa_euro_icon.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es) 2024 [J. Garay](mailto:juliogaray.informatica@iespacomolla.es), [IES Poeta Paco Mollà](https://iespacomolla.es/), Alicante (España)

