# Rangos

Si el compilador de D encuentra un bucle `foreach`

```
foreach (element; range) {
    // Cuerpo del bucle
}
```

este es reescrito internamente de forma similar a lo siguiente:

```
for (auto __rangeCopy = range;
     !__rangeCopy.empty;
     __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // Cuerpo del bucle
}
```

Se llama **rango** (*range* en inglés) a cualquier objeto que cumpla con la
siguiente interfaz:

```
    interface InputRange(E) {
        bool empty();
        E front();
        void popFront();
    }
```

Más concretamente esta interfaz es un `InputRange` y es gracias a ella por
la que los elementos de los objetos de este tipo pueden ser iterados.

En el ejemplo de la derecha se puede ver la implementación y el
uso de un rango de este tipo (*input range*).

## Pereza (*laziness*)

Los rangos son ***perezosos*** (*lazy* en inglés), lo que significa que estos no
son evaluados hasta que no es requerido. Es por esto por lo que se puede coger
un número finito de elementos de un rango infinito:

```d
42.repeat.take(3).writeln; // [42, 42, 42]
```

## Tipos de datos por valor y por referencia

Si el objeto de tipo rango es un valor, el rango se copia y sólo esta copia
será consumida en la iteración:

```d
auto r = 5.iota;
r.drop(5).writeln; // []
r.writeln; // [0, 1, 2, 3, 4]
```

Si el objeto de tipo rango es una referencia (por ejemplo una clase declarada
mediante `class` o un [`std.range.refRange`](https://dlang.org/phobos/std_range.html#refRange)),
los valores del rango serán consumidos en la iteración y no podrán ser
restablecidos.

```d
auto r = 5.iota;
auto r2 = refRange(&r);
r2.drop(5).writeln; // []
r2.writeln; // []
```

### Los `InputRanges` que se pueden copiar son `ForwardRanges`

Muchos de los rangos usados en la librería estándar son estructuras (`struct`),
por lo que los bucles `foreach` son generalmente no destructivos, aunque esto
no se garantiza. Si esta garantía es importante, se puede usar una
especialización de los `InputRange`: rangos con el método `.save`, también
conocidos como `ForwardRange`:

```
interface ForwardRange(E) : InputRange!E {
    typeof(this) save();
}
```

```d
// por valor (estructuras)
auto r = 5.iota;
auto r2 = refRange(&r);
r2.save.drop(5).writeln; // []
r2.writeln; // [0, 1, 2, 3, 4]
```

### Los `ForwardRanges` se pueden extender para ser bidireccionales y con acceso aleatorio a elementos

Hay dos extensiones de los `ForwardRange`: (1) un rango bidireccional y (2) un
rango con acceso aleatorio a sus elementos.

Un rango bidireccional permite la iteración desde el final:

```d
interface BidirectionalRange(E) : ForwardRange!E {
     E back();
     void popBack();
}
```

```d
5.iota.retro.writeln; // [4, 3, 2, 1, 0]
```

Un rango con acceso aleatorio a elementos tiene una longitud conocida,
`length`, y se puede acceder a cada elemento de forma directa:

```d
interface RandomAccessRange(E) : ForwardRange!E {
     E opIndex(size_t i);
     size_t length();
}
```

Los rangos con acceso aleatorio a elementos más famosos son los arrays de D.

```d
auto r = [4, 5, 6];
r[1].writeln; // 5
```

### Algoritmos con acceso *perezoso* (*lazy*) a rangos

Las funciones en [`std.range`](http://dlang.org/phobos/std_range.html) y
[`std.algorithm`](http://dlang.org/phobos/std_algorithm.html) proporcionan
bloques de construcción que hacen uso de esta interfaz de rangos. Los rangos
permiten componer complejos algoritmos detrás de objetos sobre los que se
itera con facilidad. Además, los rangos permiten crear objetos ***perezosos***
(*lazy* en inglés) que sólo realizan cálculos cuando realmente se necesitan
en la iteración, es decir, cuando se accede al siguiente elemento del rango.
Algoritmos especiales sobre rangos se verán en la sección
[joyas de D](gems/range-algorithms).

### En profundidad

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writeln;

struct FibonacciRange {
    // Estados del generador deFibonacci
    int a = 1, b = 1;

    // El rango de fibonacci nunca termina
    enum empty = false;

    // Se coge el primer elemento
    int front() const @property {
        return a;
    }

    // Se borra el primer elemento
    void popFront() {
        auto t = a;
        a = b;
        b = t + b;
    }
}

void main() {
    FibonacciRange fib;

    import std.range : drop, generate, take;
    import std.algorithm.iteration :
        filter, sum;

    // Se seleccionan los 10 primeros
    // elementos del rango de fibonacci
    auto fib10 = fib.take(10);
    writeln("Fib 10: ", fib10);

    // Excepto los primeros cinco
    auto fib5 = fib10.drop(5);
    writeln("Fib 5: ", fib5);

    // Se seleccionan los pares
    auto fibEven = fib5.filter!(x => x % 2);
    writeln("FibEven : ", fibEven);

    // Se suman todos los elementos
    writeln("Sum of FibEven: ", fibEven.sum);

    // Generalmente todo esto se escribe así:
    fib10.take(10)
         .drop(5)
         .filter!(x => x % 2)
         .sum
         .writeln;
}
```
