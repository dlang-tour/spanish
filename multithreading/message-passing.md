# Paso de mensajes

En lugar de trabajar directamente con hilos de ejecución y realizar todas las
tareas de sincronización uno mismo, D permite usar **paso de mensajes** como
un medio para aprovechar la potencia de varios procesadores. Los hilos pueden
comunicarse con mensajes, que no son más que valores arbitrarios, para
distribuir el trabajo y para sincronizarse entre ellos. Los hilos no comparten
datos por diseño, lo que evita los problemas más comunes de entornos multihilo.

Todas las funciones que implementan el paso de mensajes en D se pueden encontrar
en el módulo [`std.concurrency`](https://dlang.org/phobos/std_concurrency.html).
Por ejemplo, `spawn` crea un nuevo hilo de ejecución al que se le pasa la
función que ha de ejecutar:

    auto threadId = spawn(&foo, thisTid);

La variable `thisTid` es una variable interna al módulo `std.concurrency` que
hace referencia al hilo actual, necesario para realizar el paso de mensajes.
A la función `spawn` hay que pasarle otra función como primer parámetro, que
será la función que ejecute el hilo, mientras que el resto de parámetros son
los argumentos pasados a esta primera función.

    void foo(Tid parentTid) {
        receive(
          (int i) { writeln("An ", i, " was sent!"); }
        );

        send(parentTid, "Done");
    }

La función `receive` funciona de forma parecida a como lo hace la expresión
`switch..case`, despachando los valores que recibe desde otros hilos para
posteriormente pasar dichos valores a delegados (`delegate`s) dependiendo
del tipo de dato recibido.

Para enviar un mensaje a un hilo específico se usa la función `send` junto con
el ID del hilo de destino:

    send(threadId, 42);

La función `receiveOnly` se usa para recibir exclusivamente un tipo de dato:

    string text = receiveOnly!string();
    assert(text == "Done");

La familia de funciones `receive` se bloquean hasta que algún dato se envíe
al hilo que llama a dicha función.

### En profundidad

- [Exchanging Messages between Threads](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=5)
- [Messaging passing concurrency](http://ddili.org/ders/d.en/concurrency.html)
- [Pattern Matching with receive](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=6)
- [Multi-threaded file copying](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=7)
- [Thread Termination](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=8)
- [Out-of-Band Communication](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=9)
- [Mailbox crowding](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=10)

## {SourceCode}

```d
import std.stdio : writeln;
import std.concurrency : receive, receiveOnly,
    send, spawn, thisTid, Tid;

/*
Estructura personalizada que se usa como
mensaje para un pequeño ejército de hilos.
*/
struct NumberMessage {
    int number;
    this(int i) {
        this.number = i;
    }
}

/*
Esta estructura se usa como mensaje de
parada/cancelación para otros hilos.
*/
struct CancelMessage {
}

/// Confirmación de mensaje de cancelación.
struct CancelAckMessage {
}

/*
Esta es la función principal del hilo de
ejecución que toma como argumento el ID
del hilo padre.
*/
void worker(Tid parentId) {
    bool canceled = false;
    writeln("Iniciando ", thisTid, "...");

    while (!canceled) {
      receive(
        (NumberMessage m) {
          writeln("Recibido int: ", m.number);
        },
        (string text) {
          writeln("Recibida string: ", text);
        },
        (CancelMessage m) {
          writeln("Parando ", thisTid, "...");
          send(parentId, CancelAckMessage());
          canceled = true;
        }
      );
    }
}

void main() {
    Tid[] threads;
    // Se inician 10 hilos de ejecución.
    for (size_t i = 0; i < 10; ++i) {
        threads ~= spawn(&worker, thisTid);
    }

    // Los hilos impares reciben números,
    // los hilos pares reciben cadenas.
    foreach(int idx, ref tid; threads) {
        import std.string : format;
        if (idx  % 2)
            send(tid, NumberMessage(idx));
        else
            send(tid, format("T=%d", idx));
    }

    // Se envía el mensaje de parada a todos
    // los hilos de ejecución.
    foreach(ref tid; threads) {
        send(tid, CancelMessage());
    }

    // Finalmente, se espera hasta que todos
    // los hilos hayan recibido el mensaje
    // de parada.
    foreach(ref tid; threads) {
        receiveOnly!CancelAckMessage;
        writeln("Received CancelAckMessage!");
    }
}
```
