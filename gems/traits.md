# Rasgos (*traits*)

Una de las características más potentes de D es su sistema de evaluación de
funciones en tiempo de compilación (CTFE). Gracias a este sistema y a la
introspección, se pueden tanto escribir programas genéricos como conseguir
grandes optimizaciones.

## Contratos explícitos (*explicit contracts*)

Los rasgos en D (*traits* en inglés) permiten especificar de forma explícita
qué tipos de parámetros se aceptan dentro de las funciones. Por ejemplo,
la función `splitIntoWords` funciona con cualquier tipo de dato que sea una
cadena de caracteres (`string`):

```d
S[] splitIntoWord(S)(S input)
  if (isSomeString!S)
```

Esto también se aplica a los parámetros de las plantillas, con lo que la
función `myWrapper` se puede asegurar de que el símbolo que se le pasa como
argumento es una función que se puede llamar:

```d
void myWrapper(alias f)
  if (isCallable!f)
```

Se puede analizar, como ejemplo sencillo,
[`commonPrefix`](https://dlang.org/phobos/std_algorithm_searching.html#.commonPrefix),
del módulo `std.algorithm.searching`, que devuelve el prefijo común de dos rangos:

```d
auto commonPrefix(alias pred = "a == b", R1, R2)(R1 r1, R2 r2)
  if (isForwardRange!R1 &&
      isInputRange!R2 &&
      is(typeof(binaryFun!pred(r1.front, r2.front)))) &&
      !isNarrowString!R1)
```

Esto significa que esta función sólo se puede llamar y sólo compila si:

- `r1` se puede guardar (se garantiza mediante `isForwardRange`).
- `r2` sea iterable (se garantiza mediante `isInputRange`).
- `pred` se pueda llamar con elementos de tipo `r1` y `r2`.
- `r1` no sea una cadena _estrecha_ (_narrow string_ en inglés) (`char[]`,`string`, `wchar` or `wstring`). Esto se hace por simplicidad, ya que si no fuese así se necesitaría decodificación adicional.

### Especialización

Muchas API pretenden ser de propósito general. Sin embargo, estas no quieren
que haya sobrecarga extra en tiempo de ejecución por culpa de esta
generalización. Gracias a la potencia de la introspección y al CTFE, es posible
especializar un método en tiempo de compilación para conseguir el mejor
rendimiento dados sus parámetros de entrada.

Un problema común es que, al contrario que con los arrays, podría no conocerse
la longitud exacta de una ristra de datos o una lista antes de recorrerla.
Aquí tenemos una simple implementación del método `walkLength` del módulo
`std.range` generalizado para cualquier tipo de dato que sea iterable:

```d
static if (hasMember!(r, "length"))
    return r.length; // O(1)
else
    return r.walkLength; // O(n)
```

#### La función `commonPrefix`

El uso de la introspección en tiempo de compilación es ubicua en Phobos. Por
ejemplo, la función `commonPrefix` diferencia entre rangos de tipo
`RandomAccessRange` y rangos donde la iteración es lineal. Eso es así porque
en un `RandomAccessRange` se puede saltar entre posiciones con lo que se mejora
la velocidad del algoritmo.

#### Más magia CTFE

El módulo [std.traits](https://dlang.org/phobos/std_traits.html) engloba la
mayoría de la funcionalidad de los [*traits*](https://dlang.org/spec/traits.html)
de D, excepto para algunas construcciones de tipo `compiles`, que llevarían a
un error de compilación inmediato:

```d
__traits(compiles, obvious error - $%42); // false
```

#### Palabras reservadas especiales

Adicionalmente, con el propósito de depurar código, D proporciona un par de
palabras reservadas especiales:

```d
void test(string file = __FILE__, size_t line = __LINE__, string mod = __MODULE__,
          string func = __FUNCTION__, string pretty = __PRETTY_FUNCTION__)
{
    writefln("file: '%s', line: '%s', module: '%s',\nfunction: '%s', pretty function: '%s'",
             file, line, mod, func, pretty);
}
```

Gracias a la funcionalidad de D de poder evaluar programas desde la línea de
comandos, no se necesita usar la función `time`, ya que se puede usar CTFE.

```d
rdmd --force --eval='pragma(msg, __TIMESTAMP__);'
```

## En profundidad

- [std.range.primitives](https://dlang.org/phobos/std_range_primitives.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [std.meta](https://dlang.org/phobos/std_meta.html)
- [Specification on Traits in D](https://dlang.org/spec/traits.html)

## {SourceCode}

```d
import std.functional : binaryFun;
import std.range.primitives : empty, front,
    popFront,
    isInputRange,
    isForwardRange,
    isRandomAccessRange,
    hasSlicing,
    hasLength;
import std.stdio : writeln;
import std.traits : isNarrowString;

/**
Devuelve el prefijo común a dos rangos sin
el caso especial de auto-decodificación.

Params:
    pred = Predicado para comparación común.
    r1 = Un rango de tipo 'forward range'.
    r2 = Un rango de tipo 'input range'.

Returns:
Una parte de r1 que contiene los caracteres
por los que empiezan ambos rangos.
 */
auto commonPrefix(alias pred = "a == b", R1, R2)
                 (R1 r1, R2 r2)
if (isForwardRange!R1 && isInputRange!R2 &&
    !isNarrowString!R1 &&
    is(typeof(binaryFun!pred(r1.front,
                             r2.front))))
{
    import std.algorithm.comparison : min;
    static if (isRandomAccessRange!R1 &&
               isRandomAccessRange!R2 &&
               hasLength!R1 && hasLength!R2 &&
               hasSlicing!R1) {
        immutable limit = min(r1.length,
                              r2.length);
        foreach (i; 0 .. limit) {
            if (!binaryFun!pred(r1[i], r2[i])) {
                return r1[0 .. i];
            }
        }
        return r1[0 .. limit];
    } else {
        import std.range : takeExactly;
        auto result = r1.save;
        size_t i = 0;
        for (;
             !r1.empty && !r2.empty &&
             binaryFun!pred(r1.front, r2.front);
             ++i, r1.popFront(), r2.popFront())
        {}
        return takeExactly(result, i);
    }
}

void main() {
    // Imprime: "hola, "
    writeln(commonPrefix("hola, mundo"d,
                         "hola, ahí"d));
}
```
