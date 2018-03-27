# Evaluación de funciones en tiempo de compilación (CTFE)

La evaluación de funciones en tiempo de compilación (CTFE, *Compile Time
Function Evaluation*, en inglés) es un mecanismo que permite al compilador
ejecutar funciones en **tiempo de compilación**. No existe un conjunto especial
de características del lenguaje D necesarias para usar esta característica,
simplemente cuando una función depende exclusivamente de valores conocidos en
tiempo de compilación, el compilador de D puede decidir interpretarla durante
la compilación.

    // El resultado se calcula en tiempo de compilación.
    // Si se mira el código máquina generado, este no
    // contiene ninguna llamada a función.
    static val = sqrt(50);

Palabras reservadas como `static`, `immutable` o `enum` indican al compilador
que use CTFE cuando sea posible. Lo bueno de esta técnica es que no se
necesita reescribir las funciones para ser usada, además de que este código
se puede compartir sin ningún problema:

    int n = doSomeRuntimeStuff();
    // La misma función que antes, sólo que esta vez se
    // llama de la forma clásica: en tiempo de ejecución.
    auto val = sqrt(n);

Un prominente ejemplo en D de esta técnica es la librería [std.regex](https://dlang.org/phobos/std_regex.html).
Esta librería proporciona el tipo `ctRegex`, que usa *mixins* de cadenas de
caracteres (visto en la sección anterior) y CTFE para generar autómatas que
analicen expresiones regulares altamente optimizados. La misma base de código
se usa para la versión de `regex` que permite compilar expresiones regulares
que sólo están disponibles en tiempo de ejecución.

    auto ctr = ctRegex!(`^.*/([^/]+)/?$`);
    auto tr = regex(`^.*/([^/]+)/?$`);
    // 'ctr' y 'tr' se pueden intercambiar,
    // pero 'ctr' es más rápida.

No todas las características del lenguaje están disponibles para CTFE, pero
el soporte de esta característica por parte del lenguaje se incrementa con
cada nueva versión del compilador.

### En profundidad

- [Introduction to regular expressions in D](https://dlang.org/regular-expression.html)
- [std.regex](https://dlang.org/phobos/std_regex.html)
- [Conditional compilation](https://dlang.org/spec/version.html)

## {SourceCode}

```d
import std.stdio : writeln;

/**
Calcula la raíz cuadrada de un número
usando el esquema de aproximación de
Newton.

Params:
    x = número a calcular su raíz cuadrada

Returns: raíz cuadrada del número x
*/
auto sqrt(T)(T x) {
    // El límite del error en la aproximación
    // (no merecen la pena más iteraciones).
    enum GoodEnough = 0.01;
    import std.math : abs;
    // Se elige un buen número de partida.
    T z = x*x, old = 0;
    int iter;
    while (abs(z - old) > GoodEnough) {
        old = z;
        z -= ((z*z)-x) / (2*z);
    }

    return z;
}

void main() {
    double n = 4.0;
    writeln("Raíz cuadrada de 4 en tiempo" ~
        " de ejecución = ", sqrt(n));
    static cn = sqrt(4.0);
    writeln("Raíz cuadrada de 4 en tiempo" ~
        " de compilación = ", cn);
}
```
