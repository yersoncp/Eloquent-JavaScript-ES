{{meta {code_links: "[\"code/file_server.js\"]"}}}

# Node.js

{{quote {author: "Master Yuan-Ma", title: "The Book of Programming", chapter: true}

Un estudiante preguntó: 'Los programadores de antaño sólo usaban
máquinas simples y ningún lenguaje de programación, pero hicieron
programas hermosos. ¿Por qué usamos máquinas y lenguajes de
programación complicados?'. Fu-Tzu respondió, 'Los constructores de
antaño sólo usaban palos y arcilla, pero hicieron bellas chozas.'

quote}}

{{index "Yuan-Ma", "Book of Programming"}}

{{figure {url: "img/chapter_picture_20.jpg", alt: "Picture of a telephone pole", chapter: "framed"}}}

{{index "command line"}}

Hasta ahora, hemos usado el lenguaje JavaScript en un solo entorno: el
navegador. Este capítulo y el [siguiente](skillsharing) presentarán
brevemente ((Node.js)), un programa que  te permite aplicar tus
habilidades de JavaScript fuera del navegador. Con él, puedes crear
cualquier cosa, desde pequeñas herramientas de línea de comandos hasta
((servidor))es HTTP que potencian los ((sitios web)) dinámicos.

Estos capítulos tienen como objetivo enseñarte los principales
conceptos que Node.js utiliza y darte suficiente información para
escribir programas útiles de este. No intentan ser un tratamiento
completo, ni siquiera minucioso, de la plataforma.

{{if interactive

Mientras que en los capítulos anteriores podías ejecutar el código
directamente de esas páginas, ya que era JavaScript simple o escrito
para el navegador, los códigos de ejemplo de este capítulo están
escritos para Node y estos normalmente no se ejecutarán en el
navegador.

if}}

