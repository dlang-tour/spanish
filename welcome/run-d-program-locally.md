# Ejecutar un programa en D de forma local

D viene con un compilador, `dmd`, una herramienta para ejecutar código fuente
como si fueran __scripts__, `rdmd`, y un gestor de paquetes, `dub`.

### El compilador DMD

El compilador *DMD* compila archivos de D y crea un archivo binario. La línea de
comandos puede ser invocada con el nombre del archivo a compilar:

    dmd hello.d

Existen varias opciones que permiten modificar el comportamiento del compilador *DMD*.
Estas opciones se pueden ver en la [documentación en línea](https://dlang.org/dmd.html#switches)
o ejecutando `dmd --help` en la línea de comandos.

### Compilación __al vuelo__ con `rdmd`

La herramienta `rdmd`, distribuida con el compilador DMD, compila de forma automática todas las
dependencias de un archivo y ejecuta directamente la aplicación resultante:

    rdmd hello.d

En sistemas UNIX se puede usar el _shebang_ `#!/usr/bin/env rdmd` en la primera línea de un
archivo de D para permitir que este se comporte como un _script_.

Se pueden ver las opciones que acepta `rdmd` en la [documentación en línea](https://dlang.org/rdmd.html)
o ejecutando `rdmd --help` desde la línea de comandos.

### El gestor de paquetes `dub`

El gestor de paquetes estándar de D es [`dub`](http://code.dlang.org). Cuando `dub` está
instalado de forma local, se puede crear un nuevo proyecto llamado `hello` ejecutando la
siguiente línea de comandos:

    dub init hello

Ejecutar `dub` dentro de este directorio descargará todas las dependencias, compilará la
aplicación y la ejecutará. `dub build` compilará el proyecto.

Se puede obtener más información sobre `dub`en la [documentación en línea](https://code.dlang.org/docs/commandline)
o ejecutando `dub help` en la línea de comandos.

Todos los paquetes disponibles usando `dub` se pueden ver a través de [su interfaz web](https://code.dlang.org).
