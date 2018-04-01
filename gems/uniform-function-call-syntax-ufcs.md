# Sintaxis uniforme de llamada a funciones (*Uniform Function Call Syntax*, UFCS)

**UFCS** es una característica clave en D que habilita la reusabilidad y la
escalabilidad del código a través de encapsulación bien definida.

UFCS permite que cualquier llamada a una función libre, `fun(a)`, pueda ser
escrita como una llamada a un método, `a.fun()`.

Si el compilador ve la llamada `a.fun()` y el tipo de `a` no tiene un miembro
llamado `fun()`, trata de encontrar una función global cuyo primer parámetro
sea del mismo tipo que `a`.

Esta característica es especialmente útil para encadenar llamadas complejas
a funciones. En lugar de escribir:

    foo(bar(a))

Es posible escribir:

    a.bar().foo()

Además, en D no es necesario usar los paréntesis para llamar a funciones
que no tienen argumentos, lo que significa que **cualquier** función se
puede usar como una propiedad:

    import std.uni : toLower;
    "D mola".toLower; // "d mola"

UFCS es especialmente importante cuando se trabaja con rangos (*ranges* en
inglés) donde varios algoritmos se encadenan para realizar operaciones
complejas pero manteniendo la legibilidad y manejabilidad del código.

    import std.algorithm : group;
    import std.range : chain, retro, front, retro;
    [1, 2].chain([3, 4]).retro; // 4, 3, 2, 1
    [1, 1, 2, 2, 2].group.dropOne.front; // tuple(2, 3u)

### En profundidad

- [UFCS in _Programming in D_](http://ddili.org/ders/d.en/ufcs.html)
- [_Uniform Function Call Syntax_](http://www.drdobbs.com/cpp/uniform-function-call-syntax/232700394) by Walter Bright
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;
import std.algorithm.iteration : filter;
import std.range : iota;

void main() {
    "Hola, %s".writefln("mundo");

    10.iota // devuelve números de 0 a 9
      // filtra por números pares
      .filter!(a => a % 2 == 0)
      .writeln(); // los escribe en 'stdout'

    // Estilo tradicional:
    writeln(filter!(a => a % 2 == 0)
                   (iota(10)));
}
```
