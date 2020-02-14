{{meta {load_files: ["code/chapter/06_object.js"], zip: "node/html"}}}

# La Vida Secreta de los Objetos

{{quote {author: "Barbara Liskov", title: "Programming with Abstract Data Types", chapter: true}

Un tipo de datos abstracto se realiza al escribir un tipo especial de programa
[...] que define el tipo en base a las operaciones que puedan ser
realizadas en él.

quote}}

{{index "Liskov, Barbara", "abstract data type"}}

{{figure {url: "img/chapter_picture_6.jpg", alt: "Picture of a rabbit with its proto-rabbit", chapter: framed}}}

El [Capítulo 4](datos) introdujo los ((objeto))s en JavaScript. En la cultura
de la programación, tenemos una cosa llamada _((programación orientada a objetos))_,
la cual es un conjunto de técnicas que usan objetos (y conceptos relacionados)
como el principio central de la organización del programa.

Aunque nadie realmente está de acuerdo con su definición exacta,
la programación orientada a objetos ha contribuido al diseño de muchos
lenguajes de programación, incluyendo JavaScript. Este capítulo describirá la
forma en la que estas ideas pueden ser aplicadas en JavaScript.

## Encapsulación

{{index encapsulation, isolation, modularity}}

La idea central en la programación orientada a objetos es dividir a los programas
en piezas más pequeñas y hacer que cada pieza sea responsable de gestionar su
propio estado.

De esta forma, los conocimientos acerca de como funciona una parte
del programa pueden mantenerse _locales_ a esa pieza. Alguien trabajando en otra
parte del programa no tiene que recordar o ni siquiera tener una idea
de ese conocimiento. Cada vez que los detalles locales cambien, solo
el código directamente a su alrededor debe ser actualizado.

{{id interface}}

Las diferentes piezas de un programa como tal, interactúan entre sí a través de
_((interfaces))_, las cuales son conjuntos limitados de funciones y
vinculaciones que proporcionan funcionalidades útiles en un nivel más
abstracto, ocultando asi su implementación interna.

{{index "public properties", "private properties", "access control"}}

Tales piezas del programa se modelan usando ((objeto))s. Sus interfaces
consisten en un conjunto específico de ((método))s y propiedades. Las propiedades
que son parte de la interfaz se llaman _publicas_. Las otras, las cuales no
deberian ser tocadas por el código externo , se les llama _privadas_.

{{index "underscore character"}}

Muchos lenguajes proporcionan una forma de distinguir entre propiedades publicas
y privadas, y ademas evitarán que el código externo pueda acceder a las privadas
por completo. JavaScript, una vez más tomando el enfoque minimalista,
no hace esto. Todavía no, al menos—hay trabajo en camino para agregar
esto al lenguaje.

Aunque el lenguaje no tenga esta distinción incorporada,
los programadores de JavaScript _estan_ usando esta idea con éxito .Típicamente,
la interfaz disponible se describe en la documentación o en los comentarios.
También es común poner un carácter de guión bajo (`_`) al comienzo de los
nombres de las propiedades para indicar que estas propiedades son privadas.

Separar la interfaz de la implementación es una gran idea. Esto
usualmente es llamado _((encapsulación))_.

{{id obj_methods}}

## Métodos

{{index "rabbit example", method, property}}

Los métodos no son más que propiedades que tienen valores de función.
Este es un método simple:

```
let conejo = {};
conejo.hablar = function(linea) {
  console.log(`El conejo dice '${linea}'`);
};

conejo.hablar("Estoy vivo.");
// → El conejo dice 'Estoy vivo.'
```

{{index this, "method call"}}

Por lo general, un método debe hacer algo en el objeto con que se llamó.
Cuando una función es llamada como un método—buscada como una propiedad y
llamada inmediatamente, como en `objeto.metodo()`—la vinculación llamada `this`
("este") en su cuerpo apunta automáticamente al objeto en la cual fue llamada.

```{includeCode: "top_lines:6", test: join}
function hablar(linea) {
  console.log(`El conejo ${this.tipo} dice '${linea}'`);
}
let conejoBlanco = {tipo: "blanco", hablar};
let conejoHambriento = {tipo: "hambriento", hablar};

conejoBlanco.hablar("Oh mis orejas y bigotes, " +
                  "que tarde se esta haciendo!");
// → El conejo blanco dice 'Oh mis orejas y bigotes, que
//   tarde se esta haciendo!'
conejoHambriento.hablar("Podria comerme una zanahoria ahora mismo.");
// → El conejo hambriento dice 'Podria comerme una zanahoria ahora mismo.'
```

{{id call_method}}

{{index "call method"}}

Puedes pensar en `this` como un ((parámetro)) extra que es pasado en
una manera diferente. Si quieres pasarlo explícitamente, puedes usar
el método `call` ("llamar") de una función, que toma el valor de `this`
como primer argumento y trata a los argumentos adicionales como parámetros
normales.

```
hablar.call(conejoHambriento, "Burp!");
// → El conejo hambriento dice 'Burp!'
```

Como cada función tiene su propia vinculación `this`, cuyo valor depende de
la forma en como esta se llama, no puedes hacer referencia al `this` del
alcance envolvente en una función regular definida con la palabra clave
`function`.

{{index this, "arrow function"}}

Las funciones de flecha son diferentes—no crean su propia vinculación `this`,
pero pueden ver la vinculación`this` del alcance a su alrededor. Por lo tanto,
puedes hacer algo como el siguiente código, que hace referencia a `this`
desde adentro de una función local:

```
function normalizar() {
  console.log(this.coordinadas.map(n => n / this.length));
}
normalizar.call({coordinadas: [0, 2, 3], length: 5});
// → [0, 0.4, 0.6]
```

