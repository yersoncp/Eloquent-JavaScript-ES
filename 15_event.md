# Manejo de Eventos

{{quote {author: "Marco Aurelio", title: Meditaciones, chapter: true}

Tienes poder sobre tu mente, no sobre los acontecimientos. Date cuenta de esto,
y encontrarás la fuerza.

quote}}

{{index stoicism, "Marcus Aurelius", input, timeline}}

{{figure {url: "img/chapter_picture_15.jpg", alt: "Imagínese una máquina de Rube Goldberg", chapter: "framed"}}}

Algunos programas funcionan con la entrada directa del usuario, como las
acciones del mouse y el teclado. Ese tipo de entrada no está disponible como una
estructura de datos bien organizada, viene pieza por pieza, en tiempo real, y se
espera que el programa responda a ella a medida que sucede.

## Manejador de eventos

{{index polling, button, "real-time"}}

Imagina una interfaz en donde la única forma de saber si una tecla del
((teclado)) está siendo presionada es leer el estado actual de esa tecla. Para
poder reaccionar a las pulsaciones de teclas, tendrías que leer constantemente
el estado de la tecla para poder detectarla antes de que se vuelva a soltar.
Esto sería peligroso al realizar otros cálculos que requieran mucho tiempo, ya
que se podría perder una pulsación de tecla.

Algunas máquinas antiguas manejan las entradas de esa forma. Un paso adelante
de esto sería que el hardware o el sistema operativo detectaran la pulsación de
la tecla y lo pusieran en una cola. Luego, un programa puede verificar
periódicamente la cola por nuevos eventos y reaccionar a lo que encuentre allí.

{{index responsiveness, "user experience"}}

Por supuesto, este tiene que recordar de mirar la cola, y hacerlo con
frecuencia, porque en cualquier momento entre que se presione la tecla y que el
programa se de cuenta del evento causará que que el programa no responda. Este
enfoque es llamado _((sondeo))_. La mayororía de los programadores prefieren
evitarlo.

{{index "callback function", "event handling"}}

Un mejor mecanismo es que el sistema notifique activamente a nuestro código
cuando un evento ocurre. Los navegadores hacen esto permitiéndonos registrar
funciones como _manejadores_ (_((manejadores))_) para eventos específicos.

```{lang: "text/html"}
<p>Da clic en este documento para activar el manejador.</p>
<script>
  window.addEventListener("click", () => {
    console.log("¿Tocaste?");
  });
</script>
```

{{index "click event", "addEventListener method", "window object", [browser, window]}}

La _vinculación_ `window` se refiere a un objeto integrado proporcionado por el
navegador. Este representa la ventana del navegador que contiene el documento.
Llamando a su método `addEventListener` se registra el segundo argumento que se
llamará siempre que ocurra el evento descrito por su primer argumento.

## Eventos y nodos DOM

{{index "addEventListener method", "event handling", "window object", browser, [DOM, events]}}

Cada manejador de eventos del navegador es registrado dentro de un contexto. En
el ejemplo anterior llamamos a `addEventListener` en el objeto `window` para
registrar un manejador para toda la ventana. Este método puede también ser
encontrado en elementos DOM y en algunos otros tipos de objetos. Los
controladores de eventos son llamados únicamente cuando el evento ocurra en el
contexto del objeto en que están registrados.

```{lang: "text/html"}
<button>Presióname</button>
<p>No hay manejadores aquí.</p>
<script>
  let boton = document.querySelector("button");
  boton.addEventListener("click", () => {
    console.log("Botón presionado.");
  });
</script>
```

{{index "click event", "button (HTML tag)"}}

Este ejemplo adjunta un manejador al nodo del botón. Los clics sobre el botón
hacen que se ejecute ese manejador, pero los clics sobre el resto del documento
no.

{{index "onclick attribute", encapsulation}}

Dar a un nodo un atributo `onclick` tiene un efecto similar. Esto funciona para
la mayoría de tipos de eventos—se puede adjuntar un manejador a través del
atributo cuyo nombre es el nombre del evento con `on` en frente de este.

Pero un nodo puede tener únicamente un atributo `onclick`, por lo que se puede
registrar únicamente un manejador por nodo de esa manera. El método
`addEventListener` permite agregar cualquier número de manejadores siendo seguro
agregar manejadores incluso si ya hay otro manejador en el elemento.

{{index "removeEventListener method"}}

El método `removeEventListener`, llamado con argumentos similares a
`addEventListener`, remueve un manejador:

```{lang: "text/html"}
<button>Botón de única acción</button>
<script>
  let boton = document.querySelector("button");
  function unaVez() {
    console.log("Hecho.");
    boton.removeEventListener("click", unaVez);
  }
  boton.addEventListener("click", unaVez);
</script>
```

{{index [function, "as value"]}}

La función dada a `removeEventListener` tiene que ser el mismo valor de función
que se le dio a `addEventListener`. Entonces, para desregistrar un manejador, se
le tiene que dar un nombre a la función (`unaVez`, en el ejemplo) para poder
pasar el mismo valor de función a ambos métodos.

## Objetos de evento

{{index "button property", "event handling"}}

Aunque lo hemos ignorado hasta ahora, las funciones del manejador de eventos
reciben un argumento: el _((objeto de evento))_. Este objeto contiene
información adicional acerca del evento. Por ejemplo, si queremos saber _cuál_
((botón del mouse)) fue presionado, se puede ver la propiedad `button` del
objeto de evento.

```{lang: "text/html"}
<button>Haz clic en mí de la forma que quieras</button>
<script>
  let boton = document.querySelector("button");
  boton.addEventListener("mousedown", evento => {
    if (evento.button == 0) {
      console.log("Botón izquierdo");
    } else if (evento.button == 1) {
      console.log("Botón central");
    } else if (evento.button == 2) {
      console.log("Botón derecho");
    }
  });
</script>
```

