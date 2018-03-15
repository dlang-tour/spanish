# Tipos básicos

D proporciona varios tipos de datos básicos que siempre tienen el mismo
tamaño **independientemente** de la plataforma, siendo el
tipo `real` la única excepción, proporcionando la mayor precisión
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

#### Tipos de coma flotante

| tipo    | tamaño
|---------|--------------------------------------------------
|`float`  | 32 bits
|`double` | 64 bits
|`real`   | >= 64 bits (generalmente 64 bits, pero 80 bits en Intel x86 de 32 bits)

El prefijo `u` denota tipos numéricos **sin signo**. `char` especifica
caracteres UTF-8, `wchar` se usa para cadenas UTF-16 y `dchar`
se usa para cadenas UTF-32.

Las conversiones entre variables de tipos distintos sólo son
permitidas por el compilador si no se pierde precisión. Sin
embargo, las conversiones entre tipos de coma flotante (por ejemplo, de
`double` a `float`) sí están permitidas.

La palabra clave especial `auto` crea una variable e infiere
su tipo a partir del lado derecho de la expresión. `auto miVar = 7`
deducirá el tipo `int` para `miVar`. Aunque se use `auto` en la declaración
de variables, su tipo se define durante la compilación y no puede ser cambiado
durante la ejecución, al igual que con cualquier otra variable declarada
con un tipo asignado explícitamente.

### Propiedades de los tipos

Todos los tipos de datos tienen una propiedad `.init` usada para la
inicialización de variables. Para todos los enteros, esta vale `0`
y para variables de coma flotante esta vale `nan` (no es un número,
*not a number* en inglés).

Los tipos enteros y de coma flotante tienen la propiedad `.max` que es
el máximo valor que pueden representar. Los tipos enteros también
tienen la propiedad `.min` para el valor más pequeño que pueden
representar, mientras que los tipos de coma flotante tienen la
propiedad `.min_normal` que se define como el mínimo valor normalizado
representable que no es 0.

Los tipos de coma flotante también tienen, entre otras, las propiedades `.nan`
(el valor NaN), `.infinity` (el valor infinito), `.dig` (el
número de dígitos decimales de precisión) y `.mant_dig`
(número de bits en la mantisa).

Todos los tipos de datos tienen también la propiedad `.stringof` que
devuelve el nombre del tipo de dato como una cadena de caracteres (`string`).

### Índices en D

Los índices en D usan generalmente el tipo de dato `size_t` ya que es un tipo
lo suficientemente grande como para representar un desplazamiento (*offset*)
que cubra toda la memoria direccionable. Este tipo de dato es `uint` en
arquitecturas de 32 bits y `ulong` en arquitecturas de 64 bits.

### Expresión `assert`

`assert` es una expresión que verifica condiciones en modo de depuración y,
en caso de no cumplirse la condición, esta aborta la ejecución del programa con
un `AssertionError`. `assert(0)` se usa para indicar código inalcanzable.

### En profuncidad

#### Referencias básicas

- [Assignment](http://ddili.org/ders/d.en/assignment.html)
- [Variables](http://ddili.org/ders/d.en/variables.html)
- [Arithmetics](http://ddili.org/ders/d.en/arithmetic.html)
- [Floating Point](http://ddili.org/ders/d.en/floating_point.html)
- [Fundamental types in _Programming in D_](http://ddili.org/ders/d.en/types.html)

#### Referencias avanzadas

- [Overview of all basic data types in D](https://dlang.org/spec/type.html)
- [`auto` and `typeof` in _Programming in D_](http://ddili.org/ders/d.en/auto_and_typeof.html)
- [Type properties](https://dlang.org/spec/property.html)
- [Assert expression](https://dlang.org/spec/expression.html#AssertExpression)

## {SourceCode}

```d
import std.stdio : writeln;

void main() {
    // Los números grandes se puede separar
    // con un guión bajo "_"
    // para mejorar la legibilidad.
    int b = 7_000_000;
    short c = cast(short) b; // cast necesario
    uint d = b; // bien
    int g;
    assert(g == 0);

    auto f = 3.1415f; // f indica que es float

    // typeid(VAR) devuelve información del tipo
    // de una expresión.
    writeln("el tipo de f es ", typeid(f));
    double pi = f; // bien
    // para tipos de coma flotante
    // se permiten conversiones implícitas
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
