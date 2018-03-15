# Funciones

Una función en D ya ha sido presentada: `main()`. Esta función es el punto de
entrada de todo programa escrito en D. Una función puede devolver un valor (o
puede ser declaraca como `void` y no devolver nada) y aceptar un número arbitrario
de argumentos:

    int add(int lhs, int rhs) {
        return lhs + rhs;
    }

### Tipos de retorno `auto`

Si el tipo de dato devuelto por una función se define como `auto`, el compilador
de D infiere el tipo de dato automáticamente. Es por esto que si una función
tiene múltiples declaraciones `return`, estas tienen que devolver tipos de
datos compatibles.

    auto add(int lhs, int rhs) { // devuelve `int`
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // devuelve `double`
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }

### Argumentos predeterminados

Las funciones puede definir de forma opcional argumentos con valores
predeterminados. Esto evita la tediosa tarea de declarar funciones
sobrecargadas redundantes.

    void plot(string msg, string color = "rojo") {
        ...
    }
    plot("D mola");
    plot("D mola", "azul");

Una vez que un argumento predeterminado ha sido especificado, el resto de
argumentos de la función también tienen que tener valores predeterminados.

### Funciones locales

Las funciones también puede ser declaradas dentro de otras funciones. Estas
funciones se pueden usar de forma local y no son visibles fuera del ámbito de
la función donde han sido declaradas. Además, estas funciones pueden acceder
a objetos que son locales al ámbito de la función padre.

    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // esto es legal
        }
        ...

Este tipo de funciones anidadas se llaman *delegates* y serán explicadas con
mayor detalle [en breve](basics/delegates).

### En profundidad

- [Functions in _Programming in D_](http://ddili.org/ders/d.en/functions.html)
- [Function parameters in _Programming in D_](http://ddili.org/ders/d.en/function_parameters.html)
- [Function specification](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    // Se definen 4 funciones locales para
    // 4 operaciones matemáticas
    auto add(int lhs, int rhs) {
        return lhs + rhs;
    }
    auto sub(int lhs, int rhs) {
        return lhs - rhs;
    }
    auto mul(int lhs, int rhs) {
        return lhs * rhs;
    }
    auto div(int lhs, int rhs) {
        return lhs / rhs;
    }

    int a = 10;
    int b = 5;

    // 'uniform' genera un número entre START
    // y END, donde END NO está incluido.
    // Dependiendo del resultado, se llama al
    // correspondiente operador matemático.
    switch (uniform(0, 4)) {
        case 0:
            writeln(add(a, b));
            break;
        case 1:
            writeln(sub(a, b));
            break;
        case 2:
            writeln(mul(a, b));
            break;
        case 3:
            writeln(div(a, b));
            break;
        default:
            // marca especial que indica que
            // el código es inalcanzable
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // add(), sub(), mul() y div()
    // no son visibles fuera de su ámbito
    static assert(!__traits(compiles,
                            add(1, 2)));
}

```