{{index "event type", "type property"}}

La información almacenada en un objeto de evento es diferente por cada tipo de
evento. Se discutirán los distintos tipos de eventos más adelante en el
capítulo. La propiedad `type` del objeto siempre contiene una cadena que
identifica al evento (como `"click"` o `"mousedown"`)

## Propagación

{{index "event propagation", "parent node"}}

{{indexsee bubbling, "event propagation"}}

{{indexsee propagation, "event propagation"}}

Para la mayoría de tipos de eventos, los manejadores registrados en nodos con
hijos también recibirán los eventos que sucedan en los hijos. Si se hace clic a
un botón dentro de un párrafo, los manejadores de eventos del párrafo también
verán el evento clic.

{{index "event handling"}}

Pero si tanto el párrafo como el botón tienen un manejador, el manejador más
específico—el del botón—es el primero en lanzarse. Se dice que el evento se
_propaga_ hacia afuera, desde el nodo donde este sucedió hasta el nodo padre del
nodo y hasta la raíz del documento. Finalmente, después de que todos los
manejadores registrados en un nodo específico hayan tenido su turno, los
manejadores registrados en general ((ventana)) tienen la oportunidad de
responder al evento.

{{index "stopPropagation method", "click event"}}

En cualquier momento, un manejador de eventos puede llamar al método
`stopPropagation` en el objeto de evento para evitar que los manejadores que se
encuentran más arriba reciban el evento. Esto puede ser útil cuando, por
ejemplo, si tienes un botón dentro de otro elemento en el que se puede hacer clic
y que no se quiere que los clics sobre el botón activen el comportamiento de
clic del elemento exterior.

{{index "mousedown event", "pointer event"}}

El siguiente ejemplo registra manejadores `"mousedown"` tanto en un botón como
el párrafo que lo rodea. Cuando se hace clic con el botón derecho del mouse, el
manejador del botón llama a `stopPropagation`, lo que evitará que se ejecute el
manejador del párrafo. Cuando se hace clic en el botón con otro ((botón del
mouse)), ambos manejadores se ejecutarán.

```{lang: "text/html"}
<p>Un párrafo con un <button>botón</button>.</p>
<script>
  let parrafo = document.querySelector("p");
  let boton = document.querySelector("button");
  parrafo.addEventListener("mousedown", () => {
    console.log("Manejador del párrafo.");
  });
  boton.addEventListener("mousedown", evento => {
    console.log("Manejador del botón.");
    if (evento.button == 2) evento.stopPropagation();
  });
</script>
```

{{index "event propagation", "target property"}}

La mayoría de objetos de eventos tienen una propiedad `target` que se refiere al
nodo donde se originaron. Se puede usar esta propiedad para asegurar de que no
se está manejando accidentalmente algo que se propagó desde un nodo que no se
desea manejar.

También es posible utilizar la propiedad `target` para lanzar una red amplia para
un evento específico. Por ejemplo, si tienes un nodo que contiene una gran
cantidad de botones, puede ser más conveniente el registrar un manejador en un
solo clic en el nodo externo y hacer que use la propiedad `target` para
averiguar si se hizo clic en un botón, en lugar de registrar manejadores
individuales en todos los botones.

```{lang: "text/html"}
<button>A</button>
<button>B</button>
<button>C</button>
<script>
  document.body.addEventListener("click", evento => {
    if (evento.target.nodeName == "BUTTON") {
      console.log("Presionado", evento.target.textContent);
    }
  });
</script>
```

## Acciones por defecto

{{index scrolling, "default behavior", "event handling"}}

La mayoría de eventos tienen una acción por defecto asociada a ellos. Si haces
clic en un ((enlace)), se te dirigirá al destino del enlace. Si presionas la
flecha hacia abajo, el navegador desplazará la página hacia abajo. Si das clic
derecho, se obtendrá un menú contextual. Y así.

{{index "preventDefault method"}}

Para la mayoría de los tipos de eventos, los manejadores de eventos de
JavaScript se llamarán _antes_ de que el comportamiento por defecto se produzca.
Si el manejador no quiere que suceda este comportamiento por defecto,
normalmente porque ya se ha encargado de manejar el evento, se puede llamar al
método `preventDefault` en el objeto de evento.

{{index expectation}}

Esto puede ser utilizado para implementar un atajo de ((teclado)) propio o
((menú contextual)). Esto también puede ser utilizado para interferir de forma
desagradable el comportamiento que los usuarios esperan. Por ejemplo, aquí hay
un enlace que no se puede seguir:

```{lang: "text/html"}
<a href="https://developer.mozilla.org/">MDN</a>
<script>
  let enlace = document.querySelector("a");
  enlace.addEventListener("click", evento => {
    console.log("Nope.");
    evento.preventDefault();
  });
</script>
```

{{index usability}}

Trata de no hacer tales cosas a menos que tengas una buena razón para hacerlo.
Será desagradable para las personas que usan tu página cuando el comportamiento
esperado no funcione.

Dependiendo del navegador, algunos eventos no pueden ser interceptados en lo
absoluto. En Chrome, por ejemplo, el atajo de ((teclado)) para cerrar la pestaña
actual ([control]{keyname}-W o [command]{keyname}-W) no se puede manejar con
JavaScript.

## Eventos de teclado

{{index keyboard, "keydown event", "keyup event", "event handling"}}

Cuando una tecla del teclado es presionado, el navegador lanza un evento
`"keydown"`. Cuando este es liberado, se obtiene un evento `"keyup"`.

