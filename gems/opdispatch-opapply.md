# Las funciones especiales `opDispatch` y `opApply`

D permite sobreescribir operadores como `+`, `-` o el operador de llamada a
función, `()`, para [clases y estructuras](https://dlang.org/spec/operatoroverloading.html).
En este capítulo se verán las funciones especiales de sobrecarga de operadores
`opDispatch` y `opApply`.

### La función especial `opDispatch`

La función especial `opDispatch` se puede definir como una función miembro
tanto dentro de clases como de estructuras. Cualquier llamada a una función
miembro desconocida dentro de estos tipos de datos será pasada a `opDispatch`,
pasando como parámetros el nombre de la función miembro desconocida como una
cadena de caracteres como parámetro de plantilla. `opDispatch` se puede
considerar como una función que procesa todas las llamadas a funciones no
conocidas permitiendo un nuevo nivel de programación genérica, siendo esta
totalmente realizada en tiempo de compilación.

    struct C {
        void callA(int i, int j) { ... }
        void callB(string s) { ... }
    }

    struct CallLogger(C) {
        C content;
        void opDispatch(string name, T...)(T vals) {
            writeln("Llamada a: ", name);
            mixin("content." ~ name)(vals);
        }
    }

    CallLogger!C l;
    l.callA(1, 2);
    l.callB("ABC");

### La función especial `opApply`

Un método alternativo a la implementación de un rango para ser usado por
`foreach` es implementar la función miembro `opApply`. Si se itera mediante
un `foreach` en tipos de datos que implementen `opApply`, en cada iteración
se llamará a esta función con un `delegate` como parámetro:

    class Tree {
        Tree lhs;
        Tree rhs;
        int opApply(int delegate(Tree) dg) {
            if (lhs && lhs.opApply(dg)) return 1;
            if (dg(this)) return 1;
            if (rhs && rhs.opApply(dg)) return 1;
            return 0;
        }
    }

    Tree tree = new Tree;
    foreach(node; tree) {
        ...
    }

El compilador transforma el cuerpo del `foreach` en un `delegate` que se pasa
al objeto. El único parámetro pasado es el elemento actual de la iteración.
El valor entero *mágico* que se devuelve se interpreta y, en caso de que no
sea `0`, la iteración se para.

### En profundidad

- [Operator overloading in _Programming in D_](http://ddili.org/ders/d.en/operator_overloading.html)
- [`opApply` in _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [Operator overloading in D](https://dlang.org/spec/operatoroverloading.html)

## {SourceCode}

```d
/*
Un Variant es un tipo de dato que contiene
cualquier otro tipo de dato:
https://dlang.org/phobos/std_variant.html
*/

import std.variant : Variant;

/*
Tipo de dato al que se le puede llamar
con cualquier método gracias a opDispatch
igual que los tipos `var` de Javascript.
*/
struct var {
    private Variant[string] values;

    @property
    Variant opDispatch(string name)() const {
        return values[name];
    }

    @property
    void opDispatch(string name, T)(T val) {
        values[name] = val;
    }
}

void main() {
    import std.stdio : writeln;

    var test;
    test.foo = "test";
    test.bar = 50;
    writeln("test.foo = ", test.foo);
    writeln("test.bar = ", test.bar);
    test.foobar = 3.1415;
    writeln("test.foobar = ", test.foobar);
    // ERROR: todavía no existe.
    // writeln("test.notthere = ",
    //   test.notthere);
}
```
