# Almacenamiento local de los hilos de ejecución

La palabra reservada `static`, que modifica la forma en cómo se guardan las
variables, permite declarar objetos que son inicializados solamente una vez.
Si la misma línea de código se ejecuta una segunda vez, la inicialización se
omite.

Cada hilo de ejecución tiene sus propios objetos estáticos que se guardan
en el almacenamiento local al hilo (*Thread Local Storage*, TLS, en inglés),
y este hilo no puede leer ni modificar los objetos estáticos de otros hilos,
aunque el nombre de las variables siempre sea el mismo. En consecuencia,
mediante `static` se declaran objetos que mantienen un estado que es global
pero sólo para el hilo actual.

Esto es diferente a cómo funciona `static` en C/C++ y Java, donde los objetos
declarados como estáticos son globales a toda la aplicación, no sólo al hilo
actual. Esto implica problemas de sincrinización en aplicaciones multihilo.

El valor asignado a variables declaradas como `static` tiene que ser evaluable
en tiempo de compilación, es decir, no debe tener dependencias en tiempo de
ejecución. Es posible inicializar variables estáticas en tiempo de ejecución
mediante la expresión `static this()`, que es un constructor que se ejecuta
una sola vez para estructuras, clases y módulos.

    static int b = 42;
    // 'b' se inicializa sólo una vez.
    // Cuando se ejecuta desde diferentes hilos,
    // cada hilo ve su "propia" variable 'b'
    // sin interferir con otros hilos.

Para declarar una variable global “clásica”, es decir, una variable global
que puedan ver y modificar todos los hilos de ejecución, se usa la palabra
reservada `__gshared`, que es la equivalente a la palabra reservada `static`
de C. Este nombre tan feo es un recordatorio amistoso de que debería usarse
muy raramente.

    __gshared int b = 50;
    // Sólo se inicializa una vez.
    // La variable 'b' la pueden ver y modificar
    // (lo que hace que sea peligroso) todos los
    // hilos de ejecución.

### En profundidad

- [Thread-local storage on Wikipedia](https://en.wikipedia.org/wiki/Thread-local_storage)

## {SourceCode}

```d
import std.concurrency : spawn, thisTid;

void worker(bool firstTime) {
    import std.stdio : writeln;
    // 'threadState' es global pero sólo al
    // hilo actual. Ningún otro hilo puede
    // acceder a dicha variable. Hay que tener
    // en cuenta que la variable se inicializa
    // solamente la primera vez que se ejecuta
    // esa línea.
    static int threadState = 0;
    writeln("Thread ", thisTid,
        ": My state = ", threadState++);
    if (firstTime)
        worker(false);
}

void main() {
    // Se crean 5 hilos donde cada uno
    // llama a la función 'worker(true,i)'.
    // worker(true,i) each.
    for (size_t i = 0; i < 5; ++i) {
        spawn(&worker, true);
    }
}
```
