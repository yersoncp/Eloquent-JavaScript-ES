{{meta {load_files: ["code/chapter/08_error.js"]}}}

# Bugs y Errores

{{quote {author: "Brian Kernighan and P.J. Plauger", title: "The Elements of Programming Style", chapter: true}

Arreglar errores es dos veces mas difícil que escribir el código en primer lugar.
Por lo tanto, si escribes código de la manera más inteligente posible, eres,
por definición, no lo suficientemente inteligente como para depurarlo.

quote}}

{{figure {url: "img/chapter_picture_8.jpg", alt: "Picture of a collection of bugs", chapter: framed}}}

{{index "Kernighan, Brian", "Plauger, P.J.", debugging, "error handling"}}

Los defectos en los programas de computadora usualmente se llaman _((bug))s_
(o "insectos"). Este nombre hace que los programadores se sientan bien al
imaginarlos como pequeñas cosas que solo sucede se arrastran hacia nuestro trabajo.
En la realidad, por supuesto, nosotros mismos los ponemos allí.

Si un programa es un pensamiento cristalizado, puedes categorizar en grandes
rasgos a los bugs en aquellos causados ​​al confundir los pensamientos, y
los causados ​​por cometer errores al convertir un pensamiento en código.
El primer tipo es generalmente más difícil de diagnosticar y corregir que
el último.

## Lenguaje

{{index parsing, analysis}}

Muchos errores podrían ser señalados automáticamente por la
computadora, si esta supiera lo suficiente sobre lo que estamos tratando de hacer.
Pero aquí la soltura de JavaScript es un obstáculo. Su concepto de vinculaciones y
propiedades es lo suficientemente vago que rara vez atrapará errores
ortograficos antes de ejecutar el programa. E incluso entonces, te permite
hacer algunas cosas claramente sin sentido, como calcular
`true * "mono"`.

{{index syntax}}

Hay algunas cosas de las que JavaScript se queja. Escribir un
programa que no siga la ((gramática)) del lenguaje
inmediatamente hara que la computadora se queje. Otras cosas, como llamar a
algo que no sea una función o buscar una ((propiedad)) en un
valor ((indefinido)), causará un error que sera reportado cuando el
programa intente realizar la acción.

{{index NaN, error}}

Pero a menudo, tu cálculo sin sentido simplemente producirá `NaN` (no es un
número) o un valor indefinido. Y el programa continuara felizmente,
convencido de que está haciendo algo significativo. El error solo se
manifestara más tarde, después de que el valor falso haya viajado a traves de
varias funciones. Puede no desencadenar un error en absoluto, pero en silencio
causara que la salida del programa sea incorrecta. Encontrar la fuente de tales
problemas puede ser algo difícil.

El proceso de encontrar errores—bugs—en los programas se llama
_((depuración))_.

## Modo estricto

{{index "strict mode", syntax, function}}

{{indexsee "use strict", "strict mode"}}

JavaScript se puede hacer un _poco_ más estricto al habilitar el _modo estricto_.
Esto se hace al poner el string `"use strict"` ("usar estricto")
en la parte superior de un archivo o cuerpo de función. Aquí hay un ejemplo:

```{test: "error \"ReferenceError: contador is not defined\""}
function puedesDetectarElProblema() {
  "use strict";
  for (contador = 0; contador < 10; contador++) {
    console.log("Feliz feliz");
  }
}

puedesDetectarElProblema();
// → ReferenceError: contador is not defined
```

{{index "let keyword", [binding, global]}}

Normalmente, cuando te olvidas de poner `let` delante de tu vinculación, como
con `contador` en el ejemplo, JavaScript silenciosamente crea una
vinculación global y utiliza eso. En el modo estricto, se reportara un ((error))
en su lugar. Esto es muy útil. Sin embargo, debe tenerse en cuenta que esto
no funciona cuando la vinculación en cuestión ya existe como una
vinculación global. En ese caso, el ciclo aún sobrescribirá silenciosamente
el valor de la vinculación.

{{index this, "global object", undefined, "strict mode"}}

Otro cambio en el modo estricto es que la vinculación `this` contiene el
valor `undefined` en funciones que no se llamen como ((método))s.
Cuando se hace una llamada fuera del modo estricto, `this` se refiere al
objeto del alcance global, que es un objeto cuyas propiedades son
vinculaciones globales. Entonces, si llamas accidentalmente a un método o
constructor incorrectamente en el modo estricto, JavaScript producirá un error
tan pronto trate de leer algo de `this`, en lugar de escribirlo felizmente
al alcance global.

Por ejemplo, considera el siguiente código, que llama una función
((constructora)) sin la palabra clave `new` de modo que su `this`
_no_ hara referencia a un objeto recién construido:

