# Delegados (*delegates*)

### Funciones como argumentos

Una función también puede ser un parámetro de otra función.

    void doSomething(int function(int, int) doer) {
        // se llama a la función pasada como parámetro
        doer(5,5);
    }

    doSomething(&add); // se usa la función global `add` y esta
                       // debe tener dos enteros como parámetros

Se puede llamar a la función `doer` como a cualquier otra función.

### Funciones locales con contexto

El ejemplo anterior usa el tipo de dato `function`, que no es más que un
puntero a una función local. Se debe usar un delegado (`delegate`) en el
momento en que una función miembro o una función local se referencia.
Un `delegate` es un puntero a una función que contiene información adicional
sobre su contexto o su *enclosure* en inglés, de ahí que también se les llame
en otros lenguajes, en inglés, ***closures***. Por ejemplo, un `delegate` que
apunte a un método de una clase incluye tanto el puntero a la función como
el puntero al objeto de la clase. Un `delegate` creado por una función anidada
incluye un enlace al contexto de la función padre en lugar de a una clase.
Sin embargo, en caso necesario el compilador de D puede hacer automáticamente
una copia de dicho contexto en el *heap* para garantizar la seguridad de la
memoria. Entonces el `delegate` llevará un enlace a dicha zona de memoria.

    void foo() {
        void local() {
            writeln("local");
        }
        auto f = &local; // f es de tipo delegate()
    }

La misma función `doSomething` tomando un `delegate` como parámetro sería:

    void doSomething(int delegate(int,int) doer);

Los objetos `delegate` y los objetos `function` no se pueden mezclar, pero
la función de la librería estándar [`std.functional.toDelegate`](https://dlang.org/phobos/std_functional.html#.toDelegate)
convierte una `function` a un `delegate`.

### Funciones anónimas y lambdas

Como las funciones pueden ser guardadas en variables y pasadas como parámetro
a otras funciones, puede ser laborioso darles su propio nombre para definirlas.
Es por esto que D permite funciones que no tengan nombre y funciones *de una
sola línea* llamadas *lambdas*.

    auto f = (int lhs, int rhs) {
        return lhs + rhs;
    };
    auto f = (int lhs, int rhs) => lhs + rhs; // lambda: internamente convertida a lo de arriba

También es posible pasar cadenas de caracteres como parámetros de plantillas
a partes funcionales de la librería estándar de D. Por ejemplo este conveniente
modo de definir un *folding* (también conocido como reductor o *reducer* en inglés):

    [1, 2, 3].reduce!`a + b`; // 6

Las funciones de tipo cadena de caracteres sólo son posibles con **uno o dos**
parámetros, usando `a` como primer argumento y `b` como segundo.

### En profundidad

- [Delegate specification](https://dlang.org/spec/function.html#closures)

## {SourceCode}

```d
import std.stdio : writeln;

enum IntOps {
    add = 0,
    sub = 1,
    mul = 2,
    div = 3
}

/**
Proporciona un cálculo matemático
Parámetros:
    op = operación matemática seleccionada
Devuelve: `delegate` que hace la operación
*/
auto getMathOperation(IntOps op) {
    // Se definen 4 funciones lambda para
    // 4 operaciones matemáticas diferentes
    auto add = (int lhs, int rhs) => lhs + rhs;
    auto sub = (int lhs, int rhs) => lhs - rhs;
    auto mul = (int lhs, int rhs) => lhs * rhs;
    auto div = (int lhs, int rhs) => lhs / rhs;

    // nos aseguramos de que el switch cubre
    // todos los casos posibles
    final switch (op) {
        case IntOps.add:
            return add;
        case IntOps.sub:
            return sub;
        case IntOps.mul:
            return mul;
        case IntOps.div:
            return div;
    }
}

void main() {
    int a = 10;
    int b = 5;

    auto func = getMathOperation(IntOps.add);
    writeln("¡El tipo de la función es ",
        typeof(func).stringof, "!");

    // se ejecuta el `delegate` que es el que
    // hace todo el trabajo real por nosotros
    writeln("resultado: ", func(a, b));
}
```
