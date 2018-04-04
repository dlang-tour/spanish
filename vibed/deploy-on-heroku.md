# Despliegue en Heroku

### Requerimientos previos

- [Cuenta en Heroku](https://signup.heroku.com/login).
- Tener instalado [Git](https://git-scm.com/).
- La aplicación tiene que compilar sin errores usando `dmd` (`dub build`).

### Paso 1: configuración de la aplicación

Heroku necesita conocer cómo comunicarse con las aplicaciones desplegadas.
Es por esto que Heroku proporciona una variable global llamada `PORT` que
necesita ser inyectada en la aplicación y esta debe escuchar en el puerto
indicado en la variable. Para desarrollo se debe indicar un puerto
predeterminado (aquí es el **8080**):

```d
shared static this() {
  // ...
  auto settings = new HTTPServerSettings;
  // Puerto predeterminado en el caso de que $PORT no esté asignada.
  settings.port = environment.get("PORT", "8080").to!ushort;
  listenHTTP(settings, router);
}
```

Además hay que crear un archivo `Procfile`, que es un archivo de texto en el
directorio raíz de la aplicación que explícitamente declara qué comando se
tiene que ejecutar para arrancar la aplicación.

El archivo `Procfile` de este ejemplo es algo como:

```
web: ./hello-world
```

### Paso 2: preparación de la aplicación

Antes de avanzar hay que hacer *login* en la línea de comandos de Heroku usando
las herramientas de [Heroku Toolbelt](https://toolbelt.heroku.com/standalone)

Esto proporciona acceso a la interfaz de línea de comandos de Heroku que se
puede usar para gestionar y escalar las aplicaciones y los *add-ons*.

Después de instalar las herramientas (*Heroku Toolbelt*) hay que ejecutar lo
siguiente:

```
$ heroku login
```

### Paso 3: creación de la aplicación

Para crear una aplicación hay que ir al [panel de control de Heroku](https://dashboard.heroku.com)
y seguir los pasos para crear dicha aplicación. Hay que recordar el nombre de
la aplicación creada, ya que será útil más adelante.

También se puede usar la línea de comandos en lugar del panel de control para
crear una aplicación:

```
$ heroku create
Creating app... done, ⬢ rocky-hamlet-67506
https://rocky-hamlet-67506.herokuapp.com/ | https://git.heroku.com/rocky-hamlet-67506.git
```

Aquí el nombre de la aplicación es *rocky-hamlet-67506*.

### Despliegue usando Git

Para desplegar la aplicación se usan directamente comandos de `git`. Se debería
añadir un nuevo servidor remoto de Git donde se publiquen las versiones finales.
Este nuevo servidor remoto tiene el nombre de la aplicación indicado en el punto
anterior (en este caso es *rocky-hamlet-67506*).


```
$ heroku git:remote -a rocky-hamlet-67506
```

El nuevo remoto se añade a la configuración de Git:

```
$ git remote -v
heroku    https://git.heroku.com/rocky-hamlet-67506.git (fetch)
heroku    https://git.heroku.com/rocky-hamlet-67506.git (push)
```

### Añadiendo el *buildpack*

Los *buildpacks* son los responsables de generar recursos y/o código compilado.

Para obtener más información sobre los *buildpacks* se puede buscar en la
[documentación de Heroku](https://devcenter.heroku.com/articles/buildpacks).

Para desplegar el [*buildpack* de Vibe.d](https://github.com/MartinNowak/heroku-buildpack-d)
se usa el comando:

```
$ heroku buildpacks:set https://github.com/MartinNowak/heroku-buildpack-d
```

Este *buildpack* usa de forma predeterminada la última versión de compilador
de D. También es posible usar GCD, LDC o una versión específica de cualquiera
de los compiladores añadiendo el archivo `.d-compiler` al proyecto.

En este archivo se puede usar `dmd`, `ldc` o `gdc` para seleccionar la última
versión de cada compilador o se puede usar `dmd-2.0xxx`, `ldc-1.0xxx` o
`gdc-4.9xxx` para seleccionar versiones específicas de cada compilador.

### Despliegue del código

Para lanzar una nueva versión después de seguir los hábitos normales de
escritura de código usando Git, basta con hacer *push* de la última versión
al remoto de Heroku añadido en puntos anteriores.

```
$ git add .
$ git commit -am "My first vibe.d release"
$ git push heroku master
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 997 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)

-----> Fetching custom git buildpack... done
-----> D (dub package manager) app detected
-----> Building libevent
-----> Building libev
-----> Downloading DMD
-----> Downloading dub package manager
-----> Setting PATH:
-----> Initializing toolchain
-----> Building app
       Running dub build ...
Building configuration "application", build type release
Running dmd (compile)...
Compiling diet template 'index.dt' (compat)...
Linking...
       Build was successful
-----> Discovering process types
       Procfile declares types -> web
-----> Compiled slug size: 3.5MB
-----> Launching... done, v4
       https://rocky-hamlet-67506.herokuapp.com/ deployed to Heroku
To git@heroku.com:rocky-hamlet-67506.git
 * [new branch]      master -> master
```

Con el siguiente comando se abre la aplicación en el navegador:

```
$ heroku open
```

### Escalando contenedores *dynos*

Después del despliegue, la aplicación se está ejecutnado en la web *dyno*.
Se puede pensar en *dyno* como un contenedor ligero que ejecuta el comando
indicado en el archivo `Procfile`.

Usando el comando `ps` de Heroku se puede ver cuántos contenedores *dyno* se
están ejecutando:

```
$ heroku ps
Free dyno hours quota remaining this month: 550h 0m (100%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

No dynos on ⬢ rocky-hamlet-67506
```

De forma predeterminada la aplicación se despliega en un contenedor *dyno*
libre que no acepta peticiones. El contenedor *dyno* se pondrá en reposo
después de media hora sin actividad. Esto causa un retraso de varios segundos
en la primera petición ya que el contenedor tiene que despertar.

Para arrancar un contenedor *dyno* se ejecuta el siguiente comando:

```
$ heroku ps:scale web=1
```

### Ver los *logs*

Heroku trata los *logs* como conjuntos ordenadores por tiempo de eventos
agregados de todas las posibles salidas de *log* que existan en la aplicación
y en todos los componentes de Heroku, con lo que se propociona un único canal
para ver todos estos eventos.

```
$ heroku logs --tail
```

## Más información

Después de desplegar la aplicación en Heroku, esta se puede hacer más impresionante usando *add-ons*. Por ejemplo:

- [Postgresql](https://elements.heroku.com/addons/heroku-postgresql)
- [MongoDb](https://elements.heroku.com/addons/mongohq)
- [Logging](https://elements.heroku.com/addons#logging)
- [Caching](https://elements.heroku.com/addons#caching)
- [Error and exceptions](https://elements.heroku.com/addons#errors-exceptions)