Si quieres seguir y ejecutar el código de este capítulo, necesitarás
instalar Node.js versión 10.1 o superior. Para ello, dirigete a
[_https://nodejs.org_](https://nodejs.org) y sigue las instrucciones
de instalación para tu sistema operativo. También podrás encontrar más
documentación para Node.js allí.

## Antecedentes

{{index responsiveness, input, [network, speed]}}

Uno de los problemas más difíciles de los sistemas de escritura que se
comunican a través de la red es la gestión de entrada y salida, es
decir, la lectura y la escritura de datos hacia y desde la red y el
disco duro. Mover los datos toma tiempo, y programarlos con habilidad
puede hacer una gran diferencia en que tan rápido un sistema
responde al usuario o a las peticiones de red.

{{index ["asynchronous programming", "in Node.js"]}}

En tales programas, la programación asíncrona suele ser útil. Permite
que el programa envíe y reciba datos desde y hacia múltiples
dispositivos al mismo tiempo sin necesidad de una complicada
administración y sincronización de hilos.

{{index "programming language", "Node.js", standard}}

Node fue concebido inicialmente con el propósito de hacer la
programación asíncrona fácil y conveniente. JavaScript se presta bien
a un sistema como Node. Es uno de los pocos lenguajes de programación
que no tiene una forma incorporada de hacer entrada- y salida. Por lo
tanto, JavaScript podría encajar en un enfoque bastante excéntrico de
Node para hacer entrada y salida sin terminar con dos interfaces
inconsistentes. En 2009, cuando se diseñó Node, la gente ya estaba
haciendo programación basada en llamadas en el navegador, por lo que
la comunidad en torno al lenguaje estaba acostumbrada a un estilo de
programación asíncrono.

## El comando Node

{{index "node program"}}

Cuando ((Node.js)) se instala en un sistema, proporciona un programa
llamado `node`, que se utiliza para ejecutar archivos JavaScript.
Digamos que tienes un archivo `hello.js`, que contiene este código:

```
let message = "Hello world";
console.log(message);
```

Entonces podrás lanzar `node` desde la ((línea de comandos)) de esta
manera para ejecutar el programa:

```{lang: null}
$ node hello.js
Hello world
```

{{index "console.log"}}

El método `console.log` en Node hace algo similar a lo que realiza en
el navegador. Imprime una porción de texto. Pero en Node, el texto irá
al flujo de ((la salida estándar)) del proceso, en lugar de la ((consola
de JavaScript)) del navegador. Cuando ejecutas Node desde la línea de
comandos, eso significa que verás los valores escritos en tu
((terminal)).

{{index "node program", "read-eval-print loop"}}

Si ejecutas `node` sin indicar un archivo, te proporciona un prompt en
el cual podrás escribir código JavaScript e inmediatamente ver el
resultado.

```{lang: null}
$ node
> 1 + 1
2
> [-1, -2, -3].map(Math.abs)
[1, 2, 3]
> process.exit(0)
$
```

{{index "process object", "global scope", [binding, global], "exit method", "status code"}}

La referencia `process`, al igual que la referencia `console`, está
disponible globalmente en Node. Esta proporciona varias formas de
inspeccionar y manipular el programa actual. El método `exit` finaliza
el proceso y se le puede pasar un código de estado de salida, esto le
dice al programa que inició node (en este caso, el shell de la línea
de comandos) si el programa finalizó con éxito (código cero) o si se
encontró con un error (cualquier otro código).

{{index "command line", "argv property"}}

Para obtener los argumentos de la línea de comandos proporcionados a
tu script, puede leer `process.argv`, que es un conjunto de cadenas.
Ten en cuenta que también incluye el nombre del comando `node` y el
nombre de tu script, por lo que los argumentos reales comienzan en el
índice 2. Si `showargv.js` contiene la sentencia
console.log(process.argv), podrías ejecutarlo así:

```{lang: null}
$ node showargv.js one --and two
["node", "/tmp/showargv.js", "one", "--and", "two"]
```

{{index [binding, global]}}

Todas las referencias globales JavaScript ((estándar)), como Array,
Math y JSON, también están presentes en el entorno de Node. La
funcionalidad relacionada con el navegador, como el documento o el
prompt, no lo son.

## Módulos

{{index "Node.js", "global scope", "module loader"}}

Más allá de las referencias que mencioné, como `console` y `process`,
Node pone unas cuantas referencias adicionales en el ámbito global.
Si quieres acceder a la funcionalidad integrada, tienes que pedírsela
al sistema de módulos.

{{index "require function"}}

El sistema de módulos ((CommonJS)), basado en la función `require`, se
describió en el [Capítulo ?](modules#commonjs). Este sistema está
incorporado en Node y se usa para cargar cualquier cosa desde módulos
incorporados hasta ((paquete))s descargados y ((archivo))s que forman
parte de tu propio programa.

{{index [path, "file system"], "relative path", resolution}}

Cuando se llama a `require`, Node tiene que resolver la cadena dada a
un ((archivo)) real que pueda cargar. Las rutas que empiezan por `/`,
`./`, o `../` se resuelven relativamente a la ruta del módulo actual,
donde `.` se refiere al directorio actual, `../` a un directorio
superior, y `/` a la raíz del sistema de archivos. Así que si pide
`"./graph"` del archivo `/tmp/robot/robot.js`, Node intentará cargar
el archivo `/tmp/robot/graph.js`.

{{index "index.js"}}

La ((extension)) `.js` puede ser omitida, y Node la añadirá si existe
tal archivo. Si la ruta requerida se refiere a un ((directorio)), Node
intentará cargar el archivo llamado `index.js` en ese directorio.

{{index "node_modules directory", directory}}


Cuando una cadena que no parece una ruta relativa o absoluta es dada a
`require`, se asume que se refiere a un ((módulo)) incorporado o a
un módulo instalado en un directorio `node_modules`. Por ejemplo,
`require("fs")` te dará el módulo incorporado del sistema de archivos
de Node. Y `require("robot")` podría intentar cargar la biblioteca que
se encuentra en `node_modules/robot/`. Una forma común de instalar
tales librerías es usando ((NPM)), al cual volveremos en un momento.

{{index "require function", "Node.js", "garble example"}}

Vamos a crear un pequeño proyecto que consiste en dos archivos. El
primero, llamado `main.js`, define un script que puede ser llamado
desde la ((línea de comandos)) para invertir una cadena.

```
const {reverse} = require("./reverse");

// El índice 2 contiene el primer argumento de la línea de comandos
let argument = process.argv[2];

console.log(reverse(argument));
```

{{index reuse, "Array.from function", "join method"}}

El archivo `reverse.js` define una biblioteca para invertir cadenas,
el cual puede ser utilizado tanto por la herramienta de línea de
comandos como por otros scripts que necesiten acceso directo a una
función de inversión de cadenas.

```
exports.reverse = function(string) {
  return Array.from(string).reverse().join("");
};
```

{{index "exports object", CommonJS, [interface, module]}}

Recuerde que al añadir propiedades a `exports` estas se agregan a la
interfaz del módulo. Dado que Node.js trata a los archivos como
((módulo))s ((CommonJS)), `main.js` puede tomar la función exportada
`reverse` desde `reverse.js`.

Ahora podemos llamar a nuestra herramienta así:

```{lang: null}
$ node main.js JavaScript
tpircSavaJ
```

## Instalación con NPM

{{index NPM, "Node.js", "npm program", library}}

NPM, el cual fue introducido en el [Capítulo ?](modules#modules_npm),
es un repositorio en línea de ((módulo))s JavaScript, muchos de los
cuales están escritos específicamente para Node. Cuando instalas Node
en tu  ordenador, también obtienes el comando `npm`, que puedes usar
para interactuar con este repositorio.

{{index "ini package"}}

El uso principal de NPM es ((descargar)) paquetes. Vimos el paquete
`ini` en el [Capítulo ?](modules#modules_ini). nosotros podemos usar
NPM para buscar e instalar ese paquete en nuestro ordenador.

```{lang: null}
$ npm install ini
npm WARN enoent ENOENT: no such file or directory,
         open '/tmp/package.json'
+ ini@1.3.5
added 1 package in 0.552s

$ node
> const {parse} = require("ini");
> parse("x = 1\ny = 2");
{ x: '1', y: '2' }
```

{{index "require function", "node_modules directory", "npm program"}}

Después de ejecutar `npm install`, ((NPM)) habrá creado un directorio
llamado `node_modules`. Dentro de ese directorio habrá un directorio
`ini` que contiene la ((biblioteca)). Puedes abrirlo y mirar el
código. Cuando llamamos a `require("ini")`, esta librería se carga, y
podemos llamar a la propiedad `parse` para analizar un archivo de
configuración.

Por defecto, NPM instala los paquetes bajo el directorio actual, en
lugar de una ubicación central. Si está acostumbrado a otros gestores
de paquetes, esto puede parecer inusual, pero tiene ventajas, pone a
cada aplicación en control total de los paquetes que instala y
facilita la gestión de versiones y la limpieza al eliminar una
aplicación.

### Archivos de paquetes

{{index "package.json", dependency}}

En el ejemplo `npm install`, se podía ver una ((advertencia)) sobre el
hecho de que el archivo `package.json` no existía. Se recomienda crear
dicho archivo para cada proyecto, ya sea manualmente o ejecutando `npm
init`. Este contiene alguna información sobre el proyecto, como lo son
su nombre y ((versión)), y enumera sus dependencias.

La simulación del robot del [Capítulo ?](robot), tal como fue
modularizada en el ejercicio del [Capítulo ?](modules#modular_robot),
podría tener un archivo `package.json` como este:

```{lang: "application/json"}
{
  "author": "Marijn Haverbeke",
  "name": "eloquent-javascript-robot",
  "description": "Simulation of a package-delivery robot",
  "version": "1.0.0",
  "main": "run.js",
  "dependencies": {
    "dijkstrajs": "^1.0.1",
    "random-item": "^1.0.0"
  },
  "license": "ISC"
}
```

{{index "npm program", tool}}

Cuando ejecutas `npm install` sin nombrar un paquete a instalar, NPM
instalará las dependencias listadas en `package.json`. Cuando instale
un paquete específico que no esté ya listado como una dependencia, NPM
lo añadirá al `package.json`.

### Versiones

{{index "package.json", dependency, evolution}}

Un archivo `package.json` lista tanto la ((versión)) propia del
programa como las versiones de sus dependencias. Las versiones son una
forma de lidiar con el hecho de que los ((paquete))s evolucionan por
separado, y el código escrito para trabajar con un paquete tal y
como existía en un determinado momento pueda no funcionar con una
versión posterior y modificada del paquete.

{{index compatibility}}

NPM exige que sus paquetes sigan un esquema llamado _((versionado
semántico))_, el cual codifica cierta información sobre qué versiones
son _compatibles_ (no rompan la vieja interfaz) en el número de
versión.  Una versión semántica consiste en tres números, separados
por puntos, como lo es `2.3.0`. Cada vez que una nueva  funcionalidad
es agregada, el número intermedio tiene que ser incrementado. Cada vez
que se rompe la compatibilidad, de modo que el código existente que
utiliza el paquete podría no funcionar con esa nueva versión, el
primer número tiene que ser incrementado.

{{index "caret character"}}

Un carácter cuña (`^`) delante del número de versión para una
dependencia en el `package.json` indica que cualquier versión
compatible con el número dado puede ser instalada. Así, por ejemplo,
`"^2.3.0"` significaría que cualquier versión mayor o igual a 2.3.0 y
menor a 3.0.0 está permitida.

{{index publishing}}

El comando `npm` también se usa para publicar nuevos paquetes o nuevas
versiones de paquetes. Si ejecutas `npm publish` en un ((directorio))
que tiene un archivo `package.json`, esto publicará un  paquete con el
nombre y la versión que aparece en el archivo JSON. Cualquiera puede
publicar paquetes en NPM, aunque sólo bajo un nombre de paquete que
no esté en uso todavía, ya que sería algo aterrador si personas al
azar pudieran actualizar los paquetes existentes.

Dado que el programa `npm` es una pieza de software que habla con un
sistema abierto—El registro de paquetes— no es el único que lo hace.
Otro programa, `yarn`, el cual puede ser instalado desde el registro
NPM, cumple el mismo papel que `npm` usando una interfaz y una
estrategia de instalación algo diferente.

Este libro no profundizará en los detalles del uso del ((NPM)).
Consulte [_https://npmjs.org_](https://npmjs.org) para obtener más
documentación y formas para buscar paquetes.

## El módulo del sistema de archivos

{{index directory, "fs package", "Node.js", [file, access]}}

Uno de los módulos incorporados más utilizados en Node es el módulo
`fs`, que significa _((sistema de archivos))_. Este exporta funciones
para trabajar con archivos y directorios.

{{index "readFile function", "callback function"}}

Por ejemplo, la función llamada `readFile` lee un archivo y luego
llama un callback con el contenido del archivo.

```
let {readFile} = require("fs");
readFile("file.txt", "utf8", (error, text) => {
  if (error) throw error;
  console.log("The file contains:", text);
});
```

{{index "Buffer class"}}

El segundo argumento para `readFile` indica la _((codificación de
caracteres))_  utilizada para decodificar el archivo en una cadena.
Hay varias formas de codificar ((texto)) en ((datos binarios)), pero
la mayoría de los sistemas modernos utilizan ((UTF-8)). Por lo tanto,
a menos que tenga razones para creer que se utiliza otra codificación,
pase `"utf8"` cuando lea un archivo de texto. Si no pasas una
codificación, Node asumirá que estás interesado en los datos binarios
y te dará un objeto `Buffer` en lugar de una cadena. Este es un
((objeto tipo array)) que contiene números que representan los bytes
(trozos de 8 bits de datos) en los archivos.

```
const {readFile} = require("fs");
readFile("file.txt", (error, buffer) => {
  if (error) throw error;
  console.log("The file contained", buffer.length, "bytes.",
              "The first byte is:", buffer[0]);
});
```

{{index "writeFile function", "file system", [file, access]}}

Una función similar, `writeFile`, es usada para escribir un archivo en
el disco.

```
const {writeFile} = require("fs");
writeFile("graffiti.txt", "Node was here", err => {
  if (err) console.log(`Failed to write file: ${err}`);
  else console.log("File written.");
});
```

{{index "Buffer class", "character encoding"}}

Aquí no fue necesario especificar la codificación—`writeFile` asumirá
que cuando se le da una cadena a escribir, en lugar de un objeto
`Buffer`, debe escribirlo como texto usando su codificación de
caracteres por defecto, que es ((UTF-8)).

{{index "fs package", "readdir function", "stat function", "rename function", "unlink function"}}

El módulo `fs` contiene muchas otras funciones útiles: `readdir`
devolverá los archivos de un ((directorio)) como un array de cadenas,
`stat` recuperará información sobre un archivo, `rename` renombrará un
archivo, `unlink` eliminará uno, y así sucesivamente. Ve la
documentación en [_https://nodejs.org_](https://nodejs.org) para más
detalle.

{{index ["asynchronous programming", "in Node.js"], "Node.js", "error handling", "callback function"}}

La mayoría de estos toman una función de devolución como último
parámetro, el cual lo llaman ya sea con un error (el primer argumento)
o con un resultado exitoso (el segundo). Como vimos en el
[Capítulo ?](async), este estilo de programación tiene sus desventajas
, la mayor de las cuales es que el manejo de los errores se vuelve
enredado y propenso a errores.

{{index "Promise class", "promises package"}}

Aunque las promesas han sido parte de JavaScript por un tiempo, hasta
el momento de escribir este documento su integración en Node.js es
aun un trabajo en progreso. Hay un objeto `promises` exportado del
paquete `fs` desde la versión 10.1 que contiene la mayoría de las
mismas funciones que `fs` pero que utiliza promesas en lugar de
funciones de devolución de llamada.

```
const {readFile} = require("fs").promises;
readFile("file.txt", "utf8")
  .then(text => console.log("The file contains:", text));
```

{{index "synchronous programming", "fs package", "readFileSync function"}}

Algunas veces no se necesita asincronicidad, y esto sólo obstaculiza
el camino. Muchas de las funciones en `fs` también tienen una variante
síncrona, que tiene el mismo nombre con `Sync` añadido al final. Por
ejemplo, la versión síncrona de `readFile` es llamada `readFileSync`.

```
const {readFileSync} = require("fs");
console.log("The file contains:",
            readFileSync("file.txt", "utf8"));
```

{{index optimization, performance, blocking}}

Ten en cuenta que mientras se realiza esta operación sincrónica, tu
programa se detiene por completo. Si debe responder al usuario o a
otras máquinas en la red, quedarse atascado en una acción síncrona
puede producir retrasos molestos.

## The HTTP module

{{index "Node.js", "http package", [HTTP, server]}}

Another central module is called `http`. It provides functionality for
running HTTP ((server))s and making HTTP ((request))s.

{{index "listening (TCP)", "listen method", "createServer function"}}

This is all it takes to start an HTTP server:

```
const {createServer} = require("http");
let server = createServer((request, response) => {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write(`
    <h1>Hello!</h1>
    <p>You asked for <code>${request.url}</code></p>`);
  response.end();
});
server.listen(8000);
console.log("Listening! (port 8000)");
```

{{index port, localhost}}

If you run this script on your own machine, you can point your web
browser at
[_http://localhost:8000/hello_](http://localhost:8000/hello) to make a
request to your server. It will respond with a small HTML page.

{{index "createServer function", HTTP}}

The function passed as argument to `createServer` is called every time
a client connects to the server. The `request` and `response` bindings
are objects representing the incoming and outgoing data. The first
contains information about the ((request)), such as its `url` property,
which tells us to what URL the request was made.

So, when you open that page in your browser, it sends a request to your
own computer. This causes the server function to run and send back a
response, which you can then see in the browser.

{{index "200 (HTTP status code)", "Content-Type header", "writeHead method"}}

To send something back, you call methods on the `response` object. The
first, `writeHead`, will write out the ((response)) ((header))s (see
[Chapter ?](http#headers)). You give it the status code (200 for "OK"
in this case) and an object that contains header values. The example
sets the `Content-Type` header to inform the client that we'll be
sending back an HTML document.

{{index "writable stream", "body (HTTP)", stream, "write method", "end method"}}

Next, the actual response body (the document itself) is sent with
`response.write`. You are allowed to call this method multiple times
if you want to send the response piece by piece, for example to stream
data to the client as it becomes available. Finally, `response.end`
signals the end of the response.

{{index "listen method"}}

The call to `server.listen` causes the ((server)) to start waiting for
connections on ((port)) 8000. This is why you have to connect
to _localhost:8000_ to speak to this server, rather than just
_localhost_, which would use the default port 80.

{{index "Node.js", "kill process"}}

When you run this script, the process just sits there and waits. When
a script is listening for events—in this case, network
connections—`node` will not automatically exit when it reaches the end
of the script. To close it, press [control]{keyname}-C.

{{index [method, HTTP]}}

A real web ((server)) usually does more than the one in the example—it
looks at the request's ((method)) (the `method` property) to see what
action the client is trying to perform and looks at the request's ((URL)) to
find out which resource this action is being performed on. We'll see a
more advanced server [later in this chapter](node#file_server).

{{index "http package", "request function", [HTTP, client]}}

To act as an HTTP _((client))_, we can use the `request` function
in the `http` module.

```
const {request} = require("http");
let requestStream = request({
  hostname: "eloquentjavascript.net",
  path: "/20_node.html",
  method: "GET",
  headers: {Accept: "text/html"}
}, response => {
  console.log("Server responded with status code",
              response.statusCode);
});
requestStream.end();
```

{{index "Node.js", "callback function", "readable stream"}}

The first argument to `request` configures the request, telling Node
what server to talk to, what path to request from that server, which
method to use, and so on. The second argument is the function that
should be called when a response comes in. It is given an object that
allows us to inspect the response, for example to find out its status
code.

{{index "GET method", "write method", "end method", "writable stream", "request function"}}

Just like the `response` object we saw in the server, the object
returned by `request` allows us to ((stream)) data into the
((request)) with the `write` method and finish the request with the
`end` method. The example does not use `write` because `GET` requests
should not contain data in their request body.

{{index HTTPS, "https package", "request function"}}

There's a similar `request` function in the `https` module that
can be used to make requests to `https:` URLs.

{{index "fetch function", "Promise class", "node-fetch package"}}

Making requests with Node's raw functionality is rather verbose.
There are much more convenient wrapper packages available on NPM. For
example, `node-fetch` provides the promise-based `fetch` interface that
we know from the browser.

## Streams

{{index "Node.js", stream, "writable stream"}}

We have seen two instances of writable streams in the HTTP
examples—namely, the response object that the server could write to
and the request object that was returned from `request`.

{{index "callback function", ["asynchronous programming", "in Node.js"], "write method", "end method", "Buffer class"}}

_Writable streams_ are a widely used concept in Node. Such objects have
a `write` method that can be passed a string or a `Buffer` object to
write something to the stream. Their `end` method closes the stream
and optionally takes a value to write to the stream before
closing. Both of these methods can also be given a callback as an
additional argument, which they will call when the writing or closing
has finished.

{{index "createWriteStream function", "writeFile function", [file, stream]}}

It is possible to create a writable stream that points at a file
with the `createWriteStream` function from the `fs` module. Then you
can use the `write` method on the resulting object to write the file
one piece at a time, rather than in one shot as with `writeFile`.

{{index "createServer function", "request function", "event handling", "readable stream"}}

Readable ((stream))s are a little more involved. Both the `request`
binding that was passed to the HTTP server's callback and the
`response` binding passed to the HTTP client's callback are readable
streams—a server reads requests and then writes responses, whereas a
client first writes a request and then reads a response. Reading from
a stream is done using event handlers, rather than methods.

{{index "on method", "addEventListener method"}}

Objects that emit events in Node have a method called `on` that is
similar to the `addEventListener` method in the browser. You give it
an event name and then a function, and it will register that function
to be called whenever the given event occurs.

{{index "createReadStream function", "data event", "end event", "readable stream"}}

Readable ((stream))s have `"data"` and `"end"` events. The first is
fired every time data comes in, and the second is called whenever the
stream is at its end. This model is most suited for _streaming_ data that
can be immediately processed, even when the whole document isn't
available yet. A file can be read as a readable stream by using the
`createReadStream` function from `fs`.

{{index "upcasing server example", capitalization, "toUpperCase method"}}

This code creates a ((server)) that reads request bodies and streams
them back to the client as all-uppercase text:

```
const {createServer} = require("http");
createServer((request, response) => {
  response.writeHead(200, {"Content-Type": "text/plain"});
  request.on("data", chunk =>
    response.write(chunk.toString().toUpperCase()));
  request.on("end", () => response.end());
}).listen(8000);
```

{{index "Buffer class", "toString method"}}

The `chunk` value passed to the data handler will be a binary
`Buffer`. We can convert this to a string by decoding it as UTF-8
encoded characters with its `toString` method.

The following piece of code, when run with the uppercasing server
active, will send a request to that server and write out the response
it gets:

```
const {request} = require("http");
request({
  hostname: "localhost",
  port: 8000,
  method: "POST"
}, response => {
  response.on("data", chunk =>
    process.stdout.write(chunk.toString()));
}).end("Hello server");
// → HELLO SERVER
```

{{index "stdout property", "standard output", "writable stream", "console.log"}}

The example writes to `process.stdout` (the process's standard output,
which is a writable stream) instead of using `console.log`. We can't
use `console.log` because it adds an extra newline character after
each piece of text that it writes, which isn't appropriate here since
the response may come in as multiple chunks.

{{id file_server}}

## A file server

{{index "file server example", "Node.js", [HTTP, server]}}

Let's combine our newfound knowledge about HTTP ((server))s and
working with the ((file system)) to create a bridge between the two:
an HTTP server that allows ((remote access)) to a file system. Such a
server has all kinds of uses—it allows web applications to store and
share data, or it can give a group of people shared access to a bunch of
files.

{{index [path, URL], "GET method", "PUT method", "DELETE method", [file, resource]}}

When we treat files as HTTP ((resource))s, the HTTP methods `GET`,
`PUT`, and `DELETE` can be used to read, write, and delete the files,
respectively. We will interpret the path in the request as the path of
the file that the request refers to.

{{index [path, "file system"], "relative path"}}

We probably don't want to share our whole file system, so we'll
interpret these paths as starting in the server's working
((directory)), which is the directory in which it was started. If I
ran the server from `/tmp/public/` (or `C:\tmp\public\` on Windows),
then a request for `/file.txt` should refer to `/tmp/public/file.txt`
(or `C:\tmp\public\file.txt`).

{{index "file server example", "Node.js", "methods object", "Promise class"}}

We'll build the program piece by piece, using an object called
`methods` to store the functions that handle the various HTTP methods.
Method handlers are `async` functions that get the request object as
argument and return a promise that resolves to an object that
describes the response.

```{includeCode: ">code/file_server.js"}
const {createServer} = require("http");

const methods = Object.create(null);

createServer((request, response) => {
  let handler = methods[request.method] || notAllowed;
  handler(request)
    .catch(error => {
      if (error.status != null) return error;
      return {body: String(error), status: 500};
    })
    .then(({body, status = 200, type = "text/plain"}) => {
       response.writeHead(status, {"Content-Type": type});
       if (body && body.pipe) body.pipe(response);
       else response.end(body);
    });
}).listen(8000);

async function notAllowed(request) {
  return {
    status: 405,
    body: `Method ${request.method} not allowed.`
  };
}
```

{{index "405 (HTTP status code)"}}

This starts a server that just returns 405 error responses, which is
the code used to indicate that the server refuses to handle a given
method.

{{index "500 (HTTP status code)", "error handling", "error response"}}

When a request handler's promise is rejected, the `catch` call
translates the error into a response object, if it isn't one already, so
that the server can send back an error response to inform the client
that it failed to handle the request.

{{index "200 (HTTP status code)", "Content-Type header"}}

The `status` field of the response description may be omitted, in
which case it defaults to 200 (OK). The content type, in the `type`
property, can also be left off, in which case the response is assumed
to be plain text.

{{index "end method", "pipe method", stream}}

When the value of `body` is a ((readable stream)), it will have a
`pipe` method that is used to forward all content from a readable
stream to a ((writable stream)). If not, it is assumed to be either
`null` (no body), a string, or a buffer, and it is passed directly to the
((response))'s `end` method.

{{index [path, URL], "urlToPath function", "url package", parsing, [escaping, "in URLs"], "decodeURIComponent function", "startsWith method"}}

To figure out which file path corresponds to a request URL, the
`urlPath` function uses Node's built-in `url` module to parse the URL.
It takes its pathname, which will be something like `"/file.txt"`,
decodes that to get rid of the `%20`-style escape codes, and resolves
it relative to the program's working directory.

```{includeCode: ">code/file_server.js"}
const {parse} = require("url");
const {resolve, sep} = require("path");

const baseDirectory = process.cwd();

function urlPath(url) {
  let {pathname} = parse(url);
  let path = resolve(decodeURIComponent(pathname).slice(1));
  if (path != baseDirectory &&
      !path.startsWith(baseDirectory + sep)) {
    throw {status: 403, body: "Forbidden"};
  }
  return path;
}
```

As soon as you set up a program to accept network requests, you have
to start worrying about ((security)). In this case, if we aren't
careful, it is likely that we'll accidentally expose our whole ((file
system)) to the network.

File paths are strings in Node. To map such a string to an actual
file, there is a nontrivial amount of interpretation going on. Paths
may, for example, include `../` to refer to a parent directory. So
one obvious source of problems would be requests for paths like
`/../secret_file`.

{{index "path package", "resolve function", "cwd function", "process object", "403 (HTTP status code)", "sep binding", ["backslash character", "as path separator"], "slash character"}}

To avoid such problems, `urlPath` uses the `resolve` function from the
`path` module, which resolves relative paths. It then verifies that
the result is _below_ the working directory. The `process.cwd`
function (where `cwd` stands for "current working directory") can be
used to find this working directory. The `sep` binding from the
`path` package is the system's path separator—a backslash on Windows
and a forward slash on most other systems. When the path doesn't start
with the base directory, the function throws an error response object,
using the HTTP status code indicating that access to the resource is
forbidden.

{{index "file server example", "Node.js", "GET method", [file, resource]}}

We'll set up the `GET` method to return a list of files when
reading a ((directory)) and to return the file's content when reading
a regular file.

{{index "media type", "Content-Type header", "mime package"}}

One tricky question is what kind of `Content-Type` header we should
set when returning a file's content. Since these files could be
anything, our server can't simply return the same content type for all
of them. ((NPM)) can help us again here. The `mime` package (content
type indicators like `text/plain` are also called _((MIME type))s_)
knows the correct type for a large number of ((file extension))s.

{{index "require function", "npm program"}}

The following `npm` command, in the directory where the server script
lives, installs a specific version of `mime`:

```{lang: null}
$ npm install mime@2.2.0
```

{{index "404 (HTTP status code)", "stat function", [file, resource]}}

When a requested file does not exist, the correct HTTP status code to
return is 404. We'll use the `stat` function, which looks up
information about a file, to find out both whether the file exists
and whether it is a ((directory)).

```{includeCode: ">code/file_server.js"}
const {createReadStream} = require("fs");
const {stat, readdir} = require("fs").promises;
const mime = require("mime");

methods.GET = async function(request) {
  let path = urlPath(request.url);
  let stats;
  try {
    stats = await stat(path);
  } catch (error) {
    if (error.code != "ENOENT") throw error;
    else return {status: 404, body: "File not found"};
  }
  if (stats.isDirectory()) {
    return {body: (await readdir(path)).join("\n")};
  } else {
    return {body: createReadStream(path),
            type: mime.getType(path)};
  }
};
```

{{index "createReadStream function", ["asynchronous programming", "in Node.js"], "error handling", "ENOENT (status code)", "Error type", inheritance}}

Because it has to touch the disk and thus might take a while, `stat`
is asynchronous. Since we're using promises rather than callback
style, it has to be imported from `promises` instead of directly from
`fs`.

When the file does not exist, `stat` will throw an error object with a
`code` property of `"ENOENT"`. These somewhat obscure,
((Unix))-inspired codes are how you recognize error types in Node.

{{index "Stats type", "stat function", "isDirectory method"}}

The `stats` object returned by `stat` tells us a number of things
about a ((file)), such as its size (`size` property) and its
((modification date)) (`mtime` property). Here we are interested in
the question of whether it is a ((directory)) or a regular file, which
the `isDirectory` method tells us.

{{index "readdir function"}}

We use `readdir` to read the array of files in a ((directory)) and
return it to the client. For normal files, we create a readable stream
with `createReadStream` and return that as the body, along with the
content type that the `mime` package gives us for the file's name.

{{index "Node.js", "file server example", "DELETE method", "rmdir function", "unlink function"}}

The code to handle `DELETE` requests is slightly simpler.

```{includeCode: ">code/file_server.js"}
const {rmdir, unlink} = require("fs").promises;

methods.DELETE = async function(request) {
  let path = urlPath(request.url);
  let stats;
  try {
    stats = await stat(path);
  } catch (error) {
    if (error.code != "ENOENT") throw error;
    else return {status: 204};
  }
  if (stats.isDirectory()) await rmdir(path);
  else await unlink(path);
  return {status: 204};
};
```

{{index "204 (HTTP status code)", "body (HTTP)"}}

When an ((HTTP)) ((response)) does not contain any data, the status
code 204 ("no content") can be used to indicate this. Since the
response to deletion doesn't need to transmit any information beyond
whether the operation succeeded, that is a sensible thing to return
here.

{{index idempotence, "error response"}}

You may be wondering why trying to delete a nonexistent file returns a
success status code, rather than an error. When the file that is being
deleted is not there, you could say that the request's objective is
already fulfilled. The ((HTTP)) standard encourages us to make
requests _idempotent_, which means that making the same request
multiple times produces the same result as making it once. In a way,
if you try to delete something that's already gone, the effect you
were trying to do has been achieved—the thing is no longer there.

{{index "file server example", "Node.js", "PUT method"}}

This is the handler for `PUT` requests:

```{includeCode: ">code/file_server.js"}
const {createWriteStream} = require("fs");

function pipeStream(from, to) {
  return new Promise((resolve, reject) => {
    from.on("error", reject);
    to.on("error", reject);
    to.on("finish", resolve);
    from.pipe(to);
  });
}

methods.PUT = async function(request) {
  let path = urlPath(request.url);
  await pipeStream(request, createWriteStream(path));
  return {status: 204};
};
```

{{index overwriting, "204 (HTTP status code)", "error event", "finish event", "createWriteStream function", "pipe method", stream}}

We don't need to check whether the file exists this time—if it does,
we'll just overwrite it. We again use `pipe` to move data from a
readable stream to a writable one, in this case from the request to
the file. But since `pipe` isn't written to return a promise, we have
to write a wrapper, `pipeStream`, that creates a promise around the
outcome of calling `pipe`.

{{index "error event", "finish event"}}

When something goes wrong when opening the file, `createWriteStream`
will still return a stream, but that stream will fire an `"error"`
event. The output stream to the request may also fail, for example if
the network goes down. So we wire up both streams' `"error"` events to
reject the promise. When `pipe` is done, it will close the output
stream, which causes it to fire a `"finish"` event. That's the point
where we can successfully resolve the promise (returning nothing).

{{index download, "file server example", "Node.js"}}

The full script for the server is available at
[_https://eloquentjavascript.net/code/file_server.js_](https://eloquentjavascript.net/code/file_server.js).
You can download that and, after installing its dependencies, run it
with Node to start your own file server. And, of course, you can modify
and extend it to solve this chapter's exercises or to experiment.

{{index "body (HTTP)", "curl program", [HTTP, client], [method, HTTP]}}

The command line tool `curl`, widely available on ((Unix))-like
systems (such as macOS and Linux), can be used to make HTTP
((request))s. The following session briefly tests our server. The `-X`
option is used to set the request's method, and `-d` is used to
include a request body.

```{lang: null}
$ curl http://localhost:8000/file.txt
File not found
$ curl -X PUT -d hello http://localhost:8000/file.txt
$ curl http://localhost:8000/file.txt
hello
$ curl -X DELETE http://localhost:8000/file.txt
$ curl http://localhost:8000/file.txt
File not found
```

The first request for `file.txt` fails since the file does not exist
yet. The `PUT` request creates the file, and behold, the next request
successfully retrieves it. After deleting it with a `DELETE` request,
the file is again missing.

## Summary

{{index "Node.js"}}

Node is a nice, small system that lets us run JavaScript in a
nonbrowser context. It was originally designed for network tasks to
play the role of a _node_ in a network. But it lends itself to all
kinds of scripting tasks, and if writing JavaScript is something you
enjoy, automating tasks with Node works well.

NPM provides packages for everything you can think of (and quite a few
things you'd probably never think of), and it allows you to fetch and
install those packages with the `npm` program. Node comes with a
number of built-in modules, including the `fs` module for working with
the file system and the `http` module for running HTTP servers and
making HTTP requests.

All input and output in Node is done asynchronously, unless you
explicitly use a synchronous variant of a function, such as
`readFileSync`. When calling such asynchronous functions, you provide
callback functions, and Node will call them with an error value and
(if available) a result when it is ready.

## Exercises

### Search tool

{{index grep, "search problem", "search tool (exercise)"}}

On ((Unix)) systems, there is a command line tool called `grep` that
can be used to quickly search files for a ((regular expression)).

Write a Node script that can be run from the ((command line)) and acts
somewhat like `grep`. It treats its first command line argument as a
regular expression and treats any further arguments as files to search. It
should output the names of any file whose content matches the regular
expression.

When that works, extend it so that when one of the arguments is a
((directory)), it searches through all files in that directory and its
subdirectories.

{{index ["asynchronous programming", "in Node.js"], "synchronous programming"}}

Use asynchronous or synchronous file system functions as you see fit.
Setting things up so that multiple asynchronous actions are requested
at the same time might speed things up a little, but not a huge
amount, since most file systems can read only one thing at a time.

{{hint

{{index "RegExp class", "search tool (exercise)"}}

Your first command line argument, the ((regular expression)), can be
found in `process.argv[2]`. The input files come after that. You can
use the `RegExp` constructor to go from a string to a regular
expression object.

{{index "readFileSync function"}}

Doing this synchronously, with `readFileSync`, is more
straightforward, but if you use `fs.promises` again to get
promise-returning functions and write an `async` function, the code
looks similar.

{{index "stat function", "statSync function", "isDirectory method"}}

To figure out whether something is a directory, you can again use
`stat` (or `statSync`) and the stats object's `isDirectory` method.

{{index "readdir function", "readdirSync function"}}

Exploring a directory is a branching process. You can do it either
by using a recursive function or by keeping an array of work (files that
still need to be explored). To find the files in a directory, you can
call `readdir` or `readdirSync`. The strange
capitalization—Node's file system function naming is loosely based on
standard Unix functions, such as `readdir`, that are all lowercase,
but then it adds `Sync` with a capital letter.

To go from a filename read with `readdir` to a full path name, you
have to combine it with the name of the directory, putting a ((slash
character)) (`/`) between them.

hint}}

### Directory creation

{{index "file server example", "directory creation (exercise)", "rmdir function"}}

Though the `DELETE` method in our file server is able to delete
directories (using `rmdir`), the server currently does not provide any
way to _create_ a ((directory)).

{{index "MKCOL method", "mkdir function"}}

Add support for the `MKCOL` method ("make collection"), which should create
a directory by calling `mkdir` from the `fs` module. `MKCOL` is not a
widely used HTTP method, but it does exist for this same purpose in
the _((WebDAV))_ standard, which specifies a set of conventions on top
of ((HTTP)) that make it suitable for creating documents.

```{hidden: true, includeCode: ">code/file_server.js"}
const {mkdir} = require("fs").promises;

methods.MKCOL = async function(request) {
  let path = urlPath(request.url);
  let stats;
  try {
    stats = await stat(path);
  } catch (error) {
    if (error.code != "ENOENT") throw error;
    await mkdir(path);
    return {status: 204};
  }
  if (stats.isDirectory()) return {status: 204};
  else return {status: 400, body: "Not a directory"};
};
```

{{hint

{{index "directory creation (exercise)", "file server example", "MKCOL method", "mkdir function", idempotency, "400 (HTTP status code)"}}

You can use the function that implements the `DELETE` method as a
blueprint for the `MKCOL` method. When no file is found, try to create
a directory with `mkdir`. When a directory exists at that path, you
can return a 204 response so that directory creation requests are
idempotent. If a nondirectory file exists here, return an error code.
Code 400 ("bad request") would be appropriate.

hint}}

### A public space on the web

{{index "public space (exercise)", "file server example", "Content-Type header", website}}

Since the file server serves up any kind of file and even includes the
right `Content-Type` header, you can use it to serve a website. Since
it allows everybody to delete and replace files, it would be an
interesting kind of website: one that can be modified, improved, and
vandalized by everybody who takes the time to create the right HTTP
request.

Write a basic ((HTML)) page that includes a simple JavaScript file.
Put the files in a directory served by the file server and open them
in your browser.

Next, as an advanced exercise or even a ((weekend project)), combine
all the knowledge you gained from this book to build a more
user-friendly interface for modifying the website—from _inside_ the
website.

Use an HTML ((form)) to edit the content of the files that make up the
website, allowing the user to update them on the server by using HTTP
requests, as described in [Chapter ?](http).

Start by making only a single file editable. Then make it so that the
user can select which file to edit. Use the fact that our file server
returns lists of files when reading a directory.

{{index overwriting}}

Don't work directly in the code exposed by the file server since if
you make a mistake, you are likely to damage the files there. Instead,
keep your work outside of the publicly accessible directory and copy
it there when testing.

{{hint

{{index "file server example", "textarea (HTML tag)", "fetch function", "relative path", "public space (exercise)"}}

You can create a `<textarea>` element to hold the content of the file
that is being edited. A `GET` request, using `fetch`, can retrieve the
current content of the file. You can use relative URLs like
_index.html_, instead of
[_http://localhost:8000/index.html_](http://localhost:8000/index.html),
to refer to files on the same server as the running script.

{{index "form (HTML tag)", "submit event", "PUT method"}}

Then, when the user clicks a button (you can use a `<form>` element
and `"submit"` event), make a `PUT` request to the same URL, with the
content of the `<textarea>` as request body, to save the file.

{{index "select (HTML tag)", "option (HTML tag)", "change event"}}

You can then add a `<select>` element that contains all the files in
the server's top ((directory)) by adding `<option>` elements
containing the lines returned by a `GET` request to the URL `/`. When
the user selects another file (a `"change"` event on the field), the
script must fetch and display that file. When saving a file, use the
currently selected filename.

hint}}
