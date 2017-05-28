# Imports y módulos

Para un sencillo programa "hola mundo" en D, se necesitan `import`s.
La sentencia `import` hace que todas las funciones y tipos públicos
del **módulo** indicado estén disponibles.

La biblioteca estándar, llamada [Phobos](https://dlang.org/phobos/),
está ubicada en el **paquete** `std` y sus módulos se referencian
usando `import std.MÓDULO`.

La sentencia `import` también puede ser usada para importar ciertos
símbolos de un módulo selectivamente:

    import std.stdio : writeln, writefln;

Los `import`s selectivos pueden ser usados para mejorar la legibilidad
pues hacen que sea obvio de dónde proviene un símbolo, y también como
una forma de prevenir que haya colisiones entre símbolos con el mismo
nombre que provienen de módulos diferentes.

Las sentencias `import` no necesitan estar únicamente al comienzo de
un archivo. También pueden ser usadas localmente dentro de funciones
o en cualquier otro ámbito.

## {SourceCode}

```d
void main()
{
    import std.stdio;
    // o import std.stdio : writeln;
    writeln("¡Hola Mundo!");
}
```
