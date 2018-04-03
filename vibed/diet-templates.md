# Plantillas DIET

Para hacer más fácil la escritura de páginas web, vibe.d soporta
[plantillas DIET](https://vibed.org/templates/diet), que es una forma
de escribir páginas HTML mediante sintaxis simplificada. Las plantillas DIET
están basadas en las [plantillas Jade](http://jade-lang.com/).

    doctype html
    html(lang="en")
      head
        // Se evalúa el código en D.
        title #{pageTitle}
        // Atributos.
        script(type='text/javascript')
          if (foo) bar(1 + 5)
        // ID = body-id
        // style = the-style
        body#body-id.the-style
          h1 Plantilla DIET

La sintaxis es sensible a la indentación y no es necesario poner las
etiquetas de cierre.

Todas las plantillas DIET se compilan y se mantienen en memoria para obtener
el máximo rendimiento. Además, estas plantillas permiten usar código en D
que se evalúa cuando se genera la página. Expresiones simples en D se encierran
entre `#{` y `}` (por ejemplo `#{ 1 + 1 }`) y se pueden poner en cualquier
parte dentro de la plantilla. Las líneas completas de código en D se marcan con
el prefijo `-` en cada una de ellas:

    - foreach(title; titles)
      h1 #{title}

También se pueden usar expresiones complejas de la misma forma, e incluso se
pueden definir funciones que se pueden usar para generar HTML final.

Las plantillas DIET se compilan usando **CTFE** y tienen que estar en el
directorio `views` de los proyectos vibe.d estándar. Para generar el HTML de
una plantilla DIET se usa la función `render` dentro de la función de gestión
de la URL:

    void foo(HTTPServerResponse res) {
        string pageTitle = "Hola";
        int test = 10;
        res.render!("my-template.dt", pageTitle, test);
    }

Todas las variables de D disponibles dentro de una plantilla DIET se pasan como
parámetros de plantilla a la función `render`.

## {SourceCode:disabled}

```d
doctype html
html
  head
    title Prueba de declaración de D
  body
    - import std.algorithm : min;
    p Cuatro elementos a continuación:
    - foreach( i; 0 .. 4 )
      - auto num = i+1;
      p Elemento
        b= num
    p Imprime 8:
    p= min(10, 2*6, 8)
```
