# Bucles

D proporciona cuatro tipos de bucles:

### 1) Bucle `while`

Los bucles `while` ejecutan el bloque de código indicado mientras cierta
condición sea satisfecha:

    while (condition) {
        foo();
    }

### 2) Bucle `do ... while`

Los bucles `do .. while` ejecutan el bloque de código indicado mientras cierta
condición sea satisfecha, pero al contrario que los bucles `while`, el bloque
de código se ejecuta **antes** de que la condición se evalúe por primera vez:

    do {
        foo();
    } while (condition);

### 3) Bucle `for`

El clásico bucle `for`, conocido por C/C++ o Java, cuenta con las famosas tres
partes: el inicializador, la condición y la sentencia de control de pasos:

    for (int i = 0; i < arr.length; i++) {
        foo();
    }

### 4) Bucle `foreach`

El [bucle `foreach`](basics/foreach), que se verá con más detalle en la
sección siguiente:

    foreach (elem; arr) {
        foo();
    }

#### Palabras reservadas (*keywords*) y etiquetas

La palabra reservada `break` aborta inmediatamente el bucle actual. En
un bucle anidado se puede indicar una **etiqueta** para saber a qué parte del
bucle exterior hay que ir cuando se rompe el bucle interior:

    outer: for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            doSomething();
            break outer;
        }
    }

La palabra reservada `continue` inicia inmediatamente la siguiente iteración
del bucle.

### En profundidad

- `for` loop in [_Programming in D_](http://ddili.org/ders/d.en/for.html), [specification](https://dlang.org/spec/statement.html#ForStatement)
- `while` loop in [_Programming in D_](http://ddili.org/ders/d.en/while.html), [specification](https://dlang.org/spec/statement.html#WhileStatement)
- `do-while` loop in [_Programming in D_](http://ddili.org/ders/d.en/do_while.html), [specification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Calcula la media de los
elementos de un array.
*/
double average(int[] array) {
    immutable initialLength = array.length;
    double accumulator = 0.0;
    while (array.length) {
        // también se puede hacer con .front
        // que se importa de std.array : front
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / initialLength;
}

void main() {
    auto testers = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20

    for (auto i = 0; i < testers.length; ++i) {
      writeln("La media de ", testers[i],
        " = ", average(testers[i]));
    }
}
```
