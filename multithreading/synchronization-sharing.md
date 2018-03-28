# Sincronización y uso compartido

Aunque el método preferido en D para usar multihilo es apoyarse en datos
inmutables (mediante la palabra reservada `immutable`) y sincronizar hilos
mediante paso de mensajes, el lenguaje soporta de forma nativa primitivas de
sincronización así como elementos que sirven para marcar objetos como
compartidos a los que se puede acceder desde múltiples hilos.

La palabra reservada `shared` sirve para indicar que una variable va a ser
compartida entre múltiples hilos de ejecución diferentes:

    shared(int)* p = new int;
    int* t = p; // ERROR

Por ejemplo, la función `std.concurrency.send` sólo permite enviar datos
marcados o con `immutable` o con `shared`, copiando el mensaje que se va a
enviar. La palabra reservada `shared` es transitiva, es decir, si una clase
o un estructura se marcan con `shared`, todos sus miembros serán marcados
de la misma forma. Hay que tener en cuenta que las variables estáticas (con
`static`) no se comparten (con `shared`) de forma predeterminada ya que la
implementación usa TLS (*Thread Local Storage* en inglés) y cada hilo de
ejecución tiene su propia copia.

Los bloques marcados con la palabra reservada `synchronized` le indican al
compilador que dicho bloque es una sección crítica y que sólo un hilo de
ejecución puede acceder a ella al mismo tiempo.

    synchronized {
        importStuff();
    }

Dentro de las funciones miembro de clases, estos bloques se pueden limitar a
objetos con exclusión mutua (*mutexes* en inglés) mediante
`synchronized(member1, member2)` con el fin de reducir la contención. El
compilador de D inserta las secciones críticas de forma automática. Además,
se puede marcar una clase completa con `synchronized`, con lo que el compilador
se asegurará de que sólo un hilo de ejecución al mismo tiempo tenga acceso
a una instancia concreta de dicha clase.

Se pueden usar operaciones atómicas en variables marcadas como `shared`
mediante la función `core.atomic.atomicOp`:

    shared int test = 5;
    test.atomicOp!"+="(4);

### En profundidad

- [Data Sharing Concurrency in _Programming in D_](http://ddili.org/ders/d.en/concurrency_shared.html)
- [`shared` type qualifier](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=11)
- [Lock-Based Synchronization with `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=13)
- [Deadlocks and `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=15)
- [`synchronized` specification](https://dlang.org/spec/statement.html#SynchronizedStatement)
- [Implicit conversions with `shared` data types](https://dlang.org/spec/const3.html#implicit_conversions)

## {SourceCode}

```d
import std.concurrency : receiveOnly, send,
    spawn, Tid, thisTid;
import core.atomic : atomicOp, atomicLoad;

/*
Clase que representa una cola que se puede
usar de forma segura en múltiples hilos.
Todos los accesos a una instancia concreta
se bloquean automáticamente gracias a la
palabra reservada `synchronized`.
*/
synchronized class SafeQueue(T) {
    // NOTA: Tiene que ser privado en clases
    // marcadas como `synchronized`, o el
    // compilador de D se queja.
    private T[] elements;

    void push(T value) {
        elements ~= value;
    }

    /// Devuelve T.init si la cola está vacía.
    T pop() {
        import std.array : empty;
        T value;
        if (elements.empty)
            return value;
        value = elements[0];
        elements = elements[1 .. $];
        return value;
    }
}

/*
Se imprimen mensajes independientemente
del número de hilos concurrentes. Los
parámetros variables se usan como
argumentos, es decir, debe haber entre
0 y N parámetros.
*/
void safePrint(T...)(T args) {
    // Sólo un hilo simultaneamente.
    synchronized {
        import std.stdio : writeln;
        writeln(args);
    }
}

void threadProducer(shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    import std.range : iota;
    // Añade valores de 1 a 11.
    foreach (i; iota(1,11)) {
        queue.push(i);
        safePrint("Añadido ", i);
        atomicOp!"+="(*queueCounter, 1);
    }
}

void threadConsumer(Tid owner,
    shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    int popped = 0;
    while (popped != 10) {
        auto i = queue.pop();
        if (i == int.init)
            continue;
        ++popped;
        // Se extrae de forma segura el valor
        // actual de queueCounter gracias a la
        // función `atomicLoad`.
        safePrint("Sacado ", i,
            " (El consumidor añadió ",
            atomicLoad(*queueCounter), ")");
    }

    // Listo.
    owner.send(true);
}

void main() {
    auto queue = new shared(SafeQueue!int);
    shared int counter = 0;
    spawn(&threadProducer, queue, &counter);
    auto consumer = spawn(&threadConsumer,
                    thisTid, queue, &counter);
    auto stopped = receiveOnly!bool;
    assert(stopped);
}
```