{{index "map method"}}

Si hubieras escrito el argumento para `map` usando la palabra clave `function`,
el código no funcionaría.

{{id prototypes}}

## Prototipos

{{index "toString method"}}

Observa atentamente.

```
let vacio = {};
console.log(vacio.toString);
// → function toString(){…}
console.log(vacio.toString());
// → [object Object]
```

{{index magic}}

Saqué una propiedad de un objeto vacío. Magia!

{{index property, object}}

Bueno, en realidad no. Simplemente he estado ocultando información acerca de
como funcionan los objetos en JavaScript. En adición a su conjunto de propiedades,
la mayoría de los objetos también tienen un _((prototipo))_. Un prototipo es otro
objeto que se utiliza como una reserva de propiedades alternativa. Cuando un
objeto recibe una solicitud por una propiedad que este no tiene,
se buscará en su prototipo la propiedad, luego en el prototipo del prototipo y
asi sucesivamente.

{{index "Object prototype"}}

Asi que, quién es el ((prototipo)) de ese objeto vacío? Es el gran
prototipo ancestral, la entidad detrás de casi todos los objetos,
`Object.prototype` ("Objeto.prototipo").

```
console.log(Object.getPrototypeOf({}) ==
            Object.prototype);
// → true
console.log(Object.getPrototypeOf(Object.prototype));
// → null
```

{{index "getPrototypeOf function"}}

Como puedes adivinar, `Object.getPrototypeOf` ("Objeto.obtenerPrototipoDe")
retorna el prototipo de un objeto.

{{index "toString method"}}

Las relaciones prototipo de los objetos en JavaScript forman una estructura
en forma de ((árbol)), y en la raíz de esta estructura se encuentra
`Object.prototype`. Este proporciona algunos métodos que pueden ser accedidos
por todos los objetos, como `toString`, que convierte un objeto en una
representación de tipo string.

{{index inheritance, "Function prototype", "Array prototype", "Object prototype"}}

Muchos objetos no tienen `Object.prototype` directamente como su ((prototipo)),
pero en su lugar tienen otro objeto que proporciona un conjunto diferente de
propiedades predeterminadas. Las funciones derivan de `Function.prototype`, y
los arrays derivan de `Array.prototype`.

```
console.log(Object.getPrototypeOf(Math.max) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf([]) ==
            Array.prototype);
// → true
```

{{index "Object prototype"}}

Tal prototipo de objeto tendrá en si mismo un prototipo, a menudo `Object.prototype`,
por lo que aún proporciona indirectamente métodos como `toString`.

{{index "rabbit example", "Object.create function"}}

Puede usar `Object.create` para crear un objeto con un ((prototipo)) especifico.

```
let conejoPrototipo = {
  hablar(linea) {
    console.log(`El conejo ${this.tipo} dice '${linea}'`);
  }
};
let conejoAsesino = Object.create(conejoPrototipo);
conejoAsesino.tipo = "asesino";
conejoAsesino.hablar("SKREEEE!");
// → El conejo asesino dice 'SKREEEE!'
```

{{index "shared property"}}

Una propiedad como `hablar(linea)` en una expresión de objeto es un atajo
para definir un método. Esta crea una propiedad llamada `hablar` y le da
una función como su valor.

El conejo "prototipo" actúa como un contenedor para las propiedades que son
compartidas por todos los conejos. Un objeto de conejo individual, como el
conejo asesino, contiene propiedades que aplican solo a sí mismo—en este
caso su tipo—y deriva propiedades compartidas desde su prototipo.

{{id classes}}

## Clases

{{index "object-oriented programming"}}

El sistema de ((prototipos)) en JavaScript se puede interpretar como un
enfoque informal de un concepto orientado a objetos llamado _((clase))es_.
Una clase define la forma de un tipo de objeto—qué métodos y
propiedades tiene este. Tal objeto es llamado una _((instancia))_ de la
clase.

Los prototipos son útiles para definir propiedades en las cuales todas las
instancias de una clase compartan el mismo valor, como ((método))s.
Las propiedades que difieren por instancia, como la ((propiedad)) `tipo`
en nuestros conejos, necesitan almacenarse directamente en los objetos mismos.

{{id constructors}}

Entonces, para crear una instancia de una clase dada, debes crear
un objeto que derive del prototipo adecuado, pero _también_ debes
asegurarte de que, en sí mismo, este objeto tenga las propiedades que
las instancias de esta clase se supone que tengan.
Esto es lo que una función _((constructora))_ hace.

```
function crearConejo(tipo) {
  let conejo = Object.create(conejoPrototipo);
  conejo.tipo = tipo;
  return conejo;
}
```

{{index "new operator", this, "return keyword", [object, creation]}}

JavaScript proporciona una manera de hacer que la definición de este tipo de
funciones sea más fácil. Si colocas la palabra clave `new` ("new") delante de
una llamada de función, la función sera tratada como un constructor. Esto
significa que un objeto con el prototipo adecuado es creado automáticamente,
vinculado a `this` en la función, y retornado al final de la función.

{{index "prototype property"}}

El objeto prototipo utilizado al construir objetos se encuentra al tomar
la propiedad `prototype` de la función constructora.

{{index "rabbit example"}}

```
function Conejo(tipo) {
  this.tipo = tipo;
}
Conejo.prototype.hablar = function(linea) {
  console.log(`El conejo ${this.tipo} dice '${linea}'`);
};

let conejoRaro = new Conejo("raro");
```

{{index constructor}}