```
function Persona(nombre) { this.nombre = nombre; }
let ferdinand = Persona("Ferdinand"); // oops
console.log(nombre);
// → Ferdinand
```

{{index error}}

Así que la llamada fraudulenta a `Persona` tuvo éxito pero retorno un
valor indefinido y creó la vinculación `nombre` global. En el modo estricto,
el resultado es diferente.

```{test: "error \"TypeError: Cannot set property 'nombre' of undefined\""}
"use strict";
function Persona(nombre) { this.nombre = nombre; }
let ferdinand = Persona("Ferdinand"); // olvide new
// → TypeError: Cannot set property 'nombre' of undefined
```

Se nos dice inmediatamente que algo está mal. Esto es útil.

Afortunadamente, los constructores creados con la notación `class`
siempre se quejan si se llaman sin `new`, lo que hace que esto sea menos
un problema incluso en el modo no-estricto.

{{index parameter, [binding, naming], "with statement"}}

El modo estricto hace algunas cosas más. No permite darle a una función
múltiples parámetros con el mismo nombre y elimina ciertas características
problemáticas del lenguaje por completo (como la declaración `with` ("con"),
la cual esta tan mal, que no se discute en este libro).

{{index debugging}}

En resumen, poner `"use strict"` en la parte superior de tu programa rara vez
duele y puede ayudarte a detectar un problema.

## Tipos

Algunos lenguajes quieren saber los tipos de todas tus vinculaciones y
expresiones incluso antes de ejecutar un programa. Estos te dirán de una vez
cuando uses un tipo de una manera inconsistente. JavaScript solo considera a los
tipos cuando ejecuta el programa, e incluso a menudo intentara convertir
implícitamente los valores al tipo que espera, por lo que no es de
mucha ayuda

Aún así, los tipos proporcionan un marco útil para hablar acerca de los
programas. Muchos errores provienen de estar confundido acerca del tipo de
valor que entra o sale de una función. Si tienes esa información
anotada, es menos probable que te confundas.

Podrías agregar un comentario como arriba de la función `robotOrientadoAMetas`
del último capítulo, para describir su tipo.

```
// (EstadoMundo, Array) → {direccion: string, memoria: Array}
function robotOrientadoAMetas(estado, memoria) {
  // ...
}
```

Hay varias convenciones diferentes para anotar programas de JavaScript con tipos.

Una cosa acerca de los tipos es que necesitan introducir su propia
complejidad para poder describir suficiente código como para poder ser útil.
Cual crees que sería el tipo de la función `eleccionAleatoria` que retorna
un elemento aleatorio de un array? Deberías introducir un _((tipo
variable))_, _T_, que puede representar cualquier tipo, para que puedas
darle a `eleccionAleatoria` un tipo como `([T]) → T` (función de un array de
*T*s a a *T*).

{{index "type checking", TypeScript}}

{{id typing}}

