# Arrays

Hay dos tipos de arrays en D: **estáticos** y **dinámicos**. Al acceder a arrays
de cualquier tipo en D siempre se comprueban los límites (excepto cuando el
compilador se asegura de que esta comprobación no es necesaria).
Si esta comprobación de límites falla, se lanza una excepción de tipo `RangeError`
que aborta la ejecución de la aplicación. El programador valiente puede desactivar
esta comprobación de seguridad mediante la opción `-boundscheck=off` del compilador
con el fin de ganar mejoras en la velocidad de ejecuión a costa de la seguridad.

#### Arrays estáticos

Los arrays estáticos se guardan en la pila (*stack* en inglés) si se definen
dentro de una función, o en la memoria estática en caso contrario. Estos
arrays tienen una longitud fija conocida en tiempo de compilación. Un array
estático incluye el tamaño:

    int[8] arr;

El array `arr` es de tipo `int[8]`. Hay que darse cuenta de que el tamaño del
array se one al lado del tipo y no después del nombre de la variable como se
hace en C/C++.

#### Arrays dinámicos

Los arrays dinámicos se guardan en el *heap* y pueden ser expandidos o
contraídos en tiempo de ejecución. Un array dinámico se crea mediante la
expresión `new` y su longitud:

    int size = 8; // variable en tiempo de ejecución
    int[] arr = new int[size];

El tipo de `arr` es `int[]`, que también es llamado ***slice***. Los *slices*
son vistas de un bloque continuo de memoria y serán explicados en más detalle
en la [siguiente sección](basic/slices). Arrays multidimensionales puede ser
creados fácilmente usando la sintaxis `auto arr = new int[3][3]`.

#### Operaciones y propiedades de los arrays

Los arrays pueden ser concatenados usando el operador `~`, operación que
creará un nuevo array dinámico.

Operaciones matemáticas pueden ser aplicadas a los arrays completos usando,
por ejemplo, la sintaxis `c[] = a[] + b[]`. Esta operación añade todos los
elementos de `a` y `b` de la forma `c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`,
etc. Además, también es posible ejecutar operaciones en todo el array con un
valor único:

    a[] *= 2; // multiplica todos los elementos por 2
    a[] %= 26; // calcula el módulo entre 26 de todos los elementos de a

Estas operaciones pueden ser optimizadas por el compilador usando instrucciones
especiales del procesador que realizan estas operaciones en paralelo.

Tanto los arrays estáticos como dinámicos tienen la propiedad `.length`, que
es una propiedad de sólo lectura en los arrays estáticos, pero que puede ser
usada en los arrays dinámicos para cambiar su tamaño de forma dinámica. La
propiedad `.dup` crea una copia del array.

Cuando se indexa un array a través de la sintaxis `arr[idx]`, se puede usar
el símbolo especial `$` que denota la longitud del array. Por ejemplo,
`arr[$ - 1]` referencia el último elemento del array y es una forma rápida
de escribir `arr[arr.length - 1]`.

### Ejercicio

Completa la función `encrypt` para cifrar un mensaje secreto. El texto debería
ser encriptado usando el método César (*Caesar encryption*), que mueve cada
caracter del alfabeto según cierto índice. El texto a ser cifrado sólo contiene
caracteres en el rango `a-z` para hacer las cosas más sencillas.

Aquí se puede ver [la solución](https://github.com/dlang-tour/core/issues/227).

### En profundidad

- [Arrays in _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [D Slices](https://dlang.org/d-array-article.html)
- [Array specification](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Mueve cada caracter en el
array `input` `shift` caracteres.
Los caracteres están limitados al rango
`a-z` y el caracter siguiente a z es a.

Parámetros:
    input = array para ser cifrado
    shift = shift distancia a mover cada
        caracter
Devuelve:
    El array con los caracteres movidos
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // TODO: mover cada caracter
    return result;
}

void main()
{
    // Encriptamos el mensaje mediante el
    // método César con un factor de 16
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // La última coma (,) está bien
      // y simplemente se ignora
    ];
    writeln("Antes: ", toBeEncrypted);
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("Después: ", encrypted);

    // Nos aseguramos del que el algoritmo
    // funciona como se espera
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```