Los constructores (todas las funciones, de hecho) automáticamente obtienen
una propiedad llamada `prototype`, que por defecto contiene un objeto simple
y vacío, que deriva de `Object.prototype`. Puedes sobrescribirlo con un nuevo
objeto si asi quieres. O puedes agregar propiedades al objeto ya existente,
como lo hace el ejemplo.

{{index capitalization}}

Por convención, los nombres de los constructores tienen la primera letra en
mayúscula para que se puedan distinguir fácilmente de otras funciones.

{{index "prototype property", "getPrototypeOf function"}}

Es importante entender la distinción entre la forma en que un prototipo
está asociado con un constructor (a través de su propiedad `prototype`)
y la forma en que los objetos _tienen_ un prototipo (que se puede
encontrar con `Object.getPrototypeOf`). El prototipo real de un constructor
es `Function.prototype`, ya que los constructores son funciones. Su
_propiedad_ `prototype` contiene el prototipo utilizado para las
instancias creadas a traves de el.

```
console.log(Object.getPrototypeOf(Conejo) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf(conejoRaro) ==
            Conejo.prototype);
// → true
```

## Notación de clase

Entonces, las ((clase))es en JavaScript son funciones ((constructoras)) con una
propiedad ((prototipo)). Así es como funcionan, y hasta 2015, esa
era la manera en como tenías que escribirlas. Estos días, tenemos una
notación menos incómoda.

```{includeCode: true}
class Conejo {
  constructor(tipo) {
    this.tipo = tipo;
  }
  hablar(linea) {
    console.log(`El conejo ${this.tipo} dice '${linea}'`);
  }
}

let conejoAsesino = new Conejo("asesino");
let conejoNegro = new Conejo("negro");
```

{{index "rabbit example"}}

La palabra clave `class` ("clase") comienza una ((declaración de clase)),
que nos permite definir un constructor y un conjunto de métodos, todo en un
solo lugar. Cualquier número de métodos se pueden escribir dentro de las llaves
de la declaración. El metodo llamado `constructor` es tratado de una manera
especial. Este proporciona la función constructora real, que estará vinculada al
nombre `Conejo`. Los otros metodos estaran empacados en el prototipo de ese
constructor. Por lo tanto, la declaración de clase anterior es equivalente a la
definición de constructor en la sección anterior. Solo que se ve mejor.

{{index ["class declaration", properties]}}

Actualmente las declaraciones de clase solo permiten que los _metodos_—propiedades
que contengan funciones—puedan ser agregados al ((prototipo)). Esto puede ser
algo inconveniente para cuando quieras guardar un valor no-funcional allí.
La próxima versión del lenguaje probablemente mejore esto. Por ahora, tú
puedes crear tales propiedades al manipular directamente el
prototipo después de haber definido la clase.

Al igual que `function`, `class` se puede usar tanto en posiciones de
declaración como de expresión. Cuando se usa como una expresión, no define una
vinculación, pero solo produce el constructor como un valor. Tienes permitido
omitir el nombre de clase en una expresión de clase.

```
let objeto = new class { obtenerPalabra() { return "hola"; } };
console.log(objeto.obtenerPalabra());
// → hola
```

## Sobreescribiendo propiedades derivadas

{{index "shared property", overriding}}

Cuando le agregas una ((propiedad)) a un objeto, ya sea que esté presente en
el prototipo o no, la propiedad es agregada al objeto _en si mismo_.
Si ya había una propiedad con el mismo nombre en el prototipo, esta propiedad
ya no afectará al objeto, ya que ahora está oculta detrás de la propiedad del
propio objeto.

```
Rabbit.prototype.dientes = "pequeños";
console.log(conejoAsesino.dientes);
// → pequeños
conejoAsesino.dientes = "largos, filosos, y sangrientos";
console.log(conejoAsesino.dientes);
// → largos, filosos, y sangrientos
console.log(conejoNegro.dientes);
// → pequeños
console.log(Rabbit.prototype.dientes);
// → pequeños
```

{{index [prototype, diagram]}}

El siguiente diagrama esboza la situación después de que este código ha sido
ejecutado. Los ((prototipo))s de `Conejo` y `Object` se encuentran detrás de
`conejoAsesino` como una especie de telón de fondo, donde las propiedades
que no se encuentren en el objeto en sí mismo puedan ser buscadas.

{{figure {url: "img/rabbits.svg", alt: "Rabbit object prototype schema",width: "8cm"}}}

{{index "shared property"}}

Sobreescribir propiedades que existen en un prototipo puede ser algo útil
que hacer. Como muestra el ejemplo de los dientes de conejo, esto se
puede usar para expresar propiedades excepcionales en instancias de una clase
más genérica de objetos, dejando que los objetos no-excepcionales tomen un
valor estándar desde su prototipo.

{{index "toString method", "Array prototype", "Function prototype"}}

También puedes sobreescribir para darle a los prototipos estándar de función y
array un método diferente `toString` al del objeto prototipo básico.

```
console.log(Array.prototype.toString ==
            Object.prototype.toString);
// → false
console.log([1, 2].toString());
// → 1,2
```

{{index "toString method", "join method", "call method"}}

Llamar a `toString` en un array da un resultado similar al de una llamada
`.join(",")` en él—pone comas entre los valores del array.
Llamar directamente a `Object.prototype.toString` con un array produce un
string diferente. Esa función no sabe acerca de los arrays, por lo que
simplemente pone la palabra _object_ y el nombre del tipo entre corchetes.

```
console.log(Object.prototype.toString.call([1, 2]));
// → [object Array]
```

## Mapas

{{index "map method"}}

