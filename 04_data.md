{{meta {load_files: ["code/journal.js", "code/chapter/04_data.js"], zip: "node/html"}}}

# Estructuras de Datos: Objetos y Arrays

{{quote {author: "Charles Babbage", title: "Passages from the Life of a Philosopher (1864)", chapter: true}

En dos ocasiones me han preguntado, 'Dinos, Sr. Babbage, si pones
montos equivocadas en la máquina, saldrán las respuestas correctas?
[...] No soy capaz de comprender correctamente el tipo de confusión de
ideas que podrían provocar tal pregunta.

quote}}

{{index "Babbage, Charles"}}

{{figure {url: "img/chapter_picture_4.jpg", alt: "Picture of a weresquirrel", chapter: framed}}}

{{index object, "data structure"}}

Los números, los booleanos y los strings son los átomos que constituyen las  
estructuras de ((datos)). Sin embargo, muchos tipos de información
requieren más de un átomo. Los _objetos_ nos permiten agrupar valores—incluidos
otros objetos— para construir estructuras más complejas.

Los programas que hemos construido hasta ahora han estado limitados por el
hecho de que estaban operando solo en tipos de datos simples. Este capítulo
introducira estructuras de datos básicas. Al final de el, sabrás lo suficiente
como para comenzar a escribir programas útiles.

El capítulo trabajara a través de un ejemplo de programación más o menos
realista, presentando nuevos conceptos según se apliquen al problema en
cuestión. El código de ejemplo a menudo se basara en funciones y vinculaciones
que fueron introducidas anteriormente en el texto.

