# Unicode en D

Unicode es un estándar global para representar texto en ordenadores. D es
totalmente compatible con Unicode, tanto el propio lenguaje como la librería
estándar.

## Qué y por qué

Los ordenadores, en su más bajo nivel, no tienen noción alguna de qué es texto
ya que estos sólo funcionan mediante números. Como resultado, el código de los
ordenadores necesita métodos para coger texto y transformarlo desde y hacia
una representación binaria. El método usado en esta transformación se llama
**esquema de codificación**, y Unicode es uno de esos esquemas.

Para ver la representación numérica que hay debajo de las cadenas de caracteres
en este ejemplo basta con ejecutar el código.

Unicode es único en su diseño ya que este permite representar todos los
idiomas del mundo usando el mismo esquema de codificación. Antes de Unicode
los ordenadores hechos por diferentes empresas o distribuidos en diferentes
áreas tenían dificultades para comunicarse ya que no todos eran compatibles
con los diferentes esquemas de codificación usados. Incluso en algunos de
ellos era totalmente imposible ver texto creado con otro ordenador.

Hay más información acerca de Unicode en el artículo de la Wikipedia
referenciado en la sección “En produndidad” de este artículo.

## Cómo

Unicode ha solucionado muchos de esos problemas y está soportado en todos los
ordenadores modernos. D ha aprendido de los errores de los lenguajes de
programación más antiguos, por lo que **todas** las cadenas de caracteres
en D son Unicode, mientras que en lenguajes como C y C++ no son más que arrays
de bytes.

En D los tipos de datos `string`, `wstring` y `dstring` son cadenas de
caracteres codificadas mediante UTF-8, UTF-16 y UTF-32 respectivamente,
así como el tipo de dato de cada caracter es `char`, `wchar` y `dchar`.

De acuerdo con la especificación, es un error guardar datos que no sean
Unicode en las cadenas de caracteres. Hay que tener en cuenta que los programas
con cadenas de caracteres que no estén codificadas de forma apropiada fallarán
de diferentes formas.

Para guardar otras codificaciones de caracteres, o para conseguir el mismo
comportamiento que hay en C/C++, se pueden usar los tipos de datos `ubyte[]`
o `char*`.

## Cadenas de caracteres en los algoritmos que trabajan sobre rangos

_NOTA:_ [_Algoritmos sobre rangos_](gems/range-algorithms) _es una lectura
recomendada de para comprender mejor las implicaciones de esta sección._

Existen ciertas precauciones importantes a tener en cuenta cuando se trabaja
con Unicode en D.

Primero, como una característica desarrollada por conveniencia, cuando se itera
sobre una cadena de caracteres usando funciones que se aplican sobre rangos,
Phobos, la librería estándar, codifica cada elemento de un `string` y de un
`wstring` en puntos de código de UTF-32. Esta práctica, conocida como
**decodicación automática** (*auto decoding* en inglés), significa que la
siguiente expresión no lanzará ningún error:

```
static assert(is(typeof(utf8.front) == dchar));
```

Este comportamiento tiene muchas implicaciones. La principal, que confunde
a muchos desarrolladores, es que `std.traits.hasLength!(string)` es igual a
`False`. Esto es debido a que, en términos del API de rangos,
la propiedad `length` de las cadenas de caracteres (`string`s) devuelve
**el número de elementos de dicha cadena de caracteres**, y no el número de
elementos del rango sobre el que iteran las funciones.

En el ejemplo se puede ver por qué estas dos cosas podrían no ser siempre
iguales. Como tal, los algoritmos sobre rangos en Phobos actúan como si
las cadenas de caracteres (`string`s) no tuvieran información sobre su
longitud.

En la sección “En profundidad” hay más detalles técnicos sobre la
decodificación automática y qué implicaciones tiene en los programas.

### En profundidad

- [Unicode on Wikipedia](https://en.wikipedia.org/wiki/Unicode)
- [Basic Unicode Functions in Phobos](https://dlang.org/phobos/std_uni.html)
- [Tools for Decoding and Encoding UTF in Phobos](https://dlang.org/phobos/std_utf.html)
- [An in Depth Look at Auto Decoding](https://jackstouffer.com/blog/d_auto_decoding_and_you.html)
- [An in Depth Essay on Benefits of Using UTF-8](http://utf8everywhere.org/)

## {SourceCode}

```d
import std.range.primitives : empty,
    front, popFront;
import std.stdio : write, writeln;

void main() {
    string utf8 = "ñ å ø ∑ 😦";
    wstring utf16 = "ñ å ø ∑ 😦";
    dstring utf32 = "ñ å ø ∑ 😦";

    writeln("Longitud utf8: ", utf8.length);
    writeln("Longitud utf16: ", utf16.length);
    writeln("Longitud utf32: ", utf32.length);

    foreach (item; utf8) {
        auto c = cast(ubyte) item;
        write(c, " ");
    }
    writeln();

    // Debido a que el elemento especificado es
    // de tipo dchar, se mira más adelante para
    // codificar la cadena en puntos de código
    // en UTF-32. Si no son cadenas de
    // caracteres, es suficiente un `cast`.
    foreach (dchar item; utf16) {
        auto c = cast(ushort) item;
        write(c, " ");
    }
    writeln();

    // El resultado de la decodificación
    // automática.
    static assert(
        is(typeof(utf8[0]) == immutable(char))
    );
    static assert(
        is(typeof(utf8.front) == dchar)
    );
}
```
