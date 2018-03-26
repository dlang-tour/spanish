# Subtipado

Las estructuras no pueden heredar de otras estructuras. Pero para lograr
extender la funcionalidad de dichas estructuras, D proporciona un método
para lograrlo: subtipado (*subtyping* en inglés).

Una estructura puede definir uno de sus miembros mediante las palabras
reservadas `alias this`:

    struct SafeInt {
        private int theInt;
        alias theInt this;
    }

Cualquier función u operación que trabaje sobre la estructura `SafeInt` y no
pueda ser manejada por este tipo de datos es enviada al miembro indicado en
el `alias this`. Desde fuera, la estructura `SafeInt` parecerá como si fuese
un número entero normal.

Esto permite extender otros tipos de datos con nueva funcionalidad pero con
cero sobrecarca en términos de memoria o tiempo de ejecución. El compilador se
asegura de realizar las operaciones correctas cuando se acceder a los miembros
marcados con `alias this`.

La construcción `alias this` también funciona con clases.

## {SourceCode}

```d
import std.stdio : writeln;

struct Point {
    private double[2] p;
    // `p` lo usa el compilador para
    // buscar símbolos desconocidos.
    alias p this;

    double dot(Point rhs) {
        return p[0] * rhs.p[0]
             + p[1] * rhs.p[1];
    }
}

void main() {
    Point p1, p2;
    // Básicamente se accede a un `double[2]`.
    p1 = [2, 1], p2 = [1, 1];
    assert(p1[$ - 1] == 1);

    // Pero con funcionalidad extendida.
    writeln("p1 dot p2 = ", p1.dot(p2));
}
```
