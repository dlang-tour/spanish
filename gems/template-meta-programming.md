# Metaprogramación mediante plantillas

Si nunca se ha tenido contacto con la metaprogramación mediante plantillas
(*template meta programming* en inglés) en C++, se puede sentir alivio
gracias a las herramientas que ofrece D para hacer la vida más fácil. La
metaprogramación mediante plantillas es una técnica que permite la toma de
decisiones dependiendo del tipo de plantilla, permitiendo así hacer que los
tipos de datos genéricos sean aún más flexibles basándose en el tipo de dato
con el que van a ser instanciados.

### Expresiones `static if` e `is`

Igual que un `if` normal, un `static if` compila un bloque de código de forma
condicional según una condición que pueda ser evaluada en tiempo de compilación:

    static if(is(T == int))
        writeln("T es un int");
    static if (is(typeof(x) :  int))
        writeln("La variable x se convierte de forma implícita a int");

La [expresión `is`](http://wiki.dlang.org/Is_expression) es una expresión
genérica de ayuda que evalúa condiciones en tiempo de compilación.

    static if(is(T == int)) { // T es un parámetro de plantilla
        int x = 10;
    }

Las llaves (`{}`) se omiten si la condición es verdadera (`true`), es decir,
no se crea un ámbito nuevo. Si se quiere crear un ámbito nuevo, hay que
hacerlo de forma explítica declarando un bloque mediante `{ {` y `} }`.

La expresión `static if` se puede usar en cualquier parte del código: en
funciones, en el ámbito global o dentro de definiciones de tipos.

### Expresión `mixin template`

En cualquier sitio donde se vea código repetitivo (*boiler plate* en inglés),
`mixin template` es tu amigo:

    mixin template Foo(T) {
        T foo;
    }
    ...
    mixin Foo!int; // `int foo` disponible a partir de aquí

La expresión `mixin template` puede contener cualquier número de expresiones
complejas que son insertadas en el punto donde se instancia la plantilla. Con
esto se puede decir adiós a las directivas del preprocesador de C.

### Restricciones de plantillas

Una plantilla se puede definir con cualquier número de restricciones. Estas
restricciones fuerzan qué propiedades tiene que tener un tipo de dato:

    void foo(T)(T value)
      if (is(T : int)) { // foo!T es sólo válida si T
                         // se puede convertir a `int`.
    }

Las restricciones se pueden combinar mediante expresiones booleanas e, incluso,
pueden contener llamadas a otras funciones que puedan ser evaluadas en tiempo
de compilación. Por ejemplo, la función `std.range.primitives.isRandomAccessRange`
comprueba si un tipo de dato es un rango que soporta el operador `[]`.

## En profundidad

### Referencias básicas

- [Tutorial to D Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Conditional compilation](http://ddili.org/ders/d.en/cond_comp.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [More templates  _Programming in D_](http://ddili.org/ders/d.en/templates_more.html)
- [Mixins in  _Programming in D_](http://ddili.org/ders/d.en/mixin.html)

### Referencias avanzadas

- [Conditional compilation](https://dlang.org/spec/version.html)
- [Traits](https://dlang.org/spec/traits.html)
- [Mixin templates](https://dlang.org/spec/template-mixin.html)
- [D Templates spec](https://dlang.org/spec/template.html)

## {SourceCode}

```d
import std.traits : isFloatingPoint;
import std.uni : toUpper;
import std.string : format;
import std.stdio : writeln;

/*
Un Vector que funciona para números,
tanto enteros como de coma flotante.
*/
struct Vector3(T)
  if (is(T: real)) {
  private:
    T x,y,z;

    /*
    Generador para los 'getter' y los
    'setters' porque el código repetitivo
    es odioso.

    var -> T getVAR() y void setVAR(T)
    */
    mixin template GetterSetter(string var) {
        // Se usa un `mixin` para construir
        // los nombres de las funciones.
        mixin("T get%s() const { return %s; }"
          .format(var.toUpper, var));

        mixin("void set%s(T v) { %s = v; }"
          .format(var.toUpper, var));
    }

    /*
    Se generan de forma sencilla las funciones
    de setX, getX, etc. gracias al `mixin
    template`.
    */
    mixin GetterSetter!"x";
    mixin GetterSetter!"y";
    mixin GetterSetter!"z";

  public:
    /*
    La función `dot` sólo está disponible
    para tipos de datos de coma flotante.
    */
    static if (isFloatingPoint!T) {
        T dot(Vector3!T rhs) {
            return x * rhs.x + y * rhs.y +
                z * rhs.z;
        }
    }
}

void main() {
    auto vec = Vector3!double(3,3,3);
    // Esto no funciona debido a las
    // restricciones de la plantilla
    // Vector3!string es ilegal.

    auto vec2 = Vector3!double(4,4,4);
    writeln("vec dot vec2 = ", vec.dot(vec2));

    auto vecInt = Vector3!int(1,2,3);
    // No dispone de la función `dot` porque
    // sólo está disponible para tipos de
    // datos de coma flotante.
    // vecInt.dot(Vector3!int(0,0,0));

    // Los 'getter' y 'setter' se generan
    // por código.
    vecInt.setX(3);
    vecInt.setZ(1);
    writeln(vecInt.getX, ",",
      vecInt.getY, ",", vecInt.getZ);
}
```