Vimos a la palabra _map_ usada en el [capítulo anterior](orden_superior#map)
para una operación que transforma una estructura de datos al aplicar una
función en sus elementos.

{{index "map (data structure)", "ages example", "data structure"}}

Un _mapa_ (sustantivo) es una estructura de datos que asocia valores (las llaves)
con otros valores. Por ejemplo, es posible que desees mapear nombres a edades.
Es posible usar objetos para esto.

```
let edades = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};

console.log(`Júlia tiene ${edades["Júlia"]}`);
// → Júlia tiene 62
console.log("Se conoce la edad de Jack?", "Jack" in edades);
// → Se conoce la edad de Jack? false
console.log("Se conoce la edad de toString?", "toString" in edades);
// → Se conoce la edad de toString? true
```

{{index "Object.prototype", "toString method"}}

Aquí, los nombres de las propiedades del objeto son los nombres de las
personas, y los valores de las propiedades sus edades. Pero ciertamente no
incluimos a nadie llamado toString en nuestro mapa. Sin embargo, debido a
que los  objetos simples se derivan de `Object.prototype`, parece que
la propiedad está ahí.

{{index "Object.create function", prototype}}

Como tal, usar objetos simples como mapas es peligroso. Hay varias
formas posibles de evitar este problema. Primero, es posible crear
objetos sin _ningun_ prototipo. Si pasas `null` a `Object.create`,
el objeto resultante no se derivará de `Object.prototype` y podra ser
usado de forma segura como un mapa.

```
console.log("toString" in Object.create(null));
// → false
```

Los nombres de las ((propiedades)) de los objetos deben ser strings.
Si necesitas un mapa cuyas claves no puedan ser convertidas fácilmente a
strings—como objetos—no puedes usar un objeto como tu mapa.

{{index "Map class"}}

Afortunadamente, JavaScript viene con una clase llamada `Map` que esta
escrita para este propósito exacto. Esta almacena un mapeo y permite cualquier
tipo de llaves.

```
let edades = new Map();
edades.set("Boris", 39);
edades.set("Liang", 22);
edades.set("Júlia", 62);

console.log(`Júlia tiene ${edades.get("Júlia")}`);
// → Júlia tiene 62
console.log("Se conoce la edad de Jack?", edades.has("Jack"));
// → Se conoce la edad de Jack? false
console.log(edades.has("toString"));
// → false
```

{{index interface, "set method", "get method", "has method", encapsulation}}

Los métodos `set` ("establecer"),` get` ("obtener"), y `has` ("tiene")
son parte de la interfaz del objeto `Map`.
Escribir una estructura de datos que pueda actualizarse rápidamente y
buscar en un gran conjunto de valores no es fácil, pero no tenemos que
preocuparnos acerca de eso. Alguien más lo hizo por nosotros, y podemos
utilizar esta simple interfaz para usar su trabajo.

{{index "hasOwnProperty method", "in operator"}}

Si tienes un objeto simple que necesitas tratar como un mapa por alguna
razón, es útil saber que `Object.keys` solo retorna las llaves propias del
objeto, no las que estan en el prototipo. Como alternativa al operador `in`,
puedes usar el método` hasOwnProperty` ("tienePropiaPropiedad"), el cual
ignora el prototipo del objeto.

```
console.log({x: 1}.hasOwnProperty("x"));
// → true
console.log({x: 1}.hasOwnProperty("toString"));
// → false
```

## Polimorfismo

{{index "toString method", "String function", polymorphism, overriding, "object-oriented programming"}}

Cuando llamas a la función `String` (que convierte un valor a un
string) en un objeto, llamará al método `toString` en ese
objeto para tratar de crear un string significativo a partir de el. Mencioné que
algunos de los prototipos estándar definen su propia versión de `toString`
para que puedan crear un string que contenga información más útil que
`"[object Object]"`. También puedes hacer eso tú mismo.

```{includeCode: "top_lines: 3"}
Conejo.prototype.toString = function() {
  return `un conejo ${this.tipo}`;
};

console.log(String(conejoNegro));
// → un conejo negro
```

{{index "object-oriented programming"}}

Esta es una instancia simple de una idea poderosa. Cuando un pedazo de código es
escrito para funcionar con objetos que tienen una cierta ((interfaz))—en este
caso, un método `toString`—cualquier tipo de objeto que soporte
esta interfaz se puede conectar al código, y simplemente funcionará.

Esta técnica se llama _polimorfismo_. El código polimórfico puede funcionar
con valores de diferentes formas, siempre y cuando soporten la interfaz
que este espera.

{{index "for/of loop", "iterator interface"}}

Mencioné en el [Capítulo 4](datos#for_of_loop) que un ciclo `for`/`of`
puede recorrer varios tipos de estructuras de datos. Este es otro caso
de polimorfismo—tales ciclos esperan que la estructura de datos exponga una
interfaz específica, lo que hacen los arrays y strings. Y también puedes agregar
esta interfaz a tus propios objetos! Pero antes de que podamos hacer eso,
necesitamos saber qué son los símbolos.

## Símbolos

Es posible que múltiples interfaces usen el mismo nombre de propiedad
para diferentes cosas. Por ejemplo, podría definir una interfaz en la que
se suponga que el método `toString` convierte el objeto a una pieza
de hilo. No sería posible para un objeto ajustarse a
esa interfaz y al uso estándar de `toString`.

Esa sería una mala idea, y este problema no es muy común. La mayoria de
los programadores de JavaScript simplemente no piensan en eso.
Pero los diseñadores del lenguaje, cuyo _trabajo_ es pensar acerca de estas
cosas, nos han proporcionado una solución de todos modos.

{{index "Symbol function", property}}

Cuando afirmé que los nombres de propiedad son strings, eso no fue del todo
preciso. Usualmente lo son, pero también pueden ser _((símbolo))s_.
Los símbolos son valores creados con la función `Symbol`. A diferencia de los
strings, los símbolos recién creados son únicos—no puedes crear el mismo símbolo
dos veces.

```
let simbolo = Symbol("nombre");
console.log(simbolo == Symbol("nombre"));
// → false
Conejo.prototype[simbolo] = 55;
console.log(conejoNegro[simbolo]);
// → 55
```

El string que pases a `Symbol` es incluido cuando lo conviertas a
string, y puede hacer que sea más fácil reconocer un símbolo cuando, por
ejemplo, lo muestres en la consola. Pero no tiene sentido más allá de
eso—múltiples símbolos pueden tener el mismo nombre.

Al ser únicos y utilizables como nombres de propiedad, los símbolos son adecuados
para definir interfaces que pueden vivir pacíficamente junto a otras
propiedades, sin importar cuáles sean sus nombres.

```{includeCode: "top_lines: 1"}
const simboloToString = Symbol("toString");
Array.prototype[simboloToString] = function() {
  return `${this.length} cm de hilo azul`;
};

console.log([1, 2].toString());
// → 1,2
console.log([1, 2][simboloToString]());
// → 2 cm de hilo azul
```

Es posible incluir propiedades de símbolos en expresiones de objetos y
clases usando ((corchete))s alrededor del nombre de la ((propiedad)).
Eso hace que se evalúe el nombre de la propiedad, al igual que la
notación de corchetes para acceder propiedades, lo cual
nos permite hacer referencia a una vinculación que contiene el símbolo.

```
let objetoString = {
  [simboloToString]() { return "una cuerda de cañamo"; }
};
console.log(objetoString[simboloToString]());
// → una cuerda de cañamo
```

## La interfaz de iterador

{{index "iterable interface", "Symbol.iterator symbol", "for/of loop"}}

Se espera que el objeto dado a un ciclo `for`/`of` sea _iterable_.
Esto significa que tenga un método llamado con el símbolo `Symbol.iterator`
(un valor de símbolo definido por el idioma, almacenado como una propiedad
de la función `Symbol`).

{{index "iterator interface", "next method"}}

Cuando sea llamado, ese método debe retornar un objeto que proporcione una
segunda interfaz, _iteradora_. Esta es la cosa real que realiza la iteración.
Tiene un método `next` ("siguiente") que retorna el siguiente resultado.
Ese resultado debería ser un objeto con una propiedad `value` ("valor"),
que proporciona el siguiente valor, si hay uno, y una propiedad `done` ("listo")
que debería ser cierta cuando no haya más resultados y falso de lo contrario.

Ten en cuenta que los nombres de las propiedades `next`, `value` y `done` son
simples strings, no símbolos. Solo `Symbol.iterator`, que probablemente sea
agregado a un _monton_ de objetos diferentes, es un símbolo real.

Podemos usar directamente esta interfaz nosotros mismos.

```
let iteradorOK = "OK"[Symbol.iterator]();
console.log(iteradorOK.next());
// → {value: "O", done: false}
console.log(iteradorOK.next());
// → {value: "K", done: false}
console.log(iteradorOK.next());
// → {value: undefined, done: true}
```

{{index "matrix example", "Matrix class", array}}

{{id matrix}}

Implementemos una estructura de datos iterable. Construiremos una clase
_matriz_, que actuara como un array bidimensional.

```{includeCode: true}
class Matriz {
  constructor(ancho, altura, elemento = (x, y) => undefined) {
    this.ancho = ancho;
    this.altura = altura;
    this.contenido = [];

    for (let y = 0; y < altura; y++) {
      for (let x = 0; x < ancho; x++) {
        this.contenido[y * ancho + x] = elemento(x, y);
      }
    }
  }

  obtener(x, y) {
    return this.contenido[y * this.ancho + x];
  }
  establecer(x, y, valor) {
    this.contenido[y * this.ancho + x] = valor;
  }
}
```

La clase almacena su contenido en un único array de elementos _altura_ × _ancho_.
Los elementos se almacenan fila por fila, por lo que, por ejemplo, el tercer
elemento en la quinta fila es (utilizando indexación basada en cero) almacenado
en la posición 4 × _ancho_ + 2.

La función constructora toma un ancho, una altura y una función opcional
de contenido que se usará para llenar los valores iniciales.
Hay métodos `obtener` y `establecer` para recuperar y actualizar elementos en
la matriz.

Al hacer un ciclo sobre una matriz, generalmente estás interesado en la posición
tanto de los elementos como de los elementos en sí mismos, así que haremos que
nuestro iterador produzca objetos con propiedades `x`, `y`, y `value` ("valor").

{{index "MatrixIterator class"}}

```{includeCode: true}
class IteradorMatriz {
  constructor(matriz) {
    this.x = 0;
    this.y = 0;
    this.matriz = matriz;
  }

  next() {
    if (this.y == this.matriz.altura) return {done: true};

    let value = {x: this.x,
                 y: this.y,
                 value: this.matriz.obtener(this.x, this.y)};
    this.x++;
    if (this.x == this.matriz.ancho) {
      this.x = 0;
      this.y++;
    }
    return {value, done: false};
  }
}
```

La clase hace un seguimiento del progreso de iterar sobre una matriz en sus
propiedades `x` y `y`. El método `next` ("siguiente") comienza comprobando si
la parte inferior de la matriz ha sido alcanzada. Si no es así, _primero_
crea el objeto que contiene el valor actual y _luego_ actualiza su
posición, moviéndose a la siguiente fila si es necesario.

Configuremos la clase `Matriz` para que sea iterable. A lo largo de este libro,
Ocasionalmente usaré la manipulación del prototipo después de los hechos para
agregar métodos a clases, para que las piezas individuales de código
permanezcan pequeñas y autónomas. En un programa regular, donde no hay necesidad
de dividir el código en pedazos pequeños, declararias estos métodos directamente
en la clase.

```{includeCode: true}
Matriz.prototype[Symbol.iterator] = function() {
  return new IteradorMatriz(this);
};
```

{{index "for/of loop"}}

Ahora podemos recorrer una matriz con `for`/`of`.

```
let matriz = new Matriz(2, 2, (x, y) => `valor ${x},${y}`);
for (let {x, y, value} of matriz) {
  console.log(x, y, value);
}
// → 0 0 valor 0,0
// → 1 0 valor 1,0
// → 0 1 valor 0,1
// → 1 1 valor 1,1
```

## Getters, setters y estáticos

{{index interface, property, "Map class"}}

A menudo, las interfaces consisten principalmente de métodos, pero también
está bien incluir propiedades que contengan valores que no sean de función.
Por ejemplo, los objetos `Map` tienen una propiedad `size` ("tamaño")
que te dice cuántas claves hay almacenanadas en ellos.

Ni siquiera es necesario que dicho objeto calcule y almacene tales
propiedades directamente en la instancia. Incluso las propiedades que
pueden ser accedidas directamente pueden ocultar una llamada a un método.
Tales métodos se llaman _((getter))s_, y se definen escribiendo `get` ("obtener")
delante del nombre del método en una expresión de objeto o declaración
de clase.

```{test: no}
let tamañoCambiante = {
  get tamaño() {
    return Math.floor(Math.random() * 100);
  }
};

console.log(tamañoCambiante.tamaño);
// → 73
console.log(tamañoCambiante.tamaño);
// → 49
```

{{index "temperature example"}}

Cuando alguien lee desde la propiedad `tamaño` de este objeto, el
método asociado es llamado. Puedes hacer algo similar cuando se escribe
en una propiedad, usando un _((setter))_.

```{test: no, startCode: true}
class Temperatura {
  constructor(celsius) {
    this.celsius = celsius;
  }
  get fahrenheit() {
    return this.celsius * 1.8 + 32;
  }
  set fahrenheit(valor) {
    this.celsius = (valor - 32) / 1.8;
  }

  static desdeFahrenheit(valor) {
    return new Temperatura((valor - 32) / 1.8);
  }
}

let temp = new Temperatura(22);
console.log(temp.fahrenheit);
// → 71.6
temp.fahrenheit = 86;
console.log(temp.celsius);
// → 30
```

La clase `Temperatura` te permite leer y escribir la temperatura ya sea
en grados ((Celsius)) o grados ((Fahrenheit)), pero
internamente solo almacena Celsius y convierte automáticamente a Celsius
en el getter y setter `fahrenheit`.

{{index "static method"}}

Algunas veces quieres adjuntar algunas propiedades directamente a tu
función constructora, en lugar de al prototipo. Tales métodos no
tienen acceso a una instancia de clase, pero pueden, por ejemplo, ser
utilizados para proporcionar formas adicionales de crear instancias.

Dentro de una declaración de clase, métodos que tienen `static` ("estatico")
escrito antes su nombre son almacenados en el constructor. Entonces, la clase
`Temperatura` te permite escribir `Temperature.desdeFahrenheit(100)` para
crear una temperatura usando grados Fahrenheit.

## Herencia

{{index inheritance, "matrix example", "object-oriented programming", "SymmetricMatrix class"}}

Algunas matrices son conocidas por ser _simétricas_. Si duplicas una matriz
simétrico alrededor de su diagonal de arriba-izquierda a derecha-abajo, esta
se mantiene igual. En otras palabras, el valor almacenado en _x_,_y_ es
siempre el mismo al de _y_,_x_.

Imagina que necesitamos una estructura de datos como `Matriz` pero que haga
cumplir el hecho de que la matriz es y siga siendo simétrica. Podríamos
escribirla desde cero, pero eso implicaría repetir algo de código muy similar
al que ya hemos escrito.

{{index overriding, prototype}}

El sistema de prototipos en JavaScript hace posible crear una _nueva_
clase, parecida a la clase anterior, pero con nuevas definiciones para
algunas de sus propiedades. El prototipo de la nueva clase deriva del antiguo
prototipo, pero agrega una nueva definición para, por ejemplo, el método `set`.

En términos de programación orientada a objetos, esto se llama
_((herencia))_. La nueva clase hereda propiedades y comportamientos de
la vieja clase.

```{includeCode: "top_lines: 17"}
class MatrizSimetrica extends Matriz {
  constructor(tamaño, elemento = (x, y) => undefined) {
    super(tamaño, tamaño, (x, y) => {
      if (x < y) return elemento(y, x);
      else return elemento(x, y);
    });
  }

  set(x, y, valor) {
    super.set(x, y, valor);
    if (x != y) {
      super.set(y, x, valor);
    }
  }
}

let matriz = new MatrizSimetrica(5, (x, y) => `${x},${y}`);
console.log(matriz.get(2, 3));
// → 3,2
```

El uso de la palabra `extends` indica que esta clase no debe estar
basada directamente en el prototipo de `Objeto` predeterminado, pero de
alguna otra clase. Esta se llama la _((superclase))_. La clase derivada es la
_((subclase))_.

Para inicializar una instancia de `MatrizSimetrica`, el constructor llama a su
constructor de superclase a través de la palabra clave `super`. Esto es necesario
porque si este nuevo objeto se comporta (más o menos) como una `Matriz`,
va a necesitar las propiedades de instancia que tienen las matrices. En orden
para asegurar que la matriz sea simétrica, el constructor ajusta el
método `contenido` para intercambiar las coordenadas de los valores por debajo del
diagonal.

El método `set` nuevamente usa `super`, pero esta vez no para llamar al
constructor, pero para llamar a un método específico del conjunto de metodos
de la superclase. Estamos redefiniendo `set` pero queremos usar el comportamiento
original. Ya que `this.set` se refiere al _nuevo_ método` set`, llamarlo
no funcionaria. Dentro de los métodos de clase, `super` proporciona una forma de
llamar a los métodos tal y como se definieron en la superclase.

La herencia nos permite construir tipos de datos ligeramente diferentes a partir
de tipos de datos existentes con relativamente poco trabajo. Es una
parte fundamental de la tradición orientada a objetos, junto con la encapsulación y
el polimorfismo. Pero mientras que los últimos dos son considerados como ideas
maravillosas en la actualidad, la herencia es más controversial.

{{index complexity, reuse, "class hierarchy"}}

Mientras que la ((encapsulación)) y el polimorfismo se pueden usar para
_separar_ piezas de código entre sí, reduciendo el enredo del programa
en general, la ((herencia)) fundamentalmente vincula las clases,
creando _mas_ enredo. Al heredar de una clase, generalmente tienes
que saber más sobre cómo funciona que cuando simplemente la usas. La herencia
puede ser una herramienta útil, y la uso de vez en cuando en mis
propios programas, pero no debería ser la primera herramienta que busques,
y probablemente no deberías estar buscando oportunidades para construir
jerarquías (árboles genealógicos de clases) de clases en una manera activa.

## El operador instanceof

{{index type, "instanceof operator", constructor, object}}

Ocasionalmente es útil saber si un objeto fue derivado de una
clase específica. Para esto, JavaScript proporciona un operador binario llamado
`instanceof` ("instancia de").

```
console.log(
  new MatrizSimetrica(2) instanceof MatrizSimetrica);
// → true
console.log(new MatrizSimetrica(2) instanceof Matriz);
// → true
console.log(new Matriz(2, 2) instanceof MatrizSimetrica);
// → false
console.log([1] instanceof Array);
// → true
```

{{index inheritance}}

El operador verá a través de los tipos heredados, por lo que una `MatrizSimetrica`
es una instancia de `Matriz`. El operador también se puede aplicar a
constructores estándar como `Array`. Casi todos los objetos son una instancia
de `Object`.

## Resumen

Entonces los objetos hacen más que solo tener sus propias propiedades. Ellos
tienen prototipos, que son otros objetos. Estos actuarán como si tuvieran
propiedades que no tienen mientras su prototipo tenga esa
propiedad. Los objetos simples tienen `Object.prototype` como su prototipo.

Los constructores, que son funciones cuyos nombres generalmente comienzan con
una mayúscula, se pueden usar con el operador `new` para crear nuevos
objetos. El prototipo del nuevo objeto será el objeto encontrado en la
propiedad `prototype` del constructor. Puedes hacer un buen uso de esto
al poner las propiedades que todos los valores de un tipo dado comparten en
su prototipo. Hay una notación de `class` que proporciona una manera clara
de definir un constructor y su prototipo.

Puedes definir getters y setters para secretamente llamar a métodos
cada vez que se acceda a la propiedad de un objeto. Los métodos estáticos
son métodos almacenados en el constructor de clase, en lugar de su prototipo.

El operador `instanceof` puede, dado un objeto y un constructor, decir
si ese objeto es una instancia de ese constructor.

Una cosa útil que hacer con los objetos es especificar una interfaz para
ellos y decirle a todos que se supone que deben hablar con ese objeto
solo a través de esa interfaz. El resto de los detalles que componen tu
objeto ahora estan _encapsulados_, escondidos detrás de la interfaz.

Más de un tipo puede implementar la misma interfaz. El código escrito para
utilizar una interfaz automáticamente sabe cómo trabajar con cualquier
cantidad de objetos diferentes que proporcionen la interfaz. Esto se llama
_polimorfismo_.

Al implementar múltiples clases que difieran solo en algunos detalles,
puede ser útil escribir las nuevas clases como _subclases_ de una
clase existente, _heredando_ parte de su comportamiento.

## Ejercicios

{{id exercise_vector}}

### Un tipo vector

{{index dimensions, "Vec class", coordinates, "vector (exercise)"}}

Escribe una ((clase)) `Vec` que represente un vector en un espacio
de dos dimensiones. Toma los parámetros (numericos) `x` y `y`, que debería
guardar como propiedades del mismo nombre.

{{index addition, subtraction}}

Dale al prototipo de `Vector` dos métodos, `mas` y `menos`, los cuales toman
otro vector como parámetro y retornan un nuevo vector que tiene la suma
o diferencia de los valores _x_ y _y_ de los dos vectores (`this` y el
parámetro).

Agrega una propiedad ((getter)) llamada `longitud` al prototipo que calcule la
longitud del vector—es decir, la distancia del punto (_x_, _y_) desde
el origen (0, 0).

{{if interactive

```{test: no}
// Your code here.

console.log(new Vector(1, 2).mas(new Vector(2, 3)));
// → Vector{x: 3, y: 5}
console.log(new Vector(1, 2).menos(new Vector(2, 3)));
// → Vector{x: -1, y: -1}
console.log(new Vector(3, 4).longitud);
// → 5
```
if}}

{{hint

{{index "vector (exercise)"}}

Mira de nuevo al ejemplo de la clase `Conejo` si no recuerdas muy bien
como se ven las declaraciones de clases.

{{index Pythagoras, "defineProperty function", "square root", "Math.sqrt function"}}

Agregar una propiedad getter al constructor se puede hacer al poner la
palabra `get` antes del nombre del método. Para calcular la distancia desde
(0, 0) a (x, y), puedes usar el teorema de Pitágoras, que dice que el
cuadrado de la distancia que estamos buscando es igual al cuadrado de
la coordenada x más el cuadrado de la coordenada y. Por lo tanto, [√(x^2^ +
y^2^)]{if html}[[$\sqrt{x^2 + y^2}$]{latex}]{if tex}
es el número que quieres, y `Math.sqrt` es la forma en que calculas una
raíz cuadrada en JavaScript.

hint}}

### Conjuntos

{{index "groups (exercise)", "Set class", "Group class", "set (data structure)"}}

{{id groups}}

El entorno de JavaScript estándar proporciona otra estructura de datos
llamada `Set` ("Conjunto"). Al igual que una instancia de `Map`,
un conjunto contiene una colección de valores. Pero a diferencia de `Map`,
este no asocia valores con otros—este solo rastrea qué valores son parte del
conjunto. Un valor solo puede ser parte de un conjunto una vez—agregarlo de
nuevo no tiene ningún efecto.

{{index "add method", "delete method", "has method"}}

Escribe una clase llamada `Conjunto`. Como `Set`, debe tener los métodos
`add` ("añadir"), `delete` ("eliminar"), y `has` ("tiene"). Su constructor
crea un conjunto vacío, `añadir` agrega un valor al conjunto (pero solo si no
es ya un miembro), `eliminar` elimina su argumento del
conjunto (si era un miembro) y `tiene` retorna un valor booleano
que indica si su argumento es un miembro del conjunto.

{{index "=== operator", "indexOf method"}}

Usa el operador `===`, o algo equivalente como `indexOf`, para
determinar si dos valores son iguales.

{{index "static method"}}

Proporcionale a la clase un método estático `desde` que tome un objeto iterable
como argumento y cree un grupo que contenga todos los valores producidos
al iterar sobre el.

{{if interactive

```{test: no}
class Conjunto {
  // Tu código aquí.
}

let conjunto = Conjunto.desde([10, 20]);
console.log(conjunto.tiene(10));
// → true
console.log(conjunto.tiene(30));
// → false
conjunto.añadir(10);
conjunto.eliminar(10);
console.log(conjunto.tiene(10));
// → false
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "indexOf method", "includes method"}}

La forma más fácil de hacer esto es almacenar un ((array)) con los miembros del
conjunto en una propiedad de instancia. Los métodos `includes` o `indexOf`
pueden ser usados para verificar si un valor dado está en el array.

{{index "push method"}}

El ((constructor)) de clase puede establecer la colección de miembros como
un array vacio. Cuando se llama a `añadir`, debes verificar si el valor dado
esta en el conjunto y agregarlo, por ejemplo con `push`, de lo contrario.

{{index "filter method"}}

Eliminar un elemento de un array, en `eliminar`, es menos
sencillo, pero puedes usar `filter` para crear un nuevo array
sin el valor. No te olvides de sobrescribir la propiedad que
sostiene los miembros del conjunto con la versión recién filtrada del array.

{{index "for/of loop", "iterable interface"}}

El método `desde` puede usar un bucle `for`/`of` para obtener los valores de
el objeto iterable y llamar a `añadir` para ponerlos en un conjunto recien
creado.

hint}}

### Conjuntos Iterables

{{index "groups (exercise)", interface, "iterator interface", "Group class"}}

{{id group_iterator}}

Haz iterable la clase `Conjunto` del ejercicio anterior. Puedes remitirte
a la sección acerca de la interfaz del iterador anteriormente en el capítulo si
ya no recuerdas muy bien la forma exacta de la interfaz.

Si usaste un array para representar a los miembros del conjunto, no solo
retornes el iterador creado llamando al método `Symbol.iterator` en
el array. Eso funcionaría, pero frustra el propósito de este ejercicio.

Está bien si tu iterador se comporta de manera extraña cuando el conjunto es
modificado durante la iteración.

{{if interactive

```{test: no}
// Tu código aquí (y el codigo del ejercicio anterior)

for (let valor of Conjunto.desde(["a", "b", "c"])) {
  console.log(valor);
}
// → a
// → b
// → c
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "next method"}}

Probablemente valga la pena definir una nueva clase `IteradorConjunto`.
Las instancias de Iterador deberian tener una propiedad que rastree la
posición actual en el conjunto. Cada vez que se invoque a `next`, este
comprueba si está hecho, y si no, se mueve más allá del valor actual y
lo retorna.

La clase `Conjunto` recibe un método llamado por `Symbol.iterator`
que, cuando se llama, retorna una nueva instancia de la clase de iterador para
ese grupo.

hint}}

### Tomando un método prestado

Anteriormente en el capítulo mencioné que el metodo `hasOwnProperty` de un
objeto puede usarse como una alternativa más robusta al operador `in` cuando
quieras ignorar las propiedades del prototipo. Pero, ¿y si tu mapa necesita
incluir la palabra `"hasOwnProperty"`? Ya no podrás llamar a ese
método ya que la propiedad del objeto oculta el valor del método.

¿Puedes pensar en una forma de llamar `hasOwnProperty` en un objeto que tiene
una propia propiedad con ese nombre?

{{if interactive

```{test: no}
let mapa = {uno: true, dos: true, hasOwnProperty: true};

// Arregla esta llamada
console.log(mapa.hasOwnProperty("uno"));
// → true
```

if}}

{{hint

Recuerda que los métodos que existen en objetos simples provienen de
`Object.prototype`.

Y que puedes llamar a una función con una vinculación `this` específica al
usar su método `call`.

hint}}
