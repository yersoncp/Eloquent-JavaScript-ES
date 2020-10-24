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

## Moviéndose a traves del árbol

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
<p><img id="gertrudiz" src="img/avestruz.png"></p>

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
la lista de hijos, o `insertBefore`, que insertará el nodo dado como
primer argumento antes del nodo dado como segundo argumento.

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
en el frente, resultando en _Tres_/_Uno_/_Dos_. Todas las operaciones
que insertan un nodo en alguna parte causarán, a modo de
((efecto secundario)), que el nodo sea removido de su posición actual
(si es que tiene una).

{{index "insertBefore method", "replaceChild method"}}

El método `replaceChild` es usado para reemplazar a un nodo hijo con
otro. Toma dos nodos como argumentos: un nuevo nodo y el nodo que será
reemplazado. El nodo reemplazado debe ser un nodo hijo del elemento
desde donde se está llamando el método. Nótese que tanto `replaceChild`
como `insertBefore` esperan que el _nuevo_ nodo sea el primer argumento.

## Creando nodos

{{index "alt attribute", "img (HTML tag)"}}

Digamos que queremos escribir un _script_ que reemplace todas las
((imagen))es (etiquetas `<img>`) en el documento con el texto contenido
en sus atributos `alt`, los cuales especifican una representación
textual alternativa de la imagen.

{{index "createTextNode method"}}

Esto no solamente involucra remover las imágenes, si no que también
involucra agregar un nuevo nodo texto que lo reemplace. Los nodos texto
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
Si empezáramos desde el frente, removiendo la primer imagen causaría que
la lista que perdiera su primer elemento de tal manera que la segunda
ocasión que el ciclo se repitiera, donde `i` es 1, se detendría dado que
la longitud de la colección ahora es también 1.

{{index "slice method"}}

Si quieres una colección de nodos _sólida_, a diferencia de una en
tiempo real, puedes convertir la colección a un arreglo real llamando
`Array.from`.

```
let casi_arreglo = {0: "one", 1: "two", length: 2};
let arreglo = Array.from(casi_arreglo);
console.log(arreglo.map(s => s.toUpperCase()));
// → ["UNO", "DOS"]
```

{{index "createElement method"}}

Para crear nodos ((elemento)), puedes utilizar el método
`document.createElement`. Este método toma un nombre de etiqueta y
regresa un nuevo nodo vacío del nodo dado.

{{index "Popper, Karl", [DOM, construction], "elt function"}}

{{id elt}}

El siguiente ejemplo define una utilidad `elt`, la cual crea un
elemento nodo y trata el resto de sus argumentos como hijos de ese
nodo. Luego entonces, esta función es utilizada para agregar una
atribución a una cita.

```{lang: "text/html"}
<blockquote id="cita">
  Ningún libro puede terminarse jamás. Mientras se trabaja en
  el aprendemos solo lo suficiente para encontrar inmaduro
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

Los ((atributo))s de algunos elementos, como `href` par los enlaces
pueden ser accedidos a través de una propiedad con el mismo nombre en
el objeto ((DOM)) del elemento. Este es el caso para los atributos
estándar más comúnmente utilizados.

{{index "data attribute", "getAttribute method", "setAttribute method", attribute}}

Pero HTML te permite establecer cualquier atributo que quieras en los
nodos. Esto puede ser útil debido a que te permite almacenar información
extra en un documento. Sin embargo, si creas tus propios nombres de
atributo, dichos atributos no estarán presentes como propiedades en el
nodo del elemento. En vez de eso, tendrás que utilizar los métodos
`getAttribute` y `setAttribute` para trabajar con ellos.

```{lang: "text/html"}
<p data-classified="secreto">El código de lanzamiento es: 00000000.</p>
<p data-classified="no-classificado">Yo tengo dos pies.</p>

