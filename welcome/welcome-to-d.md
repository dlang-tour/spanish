# Bienvenido a D

Bienvenido al tour interactivo del *lenguaje de programación D*.

{{#dmanmobile}}

Este tour ofrece una visión general de este __potente__ y __expresivo__
lenguaje que compila directamente a código de máquina __nativo__ y __eficiente__.

{{/dmanmobile}}

### ¿Qué es D?

D es la culminación de décadas de experiencia implementando __compiladores__
para muchos y diversos lenguajes teniendo este un largo número de 
[características únicas](http://dlang.org/overview.html):

{{#dmandesktop}}

- constructores de _alto nivel_ para una gran potencia de modelado
- lenguaje compilado de alto rendimiento
- tipos estáticos
- interfaz directa con las API del sistema operativo y del hardware
- tiempos de compilación asombrosamente rápidos
- manejo seguro de memoria (SafeD)
- código mantenible y fácilmente legible
- curva de aprendizaje gradual (misma sintaxis que C, Java y otros)
- compatible con la interfaz binaria de aplicaciones de C
- compatible parcialmente con la interfaz binaria de aplicaciones de C++
- multiparadigma: imperativo, estructurado, orientado a objetos, tipos genéricos, pureza en la programación funcional e, incluso, ensamblador
- prevención nativa de errores (contratos, unittests)

...y muchas más [características](http://dlang.org/overview.html).

{{/dmandesktop}}

### Acerca de este tour

Cada sección contiene un código fuente ejemplo que puede ser modificado y usado 
para experimentar con las características del lenguaje de programación D.
Haz clic en el botón *run* (o presiona `Ctrl-Enter`) para compilar y ejecutar.

Para navegar por este tour usa los enlaces “< Anterior” y “Siguiente >” que hay
al final de esta página (o usa las flechas izquierda y derecha del teclado).
O, si quieres ir a una sección en concreto, puedes usar el menú superior.

### Cómo contribuir

Este tour es [código abierto](https://github.com/dlang-tour) y agradecemos
las aportaciones con las que colaboren para hacer este tour aún mejor.

## {SourceCode}

```d
import std.stdio;

// ¡Empecemos!
void main() {
    writeln("¡Hola, mundo!");
}
```
