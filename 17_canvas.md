{{meta {load_files: ["code/chapter/16_game.js", "code/levels.js", "code/chapter/17_canvas.js"], zip: "html include=[\"img/player.png\", \"img/sprites.png\"]"}}}

# Dibujando en Canvas

{{quote {author: "M.C. Escher", title: "citado por Bruno Ernst en The Magic Mirror of M.C. Escher", chapter: true}

El dibujo es decepción.

quote}}

{{index "Escher, M.C."}}

{{figure {url: "img/chapter_picture_17.jpg", alt: "Imagen de un brazo robótico dibujando en un papel", chapter: "framed"}}}

{{index CSS, "transform (CSS)", [DOM, graphics]}}

Los navegadores nos proporcionan varias formas de mostrar((gráficos)). La más simple
es usar estilos para posiciones y colores de elementos regulares del DOM.
Esto puede ser tardado, como vimos en el [previous
chapter](juego) pasado. Agregando ((imagenes))s de fondo transparentes
 a los nodos, podemos hacer que se vean exactamente de la form que 
queremos. incluso es posible rotar o distorsionar nodos con el estilo `transform`.

Pero estaríamos usando el DOM para algo para lo que
no fue diseñado. Algunas tareas, como dibujar una ((línea)) entre
puntos arbitrarios, son extremadamente incómodas de hacer con elementos HTML
regulares.

{{index SVG, "img (HTML tag)"}}

Tenemos dos alternativas. La primera es basada en el DOM, pero utiliza 
_Scalable Vector Graphics_ (SVG), más que HTML. Piensa en SVG como un
dialecto de un ((documento)) de marcado que se centra en ((figura))s más que en
texto. Puedes embeber un documento SVG directamente en un archivo HTML o
puedes incluirlo con una etiqueta `<img>`.

{{index clearing, [DOM graphics], [interface, canvas]}}

La segunda alternativa es llamada _((canvas))_. Un canvas es un
elemento del DOM que encapsula una ((imagen)). Proporciona una
intefaz de programción para dibujar ((forma))s en el espacio
del nodo. La principal diferencia entre un canvas y una imagen
SVG es que en SVG la descripción original de las figuras es
preservada de manera que puedan ser movidas o reescaladas en cualquier momento. Un canvas,
por otro lado, convierte las figuras a ((pixele))s (puntos de color en
una rejilla) tan pronto son dibujadas y no recuerda cuáles
pixeles representa. La única forma de mover una figura en un canvas es limpíando
el canvas (o la parte del canvas alrededor de la figura) y redibujarlo
con la figura en una nueva posición.

## SVG