```{lang: "text/html", focus: true}
<p>Esta página se pone violenta cuando se mantiene presionado la tecla V.</p>
<script>
  window.addEventListener("keydown", evento => {
    if (evento.key == "v") {
      document.body.style.background = "violet";
    }
  });
  window.addEventListener("keyup", evento => {
    if (evento.key == "v") {
      document.body.style.background = "";
    }
  });
</script>
```

{{index "repeating key"}}

A pesar de su nombre, `"keydown"` se lanza no solamente cuando la tecla es
físicamente presionada. Cuando una tecla se presiona y se mantiene presionada,
el evento se lanza una vez más cada que la tecla se _repite_. Algunas veces se
debe tener cuidado con esto. Por ejemplo, si tienes un botón al DOM cuando el
botón es presionado y removido cuando la tecla es liberada, puedes agregar
accidentalmente cientos de botones cuando la tecla se mantiene presionada por
más tiempo.

{{index "key property"}}

El ejemplo analizó la propiedad `key` del objeto de evento para ver de qué tecla
se trata el evento. Esta propiedad contiene una cadena que, para la mayoría de
las teclas, corresponde a lo que escribiría al presionar esa tecla. Para teclas
especiales como [enter]{keyname}, este contiene una cadena que nombre la tecla
{`"Enter"`, en este caso}. Si mantienes presionado [shift]{keyname} mientras
que presionas una tecla, esto también puede influir en el nombre de la
tecla-`"v"` se convierte en `"V"` y `"1"` puede convertirse en `"!"`, es lo que
se produce al presionar [shift]{keyname}-1 en tu teclado.

{{index "modifier key", "shift key", "control key", "alt key", "meta key", "command key", "ctrlKey property", "shiftKey property", "altKey property", "metaKey property"}}

La teclas modificadoras como [shift]{keyname}, [control]{keyname},
[alt]{keyname} y [meta]{keyname} ([command]{keyname} en Mac) generan eventos de
teclado justamente como las teclas normales. Pero cuando se busque combinaciones
de teclas, también se puede averiguar si estas teclas se mantienen presionadas
viendo las propiedades `shiftKey`, `ctrlKey`, `altKey` y `metaKey` de los
eventos de teclado y mouse.

```{lang: "text/html", focus: true}
<p>Presiona Control-Espacio para continuar.</p>
<script>
  window.addEventListener("keydown", evento => {
    if (evento.key == " " && evento.ctrlKey) {
      console.log("¡Continuando!");
    }
  });
</script>
```

{{index "button (HTML tag)", "tabindex attribute", [DOM, events]}}

