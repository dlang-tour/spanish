# Excepciones

Esta guía es sólo para mostrar excepciones de usuario (`Exception`). Los
errores del sistema (`SystemError`) generalmente son fatales y **nunca**
se deberían atrapar.

### Atrapando excepciones

Un uso común de las excepciones es la validación de los posiblemente
inválidos datos introducidos por los usuarios. Una vez que se lanza una
excepción, se recorre la pila hasta encontrar el primer gestor válido para
la excepción lanzada.

```d
try {
    readText("dummyFile");
} catch (FileException e) {
    // ...
}
```

Es posible tener varios bloques `catch`. El bloque `finally` siempre se ejecuta
independientemente de si ha ocurrido un error o no. Las excepciones se lanzan
mediante la palabra reservada `throw`.

```d
try {
    throw new StringException("No pasarás.");
} catch (FileException e) {
    // ...
} catch (StringException e) {
    // ...
} finally {
    // ...
}
```

Recuerda que el [protector de ámbito (*scope guard* en inglés)](gems/scope-guards)
es en general una mejor solución que el patrón `try - finally`.

### Excepciones personalizadas

Se pueden crear excepciones personalizadas fácilmente heredando de la
clase `Exception`:

```d
class UserNotFoundException : Exception {
    this(string msg, string file = __FILE__, size_t line = __LINE__) {
        super(msg, file, line);
    }
}

throw new UserNotFoundException("D-Man is on vacation");
```

### Un mundo más seguro mediante `nothrow`

El compilador de D puede asegurarse de que una función no cause efectos
colaterales catastróficos. Este tipo de funciones se marcan con la palabra
reservada `nothrow`, lo que indica al compilador que tiene que prohibir
lanzar excepciones dentro de este tipo de funciones.

```d
bool lessThan(int a, int b) nothrow {
    writeln("mundo inseguro"); // esto puede lanzar excepciones, por lo que está prohibido
    return a < b;
}
```

Hay que tener en cuenta que el compilador es capaz de inferir este tipo de
atributos de forma automática para código generado a partir de plantillas.

### La clase `std.exception`

Es importante evitar usar `assert` cuando se utiliza
[*contract programming*](gems/contract-programming) (se verá en la próxima
sección) para validar la entrada de usuario, ya que tanto `assert` como los
*contracts* se quitan cuando se compila la aplicación en modo *release*.
Por conveniencia, [`std.exception`](https://dlang.org/phobos/std_exception.html)
proporciona [`enforce`](https://dlang.org/phobos/std_exception.html#enforce),
que se puede usar como `assert`, con la diferencia de que lanza excepciones
(`Exception`) en lugar de `AssertError`.

```d
import std.exception : enforce;
float magic = 1_000_000_000;
enforce(magic + 42 - magic == 42, "Las matemáticas de coma flotante son divertidas");

// se lanzan excepciones personalizadas
enforce!StringException('a' != 'A', "Algoritmo que diferencia mayúsculas y minúsculas");
```

Independientemente de lo anterior, hay más cosas en `std.exception`. Por ejemplo,
cuando un error no es fatal, este se puede coger mediante la función
[`collectException`](https://dlang.org/phobos/std_exception.html#collectException).

```d
import std.exception : collectException;
auto e = collectException(aDangerousOperation());
if (e)
    writeln("La operación peligrosa ha fallado con ", e);
```

Para saber si se lanza una excepción en los tests, hay que usar
[`assertThrown`](https://dlang.org/phobos/std_exception.html#assertThrown).

### En profundidad

- [Exception Safety in D](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- system-level [core.exception](https://dlang.org/phobos/core_exception.html)
- [object.Exception](https://dlang.org/library/object/exception.html): clase base de todas las excepciones que se pueden coger de forma segura.

## {SourceCode}

```d
import std.file : FileException, readText;
import std.stdio : writeln;

void main() {
    try {
        readText("dummyFile");
    } catch (FileException e) {
        writeln("Mensaje:\n", e.msg);
        writeln("Archivo: ", e.file);
        writeln("Línea  : ", e.line);
        writeln("Traza de la pila:\n", e.info);

        // También se puede usar
        // formateo predeterminado.
        // writeln(e);
    }
}
```
