# Arrays asociativos

Los arrays asociativos de D están incluidos en el propio lenguaje. Estos arrays
asociativos también son conocidos como *hash maps*. Un array asociativo con una
clave de tipo cadena de caracteres (`string`) y con valores de tipo entero
(`int`) se declara de la siguiente forma:

    int[string] arr;

A los valores se accede mediante su clave, con lo que pueden ser modificados:

    arr["key1"] = 10;

Para comprobar si una clave existe dentro del array asociativo se usa la
expresión `in`:

    if ("key1" in arr)
        writeln("Sí");

La expresión `in` devuelve un puntero al valor referenciado por la clave
indicada, o `null` en caso de que no exista la clave dada. Gracias a esto,
la comprobación de existencia de claves y la asignación se pueden combinar
según convenga:

    if (auto val = "key1" in arr)
        *val = 20;

Intentar acceder a una clave que no existe en el array asociativo lanza un
error de tipo `RangeError` que aborta inmediatamente la aplicación. Se puede
usar la función `get(key, defaultValue)` para acceder de forma segura a los
arrays asociativos dando un valor predeterminado.

Los arrays asociativos (conocidos con la abreviatura AA) tienen la propiedad
`.length` igual que los arrays normales. Además también tienen el método
`.remove(val)` para borrar elementos dada su clave. Se deja como ejercicio
para el lector explorar los rangos especiales `.byKey` y `.byValue`.

### En profundidad

- [Associative arrays in _Programming in D_](http://ddili.org/ders/d.en/aa.html)
- [Associative arrays specification](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.array : assocArray;
import std.algorithm.iteration: each, group,
    splitter, sum;
import std.string: toLower;
import std.stdio : writefln, writeln;

void main() {
    string text = "Rock D with D";

    // Se itera sobre todas las palabras
    // y se cuenta cada una una vez.
    int[string] words;
    text.toLower()
        .splitter(" ")
        .each!(w => words[w]++);

    foreach (key, value; words)
        writefln("clave: %s, valor: %d",
                       key, value);

    // Las propiedades `.keys` y `.values`
    // devuelve arrays
    writeln("Palabras: ", words.keys);

    // `.byKey`, `.byValue` y `.byKeyValue`
    // devuelven rangos iterables (y _vagos_).
    writeln("# Palabras: ", words.byValue.sum);

    // Se puede crear un nuevo array asociativo
    // usando la función `assocArray` pasando
    // un rango de tuplas clave/valor.
    auto array = ['a', 'a', 'a', 'b', 'b',
                  'c', 'd', 'e', 'e'];

    // La función `.group` agrupa
    // consecutivamente elementos equivalentes
    // en un tupla simple compuesta por el
    // elemento y el número de repeticiones
    // del mismo.
    auto keyValue = array.group;
    writeln("Rango clave/valor: ", keyValue);
    writeln("Array asociativo: ",
             keyValue.assocArray);
}
```
