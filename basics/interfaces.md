# Interfaces

D permite la definición de interfaces mediante la palabra reservada `interface`.
Las interfaces son ténicamente igual que clases, pero sus métodos tienen que
ser implementados por toda clase que herede de ellas.

    interface Animal {
        void makeNoise();
    }

Por ejemplo, en esta interfaz, `Animal`, el método `makeNoise` tiene que ser
implementado por la clase `Dog`, ya que esta clase hereda de dicha interfaz.
Esencialmente el método `makeNoise` se comporta como un método abstracto de
la clase base.

    class Dog : Animal {
        override void makeNoise() {
            ...
        }
    }

    auto dog = new Dog;
    Animal animal = dog; // 'cast' implícito a interfaz
    animal.makeNoise();

El número de interfaces de las que una clase puede heredar no está limitado,
pero una clase sólo puede heredar de **una** clase base.

### El patrón NVI (*Non Virtual Interface*)

El [patrón NVI](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern)
permite métodos no virtuales dentro de una interfaz común. Es por esto por lo
que este patrón previene la violación de patrones de ejecución comunes.
D activa el patrón NVI permitiendo usar la palabra reservada `final` (es
decir, que no se pueden sobreescribir) en funciones dentro de las interfaces.
Esto fuerza comportamientos específicos que pueden ser personalizados al
sobreescribir otras funciones marcadas como abstractas dentro de las interfaces.

    interface Animal {
        void makeNoise();
        final doubleNoise() { // patrón NVI
            makeNoise();
            makeNoise();
        }
    }

### En profundidad

- [Interfaces in _Programming in D_](http://ddili.org/ders/d.en/interface.html)
- [Interfaces in D](https://dlang.org/spec/interface.html)

## {SourceCode}

```d
import std.stdio : writeln;

interface Animal {
    /*
    Función virtual que tiene
    que ser sobreescrita.
    */
    void makeNoise();

    /*
    Patrón NVI. Usa makeNoise internamente
    para personalizar el comportamiento al
    heredar de otras clases.

    Parámetros:
        n =  número de repeticiones
    */
    final void multipleNoise(int n) {
        for(int i = 0; i < n; ++i) {
            makeNoise();
        }
    }
}

class Dog: Animal {
    override void makeNoise() {
        writeln("Woof!");
    }
}

class Cat: Animal {
    override void makeNoise() {
        writeln("Meeoauw!");
    }
}

void main() {
    Animal dog = new Dog;
    Animal cat = new Cat;
    Animal[] animals = [dog, cat];
    foreach(animal; animals) {
        animal.multipleNoise(5);
    }
}
```
