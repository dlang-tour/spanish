# Documentación

D intenta integrar partes importantes de la ingeniería de software moderna
dentro del propio lenguaje. Además de la programación por contrato y los
tests unitarios, D permite generar [documentación](https://dlang.org/phobos/std_variant.html)
fuera del código fuente de forma nativa.

Usando un esquema estándar para documentar tipos de datos y funciones, el
comando `dmd -D` genera convenientemente documentación en HTML basada en
los archivos de código fuente pasados en la línea de comandos. De hecho,
toda la [documentación de la librería estándar, Phobos](https://dlang.org/phobos),
está generada mediante este método, llamado ***DDoc***.

Los siguientes tipos de comentarios son considerados por el generador de
documentación, DDoc, como documentación para sacar fuera del código fuente:

* `/// Tres barras inclinadas antes de cualquier tipo o función`
* `/++ Comentarios multilínea comenzando por dos signos más (+)  +/`
* `/** Comentarios multilínea comenzando por dos asteriscos (*)  */`

Para ver cómo funciona la documentación estándar se puede echar un vistazo al
código fuente de ejemplo.

### En profundidad

- [DDoc design](https://dlang.org/spec/ddoc.html)
- [Phobos standard library documentation](https://dlang.org/phobos)

## {SourceCode:incomplete}

```d
/**
  Calcula la raíz cuadrada de un número.

  Aquí se puede poner un párrafo más largo
  que explica la gran victoria para la
  sociedad al tener una función que calcula
  la raíz cuadrada de un número.

  Ejemplo:
  -------------------
  double sq = sqrt(4);
  -------------------
  Params:
    number = el número del que se debe
             calcular la raíz cuadrada.

  Licencia: uso libre para cualquier
            propósito.
  Throws: no lanza excepciones.
  Returns: la raíz cuadrada de la entrada.
*/
T sqrt(T)(T number) {
}
```
