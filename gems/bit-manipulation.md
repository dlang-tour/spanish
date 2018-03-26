# Manipulación de bits

Un ejemplo excelente de la habilidad de D para generar código en tiempo de
compilación mediante _mixins_ es la manipulación de bits.

### Manipulación simple de bits

D ofrece los siguientes operadores para la manipulación a nivel de bit:

- `&`: 'y' a nivel de bit.
- `|`: 'o' a nivel de bit.
- `~`: negación a nivel de bit.
- `<<`: desplazamiento a la izquierda a nivel de bit.
- `>>`: desplazamiento a la derecha a nivel de bit manteniendo el signo de bit de mayor orden.
- `>>>`: desplazamiento a la derecha a nivel de bit sin mantener el signo.

### Un ejemplo práctico

Un ejemplo común de la manipulación de bits es la lectura de un valor de un
bit concreto. D proporciona la función `core.bitop.bt` para las tareas más
comunes, sin embargo, para acostumbrarse a la manipulación de bits, se va
a mostrar una implementación detallada de cómo se comprueba el valor de un bit:

```d
enum posA = 1;
enum maskA = (1 << posA);

bool getFieldA() {
    return _data & maskA;
}
```

Una generalización es comprobar bloques que sean mayores de 1. Para ello se
necesita una máscara especial con la longitud del bloque para luego
desplazar dicho bloque según sea necesario antes de aplicar dicha máscara:

```d
enum posA = 1;
enum lenA = 3;
enum maskA = (1 << lenA) - 1; // ...0111
uint getFieldA() {
    return (_data >> posA) & maskA;
}
```

La configuración de un bloque se puede definir de forma equivalente negando
la máscara para luego permitir únicamente escrituras dentro del bloque
especificado.

```d
void setFieldA(bool b) {
    return (_data & ~maskAWrite) | ((b << aPos) & maskAWrite);
}
```

## El módulo `std.bitmanip` al rescate

Es muy divertido escribir uno mismo el código para realizar manipulación de
bits, y D proporciona las herramientas para ello. Sin embargo en muchos casos 
no se quiere hacer un copia/pega de este código de manipulación de bits ya que
es muy propenso a errores además de difícil de mantener. Es por esto que D
cuenta con el módulo `std.bitmanip` y la potencia de los _mixins_, y todo ello
sin sacrificar el rendimiento.

Un ejemplo de cómo funcionan las funciones de este módulo se puede ver en el
ejercicio de esta sección, donde se define un `BitVector` que todavía usa X
bits y es prácticamente indistinguible de una estructura normal.

Los módulos `std.bitmanip` y `core.bitop` contienen más funciones que son de
gran ayuda para aplicaciones que requiran bajo consumo de memoria.

### Relleno y alineación

Como el compilador añade relleno a variables con un tamaño menor que el diseño
de la memoria del sistema operativo (`size_t.sizeof`), como por ejemplo con los
tipos `bool`, `byte` y `char`, se recomienda empezar con los campos de mayor
tamaño para facilitar el alineamiento.

## En profundidad

- [std.bitmanip](http://dlang.org/phobos/std_bitmanip.html) - Bit-level manipulation facilities
- [_Bit Packing like a Madman_](http://dconf.org/2016/talks/sechet.html)

## {SourceCode}

```d
struct BitVector {
    import std.bitmanip : bitfields;
    // Se crea un campo privado con los
    // siguientes campos y tamaños:
    mixin(bitfields!(
        uint, "x",    2,
        int,  "y",    3,
        uint, "z",    2,
        bool, "flag", 1));
}

void main() {
    import std.stdio : writefln, writeln;

    BitVector vec;
    vec.x = 2;
    vec.z = vec.x - 1;
    writefln("x: %d, y: %d, z: %d",
              vec.x, vec.y, vec.z);

    // Sólo se usan 8 bits (1 byte).
    writeln(BitVector.sizeof);

    struct Vector { int x, y, z; }
    // 4 bytes (int) por variable
    writeln(Vector.sizeof);

    struct BadVector {
        bool a;
        int x, y, z;
        bool b;
    }
    // Debido al relleno se usan 4 bytes
    // para cada campo.
    writeln(BadVector.sizeof);
}
```