Cuando se conocen los tipos de un programa, es posible que la computadora
haga un _chequeo_ por ti, señalando los errores antes de que el programa sea
ejecutado. Hay varios dialectos de JavaScript que agregan tipos al
lenguaje y y los verifica. El más popular se llama
[TypeScript](https://www.typescriptlang.org/). Si estás interesado
en agregarle más rigor a tus programas, te recomiendo que lo pruebes.

En este libro, continuaremos usando código en JavaScript crudo, peligroso y
sin tipos.

## Probando

{{index "test suite", "run-time error", automation, testing}}

Si el lenguaje no va a hacer mucho para ayudarnos a encontrar errores,
tendremos que encontrarlos de la manera difícil: ejecutando el programa y
viendo si hace lo correcto.

Hacer esto a mano, una y otra vez, es una muy mala idea. No solo es
es molesto, también tiende a ser ineficaz, ya que lleva demasiado
tiempo probar exhaustivamente todo cada vez que haces un cambio en tu programa.

Las computadoras son buenas para las tareas repetitivas, y las pruebas son las
tareas repetitivas ideales. Las pruebas automatizadas es el proceso de escribir un
programa que prueba otro programa. Escribir pruebas consiste en algo más de
trabajo que probar manualmente, pero una vez que lo haz hecho, ganas un tipo de
superpoder: solo te tomara unos segundos verificar que tu programa todavía
se comporta correctamente en todas las situaciones para las que
escribiste tu prueba. Cuando rompas algo, lo notarás inmediatamente, en
lugar aleatoriomente encontrarte con el problema en algún momento posterior.

{{index "toUpperCase method"}}

Las pruebas usualmente toman la forma de pequeños programas etiquetados que
verifican algún aspecto de tu código. Por ejemplo, un conjunto de pruebas
para el método (estándar, probablemente ya probado por otra persona)
`toUpperCase` podría verse así:

```
function probar(etiqueta, cuerpo) {
  if (!cuerpo()) console.log(`Fallo: ${etiqueta}`);
}

probar("convertir texto Latino a mayúscula", () => {
  return "hola".toUpperCase() == "HOLA";
});
probar("convertir texto Griego a mayúsculas", () => {
  return "Χαίρετε".toUpperCase() == "ΧΑΊΡΕΤΕ";
});
probar("no convierte caracteres sin mayúsculas", () => {
  return "مرحبا".toUpperCase() == "مرحبا";
});
```

{{index "domain-specific language"}}

Escribir pruebas de esta manera tiende a producir código bastante repetitivo e
incómodo. Afortunadamente, existen piezas de software que te ayudan a construir
y ejecutar colecciones de pruebas (_((suites de prueba))_) al proporcionar un
lenguaje (en forma de funciones y métodos) adecuado para expresar
pruebas y obtener información informativa cuando una prueba falla.
Estos generalmente se llaman _((corredores de pruebas))_.

{{index "persistent data structure"}}

Algunos programas son más fáciles de probar que otros programas. Por lo general,
con cuantos más objetos externos interactúe  el código, más difícil es establecer
el contexto en el cual probarlo. El estilo de programación mostrado en
el [capítulo anterior](robot), que usa valores persistentes auto-contenidos
en lugar de cambiar objetos, tiende a ser fácil de probar.

## Depuración

{{index debugging}}

Una vez que notes que hay algo mal con tu programa porque se comporta mal o
produce errores, el siguiente paso es descubir _cual_ es el problema.

A veces es obvio. El mensaje de ((error)) apuntará a una
línea específica de tu programa, y ​​si miras la descripción del error
y esa línea de código, a menudo puedes ver el problema.

{{index "run-time error"}}

Pero no siempre. A veces, la línea que provocó el problema es simplemente
el primer lugar en donde un valor extraño producido en otro lugar es
usado de una manera inválida. Si has estado resolviendo los ((ejercicios)) en
capítulos anteriores, probablemente ya habrás experimentado tales
situaciones.

{{index "decimal number", "binary number"}}

El siguiente programa de ejemplo intenta convertir un número entero a un
string en una base dada (decimal, binario, etc.) al repetidamente
seleccionar el último ((dígito)) y luego dividiendo el número para deshacerse
de este dígito. Pero la extraña salida que produce sugiere que tiene un ((error)).

```
function numeroAString(n, base = 10) {
  let resultado = "", signo = "";
  if (n < 0) {
    signo = "-";
    n = -n;
  }
  do {
    resultado = String(n % base) + resultado;
    n /= base;
  } while (n > 0);
  return signo + resultado;
}
console.log(numeroAString(13, 10));
// → 1.5e-3231.3e-3221.3e-3211.3e-3201.3e-3191.3e-3181.3…
```

{{index analysis}}

Incluso si ya ves el problema, finge por un momento que no lo has hecho.
Sabemos que nuestro programa no funciona bien, y queremos encontrar
por qué.

{{index "trial and error"}}

Aquí es donde debes resistir el impulso de comenzar a hacer cambios aleatorios
en el código para ver si eso lo mejora. En cambio, _piensa_. Analiza
lo que está sucediendo y piensa en una ((teoría)) de por qué podría ser
sucediendo. Luego, haz observaciones adicionales para probar esta teoría—o
si aún no tienes una teoría, haz observaciones adicionales para ayudarte
a que se te ocurra una.

{{index "console.log", output, debugging, logging}}

Poner algunas llamadas estratégicas a `console.log` en el programa es una buena
forma de obtener información adicional sobre lo que está haciendo el programa.
En en este caso, queremos que `n` tome los valores `13`, `1` y luego `0`.
Vamos a escribir su valor al comienzo del ciclo.

```{lang: null}
13
1.3
0.13
0.013
…
1.5e-323
```

{{index rounding}}

_Exacto_. Dividir 13 entre 10 no produce un número entero. En lugar de
`n /= base`, lo que realmente queremos es `n = Math.floor(n / base)` para
que el número sea correctamente "desplazado" hacia la derecha.

{{index "JavaScript console", "debugger statement"}}

Una alternativa al uso de `console.log` para echarle un vistazo al comportamiento
del programa es usar las capacidades del _depurador_ de tu navegador.
Los navegadores vienen con la capacidad de establecer un _((punto de interrupción))_
en una línea específico de tu código. Cuando la ejecución del programa alcanza
una línea con un punto de interrupción, este entra en pausa, y puedes
inspeccionar los valores de las vinculaciones en ese punto.
No entraré en detalles, ya que los depuradores difieren
de navegador en navegador, pero mira las ((herramientas de
desarrollador)) en tu navegador o busca en la Web para obtener más información.

Otra forma de establecer un punto de interrupción es incluir una declaración
`debugger` (que consiste simplemente de esa palabra clave) en tu programa. Si las
((herramientas de desarrollador)) en tu navegador están activas, el programa pausará
cada vez que llegue a tal declaración.

## Propagación de errores

{{index input, output, "run-time error", error, validation}}

Desafortunadamente, no todos los problemas pueden ser prevenidos por el
programador. Si tu programa se comunica con el mundo exterior de alguna manera,
es posible obtener una entrada malformada, sobrecargarse con el trabajo, o
la red falle en la ejecución.

{{index "error recovery"}}

Si solo estás programando para ti mismo, puedes permitirte ignorar tales
problemas hasta que estos ocurran. Pero si construyes algo que
va a ser utilizado por cualquier otra persona, generalmente quieres que
el programa haga algo mejor que solo estrellarse. A veces lo correcto es tomar la
mala entrada en zancada y continuar corriendo. En otros casos, es mejor
informar al usuario lo que salió mal y luego darse por vencido. Pero en
cualquier situación, el programa tiene que hacer algo activamente en
respuesta al problema.

{{index "promptInteger function", validation}}

Supongamos que tienes una función `pedirEntero` que le pide al usuario un
número entero y lo retorna. Qué deberías retornar si la entrada por
parte del usuario es "naranja"?

{{index null, undefined, "return value", "special return value"}}

Una opción es hacer que retorne un valor especial. Opciones comunes para
tales valores son `null`, `undefined`, o -1.

```{test: no}
function pedirEntero(pregunta) {
  let resultado = Number(prompt(pregunta));
  if (Number.isNaN(resultado)) return null;
  else return resultado;
}

console.log(pedirEntero("Cuantos arboles ves?"));
```

Ahora cualquier código que llame a `pedirEntero` debe verificar si un número real
fue leído y, si eso falla, de alguna manera debe recuperarse—tal vez
preguntando nuevamente o usando un valor predeterminado. O podría de nuevo
retornar un valor especial a _su_ llamada para indicar que no pudo
hacer lo que se pidió.

{{index "error handling"}}

En muchas situaciones, principalmente cuando los ((error))es son comunes
y la persona que llama debe tenerlos explícitamente en cuenta, retornar un
valor especial es una buena forma de indicar un error. Sin embargo, esto tiene
sus desventajas. Primero, qué pasa si la función puede retornar cada
tipo de valor posible? En tal función, tendrás que hacer
algo como envolver el resultado en un objeto para poder distinguir el
éxito del fracaso.

```
function ultimoElemento(array) {
  if (array.length == 0) {
    return {fallo: true};
  } else {
    return {elemento: array[array.length - 1]};
  }
}
```

{{index "special return value", readability}}

El segundo problema con retornar valores especiales es que puede conducir a
código muy incómodo. Si un fragmento de código llama a `pedirEntero` 10 veces,
tiene que comprobar 10 veces si `null` fue retornado. Y si su
respuesta a encontrar `null` es simplemente retornar `null` en sí mismo,
los llamadores de esa función a su vez tendrán que verificarlo, y
así sucesivamente.

## Excepciones

{{index "error handling"}}

Cuando una función no puede continuar normalmente, lo que nos _gustaría_ hacer es
simplemente detener lo que estamos haciendo e inmediatamente saltar a un
lugar que sepa cómo manejar el problema. Esto es lo que el
_((manejo de excepciones))_ hace.

{{index "control flow", "raising (exception)", "throw keyword", "call stack"}}

Las excepciones son un mecanismo que hace posible que el código que se encuentre
con un problema _produzca_ (o _lance_) una excepción. Una excepción puede
ser cualquier valor. Producir una se asemeja a un retorno súper-cargado
de una función: salta no solo de la función actual sino
también fuera de sus llamadores, todo el camino hasta la primera llamada que
comenzó la ejecución actual. Esto se llama _((desenrollando la
pila))_. Puede que recuerdes que la pila de llamadas de función fue
mencionada en el [Capítulo 3](funciones#pila). Una excepción se aleja de
esta pila, descartando todos los contextos de llamadas que encuentra.

{{index "error handling", syntax, "catch keyword"}}

Si las excepciones siempre se acercaran al final de la pila, estas
no serían de mucha utilidad. Simplemente proporcionarían una nueva forma de
explotar tu programa. Su poder reside en el hecho de que puedes establecer
"obstáculos" a lo largo de la pila para _capturar_ la excepción, cuando esta  
esta se dirige hacia abajo. Una vez que hayas capturado una excepción,
puedes hacer algo con ella para abordar el problema y
luego continuar ejecutando el programa.

Aquí hay un ejemplo:

{{id look}}
```
function pedirDireccion(pregunta) {
  let resultado = prompt(pregunta);
  if (resultado.toLowerCase() == "izquierda") return "I";
  if (resultado.toLowerCase() == "derecha") return "D";
  throw new Error("Dirección invalida: " + resultado);
}

function mirar() {
  if (pedirDireccion("Hacia que dirección quieres ir?") == "I") {
    return "una casa";
  } else {
    return "dos osos furiosos";
  }
}

try {
  console.log("Tu ves", mirar());
} catch (error) {
  console.log("Algo incorrecto sucedio: " + error);
}
```

{{index "exception handling", block, "throw keyword", "try keyword", "catch keyword"}}

La palabra clave `throw` ("producir") se usa para generar una excepción.
La captura de una se hace al envolver un fragmento de código en un bloque
`try` ("intentar"), seguido de la palabra clave `catch` ("atrapar").
Cuando el código en el bloque `try` cause una excepción
para ser producida, se evalúa el bloque `catch`, con el nombre en
paréntesis vinculado al valor de la excepción. Después de que el bloque `catch`
finaliza, o si el bloque `try` finaliza sin problemas, el programa
procede debajo de toda la declaración `try/catch`.

{{index debugging, "call stack", "Error type"}}

En este caso, usamos el ((constructor)) `Error` para crear nuestro
valor de excepción. Este es un constructor (estándar) de JavaScript que
crea un objeto con una propiedad `message` ("mensaje"). En la mayoría de los
entornos de JavaScript, las instancias de este constructor también recopilan información
sobre la pila de llamadas que existía cuando se creó la excepción, algo
llamado _((seguimiento de la pila))_. Esta información se almacena en la
propiedad `stack` ("pila") y puede ser útil al intentar depurar un problema:
esta nos dice la función donde ocurrió el problema y qué funciones realizaron
la llamada fallida.

{{index "exception handling"}}

Ten en cuenta que la función `mirar` ignora por completo la posibilidad de que
`pedirDireccion` podría salir mal. Esta es la gran ventaja de las
excepciones: el código de manejo de errores es necesario solamente
en el punto donde el error ocurre y en el punto donde se maneja. Las funciones
en el medio puede olvidarse de todo.

Bueno, casi...

## Limpiando después de excepciones

{{index "exception handling", "cleaning up"}}

El efecto de una excepción es otro tipo de ((flujo de control)). Cada
acción que podría causar una excepción, que es prácticamente cualquier
llamada de función y acceso a propiedades, puede causar al control
dejar tu codigo repentinamente.

Eso significa que cuando el código tiene varios efectos secundarios,
incluso si parece que el flujo de control "regular" siempre sucederá, una
excepción puede evitar que algunos de ellos sucedan.

{{index "banking example"}}

Aquí hay un código bancario realmente malo.

```{includeCode: true}
const cuentas = {
  a: 100,
  b: 0,
  c: 20
};

function obtenerCuenta() {
  let nombreCuenta = prompt("Ingrese el nombre de la cuenta");
  if (!cuentas.hasOwnProperty(nombreCuenta)) {
    throw new Error(`La cuenta "${nombreCuenta}" no existe`);
  }
  return nombreCuenta;
}

function transferir(desde, cantidad) {
  if (cuentas[desde] < cantidad) return;
  cuentas[desde] -= cantidad;
  cuentas[obtenerCuenta()] += cantidad;
}
```

La función `transferir` transfiere una suma de dinero desde una determinada
cuenta a otra, pidiendo el nombre de la otra cuenta en el proceso.
Si se le da un nombre de cuenta no válido, `obtenerCuenta` arroja una excepción.

Pero `transferir` _primero_ remueve el dinero de la cuenta, y _luego_
llama a `obtenerCuenta` antes de añadirlo a la otra cuenta. Si esto es
interrumpido por una excepción en ese momento, solo hará que el dinero
desaparezca.

Ese código podría haber sido escrito de una manera un poco más inteligente, por
ejemplo al llamar `obtenerCuenta` antes de que se comience a mover el dinero.
Pero a menudo problemas como este ocurren de maneras más sutiles. Incluso
funciones que no parece que lanzarán una excepción podría hacerlo en
circunstancias excepcionales o cuando contienen un error de programador.

Una forma de abordar esto es usar menos efectos secundarios. De nuevo, un
estilo de programación que calcula nuevos valores en lugar de cambiar
los datos existentes ayuda. Si un fragmento de código deja de ejecutarse en
el medio de crear un nuevo valor, nadie ve el valor a medio terminar, y
no hay ningún problema.

{{index block, "try keyword", "finally keyword"}}

Pero eso no siempre es práctico. Entonces, hay otra característica que las
declaraciones `try` tienen. Estas pueden ser seguidas por un bloque `finally` ("finalmente")
en lugar de o además de un bloque `catch`. Un bloque `finally`
dice "no importa lo que pase, ejecuta este código después de intentar
ejecutar el código en el bloque `try`."

```{includeCode: true}
function transferir(desde, cantidad) {
  if (cuentas[desde] < cantidad) return;
  let progreso = 0;
  try {
    cuentas[desde] -= cantidad;
    progreso = 1;
    cuentas[obtenerCuenta()] += cantidad;
    progreso = 2;
  } finally {
    if (progreso == 1) {
      cuentas[desde] += cantidad;
    }
  }
}
```

Esta versión de la función rastrea su progreso, y si, cuando este
terminando, se da cuenta de que fue abortada en un punto donde habia
creado un estado de programa inconsistente, repara el daño que hizo.

Ten en cuenta que, aunque el código `finally` se ejecuta cuando una excepción
deja el bloque `try`, no interfiere con la excepción.
Después de que se ejecuta el bloque `finally`, la pila continúa desenrollandose.

{{index "exception safety"}}

Escribir programas que funcionan de manera confiable incluso cuando aparecen
excepciones en lugares inesperados es muy difícil. Muchas personas simplemente
no se molestan, y porque las excepciones suelen reservarse para circunstancias
excepcionales, el problema puede ocurrir tan raramente que nunca siquiera es
notado. Si eso es algo bueno o algo realmente malo depende de
cuánto daño hará el software cuando falle.

## Captura selectiva

{{index "uncaught exception", "exception handling", "JavaScript console", "developer tools", "call stack", error}}

Cuando una excepción llega hasta el final de la pila
sin ser capturada, esta es manejada por el entorno. Lo que esto
significa difiere entre los entornos. En los navegadores, una descripción del
error generalmente sera escrita en la consola de JavaScript (accesible
a través de las herramientas de desarrollador del navegador). Node.js, el
entorno de JavaScript sin navegador que discutiremos en el [Capítulo 20](node),
es más cuidadoso con la corrupción de datos. Aborta todo el
proceso cuando ocurre una excepción no manejada.

{{index crash, "error handling"}}

Para los errores de programador, solo dejar pasar al error es a menudo
lo mejor que puedes hacer. Una excepción no manejada es una forma razonable de
señalizar un programa roto, y la consola de JavaScript, en los
navegadores moderno, te proporcionan cierta información acerca de qué
llamdas de función estaban en la pila cuando ocurrió el problema.

{{index "user interface"}}

Para problemas que se _espera_ que sucedan durante el uso rutinario,
estrellarse con una excepción no manejada es una estrategia terrible.

{{index syntax, [function, application], "exception handling", "Error type"}}

Usos inválidos del lenguaje, como hacer referencia a ((vinculaciones))
inexistentes, buscar una propiedad en `null`, o llamar a algo
que no sea una función, también dará como resultado que se levanten excepciones.
Tales excepciones también pueden ser atrapadas.

{{index "catch keyword"}}

Cuando se ingresa en un cuerpo `catch`, todo lo que sabemos es que _algo_ en
nuestro cuerpo `try` provocó una excepción. Pero no sabemos _que_, o _cual_
excepción este causó.

{{index "exception handling"}}

JavaScript (en una omisión bastante evidente) no proporciona soporte directo
para la captura selectiva de excepciones: o las atrapas todas
o no atrapas nada. Esto hace que sea tentador _asumir_ que la
excepción que obtienes es en la que estabas pensando cuando escribiste
el bloque `catch`.

{{index "promptDirection function"}}

Pero puede que no. Alguna otra ((suposición)) podría ser violada, o
es posible que hayas introducido un error que está causando una excepción.
Aquí está un ejemplo que _intenta_ seguir llamando `pedirDireccion`
hasta que obtenga una respuesta válida:

```{test: no}
for (;;) {
  try {
    let direccion = peirDirrecion("Donde?"); // ← error tipografico!
    console.log("Tu elegiste ", direccion);
    break;
  } catch (e) {
    console.log ("No es una dirección válida. Inténtalo de nuevo");
  }
}
```

{{index "infinite loop", "for loop", "catch keyword", debugging}}

El constructo `for (;;)` es una forma de crear intencionalmente un ciclo que
no termine por si mismo. Salimos del ciclo solamente una cuando
dirección válida sea dada. _Pero_ escribimos mal `pedirDireccion`, lo que
dará como resultado un error de "variable indefinida". Ya que el bloque
`catch` ignora por completo su valor de excepción (`e`), suponiendo que sabe
cuál es el problema, trata erróneamente al error de vinculación como indicador
de una mala entrada. Esto no solo causa un ciclo infinito, también
"entierra" el útil mensaje de error acerca de la vinculación mal escrita.

Como regla general, no incluyas excepciones a menos de que sean con el
propósito de "enrutarlas" hacia alguna parte—por ejemplo, a través de la red
para decirle a otro sistema que nuestro programa se bloqueó. E incluso entonces,
piensa cuidadosamente sobre cómo podrias estar ocultando información.

{{index "exception handling"}}

Por lo tanto, queremos detectar un tipo de excepción _específico_.
Podemos hacer esto al revisar el bloque `catch` si la excepción que
tenemos es en la que estamos interesados ​​y relanzar de otra manera. Pero como
hacemos para reconocer una excepción?

Podríamos comparar su propiedad `message` con el mensaje de ((error)) que
sucede estamos esperando. Pero esa es una forma inestable de escribir
código—estariamos utilizando información destinada al consumo humano (el mensaje)
para tomar una decisión programática. Tan pronto como alguien cambie (o
traduzca) el mensaje, el código dejaria de funcionar.

{{index "Error type", "instanceof operator", "promptDirection function"}}

En vez de esto, definamos un nuevo tipo de error y usemos `instanceof` para
identificarlo.

```{includeCode: true}
class ErrorDeEntrada extends Error {}

function pedirDireccion(pregunta) {
  let resultado = prompt(pregunta);
  if (resultado.toLowerCase() == "izquierda") return "I";
  if (resultado.toLowerCase() == "derecha") return "D";
  throw new ErrorDeEntrada("Direccion invalida: " + resultado);
}
```

{{index "throw keyword", inheritance}}

La nueva clase de error extiende `Error`. No define su propio
constructor, lo que significa que hereda el constructor `Error`,
que espera un mensaje de string como argumento. De hecho, no define
nada—la clase está vacía. Los objetos `ErrorDeEntrada` se comportan como
objetos `Error`, excepto que tienen una clase diferente por la cual
podemos reconocerlos.

{{index "exception handling"}}

Ahora el ciclo puede atraparlos con mas cuidado.

```{test: no}
for (;;) {
  try {
    let direccion = pedirDireccion("Donde?");
    console.log("Tu eliges ", direccion);
    break;
  } catch (e) {
    if (e instanceof ErrorDeEntrada) {
      console.log ("No es una dirección válida. Inténtalo de nuevo");
    } else {
      throw e;
    }
  }
}
```

{{index debugging}}

Esto capturará solo las instancias de `error` y dejará que las excepciones
no relacionadas pasen a través. Si reintroduce el error tipográfico,
el error de la vinculación indefinida será reportado correctamente.

## Afirmaciones

{{index "assert function", assertion, debugging}}

Las _afirmaciones_ son comprobaciones dentro de un programa que verifican que
algo este en la forma en la que se supone que debe estar. Se usan no para
manejar situaciones que puedan aparecer en el funcionamiento normal,
pero para encontrar errores hechos por el programador.

Si, por ejemplo, `primerElemento` se describe como una función que nunca
se debería invocar en arrays vacíos, podríamos escribirla así:

```
function primerElemento(array) {
  if (array.length == 0) {
    throw new Error("primerElemento llamado con []");
  }
  return array[0];
}
```

{{index validation, "run-time error", crash, assumption, array}}

Ahora, en lugar de silenciosamente retornar `undefined` (que es lo que obtienes
cuando lees una propiedad de array que no existe), esto explotará fuertemente
tu programa tan pronto como lo uses mal. Esto hace que sea menos probable
que tales errores pasen desapercibidos, y sea más fácil encontrar su causa
cuando estos ocurran.

No recomiendo tratar de escribir afirmaciones para todos los tipos posibles
de entradas erroneas. Eso sería mucho trabajo y llevaría a código muy ruidoso.
Querrás reservarlas para errores que son fáciles de hacer
(o que te encuentras haciendo constantemente).

## Resumen

Los errores y las malas entradas son hechos de la vida. Una parte importante de
la programación es encontrar, diagnosticar y corregir errores. Los problemas
pueden será más fáciles de notar si tienes un conjunto de pruebas
automatizadas o si agregas afirmaciones a tus programas.

Por lo general, los problemas causados ​​por factores fuera del control del
programa deberían ser manejados con gracia. A veces, cuando el problema pueda ser
manejado localmente, los valores de devolución especiales son una buena forma
de rastrearlos. De lo contrario, las excepciones pueden ser preferibles.

Al lanzar una excepción, se desenrolla la pila de llamadas hasta
el próximo bloque `try/catch` o hasta el final de la pila.
Se le dará el valor de excepción al bloque `catch` que lo atrape,
que debería verificar que en realidad es el tipo esperado de excepción
y luego hacer algo con eso. Para ayudar a controlar el impredecible
flujo de control causado por las excepciones, los bloques `finally`
se pueden usar para asegurarte de que una parte del código _siempre_ se
ejecute cuando un bloque termina.

## Ejercicios

### Reintentar

{{index "primitiveMultiply (exercise)", "exception handling", "throw keyword"}}

Digamos que tienes una función `multiplicacionPrimitiva` que, en el 20 por
ciento de los casos, multiplica dos números, y en el otro 80 por ciento,  
genera una excepción del tipo `FalloUnidadMultiplicadora`. Escribe una función
que envuelva esta torpe función y solo siga intentando hasta que una llamada
tenga éxito, después de lo cual retorna el resultado.

{{index "catch keyword"}}

Asegúrete de solo manejar las excepciones que estás tratando de manejar.

{{if interactive

```{test: no}
class FalloUnidadMultiplicadora extends Error {}

function multiplicacionPrimitiva(a, b) {
  if (Math.random() < 0.2) {
    return a * b;
  } else {
    throw new FalloUnidadMultiplicadora("Klunk");
  }
}

function multiplicacionConfiable(a, b) {
  // Tu código aqui.
}

console.log(multiplicacionConfiable(8, 8));
// → 64
```
if}}

{{hint

{{index "primitiveMultiply (exercise)", "try keyword", "catch keyword", "throw keyword"}}

La llamada a `multiplicacionPrimitiva` definitivamente debería suceder en un
bloquear `try`. El bloque `catch` correspondiente debe volver a lanzar la
excepción cuando no esta no sea una instancia de `FalloUnidadMultiplicadora`
y asegurar que la llamada sea reintentada cuando lo es.

Para reintentar, puedes usar un ciclo que solo se rompa cuando
la llamada tenga éxito, como en el [ejemplo de `mirar`](error#look)
anteriormente en este capítulo—o usar ((recursión)) y esperar que no obtengas
una cadena de fallas tan largas que desborde la pila
(lo cual es una apuesta bastante segura).

hint}}

### La caja bloqueada

{{index "locked box (exercise)"}}

Considera el siguiente objeto (bastante artificial):

```
const caja = {
  bloqueada: true,
  desbloquear() { this.bloqueada = false; },
  bloquear() { this.bloqueada = true;  },
  _contenido: [],
  get contenido() {
    if (this.bloqueada) throw new Error("Bloqueada!");
    return this._contenido;
  }
};
```

{{index "private property", "access control"}}

Es solo una ((caja)) con una cerradura. Hay un array en la caja, pero solo
puedes accederlo cuando la caja esté desbloqueada. Acceder directamente a la
propiedad privada `_contenido` está prohibido.

{{index "finally keyword", "exception handling"}}

Escribe una función llamada `conCajaDesbloqueada` que toma un valor de función
como su argumento, desbloquea la caja, ejecuta la función y luego se asegura
de que la caja se bloquee nuevamente antes de retornar, independientemente de
si la función argumento retorno normalmente o lanzo una excepción.

{{if interactive

```
const caja = {
  bloqueada: true,
  desbloquear() { this.bloqueada = false; },
  bloquear() { this.bloqueada = true;  },
  _contenido: [],
  get contenido() {
    if (this.bloqueada) throw new Error("Bloqueada!");
    return this._contenido;
  }
};

function conCajaDesbloqueada(cuerpo) {
  // Tu código aqui.
}

conCajaDesbloqueada(function() {
  caja.contenido.push("moneda de oro");
});

try {
  conCajaDesbloqueada(function() {
    throw new Error("Piratas en el horizonte! Abortar!");
  });
} catch (e) {
  console.log("Error encontrado:", e);
}
console.log(caja.bloqueada);
// → true
```

Por puntos extras, asegúrete de que si llamas a `conCajaDesbloqueada`
cuando la caja ya está desbloqueada, la caja permanece desbloqueada.

if}}

{{hint

{{index "locked box (exercise)", "finally keyword", "try keyword"}}

Este ejercicio requiere de un bloque `finally`. Tu función deberia primero
desbloquear la caja y luego llamar a la función argumento desde dentro de
cuerpo `try`. El bloque `finally` después de el debería bloquear la caja
nuevamente.

Para asegurarte de que no bloqueemos la caja cuando no estaba ya bloqueada,
comprueba su bloqueo al comienzo de la función y desbloquea y bloquea
solo cuando la caja comenzó bloqueada.

hint}}
