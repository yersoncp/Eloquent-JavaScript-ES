# El Modelo de Objeto del Documento

{{quote {author: "Friedrich Nietzsche", title: "Más allá del bien y del mal", chapter: true}

¡Tanto peor! ¡Otra vez la vieja historia! Cuando uno ha acabado de
construir su casa advierte que, mientras la construía, ha aprendido, sin darse
cuenta, algo que tendría que haber sabido absolutamente antes de comenzar
a construir.

quote}}

{{figure {url: "img/chapter_picture_14.jpg", alt: "Foto de un árbol con letras y _scripts_ colgando de sus ramas", chapter: "framed"}}}

{{index drawing, parsing}}

Cuando abres una página web en tu navegador, el navegador obtiene el
texto de la página ((HTML)) y lo analiza, de una manera bastante
similar a la manera en que nuestro analizador del [Capítulo ?](language#parsing)
analizaba los programas. El navegador construye un modelo de la
((estructura)) del documento y utiliza este modelo para dibujar la
página en la pantalla.


{{index "live data structure"}}

Esta representación del ((documento)) es uno de los juguetes que un
programa de JavaScript tiene disponible en su ((caja de arena)). Es
una ((estructura de datos)) que puedes leer o modificar. Y actúa como
una estructura en _tiempo real_: cuando se modifica, la página en la
pantalla es actualizada para reflejar los cambios.

## La estructura del documento

{{index [HTML, structure]}}

Te puedes imaginar a un documento HTML como un conjunto anidado de
((caja))s. Las etiquetas como `<body>` y `</body>` encierran otras
((etiqueta))s, que a su vez, contienen otras etiquetas o ((texto)).
Este es el documento de ejemplo del
[capítulo anterior](browser):


```{lang: "text/html", sandbox: "homepage"}
<!doctype html>
<html>
  <head>
    <title>Mi página de inicio</title>
  </head>
  <body>
    <h1>Mi página de inicio</h1>
    <p>Hola, mi nombre es Marijn y esta es mi página de inicio.</p>
    <p>También escribí un libro! Léelo
      <a href="http://eloquentjavascript.net">aquí</a>.</p>
  </body>
</html>
```

Esta página tiene la siguiente estructura:

{{figure {url: "img/html-boxes.svg", alt: "HTML document as nested boxes", width: "7cm"}}}

{{indexsee "Document Object Model", DOM}}

La estructura de datos que el navegador utiliza para representar el
documento sigue esta figura. Para cada caja, hay un objeto, con el que
podemos interactuar para descubrir cosas como que etiqueta de HTML
representa y que cajas y texto contiene. Esta representación es llamada
_Modelo de Objeto del Documento_ o ((DOM)) _(por sus siglas en inglés
"Document Object Model")_.

{{index "documentElement property", "head property", "body property", "html (HTML tag)", "body (HTML tag)", "head (HTML tag)"}}

El objeto de enlace global `document` nos da acceso a esos objetos. Su
propiedad `documentElement` hace referencia al objeto que representa
a la etiqueta `<html>`. Dado que cada documento HTML tiene una cabecera
y un cuerpo, también tiene propiedades `head` y `body` que apuntan a
esos elementos.

## Árboles

{{index [nesting, "of objects"]}}

Pensemos en los ((árbol))es ((sintáctico))s del [Capítulo ?](language#parsing)
por un momento. Sus estructuras son sorprendentemente similares a la
estructura de un documento del navegador. Cada _((nodo))_ puede
referirse a otros nodos _hijos_ que, a su vez, pueden tener hijos
propios. Esta forma es típica de las estructuras anidadas donde los
elementos pueden contener sub elementos que son similares a ellos
mismos.

{{index "documentElement property", [DOM, tree]}}

Le damos el nombre de _((árbol))_ a una estructura de datos cuando
tiene una estructura de ramificación, no tiene ((ciclo))s (un nodo
no puede contenerse a sí mismo, directa o indirectamente) y tiene
una única _((raíz))_ bien definida. En el caso del _DOM_,
`document.documentElement` hace la función de raíz.

{{index sorting, ["data structure", "tree"], "syntax tree"}}

Los árboles aparecen constantemente en las ciencias de la computación
_(computer sience)_. Además de representar estructuras recursivas como
los documentos HTML o programas, también son comúnmente usados para
mantener ((conjunto))s ordenados de datos debido a que los elementos
generalmente pueden ser encontrados o agregados más eficientemente en
un árbol que en un arreglo plano.

{{index "leaf node", "Egg language"}}

Un árbol típico tiene diferentes tipos de ((nodo))s. El árbol sintáctico
del [lenguaje _Egg_](language) tenía identificadores, valores y nodos
de aplicación. Los nodos de aplicación pueden tener hijos, mientras
que los identificadores y valores son _hojas_, o nodos sin hijos.

{{index "body property", [HTML, structure]}}

Lo mismo sucede para el DOM, los nodos para los _((elemento))s_,
los cuales representan etiquetas HTML, determinan la estructura del
documento. Estos pueden tener ((nodos hijos)). Un ejemplo de estos
nodos es `document.body`. Algunos de estos hijos pueden ser
((nodos hoja)), como los fragmentos de ((texto)) o los nodos
((comentario)).

{{index "text node", element, "ELEMENT_NODE code", "COMMENT_NODE code", "TEXT_NODE code", "nodeType property"}}

Cada objeto _nodo DOM_ tiene una propiedad `nodeType`, la cual
contiene un código (numérico) que identifica el tipo de nodo. Los
_Elementos_ tienen el código 1, que también es definido por la
propiedad constante `Node.ELEMENT_NODE`. Los nodos de texto, representando
una sección de texto en el documento, obtienen el código 3
(`Node.TEXT_NODE`). Los comentarios obtienen el código 8
(`Node.COMMENT_NODE`).

Otra forma de visualizar nuestro ((árbol)) de documento es la
siguiente:

{{figure {url: "img/html-tree.svg", alt: "HTML document as a tree",width: "8cm"}}}

Las hojas son nodos de texto, y las flechas nos indican las relaciones
padre-hijo entre los nodos.

{{id standard}}

## El estándar

{{index "programming language", [interface, design], [DOM, interface]}}

Usar códigos numéricos crípticos para representar a los tipos de
nodos no es algo que se parezca al estilo de JavaScript para hacer
las cosas. Más adelante en este capítulo, veremos cómo otras partes
de la interfaz DOM también se sienten engorrosas y alienígenas.
La razón de esto es que DOM no fue diseñado solamente para JavaScript.
Más bien, intenta ser una interfaz _independiente del lenguaje_ que
puede ser usada en otros sistemas, no solamente para HTML pero
también para ((XML)), que es un ((formato de datos)) genérico con
una sintaxis similar a la de HTML.

{{index consistency, integration}}

Esto es desafortunado. Usualmente los estándares son bastante útiles.
Pero en este caso, la ventaja (consistencia entre lenguajes) no es tan
conveniente. Tener una interfaz que está propiamente integrada con el
lenguaje que estás utilizando te ahorrará más tiempo que tener una
interfaz familiar en distintos lenguajes.

{{index "array-like object", "NodeList type"}}

A manera de ejemplo de esta pobre integración, considera la propiedad
`childNodes` que los nodos elemento en el DOM tienen. Esta propiedad
almacena un objeto parecido a un arreglo, con una propiedad `length`
y propiedades etiquetadas por números para acceder a los nodos hijos.
Pero es una instancia de tipo `NodeList`, no un arreglo real, por
lo que no tiene métodos como `slice` o `map`.

{{index [interface, design], [DOM, construction], "side effect"}}

Luego, hay problemas que son simplemente un pobre diseño. Por ejemplo,
no hay una manera de crear un nuevo nodo e inmediatamente agregar
hijos o ((attributo))s. En vez de eso, tienes que crearlo primero
y luego agregar los hijos y atributos uno por uno, usando efectos
secundarios. El código que interactúa mucho con el DOM tiende a ser
largo, repetitivo y feo.

{{index library}}

Pero estos defectos no son fatales. Dado que JavaScript nos permite
crear nuestra propias ((abstraccion))es, es posible diseñar formas
mejoradas para expresar las operaciones que estás realizando. Muchas
bibliotecas destinadas a la programación del navegador vienen con
esas herramientas.

## Moviéndose a través del árbol

{{index pointer}}

Los nodos del DOM contienen una amplia cantidad de ((enlace))s a
otros nodos cercanos. El siguiente diagrama los ilustra:

{{figure {url: "img/html-links.svg", alt: "Links between DOM nodes",width: "6cm"}}}

{{index "child node", "parentNode property", "childNodes property"}}

A pesar de que el diagrama muestra un solo enlace por cada tipo, cada
nodo tiene una propiedad `parentNode` que apunta al nodo al que
pertenece, si es que hay alguno. Igualmente, cada nodo _elemento_
(nodo tipo 1) tiene una propiedad `childNodes` que apunta a un objeto
similar a un arreglo que almacena a sus hijos.

{{index "firstChild property", "lastChild property", "previousSibling property", "nextSibling property"}}

En teoría, te deberías poder mover donde quieras en el árbol
utilizando únicamente estos enlaces entre padre e hijo. Pero JavaScript
también te otorga acceso a un número de enlaces adicionales convenientes.
Las propiedades `firstChild` y `lastChild` apuntan al primer y último
elementos hijo, o tiene el valor `null` para nodos sin hijos. De
manera similar, las propiedades `previousSibling` y `nextSibling`
apuntan a los nodos adyacentes, los cuales, son nodos con el mismo
padre que aparecen inmediatamente antes o después del nodo. Para el
primer hijo `previousSibling` será `null` y para el último hijo,
`nextSibling` será `null`.

{{index "children property", "text node", element}}

También existe una propiedad `children`, que es parecida a `childNodes`
pero contiene únicamente hijos de tipo _elemento_ (tipo 1), excluyendo
otros tipos de nodos. Esto puede ser útil cuando no estás interesando
en nodos de tipo texto.

{{index "talksAbout function", recursion, [nesting, "of objects"]}}

Cuando estás tratando con estructuras de datos anidadas como esta,
las funciones recursivas son generalmente útiles. La siguiente función
escanea un documento por ((nodos de texto)) que contengan una cadena
dada y regresan `true` en caso de que encuentren una:

{{id talksAbout}}

```{sandbox: "homepage"}
function hablaSobre(nodo, cadena) {
  if (nodo.nodeType == Node.ELEMENT_NODE) {
    for (let i = 0; i < nodo.childNodes.length; i++) {
      if (hablaSobre(nodo.childNodes[i], cadena)) {
        return true;
      }
    }
    return false;
  } else if (nodo.nodeType == Node.TEXT_NODE) {
    return nodo.nodeValue.indexOf(cadena) > -1;
  }
}

console.log(hablaSobre(document.body, "libro"));
// → true
```

{{index "childNodes property", "array-like object", "Array.from function"}}

Debido a que `childNodes` no es un arreglo real, no podemos iterar
sobre el usando `for`/`of` por lo que tenemos que iterar sobre el rango
del índice usando un `for` regular o usando `Array.from`.

{{index "nodeValue property"}}

La propiedad `nodeValue` de un nodo de texto almacena la cadena de
texto que representa.

## Buscar elementos

{{index [DOM, querying], "body property", "hard-coding", [whitespace, "in HTML"]}}

Navegar por estos ((enlace))s entre padres, hijos y hermanos suele
ser útil. Pero si queremos encontrar un nodo específico en el documento,
alcanzarlo comenzando en `document.body` y siguiendo un camino fijo de
propiedades es una mala idea. Hacerlo genera suposiciones en nuestro
programa sobre la estructura precisa del documento que tal vez quieras
cambiar después. Otro factor complicado es que los nodos de texto son
creados incluso para los espacios en blanco entre nodos. La etiqueta
`<body>` en el documento de ejemplo no tiene solamente tres hijos
(`<h1>` y dos elementos `<p>`), en realidad tiene siete: esos tres,
más los espacios posteriores y anteriores entre ellos.

{{index "search problem", "href attribute", "getElementsByTagName method"}}

Por lo que si queremos obtener el atributo `href` del enlace en ese
documento, no queremos decir algo como "Obten el segundo hijo del
sexto hijo del elemento _body_ del documento". Sería mejor si pudiéramos
decir "Obten el primer enlace en el documento". Y de hecho podemos.

```{sandbox: "homepage"}
let link = document.body.getElementsByTagName("a")[0];
console.log(link.href);
```

{{index "child node"}}

Todos los nodos _elemento_ tienen un método `getElementsByTagName`, el
cual recolecta a todos los elementos con un nombre de etiqueta dado que
son descendientes (hijos directos o indirectos) de ese nodo y los
regresa como un objeto parecido a un arreglo.

{{index "id attribute", "getElementById method"}}

Para encontrar un _único_ nodo en específico, puedes otorgarle un
atributo `id` y usar `document.getElementById`.

```{lang: "text/html"}
<p>Mi avestruz Gertrudiz:</p>
<p><img id="gertrudiz" src="img/ostrich.png"></p>

<script>
  let avestruz = document.getElementById("gertrudiz");
  console.log(avestruz.src);
</script>
```

{{index "getElementsByClassName method", "class attribute"}}

Un tercer método similar es `getElementsByClassName`, el cual, de
manera similar a `getElementsByTagName` busca a través de los
contenidos de un nodo elemento y obtiene todos los elementos que
tienen una cadena dada en su attributo `class`.

## Actualizar el documento

{{index "side effect", "removeChild method", "appendChild method", "insertBefore method", [DOM, construction], [DOM, modification]}}

Prácticamente todo sobre la estructura de datos DOM puede ser cambiado.
La forma del árbol de documento puede ser modificada cambiando las
relaciones padre-hijo. Los nodos tienen un método `remove` para ser
removidos de su nodo padre actual. Para agregar un nodo hijo a un
nodo elemento, podemos usar `appendChild`, que lo pondrá al final de
la lista de hijos, o `insertBefore`, que insertará el nodo en el
primer argumento antes del nodo en el segundo argumento.

```{lang: "text/html"}
<p>Uno</p>
<p>Dos</p>
<p>Tres</p>

<script>
  let parrafos = document.body.getElementsByTagName("p");
  document.body.insertBefore(parrafos[2], parrafos[0]);
</script>
```

Un nodo puede existir en el documento solamente en un lugar. En
consecuencia, insertar el párrafo _Tres_ enfrente del párrafo _Uno_
primero lo removerá del final del documento y luego lo insertará
en la parte delantera, resultando en _Tres_/_Uno_/_Dos_. Todas las operaciones
que insertan un nodo en alguna parte causarán, a modo de
((efecto secundario)), que el nodo sea removido de su posición actual
(si es que tiene una).

{{index "insertBefore method", "replaceChild method"}}

El método `replaceChild` es usado para reemplazar a un nodo hijo con
otro. Toma dos nodos como argumentos: un nuevo nodo y el nodo que será
reemplazado. El nodo reemplazado debe ser un nodo hijo del elemento
desde donde se está llamando el método. Nótese que tanto `replaceChild`
como `insertBefore` esperan que el _nuevo_ nodo sea el primer argumento.

## Crear nodos

{{index "alt attribute", "img (HTML tag)"}}

Digamos que queremos escribir un _script_ que reemplace todas las
((imagen))es (etiquetas `<img>`) en el documento con el texto contenido
en sus atributos `alt`, los cuales especifican una representación
textual alternativa de la imagen.

{{index "createTextNode method"}}

Esto no solamente involucra remover las imágenes, si no que también
involucra agregar un nuevo nodo texto que las reemplace. Los nodos texto
son creados con el método `document.createTextNode`.

```{lang: "text/html"}
<p>El <img src="img/cat.png" alt="Gato"> en el
  <img src="img/hat.png" alt="Sombrero">.</p>

<p><button onclick="sustituirImagenes()">Sustituir</button></p>

<script>
  function sustituirImagenes() {
    let imagenes = document.body.getElementsByTagName("img");
    for (let i = imagenes.length - 1; i >= 0; i--) {
      let imagen = imagenes[i];
      if (imagen.alt) {
        let texto = document.createTextNode(imagen.alt);
        imagen.parentNode.replaceChild(texto, imagen);
      }
    }
  }
</script>
```

{{index "text node"}}

Dada una cadena, `createTextNode` nos da un nodo texto que podemos
insertar en el documento para hacer que aparezca en la pantalla.

{{index "live data structure", "getElementsByTagName method", "childNodes property"}}

El ciclo que recorre las imágenes empieza al final de la lista. Esto es
necesario dado que la lista de nodos regresada por un método como
`getElementsByTagName` (o una propiedad como `childNodes`) se actualiza
en tiempo real. Esto es, que se actualiza conforme el documento cambia.
Si empezáramos desde el frente, remover la primer imagen causaría que
la lista perdiera su primer elemento de tal manera que la segunda
ocasión que el ciclo se repitiera, donde `i` es 1, se detendría dado que
la longitud de la colección ahora es también 1.

{{index "slice method"}}

Si quieres una colección de nodos _sólida_, a diferencia de una en
tiempo real, puedes convertir la colección a un arreglo real llamando
`Array.from`.

```
let casi_arreglo = {0: "uno", 1: "dos", length: 2};
let arreglo = Array.from(casi_arreglo);
console.log(arreglo.map(s => s.toUpperCase()));
// → ["UNO", "DOS"]
```

{{index "createElement method"}}

Para crear nodos ((elemento)), puedes utilizar el método
`document.createElement`. Este método toma un nombre de etiqueta y
regresa un nuevo nodo vacío del tipo dado.

{{index "Popper, Karl", [DOM, construction], "elt function"}}

{{id elt}}

El siguiente ejemplo define una utilidad `elt`, la cual crea un
elemento nodo y trata el resto de sus argumentos como hijos de ese
nodo. Luego, esta función es utilizada para agregar una atribución
a una cita.

```{lang: "text/html"}
<blockquote id="cita">
  Ningún libro puede terminarse jamás. Mientras se trabaja en
  él aprendemos solo lo suficiente para encontrar inmaduro
  el momento en el que nos alejamos de él.
</blockquote>

<script>
  function elt(tipo, ...hijos) {
    let nodo = document.createElement(tipo);
    for (let hijo of hijos) {
      if (typeof hijo != "string") nodo.appendChild(hijo);
      else nodo.appendChild(document.createTextNode(hijo));
    }
    return nodo;
  }

  document.getElementById("cita").appendChild(
    elt("footer", "—",
        elt("strong", "Karl Popper"),
        ", prefacio de la segunda edición de ",
        elt("em", "La sociedad abierta y sus enemigos"),
        ", 1950"));
</script>
```

{{if book

Así es como se ve el documento resultante:

{{figure {url: "img/blockquote.png", alt: "A blockquote with attribution",width: "8cm"}}}

if}}

## Atributos

{{index "href attribute", [DOM, attributes]}}

Los ((atributo))s de algunos elementos, como `href` para los enlaces,
pueden ser accedidos a través de una propiedad con el mismo nombre en
el objeto ((DOM)) del elemento. Este es el caso para los atributos
estándar más comúnmente utilizados.

{{index "data attribute", "getAttribute method", "setAttribute method", attribute}}

Pero HTML te permite establecer cualquier atributo que quieras en los
nodos. Esto puede ser útil debido a que te permite almacenar información
extra en un documento. Sin embargo, si creas tus propios nombres de
atributo, dichos atributos no estarán presentes como propiedades en el
nodo del elemento. En vez de eso, tendrás que utilizar los métodos
`getAttribute` y `setAttribute` para poder trabajar con ellos.

```{lang: "text/html"}
<p data-classified="secreto">El código de lanzamiento es: 00000000.</p>
<p data-classified="no-classificado">Yo tengo dos pies.</p>

<script>
  let parrafos = document.body.getElementsByTagName("p");
  for (let parrafo of Array.from(parrafos)) {
    if (parrafo.getAttribute("data-classified") == "secreto") {
      parrafo.remove();
    }
  }
</script>
```

Se recomienda anteponer los nombres de dichos atributos inventados con
`data-` para asegurarse de que no conflictúan con ningún otro atributo.

{{index "getAttribute method", "setAttribute method", "className property", "class attribute"}}

Existe un atributo comúnmente usado, `class`, que es una ((palabra clave))
en el lenguaje JavaScript. Por motivos históricos, algunas
implementaciones antiguas de JavaScript podrían no manejar nombres de
propiedades que coincidan con las palabras clave, la propiedad utilizada
para acceder a este atributo tiene por nombre `className`. También
puedes acceder a ella bajo su nombre real, `"class"`, utilizando los
métodos `getAttribute` y `setAttribute`.

## Layout

{{index layout, "block element", "inline element", "p (HTML tag)", "h1 (HTML tag)", "a (HTML tag)", "strong (HTML tag)"}}

Tal vez hayas notado que diferentes tipos de elementos se exponen
de manera distinta. Algunos, como en el caso de los párrafos
(`<p>`) o encabezados (`<h1>`), ocupan todo el ancho del documento
y se renderizan en líneas separadas. A estos se les conoce como
elementos _block_ (o bloque). Otros, como los enlaces (`<a>`) o el
elemento `<strong>`, se renderizan en la misma línea con su texto
circundante. Dichos elementos se les conoce como elementos
_inline_ (o en línea).

{{index drawing}}

Para cualquier documento dado, los navegadores son capaces de calcular
una estructura _(layout)_, que le da a cada elemento un tamaño y una
posición basada en el tipo y el contenido. Luego, esta estructura se
utiliza para trazar el documento.

{{index "border (CSS)", "offsetWidth property", "offsetHeight property", "clientWidth property", "clientHeight property", dimensions}}

Se puede acceder al tamaño y la posición de un elemento desde
JavaScript. Las propiedades `offsetWidth` y `offsetHeight` te dan el
espacio que el elemento utiliza en ((pixel))es. Un píxel es la unidad
básica de las medidas del navegador. Tradicionalmente correspondía al
punto más pequeño que la pantalla podía trazar, pero en los monitores
modernos, que pueden trazar puntos _muy_ pequeños, este puede no ser
más el caso, por lo que un píxel del navegador puede abarcar varios
puntos en la pantalla.

De manera similar, `clientWidth` y `clientHeight` te dan el tamaño
del espacio _dentro_ del elemento, ignorando la anchura del borde.

```{lang: "text/html"}
<p style="border: 3px solid red">
  Estoy dentro de una caja
</p>

<script>
  let parrafo = document.body.getElementsByTagName("p")[0];
  console.log("clientHeight:", parrafo.clientHeight);
  console.log("offsetHeight:", parrafo.offsetHeight);
</script>
```

{{if book

Darle un borde a un párrafo hace que se dibuje un rectángulo a su alrededor.

{{figure {url: "img/boxed-in.png", alt: "A paragraph with a border",width: "8cm"}}}

if}}

{{index "getBoundingClientRect method", position, "pageXOffset property", "pageYOffset property"}}

{{id boundingRect}}

La manera más efectiva de encontrar la posición precisa de un elemento
en la pantalla es el método `getBoundingClientRect`. Este devuelve un
objeto con las propiedades `top`, `bottom`, `left`, y `right`, indicando
las posiciones de pixeles de los lados el elemento en relación con la
parte superior izquierda de la pantalla. Si los quieres en relación a
todo el documento, deberás agregar la posición actual del scroll, la
cual puedes obtener en los _bindings_ `pageXOffset` y `pageYOffset`.

{{index "offsetHeight property", "getBoundingClientRect method", drawing, laziness, performance, efficiency}}

Estructurar un documento puede requerir mucho trabajo. En los
intereses de velocidad, los motores de los navegadores no
reestructuran inmediatamente un documento cada vez que lo cambias,
en cambio, se espera lo más que se pueda. Cuando un programa de
JavaScript que modifica el documento termina de ejecutarse, el
navegador tendrá que calcular una nueva estructura para trazar el
documento actualizado en la pantalla. Cuando un programa _solicita_
la posición o el tamaño de algo, leyendo propiedades como `offsetHeight`
o llamando a `getBoundingClientRect`, proveer la información correcta
también requiere que se calcule una nueva estructura.

{{index "side effect", optimization, benchmark}}

A un programa que alterna repetidamente entre leer la información de la
estructura DOM y cambiar el DOM, fuerza a que haya bastantes cálculos
de estructura, y por consecuencia se ejecutará lentamente. El siguiente
código es un ejemplo de esto. Contiene dos programas diferentes que
construyen una línea de _X_ caracteres con 2,000 pixeles de ancho y
que mide el tiempo que toma cada uno.

```{lang: "text/html", test: nonumbers}
<p><span id="uno"></span></p>
<p><span id="dos"></span></p>

<script>
  function tiempo(nombre, accion) {
    let inicio = Date.now(); // Tiempo actual en milisegundos
    accion();
    console.log(nombre, "utilizo", Date.now() - inicio, "ms");
  }

  tiempo("inocente", () => {
    let objetivo = document.getElementById("uno");
    while (objetivo.offsetWidth < 2000) {
      objetivo.appendChild(document.createTextNode("X"));
    }
  });
  // → inocente utilizo 32 ms

  tiempo("ingenioso", function() {
    let objetivo = document.getElementById("dos");
    objetivo.appendChild(document.createTextNode("XXXXX"));
    let total = Math.ceil(2000 / (objetivo.offsetWidth / 5));
    objetivo.firstChild.nodeValue = "X".repeat(total);
  });
  // → ingenioso utilizo 1 ms
</script>
```

## Estilización

{{index "block element", "inline element", style, "strong (HTML tag)", "a (HTML tag)", underline}}

Hemos visto que diferentes elementos HTML se trazan de manera diferente.
Algunos son desplegados como bloques, otros en línea. Algunos agregan
estilos, por ejemplo `<strong>` hace que su contenido esté en
((negritas)) y `<a>` lo hace azul y lo subraya.

{{index "img (HTML tag)", "default behavior", "style attribute"}}

La forma en la que una etiqueta `<img>` muestra una imagen o una
etiqueta `<a>` hace que un enlace sea seguido cuando se hace click en
el, está fuertemente atado al tipo del elemento. Pero podemos cambiar los
estilos asociados a un elemento, tales como el color o si está subrayado.
Este es un ejemplo que utiliza la propiedad `style`:

```{lang: "text/html"}
<p><a href=".">Enlace normal</a></p>
<p><a href="." style="color: green">Enlace verde</a></p>
```

{{if book

El segundo enlace será verde en vez del color por defecto.

{{figure {url: "img/colored-links.png", alt: "A normal and a green link",width: "2.2cm"}}}

if}}

{{index "border (CSS)", "color (CSS)", CSS, "colon character"}}

Un atributo _style_ puede llegar a contener una o más declaraciones, que
consisten en una propiedad (como `color`) seguido del símbolo de dos puntos
y un valor (como `green`). Cuando hay más de una declaración, estas deben
ser separadas por ((punto y coma)), como en `"color: red; border: none"`.

{{index "display (CSS)", layout}}

Muchos de los aspectos del documento pueden ser influenciados por la
estilización. Por ejemplo, la propiedad `display` controla si un
elemento es desplegado como un bloque o como un elemento en línea.

```{lang: "text/html"}
El texto es desplegado <strong>en línea</strong>,
<strong style="display: block">como bloque</strong>, y
<strong style="display: none">no se despliega</strong>.
```

{{index "hidden element"}}

La etiqueta `block` terminará en su propia línea dado que los elementos
_((bloque))_ no son desplegados en línea con el texto que los rodea.
La última etiqueta no se despliega—`display: none` previene que el
elemento sea mostrado en la pantalla. Esta es una manera de ocultar
elementos. A menudo esto es preferido sobre removerlos completamente
del documento debido a que hace más fácil mostrarlos nuevamente en el
futuro.

{{if book

{{figure {url: "img/display.png", alt: "Different display styles",width: "4cm"}}}

if}}

{{index "color (CSS)", "style attribute"}}

El código de JavaScript puede manipular directamente el estilo de un
elemento a través de la propiedad `style` del elemento. Esta propiedad
almacena un objeto que tiene propiedades por todas las posibles
propiedades de estilo. Los valores de esas propiedades son cadenas, que
podemos escribir para cambiar un aspecto en particular del estilo del
elemento.

```{lang: "text/html"}
<p id="parrafo" style="color: purple">
  Texto mejorado
</p>

<script>
  let parrafo = document.getElementById("parrafo");
  console.log(parrafo.style.color);
  parrafo.style.color = "magenta";
</script>
```

{{index "camel case", capitalization, "hyphen character", "font-family (CSS)"}}

Algunos nombres de propiedades pueden contener guiones, como es el caso
de `font-family`. Dado que estos nombres de propiedades son incómodos
para trabajar con ellos en JavaScript (tendrías que decir
`style["font-family"]`), los nombres de propiedades en el objeto `style`
para tales propiedades no tendrán guiones y las letra después del guion
estará en mayúsculas (`style.fontFamily`).

## Estilos en Cascada

{{index "rule (CSS)", "style (HTML tag)"}}

{{indexsee "Cascading Style Sheets", CSS}}
{{indexsee "style sheet", CSS}}

El sistema de estilos para HTML es llamado ((CSS)) por sus siglas en
ingles _Cascading Style Sheets_ (Hojas de estilo en cascada). Una
_hoja de estilos_ es un conjunto de reglas sobre cómo estilizar
a los elementos en un documento. Puede estar declarado dentro de
una etiqueta `<style>`.

```{lang: "text/html"}
<style>
  strong {
    font-style: italic;
    color: gray;
  }
</style>
<p>Ahora <strong>el texto en negritas</strong> esta en italicas y es gris.</p>
```

{{index "rule (CSS)", "font-weight (CSS)", overlay}}

La sección _cascada_ en el nombre se refiere al hecho de que varias
reglas son combinadas para producir el estilo final para un elemento.
En el ejemplo, el estilo por defecto para las etiquetas `<strong>`, que
les da `font-weight: bold`, es superpuesto por la regla en la etiqueta
`<style>`, que le agrega `font-style` y `color`.

{{index "style (HTML tag)", "style attribute"}}

Cuando varias reglas definen un valor para una misma propiedad, la
regla leída más recientemente obtiene una mayor ((precedencia))
y gana. Por lo que si la regla en la etiqueta `<style>` incluyera
`font-weight: normal`, contradiciendo la regla por defecto de
`font-weight`, el texto se vería normal, _no_ en negritas. Los estilos
en un atributo `style` aplicados directamente al nodo tienen la
mayor precedencia y siempre ganan.

{{index uniqueness, "class attribute", "id attribute"}}

Es posible apuntar a otras cosas que no sean nombres de ((etiqueta))
en las reglas CSS. Una regla para `.abc` aplica a todos los elementos
con `"abc"` en su atributo `class`. Una regla para `#xyz` aplica a
todos los elementos con un atributo `id` con valor `"xyz"` (que debería
ser único en el documento).


```{lang: "text/css"}
.sutil {
  color: gray;
  font-size: 80%;
}
#cabecera {
  background: blue;
  color: white;
}
/* Elementos p con un id principal y clases a y b */
p#principal.a.b {
  margin-bottom: 20px;
}
```

{{index "rule (CSS)"}}

La regla de precedencia que favorece a las reglas más recientemente
definidas aplican solamente cuando las reglas tienen la misma
_((especificidad))_. La especificidad de una regla es una medida de que
tan precisamente describe a los elementos que coinciden con la regla,
determinado por el número y tipo (etiqueta, clase o ID) de los
aspectos del elemento que requiera. Por ejemplo, una regla que apunta
a `p.a` es más específica que las reglas que apuntan a `p` o solamente
a `.a` y tendrá precedencia sobre ellas.

{{index "direct child node"}}

La notación `p > a {…}` aplica los estilos dados a todas las etiquetas
`<a>` que son hijas directas de las etiquetas `<p>`. De manera similar,
`p a {…}` aplica a todas las etiquetas `<a>` dentro de etiquetas `<p>`,
sin importar que sean hijas directas o indirectas.

## Selectores de consulta

{{index complexity, CSS}}

No utilizaremos mucho las hojas de estilo en este libro. Entenderlas
es útil cuando se programa en el navegador, pero son lo suficientemente
complicadas para justificar un libro por separado.

{{index "domain-specific language", [DOM, querying]}}

La principal razón por la que introduje la sintaxis de ((selección))—la
notación usada en las hojas de estilo para determinar a cuales elementos
aplicar un conjunto de estilos—es que podemos utilizar el mismo
mini-lenguaje como una manera efectiva de encontrar elementos DOM.

{{index "querySelectorAll method", "NodeList type"}}

El método `querySelectorAll`, que se encuentra definido tanto en el
objeto `document` como en los nodos elemento, toma la cadena de un
selector y regresa una `NodeList` que contiene todos los elementos
que coinciden con la consulta.

```{lang: "text/html"}
<p>And if you go chasing
  <span class="animal">rabbits</span></p>
<p>And you know you're going to fall</p>
<p>Tell 'em a <span class="caracter">hookah smoking
  <span class="animal">caterpillar</span></span></p>
<p>Has given you the call</p>

<script>
  function contar(selector) {
    return document.querySelectorAll(selector).length;
  }
  console.log(contar("p"));           // Todos los elementos <p>
  // → 4
  console.log(contar(".animal"));     // Clase animal
  // → 2
  console.log(contar("p .animal"));   // Animales dentro de <p>
  // → 2
  console.log(contar("p > .animal")); // Hijos directos de <p>
  // → 1
</script>
```

{{index "live data structure"}}

A diferencia de métodos como `getElementsByTagName`, el objeto que
regresa `querySelectorAll` no es un objeto en tiempo real. No cambiará
cuando cambies el documento. Sin embargo, sigue sin ser un arreglo real,
por lo que aún necesitas llamar a `Array.from` si lo quieres tratar como
uno real.

{{index "querySelector method"}}

El método `querySelector` (sin la parte de `All`) trabaja de una manera
similar. Este es útil si quieres un único elemento en específico. Regresará
únicamente el primer elemento que coincida o _null_ en el caso que
ningún elemento coincida.

{{id animation}}

## Posicionamiento y animaciones

{{index "position (CSS)", "relative positioning", "top (CSS)", "left (CSS)", "absolute positioning"}}

La propiedad de estilo `position` influye de un manera poderosa sobre
la estructura. Por defecto, tiene el valor de `static`, eso significa
que el elemento se coloca en su lugar normal en el documento. Cuando
se establece como `relative`, el elemento sigue utilizando espacio en el
documento pero ahora las propiedades `top` y `left` pueden ser
utilizadas para moverlo relativamente a ese espacio normal. Cuando
`position` se establece como `absolute`, el elemento es removido del
flujo normal del documento—esto es, deja de tomar espacio y puede
encimarse con otros elementos. Además, sus propiedades `top` y `left`
pueden ser utilizadas para posicionarlo absolutamente con relación a
la esquina superior izquierda del elemento envolvente más cercano cuya
propiedad `position` no sea `static`, o con relación al documento si dicho
elemento envolvente no existe.

{{index [animation, "spinning cat"]}}

Podemos utilizar esto para crear una animación. El siguiente
documento despliega una imagen de un gato que se mueve alrededor
de una ((elipse)):

```{lang: "text/html", startCode: true}
<p style="text-align: center">
  <img src="img/cat.png" style="position: relative">
</p>
<script>
  let gato = document.querySelector("img");
  let angulo = Math.PI / 2;
  function animar(tiempo, ultimoTiempo) {
    if (ultimoTiempo != null) {
      angulo += (tiempo - ultimoTiempo) * 0.001;
    }
    gato.style.top = (Math.sin(angulo) * 20) + "px";
    gato.style.left = (Math.cos(angulo) * 200) + "px";
    requestAnimationFrame(nuevoTiempo => animar(nuevoTiempo, tiempo));
  }
  requestAnimationFrame(animar);
</script>
```

{{if book

La flecha gris indica el camino por el que se mueve la imagen.

{{figure {url: "img/cat-animation.png", alt: "A moving cat head",width: "8cm"}}}

if}}

{{index "top (CSS)", "left (CSS)", centering, "relative positioning"}}

Nuestra imagen se centra en la página y se le da un valor para `position`
de `relative`. Actualizaremos repetidamente los estilos `top` y `left`
de la imagen para moverla.

{{index "requestAnimationFrame function", drawing, animation}}

{{id animationFrame}}

El script utiliza `requestAnimationFrame` para programar la función
`animar` para ejecutarse en el momento en el que el navegador está
listo para volver a pintar la pantalla. La misma función `animar`
llama a `requestAnimationFrame` otra vez para programar la siguiente
actualización. Cuando la ventana del navegador (o pestaña) está activa,
esto causará que sucedan actualizaciones a un rango de aproximadamente
60 actualizaciones por segundo, lo que tiende a producir una animación
agradable a la vista.

{{index timeline, blocking}}

Si únicamente actualizáramos el DOM en un ciclo, la página se
congelaría, y no se mostraría nada en la pantalla. Los navegadores
no actualizan la pantalla si un programa de JavaScript se encuentra
en ejecución, tampoco permiten ninguna interacción con la página. Es
por esto que necesitamos a `requestAnimationFrame`—le permite al navegador
saber que hemos terminado por el momento, y que puede empezar a hacer las
cosas que le navegador hace, cómo actualizar la pantalla y responder a las
acciones del usuario.

{{index "smooth animation"}}

A la función `animar` se le pasa el ((tiempo)) actual como un
argumento. Para asegurarse de que el movimiento del gato por milisegundo
es estable, basa la velocidad a la que cambia el ángulo en la
diferencia entre el tiempo actual y la última vez que la función
se ejecutó. Si solamente movieramos el ángulo una cierta cantidad
por paso, la animación tartamudearía si, por ejemplo, otra tarea
pesada se encontrara ejecutándose en la misma computadora que
pudiera prevenir que la función se ejecutará por una fracción de segundo.

{{index "Math.cos function", "Math.sin function", cosine, sine, trigonometry}}

{{id sin_cos}}

Moverse en círculos se logra a través de las funciones `Math.cos` y
`Math.sin`. Para aquellos que no estén familiarizados con estas,
las introduciré brevemente dado que las usaremos ocasionalmente en
este libro.

{{index coordinates, pi}}

Las funciones `Math.cos` y `Math.sin` son útiles para encontrar puntos
que recaen en un círculo alrededor del punto (0,0) con un radio de
uno. Ambas funciones interpretan sus argumentos como las posiciones
en el círculo, con cero denotando el punto en la parte más alejada
del lado derecho del círculo, moviéndose en el sentido de las manecillas del
reloj hasta que 2π (cerca de 6.28) nos halla tomado alrededor de todo el
círculo. `Math.cos` indica la coordenada x del punto que corresponde
con la posición dada, y `Math.sin` indica la coordenada y. Las
posiciones (o ángulos) mayores que 2π o menores que 0 son válidas—la
rotación se repite por lo que _a_+2π se refiere al mismo ((ángulo))
que _a_.

{{index "PI constant"}}

Esta unidad para medir ángulos se conoce como ((radian))es-un círculo
completo corresponde a 2π radianes, de manera similar a 360 grados
cuando se utilizan grados. La constante π está disponible como `Math.PI`
en JavaScript.

{{figure {url: "img/cos_sin.svg", alt: "Using cosine and sine to compute coordinates",width: "6cm"}}}

{{index "counter variable", "Math.sin function", "top (CSS)", "Math.cos function", "left (CSS)", ellipse}}

El código de animación del gato mantiene un contador, `angulo`,
para el ángulo actual de la animación y lo incrementa cada vez que la
función `animar` es llamada. Luego, se puede utilizar este ángulo para
calcular la posición actual del elemento imagen. El estilo `top` es
calculado con `Math.sin` y multiplicado por 20, que es el radio
vertical de nuestra elipse. El estilo `left` se basa en `Math.cos`
multiplicado por 200 por lo que la elipse es mucho más ancha que su
altura.

{{index "unit (CSS)"}}

Nótese que los estilos usualmente necesitan _unidades_. En este caso,
tuvimos que agregar `"px"` al número para informarle al navegador que
estábamos contando en ((pixel))es (al contrario de centímetros, "ems",
u otras unidades). Esto es sencillo de olvidar. Usar números sin
unidades resultará en estilos que son ignorados—a menos que el número
sea 0, que siempre indica la misma cosa, independientemente de su
unidad.

## Resumen

Los programas de JavaScript pueden inspeccionar e interferir con el
documento que el navegador está desplegando a través de una estructura
de datos llamada el DOM. Esta estructura de datos representa el modelo
del navegador del documento, y un programa de JavaScript puede
modificarlo para cambiar el documento visible.

El DOM está organizado como un árbol, en el cual los elementos están
ordenados jerárquicamente de acuerdo a la estructura del documento.
Estos objetos que representan a los elementos tienen propiedades como
`parentNode` y `childNodes`, que pueden ser usadas para navegar
a través de este árbol.

La forma en que un documento es desplegado puede ser influenciada por
la _estilización_, tanto agregando estilos directamente a los nodos
cómo definiendo reglas que coincidan con ciertos nodos. Hay muchas
propiedades de estilo diferentes, tales como `color` o `display`. El
código de JavaScript puede manipular el estilo de un elemento
directamente a través de su propiedad de `style`.

## Ejercicios

{{id exercise_table}}

### Construye una tabla

{{index "table (HTML tag)"}}

Una tabla HTML se construye con la siguiente estructura de etiquetas:

```{lang: "text/html"}
<table>
  <tr>
    <th>nombre</th>
    <th>altura</th>
    <th>ubicacion</th>
  </tr>
  <tr>
    <td>Kilimanjaro</td>
    <td>5895</td>
    <td>Tanzania</td>
  </tr>
</table>
```

{{index "tr (HTML tag)", "th (HTML tag)", "td (HTML tag)"}}

Para cada _((fila))_, la etiqueta `<table>` contiene una etiqueta `<tr>`.
Dentro de estas etiquetas `<tr>`, podemos poner ciertos elementos:
ya sean celdas cabecera (`<th>`) o celdas regulares (`<td>`).

Dado un conjunto de datos de montañas, un arreglo de objetos con
propiedades `nombre`, `altura` y `lugar`, genera la estructura DOM
para una tabla que enlista esos objetos. Deberá tener una columna
por llave y una fila por objeto, además de una fila cabecera con elementos
`<th>` en la parte superior, listando los nombres de las columnas.

Escribe esto de manera que las columnas se deriven automáticamente de
los objetos, tomando los nombres de propiedad del primer objeto en los
datos.

Agrega la tabla resultante al elemento con el atributo `id` de `"montañas"`
de manera que se vuelva visible en el documento.

{{index "right-aligning", "text-align (CSS)"}}

Una vez que lo tengas funcionando, alinea a la derecha las celdas que
contienen valores numéricos, estableciendo su propiedad `style.textAlign`
cómo `"right"`.

{{if interactive

```{test: no, lang: "text/html"}
<h1>Montañas</h1>

<div id="montañas"></div>

<script>
  const MONTAÑAS = [
    {nombre: "Kilimanjaro", altura: 5895, ubicacion: "Tanzania"},
    {nombre: "Everest", altura: 8848, ubicacion: "Nepal"},
    {nombre: "Monte Fuji", altura: 3776, ubicacion: "Japón"},
    {nombre: "Vaalserberg", altura: 323, ubicacion: "Países Bajos"},
    {nombre: "Denali", altura: 6168, ubicacion: "Estados Unidos"},
    {nombre: "Popocatepetl", altura: 5465, ubicacion: "México"},
    {nombre: "Mont Blanc", altura: 4808, ubicacion: "Italia/Francia"}
  ];

  // Tu codigo va aqui
</script>
```

if}}

{{hint

{{index "createElement method", "table example", "appendChild method"}}

Puedes utilizar `document.createElement` para crear nuevos nodos elemento,
`document.createTextNode` para crear nuevos nodos de texto, y el método
`appendChild` para poner nodos dentro de otros nodos.

{{index "Object.keys function"}}

Querrás recorrer los nombres de las llaves una vez para llenar la fila
superior y luego nuevamente para cada objeto en el arreglo para
construir las filas de datos. Para obtener un arreglo con los nombres
de las llaves proveniente del primer objeto, la función `Object.keys`
será de utilidad.

{{index "getElementById method", "querySelector method"}}

Para agregar la tabla al nodo padre correcto, puedes utilizar
`document.getElementById` o `document.querySelector` para encontrar el
nodo con el atributo `id` adecuado.

hint}}

### Elementos por nombre de tag

{{index "getElementsByTagName method", recursion}}

El método `document.getElementsByTagName` regresa todos los elementos
hijo con un nombre de etiqueta dado. Implementa tu propia versión de
esto como una función que toma un nodo y una cadena (el nombre de la
etiqueta) como argumentos y regresa un arreglo que contiene todos los
nodos elemento descendientes con el nombre del tag dado.

{{index "nodeName property", capitalization, "toLowerCase method", "toUpperCase method"}}

Para encontrar el nombre del tag de un elemento, utiliza su propiedad
`nodeName`. Pero considera que esto regresará el nombre de la etiqueta
todo en mayúsculas. Utiliza las funciones de las cadenas (`string`),
`toLowerCase` o `toUpperCase` para compensar esta situación.

{{if interactive

```{lang: "text/html", test: no}
<h1>Encabezado con un elemento <span>span</span>.</h1>
<p>Un parráfo con <span>uno</span>, <span>dos</span>
spans.</p>

<script>
  function byTagName(nodo, etiqueta) {
    // Tu código va aquí.
  }

  console.log(byTagName(document.body, "h1").length);
  // → 1
  console.log(byTagName(document.body, "span").length);
  // → 3
  let parrafo = document.querySelector("p");
  console.log(byTagName(parrafo, "span").length);
  // → 2
</script>
```
if}}

{{hint

{{index "getElementsByTagName method", recursion}}

La solución es expresada de manera más sencilla con una función
recursiva, similar a la [función `hablaSobre`](dom#talksAbout) definida
anteriormente en este capítulo.

{{index concatenation, "concat method", closure}}

Puedes llamar a `byTagname` recursivamente, concatenando los arreglos
resultantes para producir la salida. O puedes crear una función
interna que se llama a sí misma recursivamente y que tiene acceso a
un arreglo definido en la función exterior, al cual se
puede agregar los elementos coincidentes que encuentre. No
olvides llamar a la ((función interior)) una vez desde la función
exterior para iniciar el proceso.

{{index "nodeType property", "ELEMENT_NODE code"}}

La función recursiva debe revisar el tipo de nodo. En este caso
solamente estamos interesados por los nodos de tipo 1
(`Node.ELEMENT_NODE`). Para tales nodos, debemos de iterar sobre
sus hijos y, para cada hijo, observar si los hijos coinciden con la
consulta mientras también se realiza una llamada recursiva en él
para inspeccionar a sus propios hijos.

hint}}

### El sombrero del gato

{{index "cat's hat (exercise)", [animation, "spinning cat"]}}

Extiende la animación del gato definida [anteriormente](dom#animation)
de manera de que tanto el gato como su sombrero (`<img src="img/hat.png">`)
orbiten en lados opuestos de la elipse.

O haz que el sombrero circule alrededor del gato. O altera la
animación en alguna otra forma interesante.

{{index "absolute positioning", "top (CSS)", "left (CSS)", "position (CSS)"}}

Para hacer el posicionamiento de múltiples objetos más sencillo,
probablemente sea buena idea intercambiar a un posicionamiento absoluto.
Esto significa que `top` y `left` serán contados con relación a la
parte superior izquierda del documento. Para evitar usar coordenadas
negativas, que pueden causar que la imagen se mueva fuera de la página
visible, puedes agregar un número fijo de pixeles a los valores de las
posiciones.

{{if interactive

```{lang: "text/html", test: no}
<style>body { min-height: 200px }</style>
<img src="img/cat.png" id="cat" style="position: absolute">
<img src="img/hat.png" id="hat" style="position: absolute">

<script>
  let gato = document.querySelector("#cat");
  let sombrero = document.querySelector("#hat");

  let angulo = 0;
  let ultimoTiempo = null;
  function animar(tiempo) {
    if (ultimoTiempo != null) angulo += (tiempo - ultimoTiempo) * 0.001;
    ultimoTiempo = tiempo;
    gato.style.top = (Math.sin(angulo) * 40 + 40) + "px";
    gato.style.left = (Math.cos(angulo) * 200 + 230) + "px";

    // Tus extensiones van aquí.

    requestAnimationFrame(animar);
  }
  requestAnimationFrame(animar);
</script>
```

if}}

{{hint

Las funciones `Math.cos` y `Math.sin` miden los ángulos en radianes,
donde un círculo completo es 2π. Para un ángulo dado, puedes obtener
el ángulo inverso agregando la mitad de esto, que es `Math.PI`. Esto
puede ser útil para colocar el sombrero en el lado opuesto de la órbita.

hint}}
