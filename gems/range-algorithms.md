# Algoritmos sobre rangos

Los módulos [std.range](http://dlang.org/phobos/std_range.html)
y [std.algorithm](http://dlang.org/phobos/std_algorithm.html) de la librería
estándar proporcionan una multitud de grandes funciones que permiten ser
encadenadas para expresar operaciones complejas de forma legible, basándose
en rangos como bloques de construcción.

Lo que hace grandes a estos algoritmos es que se pueden definir rangos propios
y aprovecharse directamente de lo que ya está en la librería estándar.

### El módulo `std.algorithm`

`filter`: dada una función lambda como parámetro de plantilla, genera un nuevo
rango que filtra los elementos del rango pasado como argumento:

    filter!"a > 20"(range);
    filter!(a => a > 20)(range);

`map`: genera un nuevo rango usando un predicado definido como parámetro de
plantilla:

    [1, 2, 3].map!(x => to!string(x));

`each`: el `foreach` _de los pobres_ cuya función pasada como parámetro se
aplica a cada elemento del rango:

    [1, 2, 3].each!(a => writeln(a));

### El módulo `std.range`

`take`: limita el rango a `N` elementos:

    theBigBigRange.take(10);

`zip`: itera sobre dos rangos en paralelo devolviendo un rango de tuplas
con cada pareja de elementos:

    assert(zip([1,2], ["hello","world"]).front
      == tuple(1, "hello"));

`generate`: dada una función crea un rango que, en cada iteración, llama a
dicha función:

    alias RandomRange = generate!(() => uniform(1, 1000));

`cycle`: devuelve un rango que repite el rango de entrada de forma infinita:

    auto c = cycle([1]);
    // El rango nunca estará vacío.
    assert(!c.empty);

### ¡La documentación está esperando tu visita!

### En profundidad

- [Ranges in _Programming in D_](http://ddili.org/ders/d.en/ranges.html)
- [More Ranges in _Programming in D_](http://ddili.org/ders/d.en/ranges_more.html)

## {SourceCode}

```d
// ¡Eh, adelante, coge todo el armamento!
import std.algorithm : canFind, map,
  filter, sort, uniq, joiner, chunkBy, splitter;
import std.array : array, empty;
import std.range : zip;
import std.stdio : writeln;
import std.string : format;

void main() {
    string text = q{Este tour da una visión
de conjunto de este potente y expresivo
lenguaje de programación de sistemas que
compila directamente a código máquina
nativo y eficiente.};

    // Predicado por donde dividir.
    alias pred = c => canFind(" ,.\n", c);
    // Como buen algoritmo, simplemente
    // funciona sin hacer reservas de
    // memoria.
    auto words = text.splitter!pred
      .filter!(a => !a.empty);

    auto wordCharCounts = words
      .map!"a.count";

    // Imprime el número de caracteres
    // por palabra de forma ordenada
    // empezando con los mínimos
    // caracteres.
    zip(wordCharCounts, words)
      // Convierte a array para ordenar.
      .array()
      .sort()
      // No queremos duplicados.
      .uniq()
      // Pone en una fila aquellos con el
      // mismo número de caracteres. La
      // función `chunkBy` ayuda generando
      // rangos de rangos que son cortados
      // según su longitud.
      .chunkBy!(a => a[0])
      // Los elementos se juntan en una línea.
      .map!(chunk => format("%d -> %s",
          chunk[0],
          // Las palabras.
          chunk[1]
            .map!(a => a[1])
            .joiner(", ")))
      // `joiner` junta pero de forma
      // perezosa (sin reservas de memoria).
      // Ahora las líneas con el retorno de
      // carro.
      .joiner("\n")
      // Imprimimos en la salida estándar.
      .writeln();
}
```
