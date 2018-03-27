# Atributos

En D, las funciones se pueden marcar con atributos de varias formas. En este
capítulo se verán dos atributos propios del lenguaje así como atributos
definidos por el usuario. Además, están los atributos `@safe`, `@system` y
`@trusted` que se vieron en el primer capítulo.

### El atributo `@property`

Una función marcada con el atributo `@property` se ve como un miembro normal
fuera del contexto donde es declarada:

    struct Foo {
        @property bar() { return 10; }
        @property bar(int x) { writeln(x); }
    }

    Foo foo;
    writeln(foo.bar); // Se llama a `foo.bar()`.
    foo.bar = 10; // Se llama a `foo.bar(10)`.

### El atributo `@nogc`

Cuando el compilador de D encuentra una función marcada con el atributo
`@nogc` se asegura de que **no hay ninguna** asignación de memoria usando el
recolector de basura (*garbage collector*) dentro del contexto de dicha
función. Sólo se permite que una función `@nogc` llame a otras funciones
marcadas con `@nogc`.

    void foo() @nogc {
        // ERROR:
        auto a = new A;
    }

### Atributos definidos por el usuario (UDA)

En D, cualquier función o tipo de datos se puede marcar con atributos definidos
por el usuario (*User-Defined Attributes*, UDA, en inglés):

    struct Bar { this(int x) {} }

    struct Foo {
      @("Hola") {
          @Bar(10) void foo() {
            ...
          }
      }
    }

Cualquier tipo, elemento básico o elemento definido por el usuario puede ser
usado como atributo de funciones. La función `foo()` en el ejemplo anterior
tiene los atributos `"Hola"` (de tipo `string`) y `Bar` (de tipo `Bar` y cuyo
valor es 10). Para obtener los atributos de una función (o tipo de datos) se
usan los *rasgos* (*traits* en inglés) internos al compilador mediante la
sintaxis `__traits(getAttributes, Foo)`. Esta función devuelve un
[`AliasSeq`](https://dlang.org/phobos/std_meta.html#AliasSeq).

Los atributos definidos por el usuario (UDA) permiten mejorar el código
genérico dando a los tipos de datos definidos por el usuario otra dimensión
que ayuda a los generadores en tiempo de compilación a adaptar el código
generado para dicho tipo específico.

### En profundidad

- [User Defined Attributes in _Programming in D_](http://ddili.org/ders/d.en/uda.html)
- [Attributes in D](https://dlang.org/spec/attribute.html)
