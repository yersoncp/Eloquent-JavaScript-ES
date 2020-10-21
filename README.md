# Traducción al español de Eloquent JavaScript

En este repositorio haremos la traducción del libro Eloquent JavaScript en español.
Por favor revisa las instrucciones para contribuir y las guías de contribución.

## Instrucciones para contribuir

1. Has un fork de este repositorio
2. Revisa los **Issues** para que veas que capítulos están en progreso. Lo más recomendables es tomar un capítulo que nadie esté trabajando
3. Pon como un Issue que capítulo vas a traducir
4. Cuando lleves un porcentaje considerable del capítulo, has un PR para integrarlo y crear nuevas versiones de los libros
5. Un revisor verificará el texto y hará comentarios o aprobará el PR

## Guía de contribución

1. No uses un traductor automático.
2. Cuando un término se use comúnmente en inglés dentro del entorno de la programación, déjalo sin traducir pero usa cursiva la primera vez que se mencione.
3. Intenta usar español neutro. No uses regionalismos de tu país, queremos que esta sea una traducción lo más entendible para todos los hablantes de español.
4. Sigue las reglas de ortografía y gramática.
5. Si tienes dudas acerca de cómo traducir algo, levanta un Issue para que cooperemos.

## Roles

Aunque la mayoría de los desarrolladores que sabemos inglés podemos traducir el libro, creo que necesitamos por lo menos 2 personas que levanten la mano como:

1. Revisores. Se encargarán de verificar los PR de las nuevas traducciones y aprobar o hacer comentarios sobre el PR para continuar.
2. Editores. Revisarán la versión final del texto para unificarla en estilo y revisar que no se hayan cometido errores graves.

Si tienes tiempo disponible a la semana para hacer esto, levanta un Issue y te daremos acceso de escritura a este repo.

## Otro tipo de ayuda

Es probable que necesitemos alguien con conocimientos en edición de imágenes para las imágenes ilustrativas y la portada. Si puedes apoyarnos levanta un Issue.

Lo que sigue es el README del libro en inglés.

## Eloquent JavaScript

These are the sources used to build the third edition of Eloquent
JavaScript (https://eloquentjavascript.net).

Feedback welcome, in the form of issues and pull requests.

## Building

This builds the HTML output in `html/`, where `make` is GNU make:

    npm install
    make html

To build the PDF file (don't bother trying this unless you really need
it, since this list has probably bitrotted again and getting all this
set up is a pain):

    apt-get install texlive texlive-xetex fonts-inconsolata fonts-symbola texlive-lang-chinese inkscape
    make book.pdf

## Translating

Translations are very much welcome. The license this book is published
under allows non-commercial derivations, which includes open
translations. If you do one, let me know, and I'll add a link to the
website.

A note of caution though: This text consists of about 130 000 words,
the paper book is 400 pages. That's a lot of text, which will take a
lot of time to translate.

If that doesn't scare you off, the recommended way to go about a
translation is:

 - Fork this repository on GitHub.

 - Create an issue on the repository describing your plan to translate.

 - Translate the `.md` files in your fork. These are
   [CommonMark](https://commonmark.org/) formatted, with a few
   extensions. You may consider omitting the index terms (indicated
   with double parentheses and `{{index ...}}` syntax) from your
   translation, since that's mostly relevant for print output.

 - Publish somewhere online or ask me to host the result.

Doing this in public, and creating an issue that links to your work,
helps avoid wasted effort, where multiple people start a translation
to the same language (and possibly never finish it). (Since
translations have to retain the license, it is okay to pick up someone
else's translation and continue it, even when they have vanished from
the internet.)
