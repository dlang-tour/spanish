# Plantillas (*templates*)

D permite definir funciones como plantillas (*templates* en inglés) de forma
similar a como lo hace C++ y Java. Esto es una forma de definir funciones
***genéricas*** u objetos que pueden funcionar con cualquier tipo de dato que
compile en el cuerpo de la función:

    auto add(T)(T lhs, T rhs) {
        return lhs + rhs;
    }

El parámetro `T` de la plantilla se pone entre paréntesis antes de los
parámetros formales de la función. `T` es un hueco que el compilador reemplaza
cuando se instancia la plantilla de la función mediante el operador `!`:

    add!int(5, 10);
    add!float(5.0f, 10.0f);
    add!Animal(dog, cat); // No compila. La clase `Animal` no implementa el operador `+`.

### Parámetros implícitos de las plantillas

Las plantillas de funciones tienen dos conjuntos de parámetros: el primero es
para los argumentos en tiempo de compilación, mientras que el segundo es para
los argumentos en tiempo de ejecución (las funciones que no son plantillas sólo
aceptan argumentos en tiempo de ejecución). Si uno o más argumentos de tiempo
de compilación no se especifican cuando se llama a la función, el compilador
trata de deducirlos de la lista de argumentos de tiempo de ejecución como los
tipos de estos argumentos.

    int a = 5; int b = 10;
    add(a, b); // T se deduce como tipo `int`
    float c = 5.0;
    add(a, c); // T se deduce como tipo `float`

### Propiedades de las plantillas

Una función puede tener cualquier número de parámetros en su plantilla y
estos son especificados durante la instanciación usando la sintaxis
`func!(T1, T2 ..)`. Los parámetros de plantilla pueden ser de cualquier tipo
básico, incluyendo cadenas de caracteres (`string`s) y números de coma flotante.

Al contrario que los *generics* en Java, las plantillas en D son sólo en tiempo
de compilación, dejando código altamente optimizado ligado al conjunto
específico de tipos de parámetros cuando se llama a la función.

Obviamente, los tipos `struct`, `class` e `interface` también se pueden definir
como plantillas.

    struct S(T) {
        // ...
    }

### En profundidad

- [Tutorial to D Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Templates in _Programming in D_](http://ddili.org/ders/d.en/templates.html)

#### Avanzado

- [D Templates spec](https://dlang.org/spec/template.html)
- [Templates Revisited](http://dlang.org/templates-revisited.html): Walter Bright escribe sobre cómo D mejora las plantillas de C++.
- [Variadic templates](http://dlang.org/variadic-function-templates.html): Artículos acerca de cómo se pueden implementar funciones con número de parámetros variable (*variadic functions*) a través de plantillas con un número de parámetros variable.

## {SourceCode}

```d
import std.stdio : writeln;

/**
Clase que usa plantillas para permitir
la implementación genérica de animales.

Params:
    noise = cadena a escribir
*/
class Animal(string noise) {
    void makeNoise() {
        writeln(noise ~ "!");
    }
}

class Dog: Animal!("Woof") {
}

class Cat: Animal!("Meeoauw") {
}

/**
Función que usa plantillas y que toma como
parámetro cualquier tipo T e implementa la
función `makeNoise`.

Params:
    animal = objeto que hace ruido
    n = número de veces a llamar a makeNoise
*/
void multipleNoise(T)(T animal, int n) {
    for (int i = 0; i < n; ++i) {
        animal.makeNoise();
    }
}

void main() {
    auto dog = new Dog;
    auto cat = new Cat;
    multipleNoise(dog, 5);
    multipleNoise(cat, 5);
}
```
