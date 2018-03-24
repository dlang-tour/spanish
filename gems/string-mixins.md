# *Mixins* de cadenas de caracteres

La expresión `mixin` toma un cadena de caracteres arbitraria y la compila
para generar instrucciones en consecuencia. Es un mecanismo que se ejecuta
puramente en tiempo de compilación y sólo funciona con cadenas de caracteres
que estén disponibles durante esta fase. Comparar esta característica con
la función `eval` de Javascript no sería muy justo.

    mixin("int b = 5");
    assert(b == 5); // compila bien

La expresión `mixin` también funciona con cadenas de caracteres construidas
de forma dinámica, siempre y cuando la información disponible para crear
dicha cadena no dependa de valores en tiempo de ejecución.

Los _mixins_, junto con **CTFE** (se verá en la sección siguiente), permiten
escribir librerías impresionantes como [Pegged](https://github.com/PhilippeSigaud/Pegged),
que genera analizadores de gramáticas a partir de gramáticas definidas como
cadenas de caracteres en el código fuente.

### En profundidad

- [Mixins in D](https://dlang.org/spec/template-mixin.html)

## {SourceCode}

```d
import std.stdio : writeln;

auto calculate(string op, T)(T lhs, T rhs) {
    return mixin("lhs " ~ op ~ " rhs");
}

void main() {
    // Un nuevo enfoque del "Hola, mundo".
    mixin(`writeln("Hello World");`);

    // La operación a ejecutar se pasa como
    // un parámetro de la plantilla.
    writeln("5 + 12 = ", calculate!"+"(5,12));
    writeln("10 - 8 = ", calculate!"-"(10,8));
    writeln("8 * 8 = ", calculate!"*"(8,8));
    writeln("100 / 5 = ", calculate!"/"(100,5));
}
```
