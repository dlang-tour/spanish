# Mutabilidad

D es un lenguaje de tipado estático: una vez que una variable ha sido declarada,
su tipo no se puede cambiar desde ese momento en adelante. Esto permite al
compilador evitar errores de forma temprana e imponer restricciones en tiempo
de compilación. Una buena seguridad en los tipos de datos proporciona el apoyo
que se necesita para crear grandes programas más seguros y más mantenibles.

Hay varios elementos para calificar los de tipos de datos en D, pero los más
comunes son `const` e `inmmutable`.

### `immutable`

Junto con el sistema de tipado estático, D proporciona calificadores de tipos
(*type qualifiers* en inglés), a veces llamados constructores de tipos, que
obligan a cumplir restricciones en ciertos objectos. Por ejemplo, un objeto
`immutable` puede ser inicializado sólo una vez y, después de esta inicialización,
ya no puede ser modificado.

    immutable int err = 5;
    // o: immutable err = 5 y su tipo, int, se infiere.
    err = 5; // no compila

Los objetos marcados con `immutable` pueden así ser puestos de forma segura en
memoria compartida entre varios hilos sin necesidad de sincronización ya que
estos nunca van a cambiar por definición. Esto también implica que los objetos
marcados con `immutable` pueden ser puestos perfectamente en memorias caché.

### `const`

Los objetos marcados con `const` tampoco pueden ser modificados. Pero esta
restricción sólo se aplica al entorno actual. Un puntero marcado con `const`
puede ser creado tanto desde un objeto *mutable* como de uno `immutable`.
Esto significa que el objeto es `const` en el ámbito actual, pero alguien
podría modificarlo desde un contexto diferente. Es común que las API acepten
argumentos marcados con `const` para asegurarse que no se modifican dentro de
la función, pero dicha función puede aceptar argumentos tanto de objetos 
mutables como `immutable`s.

    void foo(const char[] s) {
        // si la siguiente línea no estuviera comentada, la
        // compilación daría un error (no se puede modificar const):
        // s[0] = 'x';

        import std.stdio : writeln;
        writeln(s);
    }

    // gracias a `const`, ambas llamadas compilan:
    foo("abcd"); // string (cadena de caracteres) es un array inmutable
    foo("abcd".dup); // .dup devuelve una copia mutable

Tanto `immutable` como `const` son calificadores _transitivos_, es decir, cuando
`const` se aplica a un tipo de datos, este se aplica recursivamente a cada
subcomponente de dicho tipo.

### En profundidad

#### Referencias básicas

- [Immutable in _Programming in D_](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Scopes in _Programming in D_](http://ddili.org/ders/d.en/name_space.html)

#### Referencias avanzadas

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Type qualifiers in D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main() {
    /**
    * Las variables con mutables de forma
    * predeterminada y editarlas está
    * permitido:
    */
    int m = 100; // mutable
    writeln("m: ", typeof(m).stringof);
    m = 10; // bien

    /**
    * Apuntando a memoria mutable:
    */
    // Un puntero constante a memoria mutable
    // está permitido
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // Por definición, `const` no se puede
    // modificar:
    // *cm = 100; // error

    // Como se garantiza que `immutable` va
    // a permanecer sin cambios, este no puede
    // apuntar a memoria mutable
    // immutable int* im = &m; // error

    /**
    * Apuntando a memoria de sólo lectura:
    */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // error

    // `const` puede apuntar a memoria de sólo
    // lectura, pero también es de sólo lectura
    const int* cv = &v;
    writeln("*cv: ", typeof(cv).stringof);
    // *cv = 10; // error
}
```
