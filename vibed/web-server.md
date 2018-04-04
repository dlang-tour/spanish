# Servidor web

Vibe.d permite escribir servidores web HTTP(S) en muy poco tiempo:

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, &foo);

Este código inicia un servidor web en el puerto 8080 donde todas las peticiones
son gestionadas por la función `foo`:

    void foo(HTTPServerRequest req,
        HTTPServerResponse res) { ... }

Para facilitar el uso típico y la configuración de diferentes rutas se
proporciona la clase `URLRouter`, que permite registrar funciones miembro para
gestionar las peticiones `GET`, `POST`, etc. de la forma `.get("path", handler)`,
`.post("path", handler)`, etc. Además, también permite registrar una clase
personalizada para gestionar la interfaz web implementando las rutas de las
URL como funciones miembro:

    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    listenHTTP(settings, router);

Las funciones de la clase `WebService` que se ejecutarán según las rutas
introducidas se deducen según un esquema muy simple:

* `index()` gestionará las peticiones a `/index`.
* `getName()` gestionará las peticiones `GET` a la ruta `/name`.
* `postUsername()` gestionará las peticiones `POST` a la ruta `/username`.

Además se pueden asignar rutas personalizadas a funciones mediante el atributo
`@path("/hola/mundo")`. Los parámetros de las peticiones `POST` están
disponibles dentro de la función en nombres de variables con el prefijo `_`.
Incluso es posible especificar parámetros directamente en la ruta:

    @path("/my/api/:id")
    void foo(int _id)

No se necesita pasar las clases `HTTPServerResponse` ni `HTTPServerRequest`
como parámetro a cada función. Vibe.d comprueba de forma estática si estos
parámetros están en la lista de parámetros de la función y las pasa si se
necesitan.

## {SourceCode:disabled}

```d
import vibe.d;

class WebService {
    /*
    Al usar variables de sesión como esta,
    se puede guardar información individual
    de usuarios entre peticiones mientras
    dure la sesión de dicho usuario.
    */
    private SessionVar!(string, "username")
        username_;

    /*
    Las peticiones a la ruta raíz ("/") se
    redirigen de forma predeterminada al
    método `index`.
    */
    void index(HTTPServerResponse res)
    {
        auto contents = q{<html><head>
            <title>¡Dime!</title>
        </head><body>
        <form action="/username" method="POST">
        Tu nombre:
        <input type="text" name="username">
        <input type="submit" value="Submit">
        </form>
        </body>
        </html>};

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    /*
    El atributo @path se puede usar para
    personalizar las rutas. En este caso,
    las peticiones a "/name" se envían al
    método `getName`.
    */
    @path("/name")
    void getName(HTTPServerRequest req,
            HTTPServerResponse res)
    {
        import std.string : format;

        // Se generan etiquetas <li>
        // mirando en las cabeceras de
        // la petición.
        string[] headers;
        foreach(key, value; req.headers) {
            headers ~=
                "<li>%s: %s</li>"
                .format(key, value);
        }
        auto contents = q{<html><head>
            <title>¡Dime!</title>
        </head><body>
        <h1>Tu nombre: %s</h1>
        <h2>Cabeceras</h2>
        <ul>
        %s
        </ul>
        </body>
        </html>}.format(username_,
                headers.join("\n"));

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    void postUsername(string username,
            HTTPServerResponse res)
    {
        username_ = username;
        auto contents = q{<html><head>
            <title>¡Dime!</title>
        </head><body>
        <h1>Tu nombre: %s</h1>
        </body>
        </html>}.format(username_);

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }
}

void helloWorld(HTTPServerRequest req,
        HTTPServerResponse res)
{
    res.writeBody("Hola");
}

void main()
{
    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    router.get("/hello", &helloWorld);

    auto settings = new HTTPServerSettings;
    // Se necesita para usar SessionVar.
    settings.sessionStore =
        new MemorySessionStore;
    settings.port = 8080;
    listenHTTP(settings, router);
    runApplication();
}
```