Este libro no hablará de ((SVG)) a detalle, pero explicaré
de forma breve como funciona. Al final de [final del capítulo](canvas#graphics_tradeoffs), regresaré a estos temas
que debes considerar cuando debas decidir cuál mecanismo de ((dibujo)) sea
apropiado dada una aplicación.

Este es un documento HTML con una ((imagen)) SVG simple:

```{lang: "text/html", sandbox: "svg"}
<p>HTML normal aquí.</p>
<svg xmlns="http://www.w3.org/2000/svg">
  <circle r="50" cx="50" cy="50" fill="red"/>
  <rect x="120" y="5" width="90" height="90"
        stroke="blue" fill="none"/>
</svg>
```

{{index "circle (SVG tag)", "rect (SVG tag)", "XML namespace", XML, "xmlns attribute"}}

El atributo `xmlns` cambia un elemento (y sus hijos) a un
_XML namespace_ diferente. Este _namespace_, identificado por una ((URL)),
especifica el dialecto que estamos usando. las etiquetas 
`<circle>` y `<rect>`, —que no existen en HTML, pero tienen un significado en
SVG— dibujan formas usando el estilo y posición especificados por sus
atributos.

{{if book

El documento muestra algo así:

{{figure {url: "img/svg-demo.png", alt: "Una imagen SVG embebida",width: "4.5cm"}}}

if}}

{{index [DOM, graphics]}}

Estas etiquetas crean elementos en el DOM, como etiquetas de HTML, con las 
que los scripts pueden interactuar. Por ejemplo, el siguiente código cambia el elemento `<circle>`
para que sea ((color))eado de cyan:

```{sandbox: "svg"}
let circulo = document.querySelector("circle");
circulo.setAttribute("fill", "cyan");
```

## El elemento canvas

{{index [canvas, size], "canvas (HTML tag)"}}

Los ((gráficos)) canvas pueden ser dibujados en un elemento `<canvas>`. Puedes
agregarle los atributos `width` y `height` para determinar su
tamaño en ((pixel))es.

Un nuevo canvas se encuentra vacío, lo que significa que es completamente ((transparente)) y
se muestra como un espacio vacío en el documento.

{{index "2d (canvas context)", "webgl (canvas context)", OpenGL, [canvas, context], dimensions, [interface, canvas]}}

La etiqueta `<canvas>` esta diseñada para permtir diferentes estilos de
((dibujo)). Para tener acceso a una verdadera interfaz de dibujo,
primero neceistamos crear un _((context))_, un objeto cuyos métodos proveen
la interfaz de dibujo. Actualmente hay 2  estilos de dibujo ampliamente soportados
: `"2d"` para gráficos en dos dimensiones y `"webgl"` para
gráficos de tres dimensiones mediante la intefaz de OpenGL.

{{index rendering, graphics, efficiency}}

Este libro no abordará WebGL —nos limitaremos a dos dimensiones—. Pero si
de verdad te interesan los gráficos tridimensionales, te recomiendo que
investigues sobre WebGL. Provee una interfaz directa hacia hardware de
gráficos y te permite renderizar escenarios complicados de forma eficiente
usando JavaScript.

{{index "getContext method", [canvas, context]}}

Crea un ((contexto)) con el método `getContext` en el
elemento `<canvas>` del DOM.

```{lang: "text/html"}
<p>Antes del canvas.</p>
<canvas width="120" height="60"></canvas>
<p>Después del canvas.</p>
<script>
  let canvas = document.querySelector("canvas");
  let contexto = canvas.getContext("2d");
  contexto.fillStyle = "red";
  contexto.fillRect(10, 10, 100, 50);
</script>
```

Después de crear el objeto contexto, el ejemplo dibuja un
((rectangulo)) rojo de 100 ((pixel))es de ancho y 50 pixeles de alto con su esquina superior izquierda
en las coordenadas (10,10).

{{if book

{{figure {url: "img/canvas_fill.png", alt: "Un canvas con un rectangulo",width: "2.5cm"}}}

if}}

{{index SVG, coordinates}}

Al igual que en HTML (y SVG), el sistema de coordenadas que usa el canvas
coloca (0,0) en la esquina superior izquierda y el ((eje)) positivo y
baja a partir de ahí. Así que (10,10) significa 10 pixeles debajo y a la derecha de
la esquina superior izquierda.

{{id fill_stroke}}

## Líneas y superficies

{{index filling, stroking, drawing, SVG}}

En la interfaz del ((canvas)), una figura puede ser _rellenada_ dado
un cierto color o diseño, o puede ser _delimtada_, que
significa una ((linea)) dibujada en los bordes. La misma terminología aplica
para SVG.

{{index "fillRect method", "strokeRect method"}}

El método `fillRect` rellena un ((rectángulo)). Usa primero las ((coordenadas)) `x` e
`y` desde la esquina superior izquierda del rectángulo, después su dimensiones de ancho
y alto. Un método parecido `strokeRect`, dibuja los
((bordes)) del rectángulo.

{{index [state, "of canvas"]}}

Ningún método toma parámetros adicionales. Tanto el color de relleno
como el grueso del orden, no son determinados por el argumento
del método (como podría esperarse normalmente) sino por las
propiedades del contexto del objeto.

{{index filling, "fillStyle property"}}

La propiedad `fillStyle` controla La manera que se rellenan las figuras. Puede ser 
estableciendo una cadena que especifique un ((color)), usando la misma notación
que en ((CSS)).

{{index stroking, "line width", "strokeStyle property", "lineWidth property", canvas}}

La propiedad `strokeStyle` funciona de forma similar, pero determinando el color
usado por la línea. El ancho de dicha línea es determinado por la
propiedad `lineWidth`, que puede ser cualquier número positivo.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.strokeStyle = "blue";
  cx.strokeRect(5, 5, 50, 50);
  cx.lineWidth = 5;
  cx.strokeRect(135, 5, 50, 50);
</script>
```

{{if book

Este código dibuja dos cuadrados azules, usando un borde más delgado para el segundo.

{{figure {url: "img/canvas_stroke.png", alt: "Dos cuadrados con borde",width: "5cm"}}}

if}}

{{index "default value", [canvas, size]}}

Cuando no se especifican atributos `width` or `height` como en el ejemplo,
el canvas asigna un valor por defecto de 300 pixeles de ancho y 150
pixeles de alto.

## Rutas

{{index [path, canvas], [interface, design], [canvas, path]}}

Una ruta es una secuencia de ((linea))s. La interfaz del canvas 2D 
usa una forma particular para describir dicha ruta. Usualmente se infieren.
Las rutas no son valores que se puedan almacenar y usar.
En su lugar, si quieres hacer algo con una ruta, debes
hacer llamar a una secuencia de métodos para describir su figura.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.beginPath();
  for (let y = 10; y < 100; y += 10) {
    cx.moveTo(10, y);
    cx.lineTo(90, y);
  }
  cx.stroke();
</script>
```

{{index canvas, "stroke method", "lineTo method", "moveTo method", shape}}

Este ejemplo crea una ruta con un número de segmentos de ((líneas))
horizontales y las une usando el método `stroke`. Cada segmento
creado con `lineTo` empieza en la posición _actual_ de la ruta. Esa
posición suele ser la última del segmento anterior, a menos que `moveTo` fuera
llamada. En ese caso, the next segment would start at the position
passed to `moveTo`.

{{if book

La ruta descrita por el programa anterior se ve así:

{{figure {url: "img/canvas_path.png", alt: "Uniendo un número de líneas",width: "2.1cm"}}}

if}}

{{index [path, canvas], filling, [path, closing], "fill method"}}

Cuando se llena una ruta (usando el método`fill`), cada ((figura)) es
rellenada de forma separada. Una ruta puede contener múltiples figuras —cada movimiento `moveTo`
comienza una nueva—. Pero la ruta debe _cerrarse_ (es decir, que empieza y termina en el mismo punto) antes de ser rellenada.
Si la ruta no se ha cerrado, se agrega una linea desde el final hasta el 
principio, y la figura definida por la ruta será rellenada.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.beginPath();
  cx.moveTo(50, 10);
  cx.lineTo(10, 70);
  cx.lineTo(90, 70);
  cx.fill();
</script>
```

Este ejemplo dibuja y rellena un triangulo. Nota que sólo dos de
sus lados estan explícitamente dibujados. El tercero,
de la esquina inferior derecha al vértice superior, está inferido y no debería estar ahí
cuando definas la ruta.

{{if book

{{figure {url: "img/canvas_triangle.png", alt: "Rellenando una ruta",width: "2.2cm"}}}

if}}

{{index "stroke method", "closePath method", [path, closing], canvas}}

También podrías usar el método `closePath` para cerrar una ruta de
forma explícita agregando un segmento de ((linea)) de vuelta al inicio de la ruta.
Este segmento _es_ dibujado cuando delineas la ruta.

## Curvas

{{index [path, canvas], canvas, drawing}}

Una ruta puede contener ((linea))s ((curva))s. Desafortunadamente
requieren algo más que dibujar.

{{index "quadraticCurveTo method"}}

El método `quadraticCurveTo` dibuja una curva dado un punto.
Para determinar la curvatura de la linea, el método proporciona un ((punto de
control)) que funciona como punto de destino. Imagina este punto de control como
_ancla_ de la línea, dándole su curvatura. La línea no irá a través
del punto de control, pero su dirección entre los puntos de inicio y cierre
será como una línea recta en esa dirección que guía el punto de control. Veamos el siguiente ejemplo:

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.beginPath();
  cx.moveTo(10, 90);
  // control=(60,10) goal=(90,90)
  cx.quadraticCurveTo(60, 10, 90, 90);
  cx.lineTo(60, 10);
  cx.closePath();
  cx.stroke();
</script>
```

{{if book

Genera una ruta que se ve así:

{{figure {url: "img/canvas_quadraticcurve.png", alt: "Una curva cuadrática",width: "2.3cm"}}}

if}}

{{index "stroke method"}}

Dibujamos una ((curva cuadrática)) de la izquierda a la derecha, con la coordenada (60,10)
como punto de control, y dibujamos dos segmentos de ((linea)) a través 
del punto de control y de regreso al principio de la línea. El resultado
se asemeja a una insignia de _((Star Trek))_. Puedes ver el efecto
del punto de control: las líneas empiezan en la esquina inferior
y toman la dirección del punto de control y se ((curvan)) hacia
el objetivo.

{{index canvas, "bezierCurveTo method"}}

El método `bezierCurveTo` dibuja un tipo de curva similar. En vez de
un sólo ((punto de control)), este posee dos para cada uno
de los vértices de la ((linea)). A continuación un ejemplo para
ilustrar el comportamiento de la curva:

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.beginPath();
  cx.moveTo(10, 90);
  // control1=(10,10) control2=(90,10) goal=(50,90)
  cx.bezierCurveTo(10, 10, 90, 10, 50, 90);
  cx.lineTo(90, 10);
  cx.lineTo(10, 10);
  cx.closePath();
  cx.stroke();
</script>
```

Ambos puntos de control especifican la dirección en la que ambos terminan
la curva. Mientras más lejos estén de los puntos de inicio correspondientes, más
se "abultará" la curva en esa dirección.

{{if book

{{figure {url: "img/canvas_beziercurve.png", alt: "Una curva abultada",width: "2.2cm"}}}

if}}

{{index "trial and error"}}

Dichas ((curva))s pueden ser difíciled de trabajar, dado que no siempre
es posible encontrar los ((puntos de control)) que proporciona la ((figura)) que quieres
dibujar. A veces puedes calcularlos, y otras debes encontrar
el valor mediante prueba y error.

{{index "arc method", arc}}

El método `arc` es una manera de dibujar una linea que se curva en el
borde de un circulo. Toma un par de ((coordenadas)) para el centro del arco, un
radio, y un ángulo de inicio y un ángulo de fin.

{{index pi, "Math.PI constant"}}

Estos últimos dos parámetros hacen posible dibujar sólo parte del
circulo. Los ((ángulo))s se miden en ((radian))es, no en ((grados)).
Esto implica que un ((círculo)) tiene un ángulo de 2π, or `2 * Math.PI`,
que es alrededor de 6.28. El ángulo comienza a contarse desde el
punto a la derecha del centro del círculo y sigue el sentido de 
las manecillas del reloj.
Puedes usar un inicio de 0 y con un final mayor que 2π (digamos, 7)
para dibujar un círculo completo.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.beginPath();
  // center=(50,50) radius=40 angle=0 to 7
  cx.arc(50, 50, 40, 0, 7);
  // center=(150,50) radius=40 angle=0 to ½π
  cx.arc(150, 50, 40, 0, 0.5 * Math.PI);
  cx.stroke();
</script>
```

{{index "moveTo method", "arc method", [path, " canvas"]}}

La ilustración resultante muestra una ((línea)) desde la derecha del 
círculo (llamando primero a `arc`) hasta la derecha de la semiluna
(segunda llamada). Al igual que otros métodos, una línea dibujada
con `arc` esta relacionada con la ruta del segmento anterior.
Puedes llamar a `moveTo` o empezar una nueva ruta para evitar esto.

{{if book

{{figure {url: "img/canvas_circle.png", alt: "Dibujando un círcuo",width: "4.9cm"}}}

if}}

{{id pie_chart}}

## Dibujando una gráfica de pastel

{{index "pie chart example"}}

Imagina que tienes un ((trabajo)) en EconomiCorp, Inc., y tu
primera tarea es dibujar una gráfica de pastel de los resultados 
de la ((encuesta)) de satifacción al cliente.

En este caso `resultados` contiene un arreglo de objetos que 
representa las respuestas de las encuestas.

```{sandbox: "pie", includeCode: true}
const resultados = [
  {name: "Satisfecho", count: 1043, color: "lightblue"},
  {name: "Neutral", count: 563, color: "lightgreen"},
  {name: "Insatisfecho", count: 510, color: "pink"},
  {name: "No comentó", count: 175, color: "silver"}
];
```

{{index "pie chart example"}}

Para dibujar una gráfica de pastel, dibujamos un número de rebanadas, cada una hecha de
un ((arco)) y un par de ((línea))s hacia el centro de dicho arco. 
Podemos calcular el ((ángulo)) de cada arco diviendo el círculo
(2π) entre el total de respuestas y multiplicar el resultado por el
(el ángulo por respuesta) por el numero de personas que seleccionaron una opción.

```{lang: "text/html", sandbox: "pie"}
<canvas width="200" height="200"></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  let total = resultados
    .reduce((sum, {count}) => sum + count, 0);
  // Comienza desde el valor más alto
  let currentAngle = -0.5 * Math.PI;
  for (let resultado of resultados) {
    let sliceAngle = (resultado.count / total) * 2 * Math.PI;
    cx.beginPath();
    // center=100,100, radius=100
    // desde el ángulo actual, sigue en el sentido de las maneciilas del reloj
    cx.arc(100, 100, 100,
           currentAngle, currentAngle + sliceAngle);
    currentAngle += sliceAngle;
    cx.lineTo(100, 100);
    cx.fillStyle = resultado.color;
    cx.fill();
  }
</script>
```

{{if book

Lo anterior muestra la siguiente gráfica:

{{figure {url: "img/canvas_pie_chart.png", alt: "Una gráfica de pastel",width: "5cm"}}}

if}}

Pero una gráfica que no indica que significa las secciones no es
muy útil. Necesitamos mostrar texto en el ((canvas)).

## Texto

{{index stroking, filling, "fillStyle property", "fillText method", "strokeText method"}}

El contexto de un dibujo en un canvas 2D proporciona los métodos
`fillText` y `strokeText`. El útltimo es muy útil para delinear las
letras, pero por lo general `fillText` es lo que usarás. Para el delinear el ((text)) con el `fillStyle` actual.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.font = "28px Georgia";
  cx.fillStyle = "fuchsia";
  cx.fillText("¡También puedo dibujar texto!", 10, 50);
</script>
```

Puedes específicar el tamaño, estilo, y ((fuente)) del texto con
la propiedad `font`. Este ejemplo sólo muestra un tamaño de fuente
y el nombre de la familia.
También es posible agregar `italic` (cursiva) o `bold` (negrita) 
al principio de una cadena para darles un estilo.

{{index "fillText method", "strokeText method", "textAlign property", "textBaseline property"}}

Los últimos dos argumentos de `fillText` y `strokeText` indican la
posición en la cuál se dibujan las fuentes. Por defecto, indican
la posición del comienzo de la línea base del alfabeto del texto,
que es la línea en las que las letras se apoyan, no se cuentan las
letras con ganchos como la _j_ o la _p_. Puedes cambiar la 
propiedad `textAlign`  a `"end"` (final) o `"center"` (centrar) y 
la posición vertical cambiando `textBaseline` a `"top"` (superior)
, `"middle"` (en medio), o`"bottom"` (inferior).

{{index "pie chart example"}}

Regresaremos a nuestra gráfica de pastel, y el problema de ((etiquetar)) las
secciones, en los [ejercicios](canvas#exercise_pie_chart) al final
del capítulo.

## Imágenes

{{index "vector graphics", "bitmap graphics"}}

En computación ((gráfica)), a menudo se distingue entre gráficos de _vectores_
y gráficos de _mapa de bits_. Los primeros son los que hemos estado 
trabajando en este capítulo, especificando una imagen mediante la
descripción lógica de sus ((figura))s. Los gráficos de mapa de bits,
por otro lado, especifican figuras, pero funcionan con datos de pixeles 
(rejillas de puntos coloreados).

{{index "load event", "event handling", "img (HTML tag)", "drawImage method"}}

El método `drawImage` nos permite dibujar datos de ((pixel))es en
un ((canvas)). Estos datos pueden originarse desde un elemento `<img>` o
desde otro canvas. El siguiente ejemplo crea un elemento `<img>`
y carga un archivo de imagen en él. Pero no podemos empezar a
de esta imagen porquen el navegador podría no haberla cargadao aún.
Para lidiar con esto, registramos un `"load"` _event handler_
y dibujamos después de que la imagen se ha cargado.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  let img = document.createElement("img");
  img.src = "img/hat.png";
  img.addEventListener("load", () => {
    for (let x = 10; x < 200; x += 30) {
      cx.drawImage(img, x, 10);
    }
  });
</script>
```

{{index "drawImage method", scaling}}

Por defecto, `drawImage` dibujará la imagen en su tamaño original.
También puedes darle dos argumentos adicionales para definir 
ancho y alto distintos.

Cuando `drawImage` recibe _nueve_ argumentos, puede usarse para
dibujar solo un fragmento de la imagen. Del segundo al quinto argumentos
indican el rectángulo (x, y, ancho y alto) en la imagen de origen
que debe copiarse, y de los argumentos cinco a nueve indicen el
otro (en el canvas) en donde serán copiados.

{{index "player", "pixel art"}}

Esto puede ser usado para empaquetar múltiples _((sprite))s_ (elementos de imagen) 
en una sola imagen y dibujar solo la parte que necesitas. Por ejemplo,
tenemos una imagen con un personaje de un juego en múltiples
((pose))s:

{{figure {url: "img/player_big.png", alt: "Varias poses de un personaje",width: "6cm"}}}

{{index [animation, "platform game"]}}

Alternando las poses que dibujamos, podemos mostrar una animación 
en la que se vea nuestro personaje caminando.

{{index "fillRect method", "clearRect method", clearing}}

Para animar una ((imagen)) en un ((canvas)), el método `clearRect` es
muy útil. Reutiliza `fillRect`, pero en vez de colorear el
rectángulo, lo vuelve ((transparente)), quitando los pixeles
previamente dibujados.

{{index "setInterval function", "img (HTML tag)"}}

Sabemos que cada _((sprite))_, cada subimagen, es de 24 ((pixele))s de ancho
y 30 pixeles de alto. El siguiente código carga la imagen y establece
un intervalo de tiempo para dibujar el siguiente ((frame)):

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  let img = document.createElement("img");
  img.src = "img/player.png";
  let spriteW = 24, spriteH = 30;
  img.addEventListener("load", () => {
    let ciclo = 0;
    setInterval(() => {
      cx.clearRect(0, 0, spriteW, spriteH);
      cx.drawImage(img,
                   // source rectangle
                   ciclo * spriteW, 0, spriteW, spriteH,
                   // destination rectangle
                   0,               0, spriteW, spriteH);
      ciclo = (ciclo + 1) % 8;
    }, 120);
  });
</script>
```

{{index "remainder operator", "% operator", [animation, "platform game"]}}

El valor `ciclo` rastrea la posición en la animación. Para cada
((frame)), se incrementa y regresa al rango del 0 al 7 usando
el operador del remanente. Entonces este valor es usado para
calcular la coordenada en x que tiene el _sprite_ para la pose que
tiene actualmente la imagen.

## Transformaciones

{{index transformation, mirroring}}

{{indexsee flipping, mirroring}}

¿Pero que pasa si quieremos que nuestro personaje camine a la izquierda
en vez de la derecha? Podríamos dibujar otro conjunto de _sprites_... o podríamos
decirle al ((canvas)) que redibuje la ilustración hacia el otro lado.

{{index "scale method", scaling}}

Llamar al método `scale` provocará que todo lo que dibujemos después sea escalado. Este método toma dos parámetros, uno para
establecer la escala horizontal y otro para establecer la 
escala vertical.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  cx.scale(3, .5);
  cx.beginPath();
  cx.arc(50, 50, 40, 0, 7);
  cx.lineWidth = 3;
  cx.stroke();
</script>
```

{{if book

El resultado de llamar a `scale`, es que el círculo es dibujado con
el triple de ancho y la mitad de alto.

{{figure {url: "img/canvas_scale.png", alt: "Un círculo escalado",width: "6.6cm"}}}

if}}

{{index mirroring}}

Escalar provoca que todo lo que se encuentre sobre la imagen, incluyendo el
((ancho de línea)), y se ajustan de acuerdo a las instrucciones que se dieron.
Escalar en una escala negativa dará vuelta a la figura. El giro
sucede sobre la coordenada (0,0), lo que significa que también gira
la dirección del sistema de coordinadas. Cuando el escalado
horzontal es de -1 es aplicado, una figura en la posición 100
terminará en lo que era la posición -100.

{{index "drawImage method"}}

Para girar una imagen, podemos sencillamente agregar `cx.scale(-1, 1)`
antes de llamar `drawImage` porque eso movería nuestra figura 
fuera del ((canvas)), donde no será visible. Puedes ajustar
las ((coordenadas)) dadas a `drawImage` para compensar que la
la imagen se dibuja en la posición x -50 en vez de 0. Otra solución,
que no requiere que el código que hace le dibujo necesite saber
sobre el cambio de escala, es ajustar el ((eje)) sobre el que el
escalado sucede.

{{index "rotate method", "translate method", transformation}}

Hay otros métodos además de `scale` que influencian el sistema
de coordenadas de un ((canvas)). Puedes rotar las figuras de forma
subsecuente con el método `rotate` y movelos con el método 
`translate`. Lo interesante —y confuso— de esto es que las
transformaciones se _apilan_, lo que significa que cada una sucede
de forma relativa a la transformación anterior.

{{index "rotate method", "translate method"}}

Entonces trasladando por 10 pixeles horizontales dos veces, todo de dibujará
20 pixeles a la derecha. Si primero movemos el centro del sistema
de coordenadas hacia (50,50) y lo rotamos por 20 ((grado))s (alrededor
de 0.1π ((radian))es), la rotación sucederá _alredador_ del punto (50,50).

{{figure {url: "img/transform.svg", alt: "Apilando transformaciones",width: "9cm"}}}

{{index coordinates}}

Pero si _primero_ rotamos por 20 grados _y entonces_ trasladamos
(50,50), la traslación sucederá en el sistema de coordenadas rotadas
y esto producirá una orientación distinta. El orden en el cual se
aplican las transformaciones importa.

{{index axis, mirroring}}

Para voltear una imagen alrededor de la línea vertical dada una 
posición en x, podemos hacer lo siguiente:

```{includeCode: true}
function voltearHorizontalmente(context, around) {
  context.translate(around, 0);
  context.scale(-1, 1);
  context.translate(-around, 0);
}
```

{{index "flipHorizontally method"}}

Recorremos el ((eje))-y donde queramor colocar el ((espejo)),
aplicamos el espejeado, y regresamos el eje-y  de regreso a su lugar
en el universo espejeado. la sigiente imagen explica como funciona:

{{figure {url: "img/mirror.svg", alt: "Espejeando sobre la línea vertical",width: "8cm"}}}

{{index "translate method", "scale method", transformation, canvas}}

Esto muestra el sistema de coordenadas antes y después de espejear
atravesando la línea central. Los tríangulos están numerados para 
illustrar cada paso.
Si dibujamos un triángulo en una posición x positiva, debería estar
por defecto en el lugar donde se encuentra el tríangulo 1. Una 
llamada a  `voltearHorizontalmente`
realiza primero la traslación a la derecha, la cual nos da el
triángulo 2. Cuando lo escala, voltea el triángulo hacia la 
posición 3. Esto no debería ser así, si espejeamos en la línea inicial.
La segunda llamada a `translate` corrige esto, "cancelando" la 
traslación inicial y el triángulo 4 aparece donde debe.

Ahora podemos dibujar un personaje en la posición (100,0) girando el
universo alrededor del centro vertical de los personajes.

```{lang: "text/html"}
<canvas></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  let img = document.createElement("img");
  img.src = "img/player.png";
  let spriteW = 24, spriteH = 30;
  img.addEventListener("load", () => {
    voltearHorizontalmente(cx, 100 + spriteW / 2);
    cx.drawImage(img, 0, 0, spriteW, spriteH,
                 100, 0, spriteW, spriteH);
  });
</script>
```

## Almacenando y limpiando transformaciones

{{index "side effect", canvas, transformation}}

La transformaciones se mantienen. Todo lo que dibujemos después de
((dibujar)) de nuestro personaje espejeado también puede ser
espejeado. Eso puede ser un inconveniente.

Es posible guardar el estado actual de la transformación, dibujar
de nuevo y restaurar la transformación anterior. Usualmente esto
es lo que debemos hacer para una función que necesite transformar
de forma temporal el sistema de coordenadas. Primero, guardamos el
código de la transformación que haya llamado a la función que estamos
usando. La función hace lo suyo, agregando más transformaiones
sobre la transformación actual. Finalmente, revertimos a la 
transformación con la que empezamos.

{{index "save method", "restore method", [state, "of canvas"]}}

Los métodos `save` y `restore` en el contexto del ((canvas)) 2D 
realizan el manejo de las ((transformaciones)). Conceptualmente
mantienen una pila de los estados de la transformación. Cuando
se llama `save`, el estado actual se agrega a la pila, y cuando se
llama `restore`, el estado en la cima de la pila se usa en el
contexto actual de la transformación. También puedes llamar 
`resetTransform` para resetear por completo la transformación.

{{index "branching recursion", "fractal example", recursion}}

La función `ramificar` en el siguiente ejemplo ilustra lo que puedes
hacer con una función que cambia la transformación y llama una
función (en este caso a sí misma), que continúa dibujando con una
transformación dada.

La función dibuja un árbol dibujando una línea, moviendo el centro
del sistema de coordenadas hacia el final de la línea, y llamándose
a sí misma rotándose a la izquierda y luego a la derecha.
Cada cada llamada reduce el ancho de la rama dibujada, y la
recursión se detiene cuando el ancho es menor a 8.

```{lang: "text/html"}
<canvas width="600" height="300"></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  function ramificar(length, angle, scale) {
    cx.fillRect(0, 0, 1, length);
    if (length < 8) return;
    cx.save();
    cx.translate(0, length);
    cx.rotate(-angle);
    ramificar(length * scale, angle, scale);
    cx.rotate(2 * angle);
    ramificar(length * scale, angle, scale);
    cx.restore();
  }
  cx.translate(300, 0);
  ramificar(60, 0.5, 0.8);
</script>
```

{{if book

El resultado es un simple fractal.

{{figure {url: "img/canvas_tree.png", alt: "Una imagen recursiva",width: "5cm"}}}

if}}

{{index "save method", "restore method", canvas, "rotate method"}}

Sí la llamada a `save` y `restore` no estuvieran, la segunda llamada
recursiva a `ramificar` terminarían con la posición y rotación
creados en la primera llamada. No se conectarían con la rama actual,
excepto por las mas cercanas, la mayoría dibujadas en la primera
llamada. La figura resultante sería interesante, pero no es un árbol definitivamente.

{{id canvasdisplay}}

## De regreso al juego

{{index "drawImage method"}}

Ahora que sabemos dibujar en el ((canvas)), es tiempo de empezar
a trabajar en el sistema de ((_display_)) basado en el ((canvas))
para el ((juego)) del [capítulo anterior](game). El nuevo display 
no mostrará sólo cajas de colores. En vez de eso, usaremos 
`drawImage` para dibujar imágenes que representen los elementos 
del juego.

{{index "CanvasDisplay class", "DOMDisplay class", [interface, object]}}

Definimos otro objeto del _display_ llamado `CanvasDisplay`,
que soporta la misma interfaz que en `DOMDisplay` del [Capítulo
?](game#domdisplay), los métodos `syncState` y `clear`.

{{index [state, "in objects"]}}

Este objeto posee algo más de información que `DOMDisplay`. En vez
de usar la posición del _scroll_ de su elemento DOM, rastrea su propio
((_viewport_)), que nos dice en que parte del nivel nos encontramos. 
Finalmente, hace uso de la propiedas `flipPlayer` de modo que 
incluso cuando el jugador se encuentra detenido, lo hace en la última
dirección en que se movió.

```{sandbox: "game", includeCode: true}
class CanvasDisplay {
  constructor(parent, level) {
    this.canvas = document.createElement("canvas");
    this.canvas.width = Math.min(600, level.width * scale);
    this.canvas.height = Math.min(450, level.height * scale);
    parent.appendChild(this.canvas);
    this.cx = this.canvas.getContext("2d");

    this.flipPlayer = false;

    this.viewport = {
      left: 0,
      top: 0,
      width: this.canvas.width / scale,
      height: this.canvas.height / scale
    };
  }

  clear() {
    this.canvas.remove();
  }
}
```

El método `syncState` calcula primero un nuevo _viewport_ y después 
dibuja la escena del juego en la posición apropiada.

```{sandbox: "game", includeCode: true}
CanvasDisplay.prototype.syncState = function(state) {
  this.updateViewport(state);
  this.clearDisplay(state.status);
  this.drawBackground(state.level);
  this.drawActors(state.actors);
};
```

{{index scrolling, clearing}}

Contrario al `DOMDisplay`, este estilo de display _tiene que_ redibujar
el fondo en cada _frame_. Esto se debe a que las figuras en un canvas
son solo ((pixel))es, después de que los dibujamos no hay un método
apropiado para movelos (o eliminarlos). La única forma de actualizar
el canvas es limpiarlo y redibujar la escena. También podríamos desplazarlo,
Lo que requiere que el fondo este en una posición diferente.

{{index "CanvasDisplay class"}}

El método `updateViewport` es similar al método `scrollPlayerIntoView`
del método `DOMDisplay`. Comprueba si el jugador esta muy cerca 
del borde de la pantalla y mueve el ((_viewport_)) cuando asi sea el caso

```{sandbox: "game", includeCode: true}
CanvasDisplay.prototype.updateViewport = function(state) {
  let view = this.viewport, margin = view.width / 3;
  let player = state.player;
  let center = player.pos.plus(player.size.times(0.5));

  if (center.x < view.left + margin) {
    view.left = Math.max(center.x - margin, 0);
  } else if (center.x > view.left + view.width - margin) {
    view.left = Math.min(center.x + margin - view.width,
                         state.level.width - view.width);
  }
  if (center.y < view.top + margin) {
    view.top = Math.max(center.y - margin, 0);
  } else if (center.y > view.top + view.height - margin) {
    view.top = Math.min(center.y + margin - view.height,
                        state.level.height - view.height);
  }
};
```

{{index boundary, "Math.max function", "Math.min function", clipping}}

Las llamadas a `Math.max` y `Math.min` aseguran que el _viewport_
no termine mostrando espacio fuera del nivel. 
`Math.max(x, 0)` aseguramos que el resultado sea mayor a cero. 
`Math.min` hacer algo similar al garantizar que un valor se mantenga
por debajo de lo indicado.

Cuando se ((limpia)) el _display_, usamos un ((color)) ligeramente
distinto dependiendo si el jugador ganó (más claro) o perdió (más oscuro).

```{sandbox: "game", includeCode: true}
CanvasDisplay.prototype.clearDisplay = function(status) {
  if (status == "won") {
    this.cx.fillStyle = "rgb(68, 191, 255)";
  } else if (status == "lost") {
    this.cx.fillStyle = "rgb(44, 136, 214)";
  } else {
    this.cx.fillStyle = "rgb(52, 166, 251)";
  }
  this.cx.fillRect(0, 0,
                   this.canvas.width, this.canvas.height);
};
```

{{index "Math.floor function", "Math.ceil function", rounding}}

Para dibujar el fondo, hacemos que los mosaicos sean visibles en el
_viewport_ actual, usando el mismo truco que en el método `touches`
del [capítulo anterior](game#touches).

```{sandbox: "game", includeCode: true}
let otherSprites = document.createElement("img");
otherSprites.src = "img/sprites.png";

CanvasDisplay.prototype.drawBackground = function(level) {
  let {left, top, width, height} = this.viewport;
  let xStart = Math.floor(left);
  let xEnd = Math.ceil(left + width);
  let yStart = Math.floor(top);
  let yEnd = Math.ceil(top + height);

  for (let y = yStart; y < yEnd; y++) {
    for (let x = xStart; x < xEnd; x++) {
      let tile = level.rows[y][x];
      if (tile == "empty") continue;
      let screenX = (x - left) * scale;
      let screenY = (y - top) * scale;
      let tileX = tile == "lava" ? scale : 0;
      this.cx.drawImage(otherSprites,
                        tileX,         0, scale, scale,
                        screenX, screenY, scale, scale);
    }
  }
};
```

{{index "drawImage method", sprite, tile}}

Los mosaicos que no están vacíos se dibujan con `drawImage`. La
imagen `otherSprites` contiene las imagenes usadas para los elementos
distintos al jugador. Contiene, de izquierda a derecha, el 
mosaico del muro, el mosaico de lava, y el _sprite_ de una moneda.

{{figure {url: "img/sprites_big.png", alt: "Sprites para nuestro juego",width: "1.4cm"}}}

{{index scaling}}

Los mosaicos del fondo son de 20 por 20 pixeles dado que usaremos
la misma escala que en `DOMDisplay`. Así, el _offset_ para el
mosaico de lava es de 20 (el valor encapsulado de `scale`), y 
el _offset_ para el de muro es de 0.

{{index drawing, "load event", "drawImage method"}}

No hace falta quedarse esperando a que carge el _sprite_ de la imagen.
Llamar a `drawImage` con una imagen que aún no ha sido cargada,
simplemente no hace anda. Aun así, podría fallar en dibujar el luego
de forma adecuada durante los primeros ((_frame_))s, mientras la 
imagen siga cargádose, pero esto no es un problema. Mietras sigamos
actualizando la pantalla, la escena correcta aparecerá tan pronto
la imagen termine de cargarse.

{{index "player", [animation, "platform game"], drawing}}

El personaje ((caminante)) mostrado antes será usado para representar al
jugador. El código que lo dibuja necesita escoger el ((sprite))  correcto y la
dirección basados en el movimiento actual del jugador. Los primeros ocho
_sprites_ contienen una animación de caminata. Cuando el jugador se mueve sobre
el suelo, los pasamos conforme al tiempo actual. Como queremos
cambiar _frames_ cada 60 millisegundos, entonces el ((tiempo)) es dividido entre 60
ṕrimero. Cuando el jugador se detiene, dibujamos el noveno _sprite_.
Durante los saltos, que reconoceremos por el hecho de que la velocidad vertical
no es cero, usaremos el décimo _sprite_.

{{index "flipHorizontally function", "CanvasDisplay class"}}

Ya que los ((_sprite_))s son ligeramente más anchos que el jugador
—24 píxeles en vez de 16 para permitir algo de espacio para
pies y brazos— tenemos que ajustar las coordenadas en x y el ancho por el cantidad dada por (`playerXOverlap`).

```{sandbox: "game", includeCode: true}
let playerSprites = document.createElement("img");
playerSprites.src = "img/player.png";
const playerXOverlap = 4;

CanvasDisplay.prototype.drawPlayer = function(player, x, y,
                                              width, height){
  width += playerXOverlap * 2;
  x -= playerXOverlap;
  if (player.speed.x != 0) {
    this.flipPlayer = player.speed.x < 0;
  }

  let tile = 8;
  if (player.speed.y != 0) {
    tile = 9;
  } else if (player.speed.x != 0) {
    tile = Math.floor(Date.now() / 60) % 8;
  }

  this.cx.save();
  if (this.flipPlayer) {
    voltearHorizontalmente(this.cx, x + width / 2);
  }
  let tileX = tile * width;
  this.cx.drawImage(playerSprites, tileX, 0, width, height,
                                   x,     y, width, height);
  this.cx.restore();
};
```

El método `drawPlayer` es llamado por `drawActors`, que es
responsable de dibujar todos los involucrados en el juego.

```{sandbox: "game", includeCode: true}
CanvasDisplay.prototype.drawActors = function(actors) {
  for (let actor of actors) {
    let width = actor.size.x * scale;
    let height = actor.size.y * scale;
    let x = (actor.pos.x - this.viewport.left) * scale;
    let y = (actor.pos.y - this.viewport.top) * scale;
    if (actor.type == "player") {
      this.drawPlayer(actor, x, y, width, height);
    } else {
      let tileX = (actor.type == "coin" ? 2 : 1) * scale;
      this.cx.drawImage(otherSprites,
                        tileX, 0, width, height,
                        x,     y, width, height);
    }
  }
};
```

Cuando  se trata de ((dibujar)) algo que no es el ((jugador)), nos fijamos en
el tipo de objeto que es para encontrar el _offset_ del _sprite_ correcto. 
El mosaico de ((lava)) se encuentra en un _offset_ de 20, y el _sprite_
de ((moneda)) se encuentra en 40 (dos veces `scale`).

{{index viewport}}

Debemos restar la posición del viewport cuando calculamos la posición
de los involucrados desde la posición (0,0) en nuestro ((canvas)) 
correspondiente a la esquina superior izquierda del viewport,
no la esquina superior izquierda del nivel. También podemos hacer
uso de `translate` para esto. Ambos métodos funcionan.

{{if interactive

Este documento se enlaza con el nuevo display en `runGame`:

```{lang: "text/html", sandbox: game, focus: yes, startCode: true}
<body>
  <script>
    runGame(GAME_LEVELS, CanvasDisplay);
  </script>
</body>
```

if}}

{{if book

{{index [game, screenshot], [game, "with canvas"]}}

Con eso terminamos el nuevo sistema de ((display)). El luego resultante
luce como algo así:

{{figure {url: "img/canvas_game.png", alt: "El juego se muestra en el canvas",width: "8cm"}}}

if}}

{{id graphics_tradeoffs}}

## Escogiendo una interfaz gráfica

Cuando se necesita generar gráficos en el navegador, se puede escoger
entre HTML plano, ((SVG)), y ((canvas)). No existe un enfoque que
funcione mejor en cualquier situación. Cada opción tiene sus pros
y sus contras.

{{index "text wrapping"}}

El HTML plano tiene la ventaja de ser simple. También puede integrar 
((texto)). Tanto el SVG como el canvas permiten dibujar texto, pero
no te ayudarán si necesitas cambiar la posición o acomodarlo cuando
necesites más de una línea. En una imagen basada en HTML es mucho 
más fácil incluir bloques de texto.

{{index zooming, SVG}}

SVG puede usarse para generar ((gráficos)) ((claros)) que se ven 
bien con cualquier nivel de zoom. a diferencia del HTML, está diseñado 
para dibjar y es más adecuado a para ese propósito.

{{index [DOM, graphics], SVG, "event handling", ["data structure", tree]}}

Tanto SVG como HTML contienen una estrucura de datos (el DOM) que
representa tu imagen. Esto hace posible modificar elementos después
después de que son dibujados. Si necesitas cambiar de forma repetida
una pequeña parte de una ((imagen)) grande como respuesta a lo que
el usuario este haciendo o como parte de una ((animación)), hacerlo
en un canvas puede ser innecesariamente difícil.
El DOM también nos permite registrar los _event handlers_ del mouse
en cada elemento en la imagen (incluso en figuras dibujadas en SVG), cosa que no se puede hacer con el canvas.

{{index performance, optimization}}

Pero el enfoque orientado a ((pixeles)) del ((canvas)) puede ser una ventaja
cuando se trata de dibujar un gran número de elementos pequeños.
El hecho de que no posea una estructura de datos sino únicamente de 
dibujar de forma repetida sobre la misma superficie de pixeles le
da al canvas un costo menor por figura.

{{index "ray tracer"}}

También se pueden agregar efectos, como renderizar una escena con un pixel
a la vez (por ejemplo, usando trazado de rayos) o postprocesar una
imagen con JavaScript (agregando _blur o distorsionándola_), que
puede hacerse de forma realista usando un enfoque basados en pizeles.

En algunso casos, podrías intentar combinar varias de estas técnicas.
Por ejemplo, podrías dibujar un ((gráfico)) con ((SVG)) o ((canvas)) pero
mostral información con ((texto)) posicionando un elemnto HTML en
la parte superior de la imagen.

{{index display}}

Para aplicaciones de baja demanda, no importa mucho que interfaz
escogas. El display que construimos para nuestro juego en este
capítulo podría haberse hecho implementando cual sea de estas tres
tecnologías de ((gráficos)) dado que no necesitamos dibujar texto,
manejar interacciones del mouse o trabajar con un numero de elementos
extraordinariamente grande.

## Resumen

En este capítulo discutimos técnicas para dibujo de gráficos en el
navegador, enfoncándonos en el elemento `<canvas>`.

Un nodo de canvas representa un área en un documento en el que nuestro
programa puede dibujar. Este dibujo se hace a través de objetos de
contexto de dibujo, usando el método `getContext`.

La interfaz de dibujo 2D nos permite rellenar y delineas varias figuras.
La propiedad `fillStyle` del contexto determina como las figuras son rellenadas.
Las propiedades `strokeStyle` y `lineWidth` controlan la manera en
que las líneas se dibujan.

Los rectángulos y textos se pueden dibujar con una simple llamada de método.
Los métodos `fillRect` y `strokeRect` dibujan rectángulos y los
métodos `fillText` y `strokeText` dibujan texto. Para crear 
figuras a medida, primero debemos crear una ruta.

{{index stroking, filling}}

Llamar a `beginPath` empieza una nueva ruta. Otra serie de métodos
agregan líneas y curvas a la ruta actual. Por ejemplo `lineTo` 
puede dibujar una línea reacta. Cuando la ruta de termina, puede
rellenarse con el método `fill` o delinearse con el método `stroke`.

Mover pixeles desde una imagen o desde un canvas a otro puede hacerse
con el método `drawImage`. Por defecto, este método dibuja la imagen
fuente completa, pero dándole parámetros, puedes copiar un área
específica de la imagen. La usamos para nuestro juego copiando poses
individuales del personajes salidas de una imagen que contiene
muchas poses.

Las transformaciones te permiten dibujar una figura en múltiples orientaciones
un contexto de dibujo 2D tiene transformaciones que se pueden usar
con los métodos `translate`, `scale` y `rotate`. Estos afectarán
todas las operaciones de dibujo subsecuentes. El estado de una
transformación se puede guardar con el método `save` y restaurar
con el método `restore`.

Cuando se muestra una animación en un canvas, el método `clearRect`
puede usarse para limpiar parte del canvas antes de redibujarlo.

## Ejercicios

### Figuras

{{index "shapes (exercise)"}}

Escribe un programa que dibuje las siguientes ((figura))s en un ((canvas)):

{{index rotation}}

1. Un ((trapezoide)) (un ((rectángle)) que es más inclinado de un lado).

2. Un ((diamante)) rojo (un rentángulo rotado 45 grados o ¼π radianes).

3. Una ((línea)) zigzaggeada.

4. Una ((espiral)) hecha de 100 segmentos de línea.

5. Una ((estrella)) amarilla.

{{figure {url: "img/exercise_shapes.png", alt: "Las figuras a dibujar",width: "8cm"}}}

Cuando dibujes las últimas dos, quizás te interese ver las explicaciones
de `Math.cos` y `Math.sin` en el  [capítulo ?](dom#sin_cos), que
describe como obtener coordenadas en un círculo usando estas funciones.

{{index readability, "hard-coding"}}

Recomiendo crear una función para cada figura. Pasa la prporción y
optionalmente otras propiedades como el tamaño o el número de puntos
como parámetros. La alternativa, que requiere escribir una numerología
complicada, tiende a hacer el código innecesariamente difícil de
leer y modificar.

{{if interactive

```{lang: "text/html", test: no}
<canvas width="600" height="200"></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");

  // Tu código va aquí.
</script>
```

if}}

{{hint

{{index [path, canvas], "shapes (exercise)"}}

El ((trapezoide)) (1) es el más sencillo de dibujar usando una ruta.
Escoge unas coordenadas para el centro y agrega una de las esquinas
alrededor del centro.

{{index "flipHorizontally function", rotation}}

El ((diamante)) (2) puede dibujarse de la forma simple, con una ruta,
o de la forma interesante, con una ((transformación)) `rotate`. 
Para usar rotación, tendrás que usar un truco similar al que usamos
con la función `voltearHorizontalmente`. Ya que lo que se quiere
es rotar alrededor del centro de su rectángulo y no alrededor del
punto (0,0), deberías primero usar `translate` ahí, luego rotar, y
trasladar de regreso.
Asegúrate de reiniciar la transformación después de dibujar cualquier
figura que se haya creado.

{{index "remainder operator", "% operator"}}

Para el ((zigzag)) (3) se vuelve impráctico escribir una nueva llamada
a `lineTo` para cada línea de segmento. En vez de eso, deberías 
usar un ((loop)). Puedes tener una iteración de dibujo por cada dos
segmentos de ((línea))s (derecha y luego izquierda de nuevo) o uno,
en cuyo caso deberías usar el operador (`% 2`) del índice del loop
para determinar la dirección a la derecha o a la izquierda.

También necesitarás usar un loop para el ((espiral)) (4). Sí dibujas
una serie de puntos, con cada punto moviéndose en un círculo alrededor
del centro de la espiral, obtienes un círculo. Sí durante el loop 
varías el radio del círculo en el que colocas el punto y lo repites,
el resultado es una espiral.

{{index "quadraticCurveTo method"}}

La ((estrella)) (5) se puede dibujar a partir de las líneas de `quadraticCurveTo`.
También podrías dibujar una con líneas gruesas. Divide un círculo
en ocho piezas para una estrella de ocho puntos, o cuantas piezas
quieras. Dibuja líneas entre estos puntos, haciéndolas curvas alrededor
del centro de la estrella. Con `quadraticCurveTo`, puedes hacer uso
del centro como punto de control.

hint}}

{{id exercise_pie_chart}}

### La gráfica de pastel

{{index label, text, "pie chart example"}}

[Anteriormente](canvas#pie_chart) en este capítulo, vimos el ejemplo
de un programa que dibuja una gráfica de pastel. Modifica este programa
de forma que el nombre de cada categoría este al lado de la sección
que representa. Trata de encontrar una forma que se posicione automáticamente
de forma adecuada que funcione con otros conjuntos de datos. Puedes
asumir que esas categorías son lo suficientemente amplias para las etiquetas.

Podrías necesitar `Math.sin` y `Math.cos` de nuevo, las cuales se 
describen en el [Capítulo ?](dom#sin_cos).

{{if interactive

```{lang: "text/html", test: no}
<canvas width="600" height="300"></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");
  let total = results
    .reduce((sum, {count}) => sum + count, 0);
  let currentAngle = -0.5 * Math.PI;
  let centerX = 300, centerY = 150;

  // Agrega código en este loop para dibujar las etiquetas 
  // de las secciones
  for (let result of results) {
    let sliceAngle = (result.count / total) * 2 * Math.PI;
    cx.beginPath();
    cx.arc(centerX, centerY, 100,
           currentAngle, currentAngle + sliceAngle);
    currentAngle += sliceAngle;
    cx.lineTo(centerX, centerY);
    cx.fillStyle = result.color;
    cx.fill();
  }
</script>
```

if}}

{{hint

{{index "fillText method", "textAlign property", "textBaseline property", "pie chart example"}}

Necesitarás llamar `fillText` y establecer el contexto de `textAlign`
y las propiedades de `textBaseline` de manera que el texto termine
donde quieres.

Una manera sensible de posicionar las etiquetas podría ser poner el
texto en la línea desde el centro de la gráfica a través de la mitad
de la sección.
No queremos que el texto quede directamente al lado de la gráfica,
sino moverlo al lado de la gráfica por unos cuantos píxeles.

El ((ángulo)) de está línea es `currentAngle + 0.5 * sliceAngle`. 
El siguiente código encuentra una posición en esta línea 120 píxels
desde el centro:

```{test: no}
let middleAngle = currentAngle + 0.5 * sliceAngle;
let textX = Math.cos(middleAngle) * 120 + centerX;
let textY = Math.sin(middleAngle) * 120 + centerY;
```

Para `textBaseline`, el valor `"middle"` es muy apropiado cuando
se quiere usar este enfoque. Para lo que se use `textAlign` depende
de que lado del círculo este. A la izquierda, deberá ser `"right"`, a la derecha, deberá ser `"left"`, para que el texto se coloque lejos de la gráfica.

{{index "Math.cos function"}}

Si no estamos seguros de como encontrar el lado del círculo dado
un ángulo, mira la explicación de `Math.cos` en el [Capítulo
?](dom#sin_cos). El coseno de un ángulo nos dice en que coordenada
en x le corresponde para saber exactamente en que lado del círculo
nos encontramos.

hint}}

### Un balón botador

{{index [animation, "bouncing ball"], "requestAnimationFrame function", bouncing}}

Usar la técnica de `requestAnimationFrame` que vimos en el [Capítulo
?](dom#animationFrame) y [Chapter ?](game#runAnimation) para dibujar una
((caja)) con un ((balón)) botando en el. El balón se mueve a una
((velocidad)) constante y bota fuera de la caja cuando golpea un borde de la caja.

{{if interactive

```{lang: "text/html", test: no}
<canvas width="400" height="400"></canvas>
<script>
  let cx = document.querySelector("canvas").getContext("2d");

  let lastTime = null;
  function frame(time) {
    if (lastTime != null) {
      updateAnimation(Math.min(100, time - lastTime) / 1000);
    }
    lastTime = time;
    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);

  function updateAnimation(step) {
    // Tu código va aquí.
  }
</script>
```

if}}

{{hint

{{index "strokeRect method", animation, "arc method"}}

Una ((caja)) es fácil de dibujar con `strokeRect`. Define los bordes
para su tamaño o define dos bordes si el ancho y largo de la caja
son distintos. Para crear el ((balón)), empieza una ruta y llama
`arc(x, y, radius, 0, 7)`, que crea un arco desde cero hasta algo
más de un círculo entero. Entonces rellena la ruta.

{{index "collision detection", "Vec class"}}

Para modelar la posición y ((velocidad)), puedes usar la clase `Vec`
del [Capítulo ?](game#vector)[ (que está disponible en está página
)]{if interactive}. Dada una velocidad inicial, preferentemente uno
que no es puramente vertical o horizontal, y para cada ((frame))
multiplica esa velocidad por el monto de tiempo que transcurra.
Cuando el balón esté cerca de un muro vertical, invierte el componente x
en su velocidad. De manera similar, invierte el componente y cuando
golpee un muro horizontal.

{{index "clearRect method", clearing}}

Después de encontrar la nueva posición y velocidad del balón, usa 
`clearRect` para borrar la escena y redibujarla usando la nueva posición.

hint}}

### Precomputed mirroring

{{index optimization, "bitmap graphics", mirror}}

One unfortunate thing about ((transformation))s is that they slow down
the drawing of bitmaps. The position and size of each ((pixel)) has to be
transformed, and though it is possible that ((browser))s will get
cleverer about transformation in the ((future)), they currently cause a
measurable increase in the time it takes to draw a bitmap.

In a game like ours, where we are drawing only a single transformed
sprite, this is a nonissue. But imagine that we need to draw hundreds
of characters or thousands of rotating particles from an explosion.

Think of a way to allow us to draw an inverted character without
loading additional image files and without having to make transformed
`drawImage` calls every frame.

{{hint

{{index mirror, scaling, "drawImage method"}}

The key to the solution is the fact that we can use a ((canvas))
element as a source image when using `drawImage`. It is possible to
create an extra `<canvas>` element, without adding it to the document,
and draw our inverted sprites to it, once. When drawing an actual
frame, we just copy the already inverted sprites to the main canvas.

{{index "load event"}}

Some care would be required because images do not load instantly. We
do the inverted drawing only once, and if we do it before the image
loads, it won't draw anything. A `"load"` handler on the image can be
used to draw the inverted images to the extra canvas. This canvas can
be used as a drawing source immediately (it'll simply be blank until
we draw the character onto it).

hint}}

