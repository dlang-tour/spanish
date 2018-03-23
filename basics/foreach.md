# Bucle `foreach`

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

D cuenta con el bucle `foreach`, que permite realizar iteraciones más legibles
y menos propensas a errores.

### Iteración sobre elementos

Dado el array `arr` de tipo `int[]`, es posible iterar sobre sus elementos
usando el bucle `foreach`:

    foreach (int e; arr) {
        writeln(e);
    }

El primer campo dentro de la definición del bucle `foreach` es el nombre de la
variable usada en la iteración. Si no se indica su tipo, este se deduce
automáticamente:

    foreach (e; arr) {
        // typeof(e) es int
        writeln(e);
    }

El segundo campo debe ser un array, aunque también puede ser un tipo de datos
especial que sea iterable llamado **rango** (*range* en inglés), que se
verá en la [siguiente sección](basics/ranges).

### Acceso por referencia

Durante la iteración, los elementos del array son copiados. Esto es aceptable
para tipos de datos básicos, pero podría ser un problema para tipos de datos
grandes. Para prevenir esta copia y/o para activar la edición de los datos
dentro del propio bucle, se usa la palabra reservada `ref`:

    foreach (ref e; arr) {
        e = 10; // se sobreescribe el valor
    }

### Iterar `n` veces

D permite escribir iteraciones que son ejecutadas `n` veces de forma más
precisa mediante la sintaxis `..`:

    foreach (i; 0 .. 3) {
        writeln(i);
    }
    // imprimirá 0 1 2

El último número del rango `a .. b` se excluye del mismo, por lo que el cuerpo
del bucle del ejemplo se ejecutará sólo 3 veces.

### Iteración con índice/contador

Para los arrays también es posible acceder al índice de cada elemento a través
de otra variable diferente:

    foreach (i, e; [4, 5, 6]) {
        writeln(i, ":", e);
    }
    // imprimirá 0:4 1:5 2:6

### Iteración en orden inverso con `foreach_reverse`

Una coleción puede ser iterada en orden inverso mediante `foreach_reverse`:

    foreach_reverse (e; [1, 2, 3]) {
        writeln(e);
    }
    // imprimirá 3 2 1

### En profundidad

- [`foreach` in _Programming in D_](http://ddili.org/ders/d.en/foreach.html)
- [`foreach` with Structs and Classes  _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [`foreach` specification](https://dlang.org/spec/statement.html#ForeachStatement)

## {SourceCode}

```d
import std.stdio : writefln;

void main() {
    auto arr = [
        [5, 15],      // 20
        [2, 3, 2, 3], // 10
        [3, 6, 2, 9], // 20
    ];

    foreach (i, row; arr) {
        double total = 0.0;
        foreach (e; row)
            total += e;

        auto avg = total / row.length;
        writefln("AVG [row=%d]: %.2f", i, avg);
    }
}
```
