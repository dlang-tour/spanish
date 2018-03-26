# Programación por contrato (*contract programming*)

La programación por contrato (*contract programming* en inglés) en D incluye
un conjunto de construcciones del lenguaje que permiten incrementar la calidad
del código implementando pruebas para estar seguro de que el código base se
comporta como se requiere. Los contratos (*contracts* en inglés) se compilan
y se ejecutan cuando se construye el software para pruebas o depuración.
Cuando se compila para lanzar una versión (activado en el compilador mediante
la opción `-release`), estos contratos son totalmente omitidos por el
compilador, por lo que no se deberían usar para validar la entrada de usuario
o como alternativa a las excepciones.

### La expresión `assert`

La forma más simple de programación por contrato en D es la expresión
`assert(...)`, que prueba que se cumple cierta condición, abortando la
ejecución del programa mediante un `AssertError` cuando esta no se cumple.

    assert(sqrt(4) == 2);
    // El mensaje de error es opcional.
    assert(sqrt(16) == 4, "¡sqrt está roto!");

### Compromisos en las funciones

Las palabras reservadas `in` y `out` permiten formalizar estos contratos
para parámetros de entrada y valores de retorno de las funciones.

    long square_root(long x)
    in {
        assert(x >= 0);
    } out (result) {
        assert((result * result) <= x
            && (result+1) * (result+1) > x);
    } do {
        return cast(long)std.math.sqrt(cast(real)x);
    }

El contenido dentro del bloque `in` también se puede poner dentro del cuerpo
de la función, pero la intención queda mucho más clara de esta forma. En el
bloque `out`, el valor de retorno de la función se puede capturar mediante la
expresión `out(result)` y verificar en consecuencia.

### Pruebas invariantes

Mediante la palabra reservada `invariant()` se declara una función miembro
especial dentro de las estructuras y de las clases que permiten realizar
pruebas sobre el estado del objeto durante toda su vida. Los invariantes:

* Se llaman después del constructor y antes del destructor.
* Se llaman antes de entrar dentro de una función miembro.
* Se llaman después de salir de una función miembro.

### Validar la entrada de usuario

Como todos los contratos se eliminan en las versiones finales, la entrada de
usuario no se debería validar mediante esta técnica. Por otra parte, las
expresiones `assert` sí se pueden usar en funciones marcadas como `nothrow`,
ya que no lanza excepciones sino errores fatales. El análogo en tiempo de
ejecución a `assert` es la función [`std.exception.enforce`](https://dlang.org/phobos/std_exception.html#.enforce)
de la librería estándar, ya que lanza excepciones que se pueden atrapar.

### En profundidad

- [`assert` and `enforce` in _Programming in D_](http://ddili.org/ders/d.en/assert.html)
- [Contract programming in _Programming in D_](http://ddili.org/ders/d.en/contracts.html)
- [Contract Programming for Structs and Classes in _Programming in D_](http://ddili.org/ders/d.en/invariant.html)
- [Contract programming in D spec](https://dlang.org/spec/contracts.html)
- [`std.exception`](https://dlang.org/phobos/std_exception.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Tipo de dato Date simplificado.
(mejor usar std.datetime).
*/
struct Date {
    private {
        int year;
        int month;
        int day;
    }

    this(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    invariant() {
        assert(year >= 1900);
        assert(month >= 1 && month <= 12);
        assert(day >= 1 && day <= 31);
    }

    /**
    Serializa un objeto Date a partir de
    una cadena con formato 'YYYY-MM-DD'.

    Params:
        date = cadena a ser serializada

    Returns: objeto Date.
    */
    void fromString(string date)
    in {
        assert(date.length == 10);
    }
    do {
        import std.format : formattedRead;
        // La función `formattedRead` analiza
        // el formato de cadena dado y escribe
        // el resultado en las variables
        // indicadas.
        formattedRead(date, "%d-%d-%d",
            &this.year,
            &this.month,
            &this.day);
    }

    /**
    Serializa un objeto Date a una cadena
    con el formato 'YYYY-MM-DD'.

    Returns: Cadena de caracteres como
    representación de un objeto Date.
    */
    string toString() const
    out (result) {
        import std.algorithm : all, count,
                              equal, map;
        import std.string : isNumeric;
        import std.array : split;

        // Se verifica que se devuelve
        // 'YYYY-MM-DD'.
        assert(result.count("-") == 2);
        auto parts = result.split("-");
        assert(parts.map!`a.length`
                    .equal([4, 2, 2]));
        assert(parts.all!isNumeric);
    }
    do {
        import std.format : format;
        return format("%.4d-%.2d-%.2d",
                      year, month, day);
    }
}

void main() {
    auto date = Date(2016, 2, 7);

    // Esto hace que falle el invariante.
    // No se debe validar la entrada de
    // usuario mediante invariantes. Para
    // ello se deben usar excepciones.
    date.fromString("2016-13-7");

    date.writeln;
}
```
