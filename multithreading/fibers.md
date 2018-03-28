# Fibras

Las fibras (*fibers* en inglés) son una forma de implementar concurrencia de
una forma **cooperativa**. La clase `Fiber` se define en el módulo
[`core.thread`](https://dlang.org/phobos/core_thread.html).

La idea básica es que cuando una fibra no tiene nada que hacer o está esperando
por entrada del usuario, esta concede de forma **activa** su tiempo de ejecución
para que otras tareas lo puedan usar llamando a la función `Fiber.yeld()`.
El contexto padre obtiene el control, aunque el estado de dicha fibra (todas
las variables que están en la pila) se guarda. La fibra puede reanudar su
ejecución y continuar en la instrucción inmediatamente posterior a la de la
llamada a `Fiber.yield()`. ¿Magia? Sí.

    void foo() {
        writeln("Hola");
        Fiber.yield();
        writeln("mundo");
    }
    // ...
    auto f = new Fiber(&foo);
    f.call(); // Imprime 'Hola'.
    f.call(); // Imprime 'mundo'.

Esta característica se puede usar para implementar concurrencia donde múltiples
fibras comparten de forma cooperativa una única CPU. La ventaja de las fibras
sobre los hilos de ejecución es que el uso de recursos de estas es menor ya
que su uso no implica cambios de contexto.

Un buen uso de esta técnica se puede ver en el [*framework* vibe.d](http://vibed.org)
que implementa operaciones de E/S de forma no bloqueante (o asíncronas) usando
fibras, lo que lleva a código fuente mucho más limpio.

### En profundidad

- [Fibers in _Programming in D_](http://ddili.org/ders/d.en/fibers.html)
- [Documentation of core.thread.Fiber](https://dlang.org/library/core/thread/fiber.html)

## {SourceCode}

```d
import core.thread : Fiber;
import std.stdio : write;
import std.range : iota;

/**
Itera sobre un rango aplicando la función
`Fnc` a cada elemento 'x' devolviendo el
resultado. Las fibras ceden el control
después de cada aplicación.
*/
void fiberedRange(alias Fnc, R, T)(
    R range,
    ref T result)
{
    for(; !range.empty; range.popFront) {
        result = Fnc(range.front);
        Fiber.yield();
    }
}

void main() {
    int squareResult, cubeResult;
    // Crea una fibra que se inicializa con un
    // delegado que genera un rango de valores
    // elevados al cuadrado.
    auto squareFiber = new Fiber({
        fiberedRange!(x => x*x)(
            iota(1,11), squareResult);
    });
    // ...y aquí se crean los cubos.
    auto cubeFiber = new Fiber({
        fiberedRange!(x => x*x*x)(
            iota(1,9), cubeResult);
    });

    // Si el estado es TERM, la fibra termina
    // ejecutando la función asociada.
    squareFiber.call();
    cubeFiber.call();
    while (squareFiber.state
        != Fiber.State.TERM && cubeFiber.state
        != Fiber.State.TERM)
    {
        write(squareResult, " ", cubeResult);
        squareFiber.call();
        cubeFiber.call();
        write("\n");
    }

    // La fibra `squareFiber` podría estar
    // ejecutándose, ya que todavía no ha
    // terminado.
}
```
