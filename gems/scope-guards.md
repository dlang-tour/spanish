# Protectores de ámbito (*scope guards*)

Los protectores de ámbito (*scope guards* en inglés) permiten ejecutar
sentencias al cumplirse ciertas condiciones cuando el bloque actual
termina:

* `scope(exit)`: se ejecuta siempre que termina la ejecución de un bloque.
* `scope(success)`: se ejecuta cuando un bloque finaliza su ejecución sin haber
  lanzado ninguna excepción.
* `scope(failure)`: se ejecuta cuando un bloque finaliza y se ha lanzado alguna
  excepción dentro del mismo.

Al usar protectores de ámbito se escribe código mucho más limpio, ya que
permite hacer reservas de recursos y la liberación de los mismos en líneas
muy cercanas. Estos pequeños ayudantes también mejoran la seguridad, ya que
se aseguran de que cierto código de limpieza se ejecute **siempre**,
independientemente de qué rutas de salida del bloque se hayan tomado en
tiempo de ejecución.

Esta característica, `scope`, de D reemplaza de forma efectiva el modismo
RAII (_Resource Acquisition Is Initialization_, en inglés) que usa C++, que a
veces lleva a tener protectores de ámbito especiales para recursos especiales.

Los protectores de ámbito se llaman en orden inverso a como son definidos.

### En profundidad

- [`scope` in _Programming in D_](http://ddili.org/ders/d.en/scope.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;

void main() {
    writeln("<html>");
    scope(exit) writeln("</html>");

    {
        writeln("\t<head>");
        scope(exit) writeln("\t</head>");
        "\t<title>%s</title>".writefln("Hola");
    } // La sentencia scope(exit) de la línea
      // anterior se ejecuta aquí.

    writeln("\t<body>");
    scope(exit) writeln("\t</body>");

    writeln("\t\t<h1>¡Hola, mundo!</h1>");

    // Los 'scope guards' permiten poner
    // reservas de memoria y su liberación
    // muy cerca una de la otra.
    import core.stdc.stdlib : free, malloc;
    int* p = cast(int*) malloc(int.sizeof);
    scope(exit) free(p);
}
```
