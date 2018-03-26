# Programación funcional

D pone énfasis en la **programación funcional** proporcionando soporte de
primera clase para el desarrollo siguiendo un estilo funcional.

En D una función se puede declarar como *pura* mediante la palabra reservada
`pure`. Esto implica que dados los mismos parámetros de entrada, la salida
**siempre** será la misma. Las funciones puras no puede acceder ni cambiar
ningún estado global que sea modificable, por lo que desde estas funciones
sólo se permite llamar a otras funciones puras.

    int add(int lhs, int rhs) pure {
        impureFunction(); // ERROR: no se puede llamar a una función impura.
        return lhs + rhs;
    }

Esta variante de la función `add` se llama **función pura fuerte**
(_strongly pure function_ en inglés) ya que devuelve un resultado dependiendo
sólo de sus parámetros de entrada sin modificarlos. D también permite
definir **funciones puras débiles** (_weakly pure functions_ en inglés) que
pueden tener parámetros modificables.

    void add(ref int result, int lhs, int rhs) pure {
        result = lhs + rhs;
    }

Estas funciones todavía se consideran puras ya que no pueden acceder ni
modificar ningún estado global. Sólo los parámetros que se le pasan se pueden
modificar.

Debido a las restricciones impuestas por `pure`, las funciones puras son
ideales para trabajar en entornos multihilo previniendo así condiciones de
carrera **por diseño**. Además, las funciones puras se pueden poner en
memorias caché permitiendo así al compilador un amplio rango de optimizaciones.

El compilador infiere automáticamente el atributo `pure` para funciones
definidas mediante plantillas y para funciones cuyo valor de retorno se marca
como `auto` donde sea aplicable (esto también es válido para código marcado
como `@safe`, `nothrow` y/o `@nogc`).

### En profundidad

- [Functional DLang Garden](https://garden.dlang.io/)

## {SourceCode}

```d
import std.bigint : BigInt;

/**
 * Calcula la potencia de una base
 * dado un exponente.
 *
 * Returns:
 *     Resultado de la potencia como
 *     un entero de tamaño arbitrario.
 */
BigInt bigPow(uint base, uint power) pure {
    BigInt result = 1;

    foreach (_; 0 .. power)
        result *= base;

    return result;
}

void main() {
    import std.datetime : benchmark, to;
    import std.functional : memoize,
        reverseArgs;
    import std.stdio : writefln, writeln;

    // La función `memoize` cachea el
    // resultado de la función dependiendo
    // de los parámetros de entrada. Las
    // funciones puras son buenas para esto.
    alias fastBigPow = memoize!(bigPow);

    void test() {
        writefln(".uintLength() = %s ",
               fastBigPow(5, 10000).uintLength);
    }

    foreach (i; 0 .. 10)
        benchmark!test(1)[0]
            .to!("msecs", double)
            .reverseArgs!writefln
                (" took: %.2f miliseconds");
}
```
