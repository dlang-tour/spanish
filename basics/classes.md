# Clases

D tiene clases e interfaces de la misma forma que Java o C++. 

Cualquier clase (declarada con `class`) hereda de forma implícita de la clase
base [`Object`](https://dlang.org/phobos/object.html).

    class Foo { } // hereda de Object
    class Bar : Foo { } // Bar también es Foo

Las clases en D son generalmente instanciadas mediante la palabra reservada
`new` y la memoria se reserva en el *heap*:

    auto bar = new Bar;

Las instancias de las clases son siempre referencias y, al contrario que las
estructuras (*struct*s), nunca son copiadas por valor.

    Bar bar = foo; // bar apunta a foo

El recolector de basura (*garbage collector* en inglés) es el encargado de
liberar la memoria usada por las instancias cuando ya no exista ninguna
referencia a una instancia dada.

### Herencia

Si una función miembro (o método) de la clase base se sobreescribe, se debe
usar la palabra reservada `override` para indicarlo. Esto previene que se
sobreescriban métodos de forma no intencionada.

    class Bar : Foo {
        override functionFromFoo() {}
    }

En D, las clases sólo pueden heredar de una sola clase.

### Funciones miembro finales y abstractas

- Una función se puede marcar en la clase base con la palabra reservada `final`
para impedir que sea sobreescrita.
- Una función se puede declarar usando la palabra reservada `abstract` para
forzar a las clases derivadas que tienen que sobreescribirla.
- Una clase completa se puede declarar usando la palabra reservada `abstract`
para asegurarse de que nunca pueda ser instanciada.
- `super(...)` se puede usar para llamar de forma explícita al constructor de
la clase base.

### Comprobación de identidad

Para las instancias de las clases, los oepradores `==` y `!=` comparan los
contenidos de dichas instancias, por lo que comparar contra el valor `null`
no es válido, ya que `null` no tiene ningún contenido. Es el operador `is`
el que compara identidades (si dos instancias son la misma o si una instancia
es `null`). El operador `!is` es la operación contraria.

```d
MyClass c;
if (c == null)  // error
    ...
if (c is null)  // ok
    ...
```

En las estructuras (*struct*s) los objetos se comparan bit a bit. Para otros
operadores, la identidad es lo mismo que la igualdad.

### En profundidad

- [Classes in _Programming in D_](http://ddili.org/ders/d.en/class.html)
- [Inheritance in _Programming in D_](http://ddili.org/ders/d.en/inheritance.html)
- [Object class in _Programming in D_](http://ddili.org/ders/d.en/object.html)
- [Classes in D spec](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Elegante tipo que puede ser usado
para cualquier cosa...
*/
class Any {
    // El atribute `protected` hace que sólo
    // vean estas propiedades las clases
    // que heredan de esta.
    protected string type;

    this(string type) {
        this.type = type;
    }

    // `public` está implícito.
    final string getType() {
        return type;
    }

    // ¡Esto necesita implementación!
    abstract string convertToString();
}

class Integer : Any {
    // Esto sólo lo ve la clase Integer.
    private {
        int number;
    }

    // constructor
    this(int number) {
        // Llamada al constructor de la
        // clase base.
        super("integer");
        this.number = number;
    }

    // Otra forma de indicar el atributo
    // de protección `public` (aunque está
    // implícito).
    public:

    override string convertToString() {
        import std.conv : to;
        // La navaja suiza de la conversión.
        return to!string(number);
    }
}

class Float : Any {
    private float number;

    this(float number) {
        super("float");
        this.number = number;
    }

    override string convertToString() {
        import std.string : format;
        // Controlamos la precisión.
        return format("%.1f", number);
    }
}

void main()
{
    Any[] anys = [
        new Integer(10),
        new Float(3.1415f)
    ];

    foreach (any; anys) {
        writeln("Tipo de 'any' = ", any.getType());
        writeln("Contenido = ",
            any.convertToString());
    }
}
```
