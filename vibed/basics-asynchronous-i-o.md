# Conceptos básicos y E/S asíncrona

Usando la configuración predeterminada a la hora de construir un proyecto,
la función `main()` de una aplicación de vibe.d se especifica mediante el
constructor especial `shared static this()` de los módulos:

    import vibe.d;
    shared static this() {
        // Vibe.d code here
    }

El constructor de un módulo se ejecuta antes de `main()` y solamente una vez.
Vibe.d proporciona su propia función `main()` y oculta todo el bucle de gestión
de eventos y todo el código de gestión del código de usuario.

Vibe.d usa fibras para implementar la E/S asíncrona: cada vez que una llamada
a un *socket* se bloquea —porque no existen datos que leer, por ejemplo—, la
fibra que se está ejecutando actualmente pausa su ejecución (mediante la
función `yield()`) permitiendo ejecutarse a otras fibras. Cuando ya existen
datos disponibles, la fibra reanuda la ejecución:

    // Esto se podría bloquear, pero es transparente.
    // Si un socket está disponible, vibe.d se encarga
    // de volver aquí.
    line = connection.readLine();
    // También se podría bloquear.
    connection.write(line);

Además, el código parece que se ejecuta de forma síncrona y que bloquearía el
hilo actual, ¡pero no es así! El código parece más limpio y conciso aunque
usa la potencia de la E/S asíncrona permitiendo miles de conexiones por
procesador.

Todas las características de vibe.d usan fibras para realizar las operaciones
sobre *sockets* de forma asíncrona, por lo que no hay que preocuparse por que
una simple y lenta conexión a MongoDB bloquee toda la aplicación.

En el siguiente ejemplo se muestra cómo implementar un sencillo servidor de
eco (*echo server*) usando TCP.

## {SourceCode:disabled}

```d
import vibe.d;

shared static this() {
    // Escucha en el puerto 8080 (TCP).
    // Cada vez que llega una nueva
    // conexión, esta se gestiona mendiante
    // un `delegate` que se especifica en el
    // segundo parámetro. El parámetro `conn`
    // es el objeto que representa la conexión
    // TCP al cliente.
    listenTCP(8080, (TCPConnection conn) {
        string line;
        conn.write("El servidor ECHO dice " ~
            "¡Hola!\r\n");
        conn.write("Escribe 'quit' para " ~
            "cerrar.\r\n");
        while (line != "quit") {
            line = cast(string) conn.readLine();
            conn.write("ECHO: " ~ line
              ~ "\r\n");
        }

        // Aquí termina la ejecución del
        // `delegate`, con lo que se cierra
        // la conexión al cliente.
    });
}
```