El nodo DOM donde un evento de teclado se origina depende en el elemento que
tiene el ((foco)) cuando la tecla es presionada. La mayoría de los nodos no
pueden tener el foco a menos que se les de un atributo `tabindex`, pero
elementos como ((enlace))s, botones y campos de formularios sí pueden.
Volveremos a los ((campo))s de formularios en el capítulo [Chapter?](http#forms).
Cuando nadie en particular tiene el foco, `document.body` actua
como el nodo objetivo de los eventos de teclado.

Cuando el usuario está escribiendo texto, usando los eventos de teclado para
averiguar qué se está escribiendo es problematico. Algunas plataformas, sobre
todo el ((teclado virtual)) en ((teléfono))s ((Android)), no lanzan eventos de
teclado. Pero incluso cuando se tiene un teclado antiguo, algunos tipos de
entradas de texto no coinciden con las pulsaciones de teclas de forma sencilla,
como el software _editor de métodos de entrada_ (((IME))) usado por personas
cuyos _guiones_ (_((script))_) no encajan en un teclado, donde se combinan
varias teclas para crear caracteres.

Para notar cuando se escribió algo, los elementos en los que se puede escribir,
como las etiquetas `<input>` y `<textarea>`, lanzan eventos `"input"` cada vez
que el usuario cambia su contenido. Para obtener el contenido real que fue
escrito, es mejor leerlo directamente desde el campo seleccionado. El [Chapter
?](http#forms) mostrará cómo.

## Eventos de puntero

Actualmente, hay dos formas muy utilizadas para señalar en una pantalla: mouse
(incluyendo dispositivos que actuan como mouse, como páneles táctiles y
_trackballs_) y pantallas táctiles. Estos producen diferentes tipos de eventos.

### Clics de mouse

{{index "mousedown event", "mouseup event", "mouse cursor"}}

Al presionar un ((botón del mouse)) se lanzan varios eventos. Los eventos
`"mousedown"` y `"mouseup"` son similares a `"keydown"` y `"keyup"` y se lanzan
cuando el boton es presionado y liberado. Estos ocurren en los nodos del DOM que
están inmediatamente debajo del puntero del mouse cuando ocurre el evento.

{{index "click event"}}

Después del evento `"mouseup"`, un evento `"click"` es lanzado en el nodo más
específico que contiene la pulsación y la liberación del botón. Por ejemplo, si
presiono el botón del mouse sobre un párrafo y entonces muevo el puntero a
otro párrafo y suelto el botón, el evento `"click"` ocurrirá en el elemento
que contiene ambos párrafos.

{{index "dblclick event", "double click"}}

Si se producen dos clics juntos, un evento `"dblclick"` (doble-clic) también se
lanza, después del segundo evento de clic.

{{index pixel, "clientX property", "clientY property", "pageX property", "pageY property", "event object"}}

Para obtener la información precisa sobre el lugar en donde un evento del mouse
ocurrió, se puede ver en las propiedades `clientX` y `clientY`, los cuales
contienen las ((coordenadas)) (en pixeles) relativas a la esquina superior
izquierda de la ventana o `pageX` y `pageY`, las cuales son relativas a la
esquina superior izquierda de todo el documento (el cual puede ser diferente
cuando la ventana ha sido desplazada).

{{index "border-radius (CSS)", "absolute positioning", "drawing program example"}}

{{id mouse_drawing}}

Lo siguiente implementa un programa de dibujo primitivo. Cada vez que se hace
clic en el documento, agrega un punto debajo del puntero del mouse. Ver [Chapter
?](paint) para un programa de dibujo menos primitivo.

```{lang: "text/html"}
<style>
  body {
    height: 200px;
    background: beige;
  }
  .punto {
    height: 8px; width: 8px;
    border-radius: 4px; /* redondea las esquinas */
    background: blue;
    position: absolute;
  }
</style>
<script>
  window.addEventListener("click", evento => {
    let punto = document.createElement("div");
    punto.className = "punto";
    punto.style.left = (evento.pageX - 4) + "px";
    punto.style.top = (evento.pageY - 4) + "px";
    document.body.appendChild(punto);
  });
</script>
```

### Movimiento del mouse

{{index "mousemove event"}}

Cada vez que el puntero del mouse se mueve, un evento `"mousemove"` es lanzado.
Este evento puede ser utilizado para rastrear la posición del mouse. Una
situación común en la cual es útil es cuando se implementa una funcionalidad de
mouse-((arrastrar)).

{{index "draggable bar example"}}

Como un ejemplo, el siguiente programa muestra una barra y configura los
manejadores de eventos para que cuando se arrastre hacia la izquierda o a la
derecha en esta barra la haga más estrecha o más ancha:

```{lang: "text/html", startCode: true}
<p>Dibuja la barra para cambiar su anchura:</p>
<div style="background: orange; width: 60px; height: 20px">
</div>
<script>
  let ultimoX; // Rastrea la última posición de X del mouse observado
  let barra = document.querySelector("div");
  barra.addEventListener("mousedown", evento => {
    if (evento.button == 0) {
      ultimoX = evento.clientX;
      window.addEventListener("mousemove", movido);
      evento.preventDefault(); // Evitar la selección
    }
  });

  function movido(evento) {
    if (evento.buttons == 0) {
      window.removeEventListener("mousemove", movido);
    } else {
      let distancia = event.clientX - lastX;
      let nuevaAnchura = Math.max(10, barra.offsetWidth + distancia);
      barra.style.width = nuevaAnchura + "px";
      ultimoX = evento.clientX;
    }
  }
</script>
```

{{if book

La página resultante se ve así:

{{figure {url: "img/drag-bar.png", alt: "Una barra arrastrable", width:
"5.3cm"}}}

if}}

{{index "mouseup event", "mousemove event"}}

Tener en cuenta que el manejador `"mousemove"` es registrado sobre toda la
((ventana)). Incluso si el mouse se sale de la barra durante el cambio de
tamaño, siempre que se mantenga presionado el botón, su tamaño se actualizará.

{{index "buttons property", "button property", "bitfield"}}

Se debe dejar de cambiar el tamaño de la barra cuando se suelta el botón del
mouse. Para eso, podemos usar la propiedad `buttons` (nótese el plural), que
informa sobre los botones que se mantienen presionados actualmente. Cuando este
es cero, no hay botones presionados. Cuando los botones se mantienen
presionados, su valor es la suma de los códigos de esos botones—el botón
izquierdo tiene el código 1, el botón derecho 2 y el central 4. De esta manera,
puedes verificar si un botón dado es presionado tomando el resto del valor de
`buttons` y su código.

Tener en cuenta que el orden de los códigos es diferente del utilizado por
`button`, donde el botón central se encuentra que el derecho. Como se mencionó,
la coherencia no es realmente un punto fuerte de la interfaz de programación del
navegador.

### Eventos de toques

{{index touch, "mousedown event", "mouseup event", "click event"}}

El estilo del navegador gráfico que usamos fue diseñado con la interfaz de mouse
en mente, en un tiempo en el cual las pantallas táctiles eran raras. Para hacer
que la Web "funcionara" en los primeros teléfonos con pantalla táctil, los
navegadores de esos dispositivos pretendían, hasta cierto punto, que los eventos
táctiles fueran eventos del mouse. Si se toca la pantalla, se obtendrán los
eventos `"mousedown"`, `"mouseup"` y `"click"`.

Pero esta ilusión no es muy robusta. Una pantalla táctil funciona de manera
diferente a un mouse: no tiene multiples botones, no puedes rastrear el dedo
cuando no está en la pantalla (para simular `"mousemove"`) y permite que
multiples dedos estén en la pantalla al mismo tiempo.

Los eventos del mouse cubren las interacciones solamente en casos sencillos—si
se agrega un manejador `"click"` a un butón, los usuarios táctiles aún podrán
usarlo. Pero algo como la barra redimensionable del ejemplo anterior no funciona
en una pantalla táctil.

{{index "touchstart event", "touchmove event", "touchend event"}}

Hay tipos de eventos específicos lanzados por la interacción táctil. Cuando un
dedo inicia tocando la pantalla, se obtiene el evento `"touchstart"`. Cuando
este es movido mientras se toca, se lanza el evento `"touchmove"`. Finalmente,
cuando se deja de tocar la pantalla, se lanzará un evento `"touchend"`.

{{index "touches property", "clientX property", "clientY property", "pageX property", "pageY property"}}

Debido a que muchas pantallas táctiles pueden detectar multiples dedos al mismo
tiempo, estos eventos no tienen un solo conjunto de coordenadas asociados a
ellos. Más bien, sus ((objetos de  evento)) tienen una propiedad `touches`, el
cual contiene un ((objeto tipo matriz)) de puntos, cada uno de ellos tiene sus
propias propiedades `clientX`, `clientY`, `pageX` y `pageY`.

Se puede hacer algo como esto para mostrar circulos rojos alrededor de cada que
toca:

```{lang: "text/html"}
<style>
  punto { position: absolute; display: block;
        border: 2px solid red; border-radius: 50px;
        height: 100px; width: 100px; }
</style>
<p>Toca esta página</p>
<script>
  function actualizar(event) {
    for (let punto; punto = document.querySelector("punto");) {
      punto.remove();
    }
    for (let i = 0; i < evento.touches.length; i++) {
      let {paginaX, paginaY} = evento.touches[i];
      let punto = document.createElement("punto");
      punto.style.left = (paginaX - 50) + "px";
      punto.style.top = (paginaY - 50) + "px";
      document.body.appendChild(punto);
    }
  }
  window.addEventListener("touchstart", actualizar);
  window.addEventListener("touchmove", actualizar);
  window.addEventListener("touchend", actualizar);
</script>
```

{{index "preventDefault method"}}

A menudo se querrá llamar a `preventDefault` en un manejador de eventos táctiles
para sobreescribir el comportamiento por defecto del navegador (que puede
incluir desplazarse por la paǵina al deslizar el dedo) y para evitar que los
eventos del mouse se lancen, para lo cual se puede tener _también_ un manejador.

## Eventos de desplazamiento

{{index scrolling, "scroll event", "event handling"}}

Siempre que un elemento es desplazado, un evento `"scroll"` es lanzado. Esto
tiene varios usos, como son saber qué está mirando el usuario actualmente (para
deshabilitar la ((animación)) fuera de pantalla o enviar informes ((espías)) a
su sede maligna) o mostrar alguna indicación de progreso (resaltando parte de
una tabla de contenido o mostrando un número de página).

El siguiente ejemplo dibuja una ((barra de progreso)) sobre el documento y lo
actualiza para que se llene a medida que se desplza hacia abajo:

```{lang: "text/html"}
<style>
  #progreso {
    border-bottom: 2px solid blue;
    width: 0;
    position: fixed;
    top: 0; left: 0;
  }
</style>
<div id="progreso"></div>
<script>
  // Crear algo de contenido
  document.body.appendChild(document.createTextNode(
    "supercalifragilisticoespialidoso ".repeat(1000)));

  let barra = document.querySelector("#progreso");
  window.addEventListener("scroll", () => {
    let max = document.body.scrollHeight - innerHeight;
    barra.style.width = `${(pageYOffset / max) * 100}%`;
  });
</script>
```

{{index "unit (CSS)", scrolling, "position (CSS)", "fixed positioning", "absolute positioning", percentage, "repeat method"}}

Darle a un elemento una `position` de `fixed` actua como una posición `absolute`
pero también evita que se desplace junto con el resto del documento. El efecto
es hacer que la barra de progreso permanezca en la parte superior. Su ancho
cambia para indicar el progreso actual. Se usa `%`, en lugar de `px`, como una
unidad cuando se configura el ancho para que el tamaño del elemento sea relativo
al ancho de la página.

{{index "innerHeight property", "innerWidth property", "pageYOffset property"}}

La vincualación global `innerHeight` proporciona la altura de la ventana, que se
tiene que restar de la altura total desplazable—no se puede seguir desplazando
cuando se llega al final del documento. También hay un `innerWidth` para el
ancho de la ventana. Al dividir `pageYOffset`, la posición de desplazamiento
actual, por la posición de desplazamiento máximo y mulplicado por 100, se
obtiene el porcentaje de la barra de progreso.

{{index "preventDefault method"}}

Al llamar `preventDefault` en un evento de desplazamiento no evita que el
desplazamiento se produzca. De hecho, el manejador de eventos es llamado
unicamente _después_ de que se realiza el desplazamiento.

## Eventos de foco

{{index "event handling", "focus event", "blur event"}}

Cuando un elemento gana el ((foco)), el navegador lanza un evento de `"focus"`
en él. Cuando este pierde el foco, el elemento obtiene un evento `"blur"`.

{{index "event propagation"}}

A diferencia de los eventos discutidos con anterioridad, estos dos eventos no se
propagan. Un manejador en un elemento padre no es notificado cuando un elemento
hijo gana o pierde el foco.

{{index "input (HTML tag)", "help text example"}}

El siguiente ejemplo muestra un texto de ayuda para ((campo de texto)) que
actualmente tiene el foco:

```{lang: "text/html"}
<p>Nombre: <input type="text" dato-ayuda="Tu nombre"></p>
<p>Edad: <input type="text" dato-ayuda="Tu edad en años"></p>
<p id="ayuda"></p>

<script>
  let ayuda = document.querySelector("#ayuda");
  let campos = document.querySelectorAll("input");
  for (let campo of Array.from(campos)) {
    campo.addEventListener("focus", evento => {
      let texto = evento.target.getAttribute("dato-ayuda");
      ayuda.textContent = texto;
    });
    field.addEventListener("blur", evento => {
      ayuda.textContent = "";
    });
  }
</script>
```

{{if book

Esta captura de pantalla muestra el texto de ayuda para el campo edad.

{{figure {url: "img/help-field.png", alt: "Brindar ayuda cuando un campo está
enfocado", width: "4.4cm"}}}

if}}

{{index "focus event", "blur event"}}

El objeto ((window)) recibirá eventos de `"focus"` y `"blur"` cuando el usuario
se mueva desde o hacia la pestaña o ventana del navegador en la que se muestra
el documento.

## Evento de carga

{{index "script (HTML tag)", "load event"}}

Cuando una página termina de cargarse, el evento `"load"` es lanzado en la
ventana y en los objetos del cuerpo del documento. Esto es usado a menudo para
programar acciones de ((inicialización)) que requieren que todo el ((documento))
haya sido construido. Recordar que el contenido de las etiquetas `<script>` se
ejecutan inmediatamente cuando la etiqueta es encontrada. Esto puede ser
demasiado pronto, por ejemplo cuando el guión necesita hacer algo con las partes
del documento que aparecen después de la etiqueta `<script>`.

{{index "event propagation", "img (HTML tag)"}}

Elementos como ((imagenes)) y etiquetas de guiones que cargan un archivo externo
también tienen un evento `"load"` que indica que se cargaron los archivos a los
que hacen referencia. Al igual que los eventos relacionado con el foco, los
eventos de carga no se propagan.

{{index "beforeunload event", "page reload", "preventDefault method"}}

Cuando una página se cierra o se navega fuera de ella (por ejemplo, siguiendo un
enlace), un evento `"beforeunload"` es lanzado. El principal uso de este evento
es evitar que el usuario pierda su trabajo accidentalmente al cerrar un
documento. Si se evita el comportamiento por defecto en este evento _y_ se
establece la propiedad `returnValue` en el objeto de evento a una cadena, el
navegador le mostrará al usuario un diálogo preguntándole si realmente quiere
salir de la página. Ese cuadro de diálogo puede incluir una cadena de texto,
pero debido a que algunos sitios intentan usar estos cuadros de diálogo para
confundir a las personas para que permanezcan en su página para ver anuncios
poco fiables sobre la pérdida de peso, la mayoría de los navegadores ya no lo
muestran.

{{id timeline}}

## Eventos y el ciclo de eventos

{{index "requestAnimationFrame function", "event handling", timeline, "script (HTML tag)"}}

En el contexto del ciclo de eventos, como se explica en el [Chapter ?](async),
los manejadores de eventos del navegador se comportan como otras notificaciones
asíncronas. Se programan cuando el evento ocurre pero deben esperar a que
finalicen otros guiones que se están ejecutando antes de que tengan la
oportunidad de ejecutarse.

El hecho de que los eventos puedan ser procesados solo cuando no se está
ejecutando nada más que, si el ciclo de eventos está vinculado con otro trabajo,
cualquier interacción con la página (que ocurre a través de eventos) se
retrasará hasta que haya tiempo para procesarlo. Por lo tanto, si programas
demasiado trabajo, ya sea con manejadores de eventos de ejecución prolongada o
con muchos de breve ejecución, la página se volverá lenta y engorrosa de usar.

Para casos en donde _realmente_ se quiera hacer algo que requiera mucho tiempo
en segundo plano sin congelar la página, los navegadores proveeen algo llamado
__((web worker))__. Un worker es un proceso de JavaScript que se jecuta junto
con el guión principal, en su propia línea de tiempo.

Imaginar que se eleva un número al cuadrado es un cálculo pesado y de larga
duración que se quiere realizar en un ((hilo)) separado. Se podría escribir un
archivo llamado `code/cuadradoworker.js` que responde a los mensajes calculando
un cuadrado y enviando de vuelta un mensaje.

```
addEventListener("message", evento => {
  enviarMensaje(evento.data * evento.data);
});
```
Para evitar el problema de tener multiples ((hilo))s tocando los mismos datos,
los workers no comparten su ((alcance global)) o cualquier otro tipo de dato con
el entorno del guión principal. En cambio, tienes que comunicarte con ellos
enviando mensajes de un lado a otro.

Este código genera un worker que ejecuta ese guión, le envía algunos mensajes y
genera las respuestas.

```{test: no}
let cuadradoWorker = new Worker("code/cuadradoworker.js");
cuadradoWorker.addEventListener("message", evento => {
  console.log("El respondió:", evento.data);
});
cuadradoWorker.enviarMensaje(10);
cuadradoWorker.enviarMensaje(24);
```

{{index "postMessage method", "message event"}}


La función `enviarMensaje` envía un mensaje, que provocará que se lance un
evento `"message"`en el receptor. El guión que creó al worker envía y recibe
mensajes a través del objeto `Worker`, mientras que el worker habla con el guión
que lo creó enviando y escuchando directamente en su ((alcance global)). Solo
los valores que pueden ser representados como un JSON pueden ser enviados como
un mensaje—el otro lado recibirá una _copia_ de ellos, en lugar del valor en sí.

## Temporizadores

{{index timeout, "setTimeout function"}}

Se mostró la función `establecerTiempoEspera` en el [Chapter ?](async). Este
programa otra función para que se llame más tarde, después de un número
determinado de milisegundos.

{{index "clearTimeout function"}}

Algunas veces se necesita cancelar una función que se haya programado. Esto se
hace almacenando el valor retornado por `establecerTiempoEspera` y llamando a
`reinicarTiempoEspera` en él.

```
let temporizadorBomba = setTimeout(() => {
  console.log("¡BOOM!");
}, 500);

if (Math.random() < 0.5) { // 50% de cambio
  console.log("Desactivada.");
  reinicarTiempoEspera(temporizadorBomba);
}
```

{{index "cancelAnimationFrame function", "requestAnimationFrame function"}}

La función `cancelAnimationFrame` funciona de la misma forma que
`reinicarTiempoEspera`—llamarla en un valor devuelto por `requestAnimationFrame`
cancelará ese marco (asumiendo que aún no ha sido llamado).

{{index "setInterval function", "clearInterval function", repetition}}

Un conjunto similar de funciones, `setInterval` y `clearInterval`, son usadas
para restablecer los temporizadores que deberían _repetirse_ cada _X_
milisegundos.

```
let tictac = 0;
let reloj = setInterval(() => {
  console.log("tictac", tictac++);
  if (tictac == 10) {
    clearInterval(reloj);
    console.log("Detener.");
  }
}, 200);
```

## Antirrebote

{{index optimization, "mousemove event", "scroll event", blocking}}

Algunos tipos de eventos tienen el potencial de ser lanzados rápidamente, muchas
veces seguidos (los eventos `"mousemove"` y `"scroll"`, por ejmplo). Cuando se
menejan tales eventos, se debe tener cuidado de no hacer nada que consuma
demasiado tiempo o su manejador tomará tanto tiempo que la interacción con el
documento comenzará a sentirse lenta.

{{index "setTimeout function"}}

Si necesitas hacer algo no trivial en algún manejador de este tipo, se puede
usar `setTimeout` para asegurarse de que no se está haciendo con demasiada
frecuencia. Esto generalmente se llama _((antirrebote))_ del evento. Hay varios
enfoques ligeramente diferentes para esto.

{{index "textarea (HTML tag)", "clearTimeout function", "keydown event"}}

En el primer ejemplo, se quiere reaccionar cuando el usuario ha escrito algo,
pero no se quiere hacer inmediatamente por cada evento de entrada. Cuando se
está ((escribiendo)) rápidamente, se requiere esperar hasta que se produzca una
pausa. En lugar de realizar inmediatamente una acción en el manejador de
eventos, se establece un tiempo de espera. También se borra el tiempo de espera
anterior (si lo hay) para que cuando los eventos ocurran muy juntos (más cerca
que el tiempo de espera), el tiempo de espera del evento anterior será
cancelado.

```{lang: "text/html"}
<textarea>Escribe algo aquí...</textarea>
<script>
  let areaTexto = document.querySelector("textarea");
  let tiempoEspera;
  areaTexto.addEventListener("input", () => {
    clearTimeout(tiempoEspera);
    tiempoEspera = setTimeout(() => console.log("¡Escribió!"), 500);
  });
</script>
```

{{index "sloppy programming"}}

Dar un valor indefinido a `clearTimeout` o llamándolo en un tiempo de espera que
ya ha se ha lanzado no tiene ningún efecto. Por lo tanto, no debemos tener
cuidado sobre cuándo llamarlo, y simplemente se hace para cada evento.

{{index "mousemove event"}}

Podemos utilizar un patrón ligeramente diferente si se quiere espaciar las
respuestas de modo que estén separadas por al menos una cierta longitud de
((tiempo)) pero se quiere lanzar _durante_ una serie de eventos, no solo
después. Por ejemplo, se podría querer responder a los eventos `"mousemove"`
mostrando las coordenadas actuales del mourse pero solo cada 250 milisegundos.

```{lang: "text/html"}
<script>
  let programado = null;
  window.addEventListener("mousemove", evento => {
    if (!programado) {
      setTimeout(() => {
        document.body.textContent =
          `Mouse en ${programado.pageX}, ${programado.pageY}`;
        programado = null;
      }, 250);
    }
    programado = evento;
  });
</script>
```

## Resumen

Los manejadores de eventos hacen posible detectar y reaccionar a eventos que
suceden en nuestra página web. El método `addEventListener` es usado para
registrar un manejador de eventos.

Cada evento tiene un tipo (`"keydown"`, `"focus"`, etc.) que lo identifica. La
mayoría de eventos son llamados en un elemento DOM específico y luego se
_propagan_ a los ancentros de ese elemento, lo que permite que los manejadores
asociados con esos elementos los manejen.

Cuando un manejador de evento es llamado, se le pasa un objeto evento con
información adicional acerca del evento. Este objeto también tiene métodos que
permiten detener una mayor propagación (`stopPropagation`) y evitar que el
navegador maneje el evento por defecto (`preventDefault`).

Al presiosar una tecla se lanza los eventos `"keydown"` y `"keyup"`. Al
presionar un botón del mouse se lanzan los eventos `"mousedown"`, `"mouseup"` y
`"click"`. Al mover el mouse se lanzan los eventos `"mousemove"`. Las
interacción de la pantalla táctil darán como resultado eventos `"touchstart"`,
`"touchmove"` y `"touchend"`.

El desplazamiento puede ser detectado con el evento `"scroll"` y los cambios de
foco pueden ser detactados con los eventos `"focus"` y `"blur"`. Cuando el
documento termina de cargarse, se lanza el evento `"load"` en la ventana.

## Ejercicios

### Globo

{{index "balloon (exercise)", "arrow key"}}

Escribe una página que muestre un ((globo)) (usando el globo ((emoji)), 🎈).
Cuando se presione la flecha hacia arriba, debe inflarse (crecer) un 10 por
cierto, y cuando se presiona la flecha hacia abajo, debe desinflarse
(contraerse) un 10 por cierto)

{{index "font-size (CSS)"}}

Puedes controlar el tamaño del texto (los emojis son texto) configurando la
propiedad CSS `font-size` (`style.fontSize`) en su elemento padre. Recuerda
incluir una unidad en el valor, por ejemplo pixeles (`10px`).

Los nombres de las teclas de flecha son `"ArrowUp"` y `"ArrowDown"`. Asegúratede
que las teclas cambien solo al globo, sin desplazar la página.

Cuando eso funcione, agrega una nueva función en la que, si infla el globo más
allá de cierto tamaño, explote. En este caso, explotar significa que se
reemplaza con un emoji 💥, y se elimina el manejador de eventos (para que no se
pueda inflar o desinflar la explosión).

{{if interactive

```{test: no, lang: "text/html", focus: yes}
<p>🎈</p>

<script>
  // Your code here
</script>
```

if}}

{{hint

{{index "keydown event", "key property", "balloon (exercise)"}}

Querrás registrar un manejador para el evento `"keydown"` y mirar `event.key`
para averiguar si se presionó la teclas de flecha hacia arra o hacia abajo.

El tamaño actual se puede mantener en una vinculación para que puedas basar el
nuevo tamaño en él. Será útil definir una función que actualice el tamaño, tanto
el enlace como el estilo del globo en el DOM, para que pueda llamar desde su
manejador de eventos, y posiblemente también una vez al comenzar, para
establecer el tamaño inicial.

{{index "replaceChild method", "textContent property"}}

Puedes cambiar el globo a una explosión reemplazando el texto del nodo con otro
(usando `replaceChild`) o estableciendo la propiedad `textContent` de su no
padre a una nueva cadena.

hint}}

### Mouse trail

{{index animation, "mouse trail (exercise)"}}

En los primeros días de JavaScript, que era el momento de ((páginas de inicio
llamativas)) con muchas imágenes, a la gente se le ocurrieron formas realmente
inspiradoras de usar el lenguaje.

Uno de estos fue el _rastro del mouse_, una serie de elementos que seguirían el
puntero del mouse mientras lo movías por la página.

{{index "absolute positioning", "background (CSS)"}}

En este ejercicio, quiero que implementes un rastro del mouse. Utiliza elementos
`<div>` con un tamaño fijo y un color de fondo (consulta a
[code](event#mouse_drawing) en la sección "Clics del mouse" por un ejemplo).
Crea un montón de estos elementos y, cuando el mouse se mueva, muestralos
después del puntero del mouse.

{{index "mousemove event"}}

Hay varios enfoques posibles aquí. Puedes hacer tu solución tan simple o tan
compleja como desees. Una solución simple para comenzar es mantener un número de
elementos de seguimiento fijos y recorrerlos, moviendo el sigueinte a la
posición actual del mouse cada vez que ocurra un evento `"mousemove"`.

{{if interactive

```{lang: "text/html", test: no}
<style>
  .trail { /* className for the trail elements */
    position: absolute;
    height: 6px; width: 6px;
    border-radius: 3px;
    background: teal;
  }
  body {
    height: 300px;
  }
</style>

<script>
  // Your code here.
</script>
```

if}}

{{hint

{{index "mouse trail (exercise)"}}

La creación de los elementos se realiza de mjor manera con un ciclo. Añadelos al
documento para que aparezcan. Para poder acceder a ellos más tarde para cambiar
su posición, querrás alamacenar los elementos en una matriz.

{{index "mousemove event", [array, indexing], "remainder operator", "% operator"}}

Se puede hacer un ciclo a través de ellos manteniendo una ((variable contador))
y agregando 1 cada vez que se activa el evento `"mousemove"`. El operador
restante (`% elements.length`) pueden ser usado para obtener un índice de matriz
válido para elegir el elemento que se desaea colocar durante un evento
determinado.

{{index simulation, "requestAnimationFrame function"}}

Otro efecto interesante se puede lograr modelando un sistema simple ((físico)).
Utiliza el evento `"mousemove"` para actualizar un par de enlaces que rastrean
la posición del mouse. Luego usa `requestAnimationFrame` para simular que los
elementos finales son atraidos a la posición a la posición del puntero del
mouse. En cada paso de la animación, actualiza la posición en función de su
posición relativa al puntero (y, opcionalmente, una velocidad que se alamacena
para cada elemento). Averiguar una buena forma de hacerlo depende de ti.

hint}}

### Pestañas

{{index "tabbed interface (exercise)"}}

Los paneles con pestañas son utilizados ampliamente en las interfaces de
usuario. Te permiten seleccionar un panel de interfaz eligiendo entre una serie
de pestañas "que sobresalen" sobre un elemento.

{{index "button (HTML tag)", "display (CSS)", "hidden element", "data attribute"}}

En este ejercicio debes implementar una interfaz con pestañas simple. Escribe
una función, `asTabs`, que tome un nodo DOM y cree una interfaz con pestañas que
muestre los elementos secundarios de ese nodo. Se debe insertar una lista de
elementos `<button>` en la parte superior del nodo, uno para cada elemento hijo,
que contenga texto recuperado del atributo `data-tabname` del hijo. Todos menos
uno de los elementos secundarios originales deben estar ocultos (dado un estilo
`display` de` none`). El nodo visible actualmente se puede seleccionar haciendo
clic en los botones.

Cuando eso funcione, extiéndelo para diseñar el botón de la pestaña seleccionada
actualmente de manera diferente para que sea obvio qué pestaña está
seleccionada.

{{if interactive

```{lang: "text/html", test: no}
<tab-panel>
  <div data-nombrePestania="uno">Pestaña uno</div>
  <div data-nombrePestania="dos">Pestaña dos</div>
  <div data-nombrePestania="tres">Pestaña tres</div>
</tab-panel>
<script>
  function asTabs(nodo) {
    // Tu código aquí.
  }
  asTabs(document.querySelector("tab-panel"));
</script>
```

if}}

{{hint

{{index "text node", "childNodes property", "live data structure", "tabbed interface (exercise)", [whitespace, "in HTML"]}}

Un error con el que puedes encontrarte es que no puede usar directamente la
propiedad `childNodes` del nodo como una colección de nodos de pestañas. Por un
lado, cuando agregas los botones, también se convertirán en nodos secundarios y
terminarán en este objeto porque es una estructura de datos en vivo. Por otro
lado, los nodos de texto creados para el espacio en blanco entre los nodos
también están en `childNodes` pero no deberían tener sus propias pestañas.
Puedes usar `children` en lugar de` childNodes` para ignorar los nodos con
texto.

{{index "TEXT_NODE code", "nodeType property"}}

Puedes comenzar creando una colección de pestañas para que tengas fácil acceso a
ellas. Para implementar el estilo de los botones, puedes almacenar objetos que
contengan tanto el panel de pestañas como su botón.

Yo recomiendo escribir una función aparte para cambiar las pestañas. Puedes
almacenar la pestaña seleccionada anteriormente y cambiar solo los estilos
necesarios para ocultarla y mostrar la nueva, o simplemente puedes actualizar el
estilo de todas las pestañas cada vez que se selecciona una nueva pestaña.

Es posible que desees llamar a esta función inmediatamente para que la interfaz
comience con la primera pestaña visible.

hint}}
