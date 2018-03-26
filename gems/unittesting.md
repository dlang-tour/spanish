# Tests unitarios

Los tests son una forma excelente de asegurarse de escribir aplicaciones
estables y libres de errores. Estos, además, sirven como documentación
interactiva permitiendo su modificación sin miedo a romper funcionalidad.
D, mediante la palabra reservada `unittest`, proporciona sintaxis nativa
como parte del lenguaje para crear bloques de tests unitarios. Los bloques
`unittest` puede aparecer en cualquier parte de los módulos de D para probar
funcionalidad.

    // Bloque para probar mi función.
    unittest {
        assert(myAbs(-1) == 1);
        assert(myAbs(1)  == 1);
    }

Esto permite realizar [_Test-driven development_](https://en.wikipedia.org/wiki/Test-driven_development)
de forma muy sencilla y bajo demanda.

### Ejecutar los tests unitarios

Los tests unitarios (bloques `unittest`) pueden contener cualquier tipo de
código. Este código se compila y se ejecuta cuando se pasa la opción
`-unittest` al compilador DMD. El gestor de paquetes de D, DUB, también
compila y ejecuta los tests unitarios mediante el comando `dub test`.

### Verificar ejemplos mediante `assert`

Los bloques `unittest` contiene típicamente expresiones `assert` para probar
la funcionalidad de una función dada. Estos bloques están generalmente cerca
de la definición de la función a probar, que puede ser al principio del código
o, incluso, dentro de clases o estructuras.

### Incrementar la cobertura del código

Los tests unitarios son una herramienta muy potente para crear aplicaciones
a prueba de bala. Una medida común para probar cuánto código fuente de un
programa está cubierto por los tests la _cobertura del código_ (***code
coverage*** en inglés). El compilador DMD permite generar de forma sencilla
informes de cobertura de código mediante la opción `-cov`. Para cada módulo
se genera un archivo con extensión `.lst` que contiene estadísticas detalladas
sobre dicha cobertura.

Como el compilador es capaz de inferir atributos para código que usa plantillas
de forma automática, los tests unitarios se suelen marcar con dichos atributos
para asegurarse de que se cumplen en el código probado:

    @safe @nogc nothrow pure unittest {
        assert(myAbs() == 1);
    }

### En profundidad

- [Unit Testing in _Programming in D_](http://ddili.org/ders/d.en/unit_testing.html)
- [Unittesting in D](https://dlang.org/spec/unittest.html)

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    double x;
    double y;
    double z;

    double dot(Vector3 rhs) const {
        return x*rhs.x + y*rhs.y + z*rhs.z;
    }

    // Está bien.
    unittest {
        assert(Vector3(1,0,0).dot(
          Vector3(0,1,0)) == 0);
    }

    string toString() const {
        import std.string : format;
        return format("x:%.1f y:%.1f z:%.1f",
          x, y, z);
    }

    // Y esto también.
    unittest {
        assert(Vector3(1,0,0).toString() ==
          "x:1.0 y:0.0 z:0.0");
    }
}

void main() {
    Vector3 vec = Vector3(0,1,0);
    writeln(`Este vector ha sido probado: `,
      vec);
}

/*
O en cualquier otro sitio. Nada de esto
se compila ni se incluye en código normal.
Se puede ejecutar `dub test` de forma
local o se puede compilar con `-unittest`
para ejecutar los tests unitarios y probar
los módulos.
*/
unittest {
    Vector3 vec;
    // '.init' es una propiedad interna que
    // devuelve el valor inicial de un tipo.
    assert(vec.x == double.init);
}
```
