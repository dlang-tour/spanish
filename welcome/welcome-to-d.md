# Bienvenido a D

Bienvenido al tour interactivo del *lenguaje de programación D*.

{{#dmanmobile}}

Este tour ofrece una visión general de este __potente__ y __expresivo__
lenguaje que compila directamente a código de máquina __nativo__ y __eficiente__.

{{/dmanmobile}}

### ¿Qué es D?

D es la culminación de décadas de experiencia implementando __compiladores__
para muchos diversos lenguajes, y tiene un lárgo número de 
[características únicas](http://dlang.org/overview.html):

{{#dmandesktop}}

- constructores de _alto nivel_ para un gran poder de modelado
- lenguaje compilado de _alto rendimiento_
- tipos estáticos
- evolución de C++ (sin errores)
- interfaz directa con la API del sistema operativo y el hardware
- tiempos de compilación extraordinarios
- permite el manejo seguro de memoria (SafeD)
- código _mantenible_ y _legible_
- curva de aprendizaje corta (sintaxis C, similar a Java y otros)
- compatible con la interfaz binaria de aplicaciones de C
- multi-paradigma (imperativo, estructurado, orientado a objetos, genérico, pureza de programación funcional, e incluso de ensamblador)
- prevención de errores incorporada (contratos, unittests)

... y muchas más [características](http://dlang.org/overview.html).

{{/dmandesktop}}

### Acerca del tour

Cada sección contiene un código fuente ejemplo que puede ser modificado y usado 
para experimentar con las propiedades del lenguaje D.
Haga clic en el botón run (o `Ctrl-enter`) para compilar y ejecutar.

### Contribuyendo

Este tour es [código abierto](https://github.com/dlang-tour) 
y estamos alegres de aportar solicitudes para mejorarlo.

## {SourceCode}

```d
import std.stdio;

// ¡Empecemos!
void main()
{
    writeln("¡Hola mundo!");
}
```
