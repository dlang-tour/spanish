# Control de flujo

El flujo de una aplicación puede ser controlado de forma condicional con las
sentencias `if` y `else`:

    if (a == 5) {
        writeln("Condición satisfecha");
    } else if (a > 10) {
        writeln("Otra condición satisfecha");
    } else {
        writeln("¡Ninguna condición satisfecha!");
    }

Cuando un bloque `if` o un `else` sólo contiene una sentencia, se pueden
omitir las llaves de apertura y cierre.

D proporciona los mismos operadores que C/C++ y Java para comprobar la igualdad
de variables o compararlas:

* `==` y `!=` para comprobar la igualdad o desigualdad.
* `<`, `<=`, `>` y `>=` para comprobar si es menor que (o menor o igual) y si es mayor que (o mayor o igual).

Cuando se combinan varias condiciones se puede usar el operador `||` para
representar un *O* lógico (*OR* en inglés) y `&&` para representar un *Y*
lógico (*AND* en inglés).

D también define una sentencia `switch`..`case` que ejecuta un caso dependiendo
del valor de *una* variable. `switch` funciona con todos los tipos de datos
básicos y ¡con cadenas de caracteres! Incluso es posible definir rangos para
tipos de datos enteros usando la sintaxis `case START: .. case END:`. Asegúrate
de echar un vistazo al código fuente de ejemplo.

### En profundidad

#### Referencias básicas

- [Logical expressions in _Programming in D_](http://ddili.org/ders/d.en/logical_expressions.html)
- [If statement in _Programming in D_](http://ddili.org/ders/d.en/if.html)
- [Ternary expressions in _Programming in D_](http://ddili.org/ders/d.en/ternary.html)
- [`switch` and `case` in _Programming in D_](http://ddili.org/ders/d.en/switch_case.html)

#### Referencias avanzadas

- [Expressions in detail](https://dlang.org/spec/expression.html)
- [If Statement specification](https://dlang.org/spec/statement.html#if-statement)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    if (1 == 1)
        writeln("¡Las mates en D funcionan!");

    int c = 5;
    switch(c) {
        case 0: .. case 9:
            writeln(c, " está entre 0-9");
            break; // ¡necesario!
        case 10:
            writeln("¡Un diez!");
            break;
        default: // si nada coincide
            writeln("Nada");
            break;
    }
}
```
