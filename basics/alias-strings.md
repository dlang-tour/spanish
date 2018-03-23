# Alias y cadenas de caracteres (*strings*)

Ahora que sabemos lo que son los arrays, que hemos entrado en contacto con
`immutable` y que hemos echado un vistazo rápido a los tipos de datos básicos,
es momento de presentar dos nuevas construcciones en una sola línea:

    alias string = immutable(char)[];

El término `string` se define mediante una sentencia `alias` que lo declara
como un *slice* de caracteres inmutables (`immutable(char)`). Esto significa
que una vez que un `string` ha sido construido, su contenido nunca cambia.
Y esta es la segunda introducción: ¡bienvenidas, `string`s en UTF-8!

Debido a su inmutabilidad, las `string`s se puede compartir perfectamente entre
varios hilos de ejecución. Como un `string` es un *slice*, se pueden coger
ciertas partes de la misma sin necesidad de reservar memoria. La función
[`std.algorithm.splitter`](https://dlang.org/phobos/std_algorithm_iteration.html#.splitter)
de la librería estándar, por ejemplo, parte una cadena por cada retorno de
carro sin hacer ninguna reserva de memoria.

Además de tener cadenas codificadas mediante UTF-8, existen otros dos tipos más:

    alias wstring = immutable(wchar)[]; // UTF-16
    alias dstring = immutable(dchar)[]; // UTF-32

Estas variantes se convierten fácilmente unas entre otras usando el método
`to` del paquete `std.conv`:

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Cadenas de caracteres Unicode

Una cadena de caracteres plana (un `string`) se define como un array de
[*code units*](http://unicode.org/glossary/#code_unit) (unidades de código)
de Unicode, cada uno de un tamaño de 8 bits (un byte). Todas las operaciones
aplicables a los arrays se pueden usar sobre las cadenas, ya que trabajan al
nivel de las *code units* y no a nivel de carácter. Por otro lado, la librería
estándar interpreta las cadena como secuencias de
[*code points*](http://unicode.org/glossary/#code_point)
(puntos de código). Además existe otra opción que trata las cadenas como
secuencias de
[*graphemes*](http://unicode.org/glossary/#grapheme) (grafemas)
indicándolo de forma explícita. Es la función
[`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html).

Este pequeño ejemplo ilustra las diferencias en la interpretación:

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

En el ejemplo, la longitud real del array `s` es 3, ya que contiene 3 unidades
de código (*code units*): `0x41`, `0x03` y `0x08`. Estos caracteres combinados
definen un único punto de código (*code point*), ya que combina el caracter
principal con el acento diacrítico, por lo que la función
[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)
(función de la librería estándar que calcula la longitud de un rango) cuenta
dos puntos de código (*code points*) en total. Finalmente, la función
`byGrapheme` realiza costosos cálculos para reconocer que esos dos puntos de
código se combinan en único caracter a mostrar.

El procesado correcto de cadenas Unicode puede ser bastante complejo, por eso
la mayor parte del tiempo los desarrolladores que usen D pueden simplemente
considerar las variables de tipo `string` como arrays de bytes mágicos y
confiar en que los algoritmos de la librería estándar hagan su trabajo
correctamente. Si se desea iterar una cadena por unidad de código, se puede
usar la función [`byCodeUnit`](http://dlang.org/phobos/std_utf.html#.byCodeUnit).

La decodificación de caracteres Unicode en D se explica con más detalle en
el [capítulo de Unicode](gems/unicode).

### Cadenas de caracteres multilínea

Las cadenas de caracteres en D siempre se pueden poner en varias líneas:

    string multiline = "
    Esto
    podría ser un
    documento grande
    ";

Cuando aparecen las comillas en un documento, se pueden usar tanto las cadenas
*wysiwyg* (*what you see is what you get*, lo que ves es lo que obtienes) como
las [cadenas *heredoc*](http://dlang.org/spec/lex.html#delimited_strings).

### Cadenas *wysiwyg*

También es posible usar *raw strings* (cadenas sin procesar) para minimar la
laboriosa tarea de *escapar* (*escape*) símbolos reservados. Las cadenas sin
procesar pueden ser declaradas o bien usando acentos graves —*backticks* en
inglés— (`` ` ... ` ``) o bien usando el prefijo `r` —de *raw*— (`r" ... "`).

    string raw  =  `raw "string"`; // cadena sin procesar
    string raw2 = r"raw `string`"; // cadena sin procesar

D proporciona incluso más formas de representar cadenas. No dudes en
[explorarlas](https://dlang.org/spec/lex.html#string_literals).

### En profundidad

- [Unicode gem](gems/unicode)
- [Characters in _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [Strings in _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF en-/decoding algorithms
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode algorithms
- [String Literals in the D spec](http://dlang.org/spec/lex.html#string_literals)

## {SourceCode}

```d
import std.stdio : writeln, writefln;
import std.range : walkLength;
import std.uni : byGrapheme;
import std.string : format;

void main() {
    // `format` genera una cadena usando
    // sintaxis similar a la de `printf`.
    // D permite la gestión nativa de cadenas
    // en formato UTF.
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("Mi cadena: ", str);
    writeln("Longitud del array (contador de"
        ~ " unicades de código) del string: ",
        str.length);
    writeln("Longitud del rango (contador de "
        ~ "puntos de código) del string: ",
        str.walkLength);
    writeln("Longitud de caracteres (contador"
        ~ " de grafemas) del string: ",
        str.byGrapheme.walkLength);

    // Las cadenas son arrays normales, por
    // lo que cualquier operación que funcione
    // en arrays, funciona también aquí.
    import std.array : replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm : endsWith;
    writefln("¿Termina %s en 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv : to;
    // Convertir a UTF-32
    dstring dstr = to!dstring(str);
    // lo que, evidentemente, imprime lo mismo
    writeln("Mi 'dstring': ", dstr);
}
```
