# Memoria

D es un lenguaje de programación de sistemas por lo que permite gestión de
memoria manual. Sin embargo, gestionar la memoria de forma manual es muy
propenso a errores, por lo que D usa de forma predeterminada un recolector
de basura (*garbage collector* o *GC* en inglés) para gestionar la memoria.

D tiene punteros, `T*`, igual que en C:

    int a;
    int* b = &a; // b contiene la dirección de memoria de a
    auto c = &a; // c es int* y contiene la dirección de memoria de a

Para asignar un nuevo bloque de memoria en el *heap* se usa la expresión
`new`, que devuelve un puntero a la memoria reservada:

    int* a = new int;

Tan pronto como la memoria referenciada por `a` deja de estarlo, es decir, no es
accesible a través de ninguna variable del programa, el recolector de basura
libera dicha memoria.

D tiene tres niveles de seguridad diferentes para las funciones: `@system`,
`@trusted` y `@safe`. Si no se especifica ninguno, `@system` es el predeterminado.
`@safe` es un subconjunto de D que evita errores de memoria por diseño.
El código marcado como `@safe` sólo puede llamar a otro código marcado como
`@safe` o como `@trusted`. Además, la aritmética de punteros explícita está
prohibida en el código marcado como `@safe`:

    void main() @safe {
        int a = 5;
        int* p = &a;
        int* c = p + 5; // error
    }

El código marcado como `@trusted` es verificado manualmente y permite un puente
entre el código marcado como `@safe` y el mundo de la programación de bajo nivel.

### En profundidad

* [SafeD](https://dlang.org/safed.html)

## {SourceCode}

```d
import std.stdio : writeln;

void safeFun() @safe
{
    writeln("Hola, mundo.");
    // asignar memoria mediante el GC es seguro
    int* p = new int;
}

void unsafeFun()
{
    int* p = new int;
    int* fiddling = p + 5;
}

void main()
{
    safeFun();
    unsafeFun();
}
```
