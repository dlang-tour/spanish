# Importaciones y módulos

{{#img-right}}turtle.svg{{/img-right}}

Una de las decisiones principales en el diseño de D fue que el lenguaje debía
ser consistente para evitar casos extremos en el lenguaje. Esto se llama
[_turtles all the way down_](https://en.wikipedia.org/wiki/Turtles_all_the_way_down).
Un buen ejemplo de esta consistencia son las importaciones (`import`s).

## Importaciones

Para hacer un simple programa del tipo _“hola, mundo”_ en D se necesitan
importaciones. La sentencia `import` hace que todas las funciones y todos los
tipos de datos del módulo indicado estén disponibles donde se realiza la
importación.

### Las tortugas empiezan a caer

Una sentencia `import` **no necesita** estar al principio en un archivo de
fuentes, sino que puede estar localmente dentro de funciones o cualquier otro
ámbito. En los próximos capítulos se verá que esto se aplica a casi todos los conceptos
de D. El lenguaje no impone ninguna restricción arbitraria sobre los
desarrolladores.

### Importaciones selectivas

La librería estándar, llamada [Phobos](https://dlang.org/phobos/), se encuentra
debajo del paquete `std` y sus módulos se referencia mendiante la sentencia
`import std.MODULE`.

La sentencia `import` se puede usar también de forma selectiva para importar
ciertos símbolos de un módulo:

    import std.stdio : writeln, writefln;

Las importaciones selectivas se pueden usar para mejorar la legibilidad
haciendo obvio de dónde vienen los símbolos. Además, se pueden usar para
evitar conflictos en símbolos con el mismo nombre que vengan de módulos
diferentes.

### Las importaciones corresponden a directorios y archivos

El sistema de módulos de D, al contrario que en otros sistemas, se basa
enteramente en archivos. Por ejemplo, `my.cat` siempre se refiere al archivo
`cat.d` dentro del directorio `my/`. Es necesario que el directorio `my`
esté en el dictorio de trabajo o en un directorio especificado de forma
explícita en la lista de directorios a importar (`-I`). Finalmente, para
facilitar dividir módulos muy grandes en archivos más pequeños, en lugar de
tener el archivo `cat.d`, se puede usar el directorio `cat/`. De esta forma,
el compilador de D tratará de cargar el archivo `my/cat/package.d` en lugar
de `my/cat.d`.

The convention (but not a hard rule) for `package.d` files is to publicly import
all other modules in the same folder.

## {SourceCode}

```d
void main() {
    import std.stdio;
    // o `import std.stdio : writeln;`
    writeln("¡Hola, mundo!");
}
```