{{if book

La ((caja de arena)) en línea para el libro
([_eloquentjavascript.net/code_](https://eloquentjavascript.net/code)]
proporciona una forma de ejecutar código en el contexto de un capítulo en
específico. Si decides trabajar con los ejemplos en otro entorno,
asegúrate de primero descargar el código completo de este capítulo
desde la página de la caja de arena.

if}}

## El Hombre Ardilla

{{index "weresquirrel example", lycanthropy}}

De vez en cuando, generalmente entre las ocho y las diez de la noche,
((Jacques)) se encuentra a si mismo
transformándose en un pequeño roedor peludo con una cola espesa.

Por un lado, Jacques está muy contento de no tener la licantropía clásica.
Convertirse en una ardilla causa menos problemas que convertirse en un lobo.
En lugar de tener que preocuparse por accidentalmente comerse al vecino
(_eso_ sería incómodo), le preocupa ser comido por el gato del vecino.
Después de dos ocasiones en las que se despertó en una rama precariamente
delgada de la copa de un roble, desnudo y desorientado, Jacques se ha dedicado
a bloquear las puertas y ventanas de su habitación por la noche y
pone algunas nueces en el piso para mantenerse ocupado.

Eso se ocupa de los problemas del gato y el árbol. Pero Jacques preferiría
deshacerse de su condición por completo. Las ocurrencias irregulares de la
transformación lo hacen sospechar que estas podrían ser provocadas por
algo en especifico. Por un tiempo, creyó que solo sucedia en los días
en los que el había estado cerca de árboles de roble. Pero evitar los robles
no detuvo el problema.

{{index journal}}

Cambiando a un enfoque más científico, Jacques ha comenzado a mantener un
registro diario de todo lo que hace en un día determinado y si su forma
cambio. Con esta información el espera reducir las condiciones que
desencadenan las transformaciones.

Lo primero que el necesita es una estructura de datos para almacenar esta
información.

## Conjuntos de datos

{{index "data structure"}}

Para trabajar con una porción de datos digitales, primero debemos encontrar
una manera de representarlo en la ((memoria)) de nuestra máquina. Digamos,
por ejemplo, que queremos representar una ((colección)) de los números
2, 3, 5, 7 y 11.

{{index string}}

Podríamos ponernos creativos con los strings—después de todo, los strings pueden
tener cualquier longitud, por lo que podemos poner una gran cantidad de
datos en ellos—y usar `"2 3 5 7 11"` como nuestra representación.
Pero esto es incómodo. Tendrías que extraer los dígitos de alguna manera
y convertirlos a números para acceder a ellos.

{{index [array, creation], "[] (array)"}}

Afortunadamente, JavaScript proporciona un tipo de datos específicamente
para almacenar secuencias de valores. Es llamado _array_ y está escrito
como una lista de valores entre ((corchetes)), separados por comas.

```
let listaDeNumeros = [2, 3, 5, 7, 11];
console.log(listaDeNumeros[2]);
// → 5
console.log(listaDeNumeros[0]);
// → 2
console.log(listaDeNumeros[2 - 1]);
// → 3
```

{{index "[] (subscript)", [array, indexing]}}

La notación para llegar a los elementos dentro de un array también utiliza
((corchetes)). Un par de corchetes inmediatamente después de una
expresión, con otra expresión dentro de ellos, buscará al
elemento en la expresión de la izquierda que corresponde al
_((índice))_ dado por la expresión entre corchetes.

{{id array_indexing}}
{{index "zero-based counting"}}

El primer índice de un array es cero, no uno. Entonces el primer elemento es
alcanzado con `listaDeNumeros[0]`. El conteo basado en cero tiene una larga
tradición en el mundo de la tecnología, y en ciertas maneras tiene mucho
sentido, pero toma algo de tiempo acostumbrarse. Piensa en el índice como
la cantidad de elementos a saltar, contando desde el comienzo del array.

{{id properties}}

## Propiedades

{{index "Math object", "Math.max function", ["length property", "for string"], [object, property], "period character"}}

Hasta ahora hemos visto algunas expresiones sospechosas como `miString.length`
(para obtener la longitud de un string) y `Math.max` (la función máxima)
en capítulos anteriores. Estas son expresiones que acceden a la _((propiedad))_
de algún valor. En el primer caso, accedemos a la propiedad `length` de
el valor en `miString`. En el segundo, accedemos a la propiedad llamada
`max` en el objeto `Math` (que es una colección de
constantes y funciones relacionadas con las matemáticas).

{{index property, null, undefined}}

Casi todos los valores de JavaScript tienen propiedades. Las excepciones son
`null` y `undefined`. Si intentas acceder a una propiedad en alguno de
estos no-valores, obtienes un error.

```{test: no}
null.length;
// → TypeError: null has no properties
```

{{indexsee "dot character", "period character"}}
{{index "[] (subscript)", "period character", "square brackets", "computed property"}}

Las dos formas principales de acceder a las propiedades en JavaScript son
con un punto y con corchetes. Tanto `valor.x` como `valor[x]` acceden una
((propiedad)) en `valor`—pero no necesariamente la misma propiedad. La
diferencia está en cómo se interpreta `x`. Cuando se usa un punto, la palabra
después del punto es el nombre literal de la propiedad. Cuando usas
corchetes, la expresión entre corchetes es _evaluada_ para obtener
el nombre de la propiedad. Mientras `valor.x` obtiene la propiedad de `valor`
llamada "x", `valor[x]` intenta evaluar la expresión `x` y usa
el resultado, convertido en un string, como el nombre de la propiedad.

Entonces, si sabes que la propiedad que te interesa se llama
_color_, dices `valor.color`. Si quieres extraer la propiedad
nombrado por el valor mantenido en la vinculación `i`, dices `valor[i]`.
Los nombres de las propiedades son strings. Pueden ser cualquier string,
pero la notación de puntos solo funciona con nombres que se vean como nombres
de vinculaciones válidos. Entonces, si quieres acceder a una
propiedad llamada _2_ o _Juan Perez_, debes usar corchetes:
`valor[2]` o `valor["Juan Perez"]`.

Los elementos en un ((array)) son almacenados como propiedades del array,
usando números como nombres de propiedad. Ya que no puedes usar la notación
de puntos con números, y que generalmente quieres utilizar una vinculación
que contenga el índice de cualquier manera,
debes de usar la notación de corchetes para llegar a ellos.

{{index ["length property", "for array"], [array, "length of"]}}

La propiedad `length` de un array nos dice cuántos elementos este tiene.
Este nombre de propiedad es un nombre de vinculación válido, y
sabemos su nombre en avance, así que para encontrar la longitud de un array,
normalmente escribes `array.length` ya que es más fácil de escribir que
`array["length"]`.

{{id methods}}

## Métodos

{{index [function, "as property"], method, string}}

Ambos objetos de string y array contienen, además de la propiedad
`length`, una serie de propiedades que tienen valores de función.

```
let ouch = "Ouch";
console.log(typeof ouch.toUpperCase);
// → function
console.log(ouch.toUpperCase());
// → OUCH
```

{{index "case conversion", "toUpperCase method", "toLowerCase method"}}

Cada string tiene una propiedad `toUpperCase` ("a mayúsculas").
Cuando se llame, regresará una copia del string en la que todas las
letras han sido convertido a mayúsculas. También hay `toLowerCase`
("a minúsculas"), que hace lo contrario.

{{index this}}

Curiosamente, a pesar de que la llamada a `toUpperCase` no pasa ningún
argumento, la función de alguna manera tiene acceso al string `"Ouch"`, el
valor de cuya propiedad llamamos. Cómo funciona esto se describe en el
[Capítulo 6](objeto#metodos_de_objeto).

Las propiedades que contienen funciones generalmente son llamadas _metodos_
del valor al que pertenecen. Como en, "`toUpperCase` es un método de
string".

{{id array_methods}}

Este ejemplo demuestra dos métodos que puedes usar para manipular
arrays:

```
let secuencia = [1, 2, 3];
secuencia.push(4);
secuencia.push(5);
console.log(secuencia);
// → [1, 2, 3, 4, 5]
console.log(secuencia.pop());
// → 5
console.log(secuencia);
// → [1, 2, 3, 4]
```

{{index collection, array, "push method", "pop method"}}

El método `push` agrega valores al final de un array, y el
el método `pop` hace lo contrario, eliminando el último valor en el array
y retornandolo.

Estos nombres algo tontos son los términos tradicionales para las operaciones en
una _((pila))_. Una pila, en programación, es una ((estructura de datos)) que
te permite agregar valores a ella y volverlos a sacar en el
orden opuesto, de modo que lo que se agregó de último se elimine primero.
Estas son comunes en la programación—es posible que recuerdes la ((pila))
de llamadas en [el capítulo anterior](funciones#pila), que es una
instancia de la misma idea.

## Objetos

{{index journal, "weresquirrel example", array, record}}

De vuelta al Hombre-Ardilla. Un conjunto de entradas diarias puede ser
representado como un array. Pero estas entradas no consisten en solo un
número o un string—cada entrada necesita almacenar una lista de actividades y
un valor booleano que indica si Jacques se convirtió en una ardilla
o no. Idealmente, nos gustaría agrupar estos en un solo
valor y luego agrupar estos valores en un array de registro de entradas.

{{index syntax, property, "curly braces", "{} (object)"}}

Los valores del tipo _((objeto))_ son colecciones arbitrarias de
propiedades. Una forma de crear un objeto es mediante el uso de llaves
como una expresión.

```
let dia1 = {
  ardilla: false,
  eventos: ["trabajo", "toque un arbol", "pizza", "salir a correr"]
};
console.log(dia1.ardilla);
// → false
console.log(dia1.lobo);
// → undefined
dia1.lobo = false;
console.log(dia1.lobo);
// → false
```

{{index [quoting, "of object properties"], "colon character"}}

Dentro de las llaves, hay una lista de propiedades separadas por comas.
Cada propiedad tiene un nombre seguido de dos puntos y un valor. Cuando un
objeto está escrito en varias líneas, indentar como en el
ejemplo ayuda con la legibilidad. Las propiedades cuyos nombres no sean
nombres válidos de vinculaciones o números válidos deben estar entre comillas.

```
let descripciones = {
  trabajo: "Fui a trabajar",
  "toque un arbol": "Toque un arbol"
};
```

Esto significa que las ((llaves)) tienen _dos_ significados en JavaScript. Al
comienzo de una ((declaración)), comienzan un ((bloque)) de declaraciones. En
cualquier otra posición, describen un objeto. Afortunadamente, es raramente
útil comenzar una declaración con un objeto en llaves, por lo que
la ambigüedad entre estas dos acciones no es un gran problema.

{{index undefined}}

Leer una propiedad que no existe te dará el valor `undefined`.

{{index [property, assignment], mutability, "= operator"}}

Es posible asignarle un valor a una expresión de propiedad con un
operador `=`. Esto reemplazará el valor de la propiedad si ya tenia uno
o crea una nueva propiedad en el objeto si no fuera así.

{{index "tentacle (analogy)", [property, "model of"]}}

Para volver brevemente a nuestro modelo de ((vinculaciones)) como
tentáculos—Las vinculaciones de propiedad son similares. Ellas _agarran_
valores, pero otras vinculaciones y propiedades pueden estar agarrando
esos mismos valores. Puedes pensar en los objetos como pulpos con cualquier
cantidad de tentáculos, cada uno de los cuales tiene un nombre tatuado en él.

{{index "delete operator", [property, deletion]}}

El operador `delete` ("eliminar") corta un tentáculo de dicho pulpo. Es
un operador unario que, cuando se aplica a la propiedad de un objeto,
eliminará la propiedad nombrada de dicho objeto. Esto no es algo que hagas
todo el tiempo, pero es posible.

```
let unObjeto = {izquierda: 1, derecha: 2};
console.log(unObjeto.izquierda);
// → 1
delete unObjeto.izquierda;
console.log(unObjeto.izquierda);
// → undefined
console.log("izquierda" in unObjeto);
// → false
console.log("derecha" in unObjeto);
// → true
```

{{index "in operator", [property, "testing for"], object}}

El operador binario `in` ("en"), cuando se aplica a un string y un objeto,
te dice si ese objeto tiene una propiedad con ese nombre. La diferencia
entre darle un valor de `undefined` a una propiedad y eliminarla realmente es
que, en el primer caso, el objeto todavía _tiene_ la propiedad (solo que
no tiene un valor muy interesante), mientras que en el segundo caso
la propiedad ya no está presente e `in` retornara `false`.

{{index "Object.keys function"}}

Para saber qué propiedades tiene un objeto, puedes usar la función
`Object.keys`. Le das un objeto y devuelve un array
de strings—los nombres de las propiedades del objeto.

```
console.log(Object.keys({x: 0, y: 0, z: 2}));
// → ["x", "y", "z"]
```

Hay una función `Object.assign` que copia todas las propiedades de
un objeto a otro.

```
let objetoA = {a: 1, b: 2};
Object.assign(objetoA, {b: 3, c: 4});
console.log(objetoA);
// → {a: 1, b: 3, c: 4}
```

{{index array, collection}}

Los arrays son, entonces, solo un tipo de objeto especializado para almacenar
secuencias de cosas. Si evalúas `typeof []`, este produce
`"object"`. Podrias imaginarlos como pulpos largos y planos con todos sus
tentáculos en una fila ordenada, etiquetados con números.

{{index journal, "weresquirrel example"}}

Representaremos el diario de Jacques como un array de objetos.

```{test: wrap}
let diario = [
  {eventos: ["trabajo", "toque un arbol", "pizza",
            "sali a correr", "television"],
   ardilla: false},
  {eventos: ["trabajo", "helado", "coliflor",
            "lasaña", "toque un arbol", "me cepille los dientes"],
   ardilla: false},
  {eventos: ["fin de semana", "monte la bicicleta", "descanso", "nueces",
            "cerveza"],
   ardilla: true},
  /* y asi sucesivamente... */
];
```

## Mutabilidad

Llegaremos a la programación real _pronto_. Pero primero, hay una pieza
más de teoría por entender.

{{index mutability, "side effect", number, string, Boolean, object}}

Vimos que los valores de objeto pueden ser modificados. Los tipos de valores
discutidos en capítulos anteriores, como números, strings y booleanos,
son todos _((inmutables))_—es imposible cambiar los valores de aquellos
tipos. Puedes combinarlos y obtener nuevos valores a partir de ellos, pero cuando
tomas un valor de string específico, ese valor siempre será el
mismo. El texto dentro de él no puede ser cambiado. Si tienes un string que
contiene `"gato"`, no es posible que otro código cambie un
carácter en tu string para que deletree `"rato"`.

Los objetos funcionan de una manera diferente. Tu _puedes_ cambiar sus
propiedades, haciendo que un único valor de objeto tenga contenido diferente en
diferentes momentos.

{{index [object, identity], identity, memory, mutability}}

Cuando tenemos dos números, 120 y 120, podemos considerarlos el mismo número
precisamente, ya sea que hagan referencia o no a los mismos bits físicos.
Con los objetos, hay una diferencia entre tener dos referencias a
el mismo objeto y tener dos objetos diferentes que contengan las mismas
propiedades. Considera el siguiente código:

```
let objeto1 = {valor: 10};
let objeto2 = objeto1;
let objeto3 = {valor: 10};

console.log(objeto1 == objeto2);
// → true
console.log(objeto1 == objeto3);
// → false

objeto1.valor = 15;
console.log(objeto2.valor);
// → 15
console.log(objeto3.valor);
// → 10
```

{{index "tentacle (analogy)", [binding, "model of"]}}

Las vinculaciones `objeto1` y `objeto2` agarran el _mismo_ objeto, que es
la razon por la cual cambiar `objeto1` también cambia el valor de `objeto2`.
Se dice que tienen la misma _identidad_. La vinculación `objeto3` apunta a un
objeto diferente, que inicialmente contiene las mismas propiedades que
`objeto1` pero vive una vida separada.

{{index "const keyword", "let keyword"}}

Las vinculaciones también pueden ser cambiables o constantes, pero esto es
independiente de la forma en la que se comportan sus valores. Aunque los
valores numéricos no cambian, puedes usar una ((vinculación)) `let` para hacer
un seguimiento de un número que cambia al cambiar el valor al que apunta la
vinculación. Del mismo modo, aunque una vinculación `const` a un objeto no
pueda ser cambiada en si misma y continuará apuntando al mismo objeto,
los _contenidos_ de ese objeto pueden cambiar.

```{test: no}
const puntuacion = {visitantes: 0, locales: 0};
// Esto esta bien
puntuacion.visitantes = 1;
// Esto no esta permitido
puntuacion = {visitantes: 1, locales: 1};
```

{{index "== operator", [comparison, "of objects"], "deep comparison"}}

Cuando comparas objetos con el operador `==` en JavaScript, este los compara
por identidad: producirá `true` solo si ambos objetos son precisamente
el mismo valor. Comparar diferentes objetos retornara `false`, incluso
si tienen propiedades idénticas. No hay una operación de comparación "profunda"
incorporada en JavaScript, que compare objetos por contenidos,
pero es posible que la escribas tu mismo (que es uno de los
[ejercicios](datos#ejercicio_comparacion_profunda) al final de este capítulo).

## El diario del licántropo

{{index "weresquirrel example", lycanthropy, "addEntry function"}}

Asi que Jacques inicia su intérprete de JavaScript y establece el
entorno que necesita para mantener su ((diario)).

```{includeCode: true}
let diario = [];

function añadirEntrada(eventos, ardilla) {
  diario.push({eventos, ardilla});
}
```

{{index "curly braces", "{} (object)"}}

Ten en cuenta que el objeto agregado al diario se ve un poco extraño. En lugar
de declarar propiedades como `eventos: eventos`, simplemente da un
nombre de ((propiedad)). Este es un atajo que representa lo mismo—si el
nombre de propiedad en la notación de llaves no es seguido por un valor, su
el valor se toma de la vinculación con el mismo nombre.

Entonces, todas las noches a las diez—o algunas veces a la mañana siguiente,
después de bajar del estante superior de su biblioteca—Jacques registra el
día.


So then, every evening at ten—or sometimes the next morning, after
climbing down from the top shelf of his bookcase—Jacques records the
day.

```
añadirEntrada(["trabajo", "toque un arbol", "pizza", "sali a correr",
          "television"], false);
añadirEntrada(["trabajo", "helado", "coliflor", "lasaña",
          "toque un arbol", "me cepille los dientes"], false);
añadirEntrada(["fin de semana", "monte la bicicleta", "descanso", "nueces",
          "cerveza"], true);
```

Una vez que tiene suficientes puntos de datos, tiene la intención de utilizar
estadísticas para encontrar cuál de estos eventos puede estar
relacionado con la transformación a ardilla.

{{index correlation}}

La _correlación_ es una medida de ((dependencia)) entre variables estadísticas.
Una variable estadística no es lo mismo que una variable de programación.
En las estadísticas, normalmente tienes un conjunto de _medidas_,
y cada variable se mide para cada medida. La correlación entre variables
generalmente se expresa como un valor que
varia de -1 a 1. Una correlación de cero significa que las variables no estan
relacionadas. Una correlación de uno indica que las dos están perfectamente
relacionadas—si conoces una, también conoces la otra. Uno negativo también
significa que las variables están perfectamente relacionadas pero que son
opuestas—cuando una es verdadera, la otra es falsa.

{{index "phi coefficient"}}

Para calcular la medida de correlación entre dos variables booleanas,
podemos usar el _coeficiente phi_ (_ϕ_). Esta es una fórmula cuya entrada
es una ((tabla de frecuencias)) que contiene la cantidad de veces que
las diferentes combinaciones de las variables fueron observadas.
El resultado de la fórmula es un número entre -1 y 1 que describe
la correlación.

Podríamos tomar el evento de comer ((pizza)) y poner eso en una
tabla de frecuencias como esta, donde cada número indica la cantidad de
veces que ocurrió esa combinación en nuestras mediciones:

{{figure {url: "img/pizza-squirrel.svg", alt: "Eating pizza versus turning into a squirrel", width: "7cm"}}}

Si llamamos a esa tabla _n_, podemos calcular _ϕ_ usando la siguiente fórmula:

{{if html

<div>
<table style="border-collapse: collapse; margin-left: 1em;"><tr>
  <td style="vertical-align: middle"><em>ϕ</em> =</td>
  <td style="padding-left: .5em">
    <div style="border-bottom: 1px solid black; padding: 0 7px;"><em>n</em><sub>11</sub><em>n</em><sub>00</sub> −
      <em>n</em><sub>10</sub><em>n</em><sub>01</sub></div>
    <div style="padding: 0 7px;">√<span style="border-top: 1px solid black; position: relative; top: 2px;">
      <span style="position: relative; top: -4px"><em>n</em><sub>1•</sub><em>n</em><sub>0•</sub><em>n</em><sub>•1</sub><em>n</em><sub>•0</sub></span>
    </span></div>
  </td>
</tr></table>
</div>

if}}

{{if tex

[\begin{equation}\varphi = \frac{n_{11}n_{00}-n_{10}n_{01}}{\sqrt{n_{1\bullet}n_{0\bullet}n_{\bullet1}n_{\bullet0}}}\end{equation}]{latex}

if}}

(Si en este momento estas bajando el libro para enfocarte en un terrible
flashback a la clase de matemática de 10° grado—espera!
No tengo la intención de torturarte con infinitas páginas de notación
críptica—solo esta fórmula para ahora. E incluso con esta,
todo lo que haremos es convertirla en JavaScript.)

La notación [_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} indica
el número de mediciones donde la primera variable (ardilla) es
falso (0) y la segunda variable (pizza) es verdadera (1). En la tabla
de pizza, [_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} es 9.

El valor [_n_~1•~]{if html}[[$n_{1\bullet}$]{latex}]{if tex} se refiere
a la suma de todas las medidas donde la primera variable es verdadera, que
es 5 en la tabla de ejemplo. Del mismo modo, [_n_~•0~]{if
html}[[$n_{\bullet0}$]{latex}]{if tex} se refiere a la suma de las
mediciones donde la segunda variable es falsa.

{{index correlation, "phi coefficient"}}

Entonces para la tabla de pizza, la parte arriba de la línea de división (el
dividendo) sería 1×76−4×9 = 40, y la parte inferior (el
divisor) sería la raíz cuadrada de 5×85×10×80, o [√340000]{if
html}[[$\sqrt{340000}$]{latex}]{if tex}. Esto da _ϕ_ ≈
0.069, que es muy pequeño. Comer ((pizza)) no parece tener
influencia en las transformaciones.

## Calculando correlación

{{index [array, "as table"], [nesting, "of arrays"]}}

Podemos representar una ((tabla)) de dos-por-dos en JavaScript con un
array de cuatro elementos (`[76, 9, 4, 1]`). También podríamos usar otras
representaciones, como un array que contiene dos arrays de dos elementos
(`[[76, 9], [4, 1]]`) o un objeto con nombres de propiedad como `"11"` y
`"01"`, pero el array plano es simple y hace que las expresiones que
acceden a la tabla agradablemente cortas. Interpretaremos los índices del
array como ((número binario))s de dos-((bits)) , donde el dígito más a la
izquierda (más significativo) se refiere a la variable ardilla y el digito
mas a la derecha (menos significativo) se refiere a la variable de evento.
Por ejemplo, el número binario `10` se refiere al caso en que Jacques se
convirtió en una ardilla, pero el evento (por ejemplo, "pizza") no ocurrió.
Esto ocurrió cuatro veces. Y dado que el `10` binario es 2 en notación decimal,
almacenaremos este número en el índice 2 del array.

{{index "phi coefficient", "phi function"}}

{{id phi_function}}

Esta es la función que calcula el coeficiente _ϕ_ de tal array:

```{includeCode: strip_log, test: clip}
function phi(tabla) {
  return (tabla[3] * tabla[0] - tabla[2] * tabla[1]) /
    Math.sqrt((tabla[2] + tabla[3]) *
              (tabla[0] + tabla[1]) *
              (tabla[1] + tabla[3]) *
              (tabla[0] + tabla[2]));
}

console.log(phi([76, 9, 4, 1]));
// → 0.068599434
```

{{index "square root", "Math.sqrt function"}}

Esta es una traducción directa de la fórmula _ϕ_ a JavaScript.
`Math.sqrt` es la función de raíz cuadrada, proporcionada por el objeto
`Math` en un entorno de JavaScript estándar. Tenemos que sumar dos campos
de la tabla para obtener campos como [n~1•~]{if
html}[[$n_{1\bullet}$]{latex}]{if tex} porque las sumas de filas o
columnas no se almacenan directamente en nuestra estructura de datos.

{{index "JOURNAL data set"}}

Jacques mantuvo su diario por tres meses. El ((conjunto de datos)) resultante
está disponible en la [caja de arena](https://eloquentjavascript.net/code#4)
para este capítulo[([_eloquentjavascript.net/code#4_](https://eloquentjavascript.net/code#4))]{if
book}, donde se almacena en la vinculación `JOURNAL`, y en un
[archivo](https://eloquentjavascript.net/code/journal.js) descargable.

{{index "tableFor function"}}

Para extraer una ((tabla)) de dos por dos para un evento en específico del
diario, debemos hacer un ciclo a traves de todas las entradas y contar
cuántas veces ocurre el evento en relación a las transformaciones de ardilla.

```{includeCode: strip_log}
function tablaPara(evento, diario) {
  let tabla = [0, 0, 0, 0];
  for (let i = 0; i < diario.length; i++) {
    let entrada = diario[i], index = 0;
    if (entrada.eventos.includes(evento)) index += 1;
    if (entrada.ardilla) index += 2;
    tabla[index] += 1;
  }
  return tabla;
}

console.log(tablaPara("pizza", JOURNAL));
// → [76, 9, 4, 1]
```

{{index [array, searching], "includes method"}}

Los array tienen un método `includes` ("incluye") que verifica si un valor dado
existe en el array. La función usa eso para determinar si el nombre del evento
en el que estamos interesados forma parte de la lista de eventos para
un determinado día.

{{index [array, indexing]}}

El cuerpo del ciclo en `tablaPara` determina en cual caja de la tabla
cae cada entrada del diario al verificar si la entrada contiene
el evento específico que nos interesa y si el evento ocurre
junto con un incidente de ardilla. El ciclo luego agrega uno a la caja correcta
en la tabla.

Ahora tenemos las herramientas que necesitamos para calcular las
((correlaciónes)) individuales. El único paso que queda es encontrar una
correlación para cada tipo de evento que se escribio en el diario
y ver si algo se destaca.

{{id for_of_loop}}

## Ciclos de array

{{index "for loop", loop, [array, iteration]}}

En la función `tablaPara`, hay un ciclo como este:

```
for (let i = 0; i < DIARIO.length; i++) {
  let entrada = DIARIO[i];
  // Hacer con algo con la entrada
}
```

Este tipo de ciclo es común en JavaScript clasico—ir a traves de los arrays
un elemento a la vez es algo que surge mucho, y para hacer eso
correrias un contador sobre la longitud del array y elegirías cada
elemento en turnos.

Hay una forma más simple de escribir tales ciclos en JavaScript moderno.

```
for (let entrada of DIARIO) {
  console.log(`${entrada.eventos.length} eventos.`);
}
```

{{index "for/of loop"}}

Cuando un ciclo `for` se vea de esta manera, con la palabra `of` ("de")
después de una definición de variable, recorrerá los elementos del valor
dado después `of`. Esto funciona no solo para arrays, sino también para
strings y algunas otras estructuras de datos. Vamos a discutir _como_ funciona
en el [Capítulo 6](objeto).

{{id analysis}}

## El análisis final

{{index journal, "weresquirrel example", "journalEvents function"}}

Necesitamos calcular una correlación para cada tipo de evento que ocurra
en el conjunto de datos. Para hacer eso, primero tenemos que _encontrar_
cada tipo de evento.

{{index "includes method", "push method"}}

```{includeCode: "strip_log"}
function eventosDiario(diario) {
  let eventos = [];
  for (let entrada of diario) {
    for (let evento of entrada.eventos) {
      if (!eventos.includes(evento)) {
        eventos.push(evento);
      }
    }
  }
  return eventos;
}

console.log(eventosDiario(DIARIO));
// → ["zanahoria", "ejercicio", "fin de semana", "pan", …]
```

Yendo a traves de todos los eventos, y agregando aquellos que aún no están en
allí en el array `eventos`, la función recolecta cada tipo de evento.

Usando eso, podemos ver todos las ((correlaciones)).

```{test: no}
for (let evento of eventosDiario(DIARIO)) {
  console.log(evento + ":", phi(tablaPara(evento, DIARIO)));
}
// → zanahoria:      0.0140970969
// → ejercicio:      0.0685994341
// → fin de semana:  0.1371988681
// → pan:           -0.0757554019
// → pudin:         -0.0648203724
// and so on...
```

La mayoría de las correlaciones parecen estar cercanas a cero. Come
zanahorias, pan o pudín aparentemente no desencadena la licantropía de ardilla.
_Parece_ ocurrir un poco más a menudo los fines de semana. Filtremos los
resultados para solo mostrar correlaciones mayores que 0.1 o menores que -0.1.

```{test: no, startCode: true}
for (let evento of eventosDiario(DIARIO)) {
  let correlacion = phi(tablaPara(evento, DIARIO));
  if (correlacion > 0.1 || correlacion < -0.1) {
    console.log(evento + ":", correlacion);
  }
}
// → fin de semana:           0.1371988681
// → me cepille los dientes: -0.3805211953
// → dulces:                  0.1296407447
// → trabajo:                -0.1371988681
// → spaghetti:               0.2425356250
// → leer:                    0.1106828054
// → nueces:                  0.5902679812
```

A-ha! Hay dos factores con una ((correlación)) que es claramente más fuerte
que las otras. Comer ((nueces)) tiene un fuerte efecto positivo en
la posibilidad de convertirse en una ardilla, mientras que cepillarse
los dientes tiene un significativo efecto negativo.

Interesante. Intentemos algo.

```{includeCode: strip_log}
for (let entrada of DIARIO) {
  if (entrada.eventos.includes("nueces") &&
     !entrada.eventos.includes("me cepille los dientes")) {
    entrada.eventos.push("dientes con nueces");
  }
}
console.log(phi(tablaPara("dientes con nueces", DIARIO)));
// → 1
```

Ese es un resultado fuerte. El fenómeno ocurre precisamente cuando Jacques
come ((nueces)) y no se cepilla los dientes. Si tan solo él no hubiese sido
tan flojo con su higiene dental, él nunca habría notado su aflicción.

Sabiendo esto, Jacques deja de comer nueces y descubre que sus transformaciones
no vuelven.

{{index "weresquirrel example"}}

Durante algunos años, las cosas van bien para Jacques. Pero en algún momento él
pierde su trabajo. Porque vive en un país desagradable donde no tener trabajo
significa que no tiene servicios médicos, se ve obligado a trabajar con
a ((circo)) donde actua como _El Increible Hombre-Ardilla_,
llenando su boca con mantequilla de maní antes de cada presentación.

Un día, harto de esta existencia lamentable, Jacques no puede cambiar
de vuelta a su forma humana, salta a través de una grieta en la carpa del
circo, y se desvanece en el bosque. Nunca se le ve de nuevo.

## Arrayología avanzada

{{index [array, methods], method}}

Antes de terminar el capítulo, quiero presentarte algunos conceptos extras
relacionados a los objetos. Comenzaré introduciendo algunos en métodos de
arrays útiles generalmente.

{{index "push method", "pop method", "shift method", "unshift method"}}

Vimos `push` y `pop`, que agregan y removen elementos en el
final de un array, [anteriormente](datos#array_methods) en este
capítulo. Los métodos correspondientes para agregar y remover cosas en
el comienzo de un array se llaman `unshift` y `shift`.

```
let listaDeTareas = [];
function recordar(tarea) {
  listaDeTareas.push(tarea);
}
function obtenerTarea() {
  return listaDeTareas.shift();
}
function recordarUrgentemente(tarea) {
  listaDeTareas.unshift(tarea);
}
```

{{index "task management example"}}

Ese programa administra una cola de tareas. Agregas tareas al final de la
cola al llamar `recordar("verduras")`, y cuando estés listo para hacer
algo, llamas a `obtenerTarea()` para obtener (y eliminar) el elemento frontal
de la cola. La función `recordarUrgentemente` también agrega una tarea pero
la agrega al frente en lugar de a la parte posterior de la cola.

{{index [array, searching], "indexOf method", "lastIndexOf method"}}

Para buscar un valor específico, los arrays proporcionan un método `indexOf`
("indice de").
Este busca a través del array desde el principio hasta el final y retorna el
índice en el que se encontró el valor solicitado—o -1 si este no fue encontrado.
Para buscar desde el final en lugar del inicio, hay un método similar
llamado `lastIndexOf` ("ultimo indice de").

```
console.log([1, 2, 3, 2, 1].indexOf(2));
// → 1
console.log([1, 2, 3, 2, 1].lastIndexOf(2));
// → 3
```

Tanto `indexOf` como `lastIndexOf` toman un segundo argumento opcional que
indica dónde comenzar la búsqueda.

{{index "slice method", [array, indexing]}}

Otro método fundamental de array es `slice` ("rebanar"), que toma índices de
inicio y fin y retorna un array que solo tiene los elementos entre ellos.
El índice de inicio es inclusivo, el índice final es exclusivo.

```
console.log([0, 1, 2, 3, 4].slice(2, 4));
// → [2, 3]
console.log([0, 1, 2, 3, 4].slice(2));
// → [2, 3, 4]
```

{{index [string, indexing]}}

Cuando no se proporcione el índice final, `slice` tomará todos los elementos
después del índice de inicio. También puedes omitir el índice de inicio
para copiar todo el array.

{{index concatenation, "concat method"}}

El método `concat` ("concatenar") se puede usar para unir arrays y asi crear un
nuevo array, similar a lo que hace el operador `+` para los strings.

El siguiente ejemplo muestra tanto `concat` como `slice` en acción. Toma un
array y un índice, y retorna un nuevo array que es una copia del
array original pero eliminando al elemento en el índice dado:

```
function remover(array, indice) {
  return array.slice(0, indice)
    .concat(array.slice(indice + 1));
}
console.log(remover(["a", "b", "c", "d", "e"], 2));
// → ["a", "b", "d", "e"]
```

Si a `concat` le pasas un argumento que no es un array, ese valor
sera agregado al nuevo array como si este fuera un array de un solo elemento.

## Strings y sus propiedades

{{index [string, properties]}}

Podemos leer propiedades como `length` y `toUpperCase` de valores string.
Pero si intentas agregar una nueva propiedad, esta no se mantiene.

```
let kim = "Kim";
kim.edad = 88;
console.log(kim.edad);
// → undefined
```

Los valores de tipo string, número, y Booleano no son objetos, y aunque
el lenguaje no se queja si intentas establecer nuevas propiedades en
ellos, en realidad no almacena esas propiedades. Como se mencionó antes,
tales valores son inmutables y no pueden ser cambiados.

{{index [string, methods], "slice method", "indexOf method", [string, searching]}}

Pero estos tipos tienen propiedades integradas. Cada valor de string tiene un
numero de metodos. Algunos muy útiles son `slice` e `indexOf`,
que se parecen a los métodos de array de los mismos nombres.

```
console.log("panaderia".slice(0, 3));
// → pan
console.log("panaderia".indexOf("a"));
// → 1
```

Una diferencia es que el `indexOf` de un string puede buscar por un string
que contenga más de un carácter, mientras que el método correspondiente al
array solo busca por un elemento único.

```
console.log("uno dos tres".indexOf("tres"));
// → 8
```

{{index whitespace, "trim method"}}

El método `trim` ("recortar") elimina los espacios en blanco (espacios, saltos
de linea, tabulaciones y caracteres similares) del inicio y final de un string.

```
console.log("  okey \n ".trim());
// → okey
```

La función `alcocharConCeros` del [capítulo anterior](funciones) también
existe como un método. Se llama `padStart` ("alcohar inicio") y
toma la longitud deseada y el carácter de relleno como argumentos.

```
console.log(String(6).padStart(3, "0"));
// → 006
```

{{id split}}

Puedes dividir un string en cada ocurrencia de otro string con el metodo
`split` ("dividir"), y unirlo nuevamente con `join` ("unir").

```
let oracion = "Los pajaros secretarios se especializan en pisotear";
let palabras = oracion.split(" ");
console.log(palabras);
// → ["Los", "pajaros", "secretarios", "se", "especializan", "en", "pisotear"]
console.log(palabras.join(". "));
// → Los. pajaros. secretarios. se. especializan. en. pisotear
```

{{index "repeat method"}}

Se puede repetir un string con el método `repeat` ("repetir"), el cual
crea un nuevo string que contiene múltiples copias concatenadas
del string original.

```
console.log("LA".repeat(3));
// → LALALA
```

{{index ["length property", "for string"], [string, indexing]}}

Ya hemos visto la propiedad `length` en los valores de tipo string. Acceder a
los caracteres individuales en un string es similar a acceder a los elementos
de un array (con una diferencia que discutiremos en el
[Capítulo 6](orden_superior#unidades_de_codigo)).

```
let string = "abc";
console.log(string.length);
// → 3
console.log(string[1]);
// → b
```

{{id rest_parameters}}

## Parámetros restantes

{{index "Math.max function"}}

Puede ser útil para una función aceptar cualquier cantidad de ((argumento))s.
Por ejemplo, `Math.max` calcula el máximo de _todos_ los argumentos que le
son dados.

{{indexsee "period character", "max example", spread}}

Para escribir tal función, pones tres puntos antes del ultimo ((parámetro))
de la función, asi:

```
function maximo(...numeros) {
  let resultado = -Infinity;
  for (let numero of numeros) {
    if (numero > resultado) resultado = numero;
  }
  return resultado;
}
console.log(maximo(4, 1, 9, -2));
// → 9
```

Cuando se llame a una función como esa, el _((parámetro restante))_ está
vinculado a un array que contiene todos los argumentos adicionales. Si hay
otros parámetros antes que él, sus valores no seran parte de ese array.
Cuando, tal como en `maximo`, sea el único parámetro, contendrá todos
los argumentos.

{{index "function application"}}

Puedes usar una notación de tres-puntos similar para _llamar_ una función con
un array de argumentos.

```
let numeros = [5, 1, 7];
console.log(max(...numeros));
// → 7
```

Esto "((extiende))" al array en la llamada de la función, pasando sus
elementos como argumentos separados. Es posible incluir un array de esa
manera, junto con otros argumentos, como en `max(9, ...numeros, 2)`.

{{index array, "square brackets"}}

La notación de corchetes para crear arrays permite al operador de tres-puntos
extender otro array en el nuevo array:


Square bracket array notation similarly allows the triple-dot operator
to spread another array into the new array:

```
let palabras = ["nunca", "entenderas"];
console.log(["tu", ...palabras, "completamente"]);
// → ["tu", "nunca", "entenderas", "completamente"]
```

## El objeto Math

{{index "Math object", "Math.min function", "Math.max function", "Math.sqrt function", minimum, maximum, "square root"}}

Como hemos visto, `Math` es una bolsa de sorpresas de utilidades relacionadas
a los numeros, como `Math.max` (máximo), `Math.min` (mínimo) y
`Math.sqrt` (raíz cuadrada).

{{index namespace, "namespace pollution", object}}

{{id namespace_pollution}}

El objeto `Math` es usado como un contenedor que agrupa un grupo de
funcionalidades relacionadas. Solo hay un objeto `Math`, y casi nunca es
útil como un valor. Más bien, proporciona un _espacio de nombre_
para que todos estas funciones y valores no tengan que ser vinculaciones
globales.

{{index [binding, naming]}}

Tener demasiadas vinculaciones globales "contamina" el espacio de nombres.
Cuanto más nombres hayan sido tomados, es más probable que accidentalmente
sobrescribas el valor de algunas vinculaciones existentes. Por ejemplo, no es
es poco probable que quieras nombrar algo `max` en alguno de tus programas.
Dado que la función `max` ya incorporada en JavaScript está escondida dentro del
Objeto `Math`, no tenemos que preocuparnos por sobrescribirla.

{{index "let keyword", "const keyword"}}

Muchos lenguajes te detendrán, o al menos te advertirán, cuando estes por
definir una vinculación con un nombre que ya este tomado. JavaScript hace
esto para vinculaciones que hayas declarado con `let` o` const`
pero-perversamente-no para vinculaciones estándar, ni para
vinculaciones declaradas con `var` o `function`.

{{index "Math.cos function", "Math.sin function", "Math.tan function", "Math.acos function", "Math.asin function", "Math.atan function", "Math.PI constant", cosine, sine, tangent, "PI constant", pi}}

De vuelta al objeto `Math`. Si necesitas hacer ((trigonometría)), `Math` te
puede ayudar. Contiene `cos` (coseno), `sin` (seno) y `tan`
(tangente), así como sus funciones inversas, `acos`, `asin`, y
`atan`, respectivamente. El número π (pi)—o al menos la aproximación
más cercano que cabe en un número de JavaScript—está disponible como
`Math.PI`. Hay una vieja tradición en la programación de escribir los nombres
de los valores ((constantes)) en mayúsculas.

```{test: no}
function puntoAleatorioEnCirculo(radio) {
  let angulo = Math.random() * 2 * Math.PI;
  return {x: radio * Math.cos(angulo),
          y: radio * Math.sin(angulo)};
}
console.log(puntoAleatorioEnCirculo(2));
// → {x: 0.3667, y: 1.966}
```

Si los senos y los cosenos son algo con lo que no estas familiarizado,
no te preocupes. Cuando se usen en este libro, en el
[Capítulo 14](dom#seno_coseno), te los explicaré.

{{index "Math.random function", "random number"}}

El ejemplo anterior usó `Math.random`. Esta es una función que
retorna un nuevo número pseudoaleatorio entre cero (inclusivo) y uno
(exclusivo) cada vez que la llamas.

```{test: no}
console.log(Math.random());
// → 0.36993729369714856
console.log(Math.random());
// → 0.727367032552138
console.log(Math.random());
// → 0.40180766698904335
```

{{index "pseudorandom number", "random number"}}

Aunque las computadoras son máquinas deterministas—siempre reaccionan de la
misma manera manera dada la misma entrada—es posible hacer que
produzcan números que parecen aleatorios. Para hacer eso, la máquina mantiene
algun valor escondido, y cada vez que le pidas un nuevo número aleatorio,
realiza calculos complicados en este valor oculto para crear un nuevo valor.
Esta almacena un nuevo valor y retorna un número derivado de él.
De esta manera, puede producir números nuevos y difíciles de predecir de
una manera que _parece_ aleatoria.

{{index rounding, "Math.floor function"}}

Si queremos un número entero al azar en lugar de uno fraccionario, podemos
usar `Math.floor` (que redondea hacia abajo al número entero más cercano) con
el resultado de `Math.random`.

```{test: no}
console.log(Math.floor(Math.random() * 10));
// → 2
```

Multiplicar el número aleatorio por 10 nos da un número mayor que o
igual a cero e inferior a 10. Como `Math.floor` redondea hacia abajo, esta
expresión producirá, con la misma probabilidad, cualquier número desde 0 hasta
9.

{{index "Math.ceil function", "Math.round function", "Math.abs function", "absolute value"}}

También están las funciones `Math.ceil` (que redondea hacia arriba hasta llegar
al número entero mas cercano), `Math.round` (al número entero más cercano), y
`Math.abs`, que toma el valor absoluto de un número, lo que significa que
niega los valores negativos pero deja los positivos tal y como estan.

## Desestructurar

{{index "phi function"}}

Volvamos a la función `phi` por un momento:

```{test: wrap}
function phi(tabla) {
  return (tabla[3] * tabla[0] - tabla[2] * tabla[1]) /
    Math.sqrt((tabla[2] + tabla[3]) *
              (tabla[0] + tabla[1]) *
              (tabla[1] + tabla[3]) *
              (tabla[0] + tabla[2]));
}
```

{{index "destructuring binding", parameter}}

Una de las razones por las que esta función es incómoda de leer es que
tenemos una vinculación apuntando a nuestro array, pero preferiríamos tener
vinculaciones para los _elementos_ del array, es decir, `let n00 = tabla[0]`
y así sucesivamente. Afortunadamente, hay una forma concisa de hacer esto en
JavaScript.

```
function phi([n00, n01, n10, n11]) {
  return (n11 * n00 - n10 * n01) /
    Math.sqrt((n10 + n11) * (n00 + n01) *
              (n01 + n11) * (n00 + n10));
}
```

{{index "let keyword", "var keyword", "const keyword"}}

Esto también funciona para ((vinculaciones)) creadas con `let`, `var`, o
`const`. Si sabes que el valor que estas vinculando es un array, puedes
usar ((corchetes)) para "mirar dentro" del valor, y asi vincular sus
contenidos.

{{index object, "curly braces"}}

Un truco similar funciona para objetos, utilizando llaves en lugar de corchetes.

```
let {nombre} = {nombre: "Faraji", edad: 23};
console.log(nombre);
// → Faraji
```

{{index null, undefined}}

Ten en cuenta que si intentas desestructurar `null` o `undefined`, obtendrás un
error, igual como te pasaria si intentaras acceder directamente a una propiedad
de esos valores.

## JSON

{{index [array, representation], [object, representation], "data format"}}

Ya que las propiedades solo agarran su valor, en lugar de contenerlo,
los objetos y arrays se almacenan en la ((memoria)) de la computadora como
secuencias de bits que contienen las _((dirección))es_—el lugar en la memoria—de
sus contenidos. Asi que un array con otro array dentro de el consiste
en (al menos) una región de memoria para el array interno, y otra para
el array externo, que contiene (entre otras cosas) un número binario que
representa la posición del array interno.

Si deseas guardar datos en un archivo para más tarde, o para enviarlo a otra
computadora a través de la red, tienes que convertir de alguna manera estos
enredos de direcciones de memoria a una descripción que se puede almacenar o
enviar. Supongo, que _podrías_ enviar toda la memoria de tu computadora
junto con la dirección del valor que te interesa, pero ese no parece el
mejor enfoque.

{{indexsee "JavaScript Object Notation", JSON}}

{{index serialization, "World Wide Web"}}

Lo que podemos hacer es _serializar_ los datos. Eso significa que son
convertidos a una descripción plana. Un formato de serialización popular llamado
_((JSON))_ (pronunciado "Jason"), que significa JavaScript Object
Notation ("Notación de Objetos JavaScript"). Es ampliamente utilizado
como un formato de almacenamiento y comunicación de datos
en la Web, incluso en otros lenguajes diferentes a JavaScript.

{{index array, object, [quoting, "in JSON"], comment}}

JSON es similar a la forma en que JavaScript escribe arrays y objetos,
con algunas restricciones. Todos los nombres de propiedad deben estar
rodeados por comillas dobles, y solo se permiten expresiones de datos
simples—sin llamadas a función, vinculaciones o cualquier otra cosa que
involucre computaciones reales. Los comentarios no están permitidos en JSON.

Una entrada de diario podria verse así cuando se representa como datos JSON:

```{lang: "application/json"}
{
  "ardilla": false,
  "eventos": ["trabajo", "toque un arbol", "pizza", "sali a correr"]
}
```

{{index "JSON.stringify function", "JSON.parse function", serialization, deserialization, parsing}}

JavaScript nos da las funciones `JSON.stringify` y `JSON.parse` para
convertir datos hacia y desde este formato. El primero toma un valor en
JavaScript y retorna un string codificado en JSON. La segunda toma
un string como ese y lo convierte al valor que este codifica.

```
let string = JSON.stringify({ardilla: false,
                             eventos: ["fin de semana"]});
console.log(string);
// → {"ardilla":false,"eventos":["fin de semana"]}
console.log(JSON.parse(string).eventos);
// → ["fin de semana"]
```

## Resumen

Los objetos y arrays (que son un tipo específico de objeto) proporcionan formas
de agrupar varios valores en un solo valor. Conceptualmente, esto nos permite
poner un montón de cosas relacionadas en un bolso y correr alredor con el
bolso, en lugar de envolver nuestros brazos alrededor de todas las cosas
individuales, tratando de aferrarnos a ellas por separado.

La mayoría de los valores en JavaScript tienen propiedades, las excepciones
son `null` y `undefined`. Se accede a las propiedades usando `valor.propiedad` o
`valor["propiedad"]`. Los objetos tienden a usar nombres para sus propiedades
y almacenar más o menos un conjunto fijo de ellos. Los arrays, por el otro lado,
generalmente contienen cantidades variables de valores conceptualmente
idénticos y usa números (comenzando desde 0) como los nombres de sus propiedades.

Hay _algunas_ propiedades con nombre en los arrays, como `length` y un
numero de metodos. Los métodos son funciones que viven en propiedades y
(por lo general) actuan sobre el valor del que son una propiedad.

Puedes iterar sobre los arrays utilizando un tipo especial de ciclo `for`—`for
(let elemento of array)`.

## Ejercicios

### La suma de un rango

{{index "summing (exercise)"}}

La [introducción](intro) de este libro aludía a lo siguiente como una
buena forma de calcular la suma de un rango de números:

```{test: no}
console.log(suma(rango(1, 10)));
```

{{index "range function", "sum function"}}

Escribe una función `rango` que tome dos argumentos, `inicio` y `final`,
y retorne un array que contenga todos los números desde `inicio` hasta
(e incluyendo) `final`.

Luego, escribe una función `suma` que tome un array de números y
retorne la suma de estos números. Ejecuta el programa de ejemplo y ve
si realmente retorna 55.

{{index "optional argument"}}

Como una misión extra, modifica tu función `rango` para tomar un
tercer argumento opcional que indique el valor de "paso" utilizado para cuando
construyas el array. Si no se da ningún paso, los elementos suben en
incrementos de uno, correspondiedo al comportamiento anterior. La llamada
a la función `rango(1, 10, 2)` deberia retornar `[1, 3, 5, 7, 9]`. Asegúrate de
que también funcione con valores de pasos negativos para que `rango(5, 2, -1)`
produzca `[5, 4, 3, 2]`.

{{if interactive

```{test: no}
// Tu código aquí.

console.log(rango(1, 10));
// → [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log(rango(5, 2, -1));
// → [5, 4, 3, 2]
console.log(sum(rango(1, 10)));
// → 55
```

if}}

{{hint

{{index "summing (exercise)", [array, creation], "square brackets"}}

Construir un array se realiza más fácilmente al inicializar primero una
vinculación a `[]` (un array nuevo y vacío) y llamando repetidamente a su
método `push` para agregar un valor. No te olvides de retornar el array al
final de la función.

{{index [array, indexing], comparison}}

Dado que el límite final es inclusivo, deberias usar el operador `<=`
en lugar de `<` para verificar el final de tu ciclo.

{{index "arguments object"}}

El parámetro de paso puede ser un parámetro opcional que por defecto (usando
el operador `=`) tenga el valor 1.

{{index "range function", "for loop"}}

Hacer que `rango` entienda valores de paso negativos es probablemente
mas facil de realizar al escribir dos ciclos por separado—uno para contar
hacia arriba y otro para contar hacia abajo—ya que la comparación que verifica
si el ciclo está terminado necesita ser `>=` en lugar de `<=` cuando se cuenta
hacia abajo.

También puede que valga la pena utilizar un paso predeterminado
diferente, es decir -1, cuando el final del rango sea menor que el inicio.
De esa manera, `rango(5, 2)` retornaria algo significativo, en lugar de quedarse
atascado en un ((ciclo infinito)). Es posible referirse a parámetros anteriores
en el valor predeterminado de un parámetro.

hint}}

### Revirtiendo un array

{{index "reversing (exercise)", "reverse method", [array, methods]}}

Los arrays tienen un método `reverse` que cambia al array invirtiendo
el orden en que aparecen sus elementos. Para este ejercicio, escribe dos
funciones, `revertirArray` y `revertirArrayEnSuLugar`. El primero,
`revertirArray`, toma un array como argumento y produce un _nuevo_ array
que tiene los mismos elementos pero en el orden inverso. El segundo,
`revertirArrayEnSuLugar`, hace lo que hace el método` reverse`:
_modifica_ el array dado como argumento invirtiendo sus elementos.
Ninguno de los dos puede usar el método `reverse` estándar.

{{index efficiency, "pure function", "side effect"}}

Pensando en las notas acerca de los efectos secundarios y las funciones puras en
el [capítulo anterior](funciones#pura), qué variante esperas que sea
útil en más situaciones? Cuál corre más rápido?

{{if interactive

```{test: no}
// Tu código aquí.

console.log(revertirArray(["A", "B", "C"]));
// → ["C", "B", "A"];
let valorArray = [1, 2, 3, 4, 5];
revertirArrayEnSuLugar(valorArray);
console.log(valorArray);
// → [5, 4, 3, 2, 1]
```

if}}

{{hint

{{index "reversing (exercise)"}}

Hay dos maneras obvias de implementar `revertirArray`. La primera es
simplemente pasar a traves del array de entrada de adelante hacia atrás y
usar el metodo `unshift` en el nuevo array para insertar cada elemento en su
inicio. La segundo es hacer un ciclo sobre el array de entrada de atrás hacia
adelante y usar el método `push`. Iterar sobre un array al revés requiere de
una especificación (algo incómoda) del ciclo `for`, como
`(let i = array.length - 1; i >= 0; i--)`.

{{index "slice method"}}

Revertir al array en su lugar es más difícil. Tienes que tener cuidado de no
sobrescribir elementos que necesitarás luego. Usar `revertirArray` o
de lo contrario, copiar toda el array (`array.slice(0)` es una buena forma de
copiar un array) funciona pero estás haciendo trampa.

El truco consiste en _intercambiar_ el primer y el último elemento,
luego el segundo y el penúltimo, y así sucesivamente. Puedes hacer esto
haciendo un ciclo basandote en la mitad de la longitud del array
(use `Math.floor` para redondear—no necesitas tocar el elemento del medio en
un array con un número impar de elementos) e intercambiar el elemento en
la posición `i` con el de la posición `array.length - 1 - i`. Puedes usar una
vinculación local para aferrarse brevemente a uno de los elementos,
sobrescribirlo con su imagen espejo, y luego poner el valor de la vinculación
local en el lugar donde solía estar la imagen espejo.

hint}}

{{id list}}

### Una lista

{{index "data structure", "list (exercise)", "linked list", object, array, collection}}

Los objetos, como conjuntos genéricos de valores, se pueden usar para construir
todo tipo de estructuras de datos. Una estructura de datos común es la
_lista_ (no confundir con un array). Una lista es un conjunto anidado
de objetos, con el primer objeto conteniendo una referencia al segundo, el
segundo al tercero, y así sucesivamente.

```{includeCode: true}
let lista = {
  valor: 1,
  resto: {
    valor: 2,
    resto: {
      valor: 3,
      resto: null
    }
  }
};
```

Los objetos resultantes forman una cadena, como esta:

{{figure {url: "img/linked-list.svg", alt: "Una lista vinculada",width: "8cm"}}}

{{index "structure sharing", memory}}

Algo bueno de las listas es que pueden compartir partes de su
estructura. Por ejemplo, si creo dos nuevos valores `{valor: 0, resto:
lista}` y `{valor: -1, resto: lista}` (con `lista` refiriéndose a la
vinculación definida anteriormente), ambos son listas independientes, pero
comparten la estructura que conforma sus últimos tres elementos.
La lista original también sigue siendo una lista válida de tres elementos.

Escribe una función `arrayALista` que construya una estructura de lista como
el que se muestra arriba cuando se le da `[1, 2, 3]` como argumento.
También escribe una función `listaAArray` que produzca un array de una lista.
Luego agrega una función de utilidad `preceder`, que tome un elemento y
una lista y creé una nueva lista que agrega el elemento al frente de la lista
de entrada, y `posicion`, que toma una lista y un número y retorne el
elemento en la posición dada en la lista (con cero refiriéndose al
primer elemento) o `undefined` cuando no exista tal elemento.

{{index recursion}}

Si aún no lo has hecho, también escribe una versión recursiva de `posicion`.

{{if interactive

```{test: no}
// Tu código aquí.

console.log(arrayALista([10, 20]));
// → {valor: 10, resto: {valor: 20, resto: null}}
console.log(listaAArray(arrayALista([10, 20, 30])));
// → [10, 20, 30]
console.log(preceder(10, preceder(20, null)));
// → {valor: 10, resto: {valor: 20, resto: null}}
console.log(posicion(arrayALista([10, 20, 30]), 1));
// → 20
```

if}}

{{hint

{{index "list (exercise)", "linked list"}}

Crear una lista es más fácil cuando se hace de atrás hacia adelante. Entonces
`arrayALista` podría iterar sobre el array hacia atrás
(ver ejercicio anterior) y, para cada elemento, agregar un objeto a la lista.
Puedes usar una vinculación local para mantener la parte de la lista que se
construyó hasta el momento y usar una asignación como
`lista = {valor: X, resto: lista}` para agregar un elemento.

{{index "for loop"}}

Para correr a traves de una lista (en `listaAArray` y `posicion`),
una especificación del ciclo `for` como esta se puede utilizar:

```
for (let nodo = lista; nodo; nodo = nodo.resto) {}
```

Puedes ver cómo eso funciona? En cada iteración del ciclo, `nodo` apunta
a la sublista actual, y el cuerpo puede leer su propiedad `valor` para
obtener el elemento actual. Al final de una iteración, `nodo` se mueve a
la siguiente sublista. Cuando eso es nulo, hemos llegado al final de la
lista y el ciclo termina.

{{index recursion}}

La versión recursiva de `posición`, de manera similar, mirará a una
parte más pequeña de la "cola" de la lista y, al mismo tiempo, contara atrás
el índice hasta que llegue a cero, en cuyo punto puede retornar la propiedad
`valor` del nodo que está mirando. Para obtener el elemento cero de una lista,
simplemente toma la propiedad `valor` de su nodo frontal.
Para obtener el elemento _N_ + 1, toma el elemento _N_ de la lista
que este en la propiedad `resto` de esta lista.

hint}}

{{id exercise_deep_compare}}

### Comparación profunda

{{index "deep comparison (exercise)", comparison, "deep comparison", "== operator"}}

El operador `==` compara objetos por identidad. Pero a veces
preferirias comparar los valores de sus propiedades reales.

Escribe una función `igualdadProfunda` que toma dos valores y retorne `true`
solo si tienen el mismo valor o son objetos con las mismas
propiedades, donde los valores de las propiedades sean iguales cuando
comparadas con una llamada recursiva a `igualdadProfunda`.

{{index null, "=== operator", "typeof operator"}}

Para saber si los valores deben ser comparados directamente (usa el
operador `==` para eso) o si deben tener sus propiedades comparadas,
puedes usar el operador `typeof`. Si produce `"object"` para ambos valores,
deberías hacer una comparación profunda. Pero tienes que tomar una
excepción tonta en cuenta: debido a un accidente histórico, `typeof null`
también produce `"object"`.

{{index "Object.keys function"}}

La función `Object.keys` será útil para cuando necesites revisar las
propiedades de los objetos para compararlos.

{{if interactive

```{test: no}
// Tu código aquí.

let objeto = {aqui: {hay: "un"}, objeto: 2};
console.log(igualdadProfunda(objeto, objeto));
// → true
console.log(igualdadProfunda(objeto, {aqui: 1, object: 2}));
// → false
console.log(igualdadProfunda(objeto, {aqui: {hay: "un"}, objeto: 2}));
// → true
```

if}}

{{hint

{{index "deep comparison (exercise)", "typeof operator", object, "=== operator"}}

Tu prueba de si estás tratando con un objeto real se verá
algo así como `typeof x == "object" && x != null`. Ten cuidado de
comparar propiedades solo cuando _ambos_ argumentos sean objetos. En todo los
otros casos, puede retornar inmediatamente el resultado de aplicar `===`.

{{index "Object.keys function"}}

Usa `Object.keys` para revisar las propiedades. Necesitas probar si
ambos objetos tienen el mismo conjunto de nombres de propiedad y si esos
propiedades tienen valores idénticos. Una forma de hacerlo es garantizar que
ambos objetos tengan el mismo número de propiedades (las longitudes de
las listas de propiedades son las mismas). Y luego, al hacer un ciclo sobre
una de las propiedades del objeto para compararlos, siempre asegúrate primero
de que el otro realmente tenga una propiedad con ese mismo nombre.
Si tienen el mismo número de propiedades, y todas las propiedades en uno
también existen en el otro, tienen el mismo conjunto de nombres de propiedad.

{{index "return value"}}

Retornar el valor correcto de la función se realiza mejor al inmediatamente
retornar falso cuando se encuentre una discrepancia y retornar
verdadero al final de la función.

hint}}
