# Tipos básicos

D provee varios tipos básicos que siempre tienen el mismo
tamaño **independientemente** de la plataforma, siendo el
tipo `real` la única excepción, proveyendo la mayor precisión
de punto flotante disponible. No hay diferencias en el tamaño
de un entero independientemente de si la aplicación es
compilada para sistemas de 32 o 64 bits.

| tipo                          | tamaño
|-------------------------------|------------
|`bool`                         | 8 bits
|`byte`, `ubyte`, `char`        | 8 bits
|`short`, `ushort`, `wchar`     | 16 bits
|`int`, `uint`, `dchar`         | 32 bits
|`long`, `ulong`                | 64 bits

#### Tipos de punto flotante:

| tipo    | tamaño
|---------|--------------------------------------------------
|`float`  | 32 bits
|`double` | 64 bits
|`real`   | >= 64 bits (generalmente 64 bits, pero 80 bits en Intel x86 de 32 bits)

El prefijo `u` denota tipos *sin signo*. `char` especifica
caracteres UTF-8, `wchar` es usado en cadenas UTF-16, y `dchar`
en cadenas UTF-32.

Las conversiones entre variables de tipos distintos solo son
permitidas por el compilador si no se pierde precisión. Sin
embargo, las conversiones entre tipos de punto flotante (ej.
`double` a `float`) sí están permitidas.

La palabra clave especial `auto` crea una variable e infiere
su tipo a partir del lado derecho de la expresión. `auto miVar = 7`
deducirá el tipo `int` para `miVar`. Note que aún con `auto` el
tipo es definido durante la compilación y no podrá ser cambiado
posteriormente durante la ejecución, al igual que con cualquier
otra variable con un tipo asignado explícitamente.

### Propiedades de los tipos

Todos los tipos de datos tienen una propiedad `.init` usada para la
inicialización de variables. Para todos los enteros, es `0`
y para variables de punto flotante es `nan` (*no es un número*).

Los tipos enteros y de punto flotante tienen una propiedad `.max`
para el máximo valor que pueden representar. Los tipos enteros también
tienen una propiedad `.min` para el valor más pequeño que pueden
representar, mientras que los tipos de punto flotante tienen una
propiedad `.min_normal` que es definida como el mínimo valor
normalizado representable que no sea 0.

los tipos de punto flotante también tienen las propiedades `.nan`
(el valor NaN), `.infinity` (el valor infinito), `.dig` (el
número de dígitos decimales de precisión), `.mant_dig`
(número de bits en la mantisa) y otras más.

Todos los tipos también tienen una propiedad `.stringof`
que retorna su nombre como una cadena (de caracteres).

### Índices en D

Los índices en D normalmente usan el alias de tipo `size_t`,
pues es un tipo lo suficientemente grande para representar
un offset/desplazamiento hacia toda la memoria direccionable, el cual es
`uint` en arquitecturas de 32 bits y `ulong` en 64 bits.

### Expresión assert

`assert` es una expresión que verifica condiciones en modo
de depuración y aborta con un `AssertionError` si es que
falla. `assert(0)` por lo tanto se usa para demarcar
código al que no se puede llegar.

### Profundizar más

Nota: las siguientes referencias están en inglés

#### Referencias básicas

- [Asignación](http://ddili.org/ders/d.en/assignment.html)
- [Variables](http://ddili.org/ders/d.en/variables.html)
- [Aritmética](http://ddili.org/ders/d.en/arithmetic.html)
- [Punto Flotante](http://ddili.org/ders/d.en/floating_point.html)
- [Tipos fundamentales en _Programando en D_](http://ddili.org/ders/d.en/types.html)

#### Referencias avanzadas

- [Listado de todos los tipos básicos en D](https://dlang.org/spec/type.html)
- [`auto` y `typeof` en _Programando en D_](http://ddili.org/ders/d.en/auto_and_typeof.html)
- [Propiedades de tipos](https://dlang.org/spec/property.html)
- [Expresión assert](https://dlang.org/spec/expression.html#AssertExpression)

## {Código fuente}

```d
import std.stdio : writeln;

void main()
{
    // Los números grandes pueden ser separados
    // con un guión bajo "_"
    // para mejor legibilidad.
    int b = 7_000_000;
    short c = cast(short) b; // cast necesario
    uint d = b; // está bien
    int g;
    assert(g == 0);

    auto f = 3.1415f; // f indica que es float

    // typeid(VAR) retorna información del tipo
    // de una expresión.
    writeln("el tipo de f es ", typeid(f));
    double pi = f; // está bien
    // para tipos de punto flotante
    // se permite conversiones implícitas
    // a tipos de menor precisión
    float demoted = pi;

    // acceso a las propiedades de los tipos
    assert(int.init == 0);
    assert(int.sizeof == 4);
    assert(bool.max == 1);
    writeln(int.min, " ", int.max);
    writeln(int.stringof); // int
}
```
