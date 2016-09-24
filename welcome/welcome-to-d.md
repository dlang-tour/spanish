# Bienvenido a D

Bienvenido al tour interactivo de *Lenguaje de programación D*.

{{#dmanmobile}}

Este tour ofrece una visión general de este __potente___ y __expresivo__
lenguaje que compila directamente a código máquina __nativo__, __eficiente__.

{{/dmanmobile}}

### ¿Qué es D?

D es la culminación de décadas de experiencia implementando compiladores_
para muchos diversos lenguajes, y tiene un lárgo número de 
[características únicas](http://dlang.org/overview.html):

{{#dmandesktop}}

- constructores de _alto nivel_ para un gran poder de modelado
- _alto rendimiento_, lenguaje compilado
- tipo estático
- evolución de C++ (sin errores)
- interfaz directa con el API del sistema operativo y el hardware
- tiempos de compilación extraordinarios
- permite el manejo seguro de memoria (SafeD)
- código _mantenible_, _legible_
- curva de aprendizaje corta (sintaxis C, similar a Java y otros)
- compatible con aplicaciones C de interfaz binaria
- multi-paradigma (imperativo, estructurado, orientado a objetos, genérico, pureza de programación funcional, e incluso de ensamblador)
- prevención de errores incorporada (contracts, unittests)

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

// ¡Vámonos!
void main()
{
    writeln("¡Hola mundo!");
}
```
