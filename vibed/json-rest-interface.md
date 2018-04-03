# Interfaz REST usando JSON

Vibe.d permite implementar de forma rápida un servicio web JSON. Para
implementar la siguiente salida en JSON para una petición HTTP a la ruta
`/api/v1/chapters`:

    [
      {
        "title": "Hello",
        "id": 1,
        "sections": [
          {
            "title": "World",
            "id": 1
          }
        ]
      },
      {
        "title": "Advanced",
        "id": 2,
        "sections": []
      }
    ]

Lo primero que hay que hacer es definir una interfaz que implemente la función
`getChapters()` y una estructura en D que será serializada **1:1**:

    interface IRest {
        struct Section {
            string title;
            int id;
        }
        struct Chapter {
            string title;
            int id;
            Section[] sections;
        }
        @path("/api/v1/chapters")
        Chapter[] getChapters();
    }

Para rellear las estructuras con datos hay que heredar de la interfaz e
implementar la lógica de negocio:

    class Rest: IRest {
        Chapter[] getChapters() {
          // fill
        }
    }

Dada una instancia de la clase `URLRouter`, se registra una instancia de la
clase `Rest` y ¡listo!

    auto router = new URLRouter;
    router.registerRestInterface(new Rest);

El generador de interfaces REST de vibe.d también soporta peticiones `POST`,
donde los hijos de los objetos JSON enviados se mapean a parámetros de las
funciones miembro.

La interfaz REST se puede usar para generar una instancia de un cliente que
envía peticiones JSON de forma transparente al servidor indicado usando las
mismas funciones miembro que en la parte del servidor (*backend*). Esto es
útil cuando el código se comparte entre el cliente y el servidor.

    auto api = new RestInterfaceClient!IRest("http://127.0.0.1:8080/");
    // Envía una petición GET a /api/v1/chapters y deserializa la
    // respuesta a IRest.Chapter[].
    auto chapters = api.getChapters();

## {SourceCode:disabled}

```d
import vibe.d;

interface IRest {

    struct Section {
        string title;
        int id;
    }

    struct Chapter {
        string title;
        int id;
        Section[] sections;
    }

    @path("/api/v1/chapters")
    Chapter[] getChapters();

    /*
    Se envían los datos (POST) como:
        { "title": "Lenguaje D" }
    */
    @path("/api/v1/add-chapter")
    @method(HTTPMethod.POST)
    int addChapter(string title);
}

class Rest: IRest {
    private Chapter[] chapters_;

    this() {
        chapters_ = [ Chapter("Hello", 1,
                [ Section("World", 1) ] ),
                 Chapter("Advanced", 2) ];
    }

    Chapter[] getChapters() {
        return chapters_;
    }

    int addChapter(string title) {
        import std.algorithm : map, max, reduce;
        // Se genera el siguiente ID máximo.
        auto newId = chapters_.map!(x => x.id)
                            .reduce!max + 1;
        chapters_ ~= Chapter(title, newId);
        return newId;
    }
}

shared static this() {
    auto router = new URLRouter;
    router.registerRestInterface(new Rest);

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, router);
}
```
