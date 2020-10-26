# JavaScript y el Navegador

{{quote {author: "Tim Berners-Lee", title: "The World Wide Web: A very short personal history", chapter: true}

El sueño detrás de la Web es el de un espacio común de información 
en el cual nos comunicamos compartiendo información. 
Su universalidad es esencial: el echo de que un enlace pueda apuntar a cualquier cosa, 
ya sea personal, local o global, ya sea un borrador o este muy pulido.

quote}}

{{index "Berners-Lee, Tim", "World Wide Web", HTTP, [JavaScript, "history of"], "World Wide Web"}}

{{figure {url: "img/chapter_picture_13.jpg", alt: "Picture of a telephone switchboard", chapter: "framed"}}}

Los próximos capítulos del libro hablarán del navegador web. Sin
((navegador))es web, no existiría JavaScript. Incluso, si existiera,
nadie le habría puesto atención.

{{index decentralization, compatibility}}

La tecnología web ha estado descentralizada desde el principio,
no sólo técnicamente, también en la forma en que ha evolucionado.
Distintos proveedores de nagevadores han agregado nueva funcionalidad
adhoc y algunas veces ha sido de una forma deficiente, y entonces,
termina siendo adoptada por otros-y finalmente es aceptada como
((estándar)).

Esta es a la vez una bendición y una maldición. Por un lado, empodera el no tener
un control central, sino que es mejorado por distintas partes trabajando
en ((colaboración)) (o a veces hostilidad abierta). Por otro lado, la
forma aleatoria en la que la web fue desarrollada significa que el sistema
resultante no es exactamente un ejemplo de ((consistencia)) interna. Algunas
partes son francamente confusas y pobremente concebidas.

## Redes e Internet

Las ((red))es de computadoras han existido de los 50s. Si pones cables
entre dos o más computadoras y les permites enviar y recibir información
a través de esos cables, puedes hacer todo tipo de cosas increíbles.

Y si conectar dos computadoras en el mismo edificio nos permite hacer
cosas increíbles, conectar computadoras alrededor del mundo debería ser
incluso mejor. La tecnología para iniciar la implementación de esta visión
fue desarrollada en los 80s, y la red resultante es llamada _((Internet))_.
Ha cumplido su promesa.

Una computadora puede usar esta red para mandar bits a otra computadora. Para
cualquier ((comunicación)) efectiva, las computadoras en ambos lados deben
saber qué se supone que representan los bits. El significado de cualquier
secuencia de bits depende enteramente del tipo de cosas que se está tratando
de expresar y del mecanismo de ((codificación)) utilizado.

{{index [network, protocol]}}

Un _((protocolo)) de red_ describe un estilo de comunicación sobre una ((red)).
Hay protocolos para mandar correos electrónicos, para recibir correos electrónicos,
para compartir archivos, e incluso para controlar computadoras que resultan estar
infectadas por software malicioso.

{{indexsee "Hypertext Transfer Protocol", HTTP}}

Por ejemplo, el Protocolo de Transferencia de Hipertexto
(((HTPP del inglés _Hypertext Transfer Protocol_))) es un protocolo
para obtener ((recurso))s nombrados (fragmentos de información, como páginas web
o imágenes). Especifica que el lado que hace la solicitud debe iniciar con
una linea como esta, indicando el recurso y la versión del protocolo
que se intenta usar:

```{lang: "text/plain"}
GET /index.html HTTP/1.1
```

Existen otras reglas acerca de cómo el solicitante puede incluir más
información en la ((solicitud)) y la forma en qué el otro lado, que
devuelve el recurso, empaca el contenido. Examinaremos HTTP en más detalle
en el capítulo [Chapter ?](http).

{{index layering, stream, ordering}}

La mayoría de los protocolos están construidos sobre otros protocolos. HTTP
utiliza la red como un dispositivo en el que pones bits y llegan a su destino
correcto en el orden correcto. Como vimos en el capítulo [Chapter ?](async),
asegurar eso ya es un problema muy complicado.

{{index TCP}}

{{indexsee "Transmission Control Protocol", TCP}}

El _Protocolo de Control de Transmisión_ (TCP del inglés Transmission
Control Protocol) es un ((protocolo)) que se encarga de este problema.
Todos los dispositivos conectados a internet lo "hablan", y la mayor
parte de la comunicación en ((internet)) está construida encima de él.

{{index "listening (TCP)"}}

Una ((conexión)) TCP funciona así: un computadora debe estar esperando,
o _escuchando_, a que otras computadoras inicien a hablarle. Para poder
escuchar diferentes tipos comunicaciones a la vez en una sola computadora,
cada oyente tiene un número (llamado ((_puerto_))) asociado a él. La
mayoria de los ((protocolo))s especifican qué puerto deben usar por
defecto. Por ejemplo, cuando queremos enviar un correo electrónico
utilizando el protocolo ((SMTP)), la máquina a través de la cual la
enviamos esté escuchando en el puerto 25.

Otra computadora pue establecer una ((conexion)) conectandose a la
ocmputadora destino usando el puerto correcto. Si la computadora destino
puede ser alcanzada y está escuchando en ee puerto, la conexión se
crea de forma exitosa. La computadora oyente es llamada el _((servidor))_,
y la computadora que se conecta es llamado el _((cliente))_.

{{index [abtraction, "of the network"]}}

Esa conexión actua como un una tubería de dos direcciones por la cual
los bits pueden fluir, las computadoras en ambos extremos pueden poner
información en esa tubería. Una vez que los bits son transmitidos de forma
exitosa, pueden ser leidos nuevamente por la computadora en el otro lado.
Este es un modelo conveniente. Podrías decir que ((TCP)) provee una
abstracción de la red.

{{id web}}