<script>
  let paras = document.body.getElementsByTagName("p");
  for (let para of Array.from(paras)) {
    if (para.getAttribute("data-classified") == "secreto") {
      para.remove();
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
propiedades que coincidan con las palabras clave-la propiedad utilizada
para acceder a este atributo tiene por nombre `className`. También
puedes acceder a él bajo su nombre real, `"class"`, utilizando los
métodos `getAttribute` y `setAttribute`.

## Layout

{{index layout, "block element", "inline element", "p (HTML tag)", "h1 (HTML tag)", "a (HTML tag)", "strong (HTML tag)"}}

Tal vez hayas notado que diferentes tipos de elementos se exponen
de manera distinta. Algunos, como en el caso de los párrafos
(`<p>`) o encabezados (`<h1>`), ocupan todo el ancho del documento
y se renderizan en líneas separadas. A estos se les conoce como
elementos _block_ (bloque). Otros, como los enlaces (`<a>`) o el
elemento `<strong>`, se renderizan en la misma línea con su texto
circundante. Dichos elementos se les conoce como elementos
_inline_ (en la misma línea).

{{index drawing}}

Para cualquier documento dado, los navegadores son capaces de calcular
una estructura _(layout)_, que le da a cada elemento un tamaño y una
posición basada en el tipo y el contenido. Luego, esta estructura se
utiliza para trazar el documento.

{{index "border (CSS)", "offsetWidth property", "offsetHeight property", "clientWidth property", "clientHeight property", dimensions}}

Se puede acceder al tamaño y la posición de un elemento pueden desde
JavaScript. Las propiedades `offsetWidth` y `offsetHeight` te dan el
espacio que el elemento ocupa en ((pixel))es. Un píxel es la unidad
básica de las medidas del navegador. Tradicionalmente correspondía al
punto más pequeño que la pantalla podía trazar, pero en los monitores
modernos, que pueden trazar puntos _muy_ pequeños, este puede no ser
más el caso, por lo que un píxel del navegador puede abarcar varios
puntos en la pantalla.

De manera parecida, `clientWidth` y `clientHeight` te dan el tamaño
del espacio _dentro_ del elemento, ignorando la anchura del borde.

```{lang: "text/html"}
<p style="border: 3px solid red">
  Estoy dentro de una caja
</p>

<script>
  let para = document.body.getElementsByTagName("p")[0];
  console.log("clientHeight:", para.clientHeight);
  console.log("offsetHeight:", para.offsetHeight);
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
parte superior izquierda de la pantalla. Si los quieres relativos a
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

A un programa que repetidamente alterna entre leer la información de la
estructura DOM y cambiar el DOM fuerza a que haya bastantes cálculos
de estructura, y por consecuencia se ejecutará lentamente. El siguiente
código es un ejemplo de esto. Contiene dos programas diferentes que
construyen una línea de _X_ caracteres con 2,000 pixeles de ancho y
que mide el tiempo que toma cada uno.

```{lang: "text/html", test: nonumbers}
<p><span id="uno"></span></p>
<p><span id="dos"></span></p>

<script>
  function tiempo(nombre, accion) {
    let inicio = Date.now(); // Current time in milliseconds
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

## Styling

{{index "block element", "inline element", style, "strong (HTML tag)", "a (HTML tag)", underline}}

We have seen that different HTML elements are drawn differently. Some
are displayed as blocks, others inline. Some add styling—`<strong>`
makes its content ((bold)), and `<a>` makes it blue and underlines it.

{{index "img (HTML tag)", "default behavior", "style attribute"}}

The way an `<img>` tag shows an image or an `<a>` tag causes a link to
be followed when it is clicked is strongly tied to the element type.
But we can change the styling associated with an element, such
as the text color or underline. Here is an example that uses
the `style` property:

```{lang: "text/html"}
<p><a href=".">Normal link</a></p>
<p><a href="." style="color: green">Green link</a></p>
```

{{if book

The second link will be green instead of the default link color.

{{figure {url: "img/colored-links.png", alt: "A normal and a green link",width: "2.2cm"}}}

if}}

{{index "border (CSS)", "color (CSS)", CSS, "colon character"}}

A style attribute may contain one or more _((declaration))s_, which
are a property (such as `color`) followed by a colon and a value (such
as `green`). When there is more than one declaration, they must be
separated by ((semicolon))s, as in `"color: red; border: none"`.

{{index "display (CSS)", layout}}

A lot of aspects of the document can be influenced by
styling. For example, the `display` property controls whether an
element is displayed as a block or an inline element.

```{lang: "text/html"}
This text is displayed <strong>inline</strong>,
<strong style="display: block">as a block</strong>, and
<strong style="display: none">not at all</strong>.
```

{{index "hidden element"}}

The `block` tag will end up on its own line since ((block element))s
are not displayed inline with the text around them. The last tag is
not displayed at all—`display: none` prevents an element from showing
up on the screen. This is a way to hide elements. It is often
preferable to removing them from the document entirely because it
makes it easy to reveal them again later.

{{if book

{{figure {url: "img/display.png", alt: "Different display styles",width: "4cm"}}}

if}}

{{index "color (CSS)", "style attribute"}}

JavaScript code can directly manipulate the style of an element
through the element's `style` property. This property holds an object
that has properties for all possible style properties. The values of
these properties are strings, which we can write to in order to change
a particular aspect of the element's style.

```{lang: "text/html"}
<p id="para" style="color: purple">
  Nice text
</p>

<script>
  let para = document.getElementById("para");
  console.log(para.style.color);
  para.style.color = "magenta";
</script>
```

{{index "camel case", capitalization, "hyphen character", "font-family (CSS)"}}

Some style property names contain hyphens, such as `font-family`.
Because such property names are awkward to work with in JavaScript
(you'd have to say `style["font-family"]`), the property names in the
`style` object for such properties have their hyphens removed and the
letters after them capitalized (`style.fontFamily`).

## Cascading styles

{{index "rule (CSS)", "style (HTML tag)"}}

{{indexsee "Cascading Style Sheets", CSS}}
{{indexsee "style sheet", CSS}}

The styling system for HTML is called ((CSS)), for _Cascading Style
Sheets_. A _style sheet_ is a set of rules for how to style
elements in a document. It can be given inside a `<style>` tag.

```{lang: "text/html"}
<style>
  strong {
    font-style: italic;
    color: gray;
  }
</style>
<p>Now <strong>strong text</strong> is italic and gray.</p>
```

{{index "rule (CSS)", "font-weight (CSS)", overlay}}

The _((cascading))_ in the name refers to the fact that multiple such
rules are combined to produce the final style for an element. In the
example, the default styling for `<strong>` tags, which gives them
`font-weight: bold`, is overlaid by the rule in the `<style>` tag,
which adds `font-style` and `color`.

{{index "style (HTML tag)", "style attribute"}}

When multiple rules define a value for the same property, the most
recently read rule gets a higher ((precedence)) and wins. So if the
rule in the `<style>` tag included `font-weight: normal`,
contradicting the default `font-weight` rule, the text would be
normal, _not_ bold. Styles in a `style` attribute applied directly to
the node have the highest precedence and always win.

{{index uniqueness, "class attribute", "id attribute"}}

It is possible to target things other than ((tag)) names in CSS rules.
A rule for `.abc` applies to all elements with `"abc"` in their `class`
attribute. A rule for `#xyz` applies to the element with an `id`
attribute of `"xyz"` (which should be unique within the document).

```{lang: "text/css"}
.subtle {
  color: gray;
  font-size: 80%;
}
#header {
  background: blue;
  color: white;
}
/* p elements with id main and with classes a and b */
p#main.a.b {
  margin-bottom: 20px;
}
```

{{index "rule (CSS)"}}

The ((precedence)) rule favoring the most recently defined rule
applies only when the rules have the same _((specificity))_. A rule's
specificity is a measure of how precisely it describes matching
elements, determined by the number and kind (tag, class, or ID) of
element aspects it requires. For example, a rule that targets `p.a` is
more specific than rules that target `p` or just `.a` and would thus
take precedence over them.

{{index "direct child node"}}

The notation `p > a {…}` applies the given styles to all `<a>` tags
that are direct children of `<p>` tags. Similarly, `p a {…}` applies
to all `<a>` tags inside `<p>` tags, whether they are direct or
indirect children.

## Query selectors

{{index complexity, CSS}}

We won't be using style sheets all that much in this book.
Understanding them is helpful when programming in the browser, but
they are complicated enough to warrant a separate book.

{{index "domain-specific language", [DOM, querying]}}

The main reason I introduced _((selector))_ syntax—the notation used
in style sheets to determine which elements a set of styles apply
to—is that we can use this same mini-language as an effective way to
find DOM elements.

{{index "querySelectorAll method", "NodeList type"}}

The `querySelectorAll` method, which is defined both on the `document`
object and on element nodes, takes a selector string and returns a
`NodeList` containing all the elements that it matches.

```{lang: "text/html"}
<p>And if you go chasing
  <span class="animal">rabbits</span></p>
<p>And you know you're going to fall</p>
<p>Tell 'em a <span class="character">hookah smoking
  <span class="animal">caterpillar</span></span></p>
<p>Has given you the call</p>

<script>
  function count(selector) {
    return document.querySelectorAll(selector).length;
  }
  console.log(count("p"));           // All <p> elements
  // → 4
  console.log(count(".animal"));     // Class animal
  // → 2
  console.log(count("p .animal"));   // Animal inside of <p>
  // → 2
  console.log(count("p > .animal")); // Direct child of <p>
  // → 1
</script>
```

{{index "live data structure"}}

Unlike methods such as `getElementsByTagName`, the object returned by
`querySelectorAll` is _not_ live. It won't change when you change the
document. It is still not a real array, though, so you still need to
call `Array.from` if you want to treat it like one.

{{index "querySelector method"}}

The `querySelector` method (without the `All` part) works in a similar
way. This one is useful if you want a specific, single element. It
will return only the first matching element or null when no element
matches.

{{id animation}}

## Positioning and animating

{{index "position (CSS)", "relative positioning", "top (CSS)", "left (CSS)", "absolute positioning"}}

The `position` style property influences layout in a powerful way. By
default it has a value of `static`, meaning the element sits in its
normal place in the document. When it is set to `relative`, the
element still takes up space in the document, but now the `top` and
`left` style properties can be used to move it relative to that normal
place. When `position` is set to `absolute`, the element is removed
from the normal document flow—that is, it no longer takes up space and
may overlap with other elements. Also, its `top` and `left` properties
can be used to absolutely position it relative to the top-left corner
of the nearest enclosing element whose `position` property isn't
`static`, or relative to the document if no such enclosing element
exists.

{{index [animation, "spinning cat"]}}

We can use this to create an animation. The following document
displays a picture of a cat that moves around in an ((ellipse)):

```{lang: "text/html", startCode: true}
<p style="text-align: center">
  <img src="img/cat.png" style="position: relative">
</p>
<script>
  let cat = document.querySelector("img");
  let angle = Math.PI / 2;
  function animate(time, lastTime) {
    if (lastTime != null) {
      angle += (time - lastTime) * 0.001;
    }
    cat.style.top = (Math.sin(angle) * 20) + "px";
    cat.style.left = (Math.cos(angle) * 200) + "px";
    requestAnimationFrame(newTime => animate(newTime, time));
  }
  requestAnimationFrame(animate);
</script>
```

{{if book

The gray arrow shows the path along which the image moves.

{{figure {url: "img/cat-animation.png", alt: "A moving cat head",width: "8cm"}}}

if}}

{{index "top (CSS)", "left (CSS)", centering, "relative positioning"}}

Our picture is centered on the page and given a `position` of
`relative`. We'll repeatedly update that picture's `top` and `left`
styles to move it.

{{index "requestAnimationFrame function", drawing, animation}}

{{id animationFrame}}

The script uses `requestAnimationFrame` to schedule the `animate`
function to run whenever the browser is ready to repaint the screen.
The `animate` function itself again calls `requestAnimationFrame` to
schedule the next update. When the browser window (or tab) is active,
this will cause updates to happen at a rate of about 60 per second,
which tends to produce a good-looking animation.

{{index timeline, blocking}}

If we just updated the DOM in a loop, the page would freeze, and
nothing would show up on the screen. Browsers do not update their
display while a JavaScript program is running, nor do they allow any
interaction with the page. This is why we need
`requestAnimationFrame`—it lets the browser know that we are done for
now, and it can go ahead and do the things that browsers do, such as
updating the screen and responding to user actions.

{{index "smooth animation"}}

The animation function is passed the current ((time)) as an
argument. To ensure that the motion of the cat per millisecond is
stable, it bases the speed at which the angle changes on the
difference between the current time and the last time the function
ran. If it just moved the angle by a fixed amount per step, the motion
would stutter if, for example, another heavy task running on the same
computer were to prevent the function from running for a fraction of a
second.

{{index "Math.cos function", "Math.sin function", cosine, sine, trigonometry}}

{{id sin_cos}}

Moving in ((circle))s is done using the trigonometry functions
`Math.cos` and `Math.sin`. For those who aren't familiar with
these, I'll briefly introduce them since we will occasionally use them
in this book.

{{index coordinates, pi}}

`Math.cos` and `Math.sin` are useful for finding points that lie on a
circle around point (0,0) with a radius of one. Both functions
interpret their argument as the position on this circle, with zero
denoting the point on the far right of the circle, going clockwise
until 2π (about 6.28) has taken us around the whole circle. `Math.cos`
tells you the x-coordinate of the point that corresponds to the given
position, and `Math.sin` yields the y-coordinate. Positions (or
angles) greater than 2π or less than 0 are valid—the rotation repeats
so that _a_+2π refers to the same ((angle)) as _a_.

{{index "PI constant"}}

This unit for measuring angles is called ((radian))s—a full circle is
2π radians, similar to how it is 360 degrees when measuring in
degrees. The constant π is available as `Math.PI` in JavaScript.

{{figure {url: "img/cos_sin.svg", alt: "Using cosine and sine to compute coordinates",width: "6cm"}}}

{{index "counter variable", "Math.sin function", "top (CSS)", "Math.cos function", "left (CSS)", ellipse}}

The cat animation code keeps a counter, `angle`, for the current angle
of the animation and increments it every time the `animate` function
is called. It can then use this angle to compute the current position
of the image element. The `top` style is computed with `Math.sin` and
multiplied by 20, which is the vertical radius of our ellipse. The
`left` style is based on `Math.cos` and multiplied by 200 so that the
ellipse is much wider than it is high.

{{index "unit (CSS)"}}

Note that styles usually need _units_. In this case, we have to append
`"px"` to the number to tell the browser that we are counting in ((pixel))s
(as opposed to centimeters, "ems", or other units). This is easy to
forget. Using numbers without units will result in your style being
ignored—unless the number is 0, which always means the same thing,
regardless of its unit.

## Summary

JavaScript programs may inspect and interfere with the document that
the browser is displaying through a data structure called the DOM.
This data structure represents the browser's model of the document,
and a JavaScript program can modify it to change the visible document.

The DOM is organized like a tree, in which elements are arranged
hierarchically according to the structure of the document. The objects
representing elements have properties such as `parentNode` and
`childNodes`, which can be used to navigate through this tree.

The way a document is displayed can be influenced by _styling_, both
by attaching styles to nodes directly and by defining rules that match
certain nodes. There are many different style properties, such as
`color` or `display`. JavaScript code can manipulate an element's
style directly through its `style` property.

## Exercises

{{id exercise_table}}

### Build a table

{{index "table (HTML tag)"}}

An HTML table is built with the following tag structure:

```{lang: "text/html"}
<table>
  <tr>
    <th>name</th>
    <th>height</th>
    <th>place</th>
  </tr>
  <tr>
    <td>Kilimanjaro</td>
    <td>5895</td>
    <td>Tanzania</td>
  </tr>
</table>
```

{{index "tr (HTML tag)", "th (HTML tag)", "td (HTML tag)"}}

For each _((row))_, the `<table>` tag contains a `<tr>` tag. Inside of
these `<tr>` tags, we can put cell elements: either heading cells
(`<th>`) or regular cells (`<td>`).

Given a data set of mountains, an array of objects with `name`,
`height`, and `place` properties, generate the DOM structure for a
table that enumerates the objects. It should have one column per key
and one row per object, plus a header row with `<th>` elements at the
top, listing the column names.

Write this so that the columns are automatically derived from the
objects, by taking the property names of the first object in the data.

Add the resulting table to the element with an `id` attribute of
`"mountains"` so that it becomes visible in the document.

{{index "right-aligning", "text-align (CSS)"}}

Once you have this working, right-align cells that contain number
values by setting their `style.textAlign` property to `"right"`.

{{if interactive

```{test: no, lang: "text/html"}
<h1>Mountains</h1>

<div id="mountains"></div>

<script>
  const MOUNTAINS = [
    {name: "Kilimanjaro", height: 5895, place: "Tanzania"},
    {name: "Everest", height: 8848, place: "Nepal"},
    {name: "Mount Fuji", height: 3776, place: "Japan"},
    {name: "Vaalserberg", height: 323, place: "Netherlands"},
    {name: "Denali", height: 6168, place: "United States"},
    {name: "Popocatepetl", height: 5465, place: "Mexico"},
    {name: "Mont Blanc", height: 4808, place: "Italy/France"}
  ];

  // Your code here
</script>
```

if}}

{{hint

{{index "createElement method", "table example", "appendChild method"}}

You can use `document.createElement` to create new element nodes,
`document.createTextNode` to create text nodes, and the `appendChild`
method to put nodes into other nodes.

{{index "Object.keys function"}}

You'll want to loop over the key names once to fill in the top row and
then again for each object in the array to construct the data rows. To
get an array of key names from the first object, `Object.keys` will be
useful.

{{index "getElementById method", "querySelector method"}}

To add the table to the correct parent node, you can use
`document.getElementById` or `document.querySelector` to find the node
with the proper `id` attribute.

hint}}

### Elements by tag name

{{index "getElementsByTagName method", recursion}}

The `document.getElementsByTagName` method returns all child elements
with a given tag name. Implement your own version of this as a
function that takes a node and a string (the tag name) as arguments
and returns an array containing all descendant element nodes with the
given tag name.

{{index "nodeName property", capitalization, "toLowerCase method", "toUpperCase method"}}

To find the tag name of an element, use its `nodeName` property. But
note that this will return the tag name in all uppercase. Use the
`toLowerCase` or `toUpperCase` string methods to compensate for this.

{{if interactive

```{lang: "text/html", test: no}
<h1>Heading with a <span>span</span> element.</h1>
<p>A paragraph with <span>one</span>, <span>two</span>
  spans.</p>

<script>
  function byTagName(node, tagName) {
    // Your code here.
  }

  console.log(byTagName(document.body, "h1").length);
  // → 1
  console.log(byTagName(document.body, "span").length);
  // → 3
  let para = document.querySelector("p");
  console.log(byTagName(para, "span").length);
  // → 2
</script>
```
if}}

{{hint

{{index "getElementsByTagName method", recursion}}

The solution is most easily expressed with a recursive function,
similar to the [`talksAbout` function](dom#talksAbout) defined earlier
in this chapter.

{{index concatenation, "concat method", closure}}

You could call `byTagname` itself recursively, concatenating the
resulting arrays to produce the output. Or you could create an inner
function that calls itself recursively and that has access to an array
binding defined in the outer function, to which it can add the
matching elements it finds. Don't forget to call the ((inner
function)) once from the outer function to start the process.

{{index "nodeType property", "ELEMENT_NODE code"}}

The recursive function must check the node type. Here we are
interested only in node type 1 (`Node.ELEMENT_NODE`). For such
nodes, we must loop over their children and, for each child, see
whether the child matches the query while also doing a recursive call
on it to inspect its own children.

hint}}

### The cat's hat

{{index "cat's hat (exercise)", [animation, "spinning cat"]}}

Extend the cat animation defined [earlier](dom#animation) so that
both the cat and his hat (`<img src="img/hat.png">`) orbit at opposite
sides of the ellipse.

Or make the hat circle around the cat. Or alter the animation in some
other interesting way.

{{index "absolute positioning", "top (CSS)", "left (CSS)", "position (CSS)"}}

To make positioning multiple objects easier, it is probably a good
idea to switch to absolute positioning. This means that `top` and
`left` are counted relative to the top left of the document. To avoid
using negative coordinates, which would cause the image to move
outside of the visible page, you can add a fixed number of pixels to
the position values.

{{if interactive

```{lang: "text/html", test: no}
<style>body { min-height: 200px }</style>
<img src="img/cat.png" id="cat" style="position: absolute">
<img src="img/hat.png" id="hat" style="position: absolute">

<script>
  let cat = document.querySelector("#cat");
  let hat = document.querySelector("#hat");

  let angle = 0;
  let lastTime = null;
  function animate(time) {
    if (lastTime != null) angle += (time - lastTime) * 0.001;
    lastTime = time;
    cat.style.top = (Math.sin(angle) * 40 + 40) + "px";
    cat.style.left = (Math.cos(angle) * 200 + 230) + "px";

    // Your extensions here.

    requestAnimationFrame(animate);
  }
  requestAnimationFrame(animate);
</script>
```

if}}

{{hint

`Math.cos` and `Math.sin` measure angles in radians, where a full
circle is 2π. For a given angle, you can get the opposite angle by
adding half of this, which is `Math.PI`. This can be useful for
putting the hat on the opposite side of the orbit.

hint}}
