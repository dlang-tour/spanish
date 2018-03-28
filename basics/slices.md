# Slices

Los *slices* son objetos de tipo `T[]` para cualquier tipo `T` que proporcionan
una vista de un subconjunto de un array de valores de tipo `T`, o
simplemente una vista del array completo.

**Los *slices* y los arrays dinámicos son la misma cosa.**

Un *slice* está formado por dos miembros: un puntero al primer elemento y
otro elemento con la longitud de dicho *slice*:

    T* ptr;
    size_t length; // entero sin signo del número de bits de la arquitectura

### Obtener un *slice* mediante la sentencia `new`

Cuando se crea un nuevo array dinámico, lo que se devuelve es un nuevo *slice*
a la memoria reservada por dicho array:

    auto arr = new int[5];
    assert(arr.length == 5); // memoria referenciada en arr.ptr

La memoria reservada en este caso está totalmente gestionada por el recolector
de basura (*garbage collector* en inglés). El *slice* devuelto actúa como una
“vista” de los elementos subyacentes.

### Obtener un *slice* de memoria previamente reservada

Al usar el operador para crear *slices* también se puede obtener otro *slice*
que apunte a memoria que ya ha sido reservada. Este operador también se puede
aplicar a otro *slice*, a arrays estáticos, a clases y a estructuras que
implementen el operador `opSlice`, y a varias entidades más.

En la expresión de ejemplo `origin[start .. end]`, el operador de *slice* se usa
para obtener un *slice* de todos los elementos de `origin` desde `start` hasta
el elemento **anterior** a `end`:

    auto newArr = arr[1 .. 4]; // el índice 4 NO se incluye
    assert(newArr.length == 3);
    newArr[0] = 10; // cambia newArr[0], es decir, arr[1]

Estos *slices* generan una nueva vista de memoria ya reservada, es decir,
**no** reservan ninguna copia de la misma. Si ya no hay ningún *slice* que
referencie un bloque de memoria (o una parte de la misma), esta memoria será
liberada por el recolector de basura.

Al usar *slices* es posible escribir código muy eficiente para ciertas cosas
(como *parsers*, por ejemplo) ya que sólo operan en un bloque de memoria y
las partes que realmente se necesitan para trabajar. De este modo no hay
ninguna necesidad de reservar nuevos bloques de memoria.

Como se ha visto en la [sección anterior](basics/arrays), la expresión `[$]`
es una forma corta de escribir `arr.length`. Es por esto que `arr[$]` referencia
el elemento más allá del último del *slice*, por lo que se generará un error de
tipo `RangeError` (si la comprobación de límites no está desactivada).

### En profundidad

- [Introduction to Slices in D](http://dlang.org/d-array-article.html)
- [Slices in _Programming in D_](http://ddili.org/ders/d.en/slices.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main() {
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    test.writeln;
    writeln("Primer elemento: ", test[0]);
    writeln("Último elemento: ", test[$ - 1]);
    writeln("Se excluyen los dos primeros " ~
        "elementos: ", test[2 .. $]);

    writeln("Los slices son vistas de " ~
        "la memoria:");
    auto test2 = test;
    auto subView = test[3 .. $];
    test[] += 1; // +1 a cada elemento
    test.writeln;
    test2.writeln;
    subView.writeln;

    // Se crea un slice vacío
    assert(test[2 .. 2].length == 0);
}
```
