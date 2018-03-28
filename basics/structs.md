# Estructuras (*structs*)

Una forma de definir tipos de datos compuestos o personalizados en D es
definirlos a través de una estructura mediante la palabra reservada `struct`.

    struct Person {
        int age;
        int height;
        float ageXHeight;
    }

Las `struct`s son siempre construidas en la pila (*stack*, en inglés; a no ser
que sean creadas mediante `new`) y son siempre copiadas **por valor** en
asignaciones o como parámetros en llamadas a funciones.

    auto p = Person(30, 180, 3.1415);
    auto t = p; // copy

Cuando un nuevo objeto de tipo `struct` se crea, todos sus miembros pueden ser
inicializados en el orden en que están definidos en dicha `struct`. Se puede
definir un constructor predeterminado a través del método `this(...)`. Si se
necesita evitar conflictos con los nombres de los parámetros, la instancia
actual de la estructura puede ser accedida mediante `this`.

    struct Person {
        this(int age, int height) {
            this.age = age;
            this.height = height;
            this.ageXHeight = cast(float)age * height;
        }
            ...

    Person p = Person(30, 180); // inicialización
    p = Person(30, 180);  // asignación a una nueva instancia

Una `struct` puede contener cualquier número de funciones miembro. Estos
métodos son públicos (`public`) de forma predeterminada y accesibles desde
cualquier sitio. Estos métodos podrían declararse como privados (`private`)
y sólo podría acceder a ellos otro método de la `struct` o código dentro
del mismo módulo.

    struct Person {
        void doStuff() {
            ...
        private void privateStuff() {
            ...

    p.doStuff(); // llamada al método doStuff
    p.privateStuff(); // prohibido

### Funciones miembro constantes

Si una función miembro es declarada usando `const`, no está permitido que
dicha función modifique ninguno de sus miembros. Esto lo fuerza el compilador.
Hacer una función miembro `const` hace que sólo se pueda llamar desde cualquier
objeto `const` o `immutable`; además se garantiza que los objetos que llaman a
dicha función nunca cambiarán el estado del objeto.

### Funciones miembro estáticas

Si una función miembro se declara como `static`, esta se podrá llamar sin
la necesidad de instanciar ningún objeto (por ejemplo: `Person.myStatic()`),
pero no está permitido acceder a miembros que no estén marcados como `static`.
Esto se puede usar cuando un método no necesita acceder a ningún miembro del
objeto pero que, evidentemente, pertenece a la misma clase. Además, esto se
puede usar para proporcionar funcionalidad sin tener que crear una instancia.
Por ejemplo, algunos patrones *Singleton* utilizan `static` para su
implementación.

### Herencia

Hay que tener en cuenta que una estructura no puede heredar de otras estructuras.
Las herencias entre tipos sólo se pueden construir usando clases, que se verán
en una sección futura. Sin embargo, mediante las sentencia `alias this` y/o
`mixins` fácilmente se puede conseguir herencia polimórfica.

### En profundidad

- [Structs in _Programming in D_](http://ddili.org/ders/d.en/struct.html)
- [Struct specification](https://dlang.org/spec/struct.html)

### Ejercicio

Dada la estructura `struct Vector3`, implementa las siguientes funciones y haz
que la aplicación de ejemplo se ejecute correctamente:

* `length()`: devuelve la longitud del vector.
* `dot(Vector3)`: devuelve el producto de dos vectores.
* `toString()`: devuelve una cadena representando este vector.
  La función [`std.string.format`](https://dlang.org/phobos/std_format.html)
  devuelve una cadena usando una sintaxis similar a la de `printf`:
  `format("MyInt = %d", myInt)`. Las cadenas de caracteres (*strings* en inglés)
  serán explicadas con más detalles en próximas secciones.

## {SourceCode:incomplete}

```d
struct Vector3 {
    double x;
    double y;
    double z;

    double length() const {
        import std.math : sqrt;
        // TODO: implementar la longitud
        return 0.0;
    }

    // rhs será copiado
    double dot(Vector3 rhs) const {
        // TODO: implementar el producto
        return 0.0;
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // Si una función miembro no tiene
    // parámetros se pueden omitir los
    // paréntesis de llamada: ()
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // Prueba de la funcionalidad del producto
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);
}
```
