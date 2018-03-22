# Instalar D de forma local

El compilador de referencia del lenguaje D se llama DMD (Digital Mars D).
También están disponibles [LDC](https://github.com/ldc-developers/ldc), un
compilador de D basado en [LLVM](http://llvm.org), y [GDC](https://gdcproject.org),
un compilador de D basado en [GCC](https://gcc.gnu.org/).
Para una información más detallada se puede visitar la [página de la wiki sobre los
compiladores de D](https://wiki.dlang.org/Compilers), pero si eres nuevo y no estás
seguro de cuál instalar, instala DMD.

## Descarga e instalación

En la [página de descargas de D](https://dlang.org/download.html) se encuentra un
resumen de las varias implementaciones de D. Además, contiene paquetes de DMD
específicos para cada sistema operativo listos para descargar e instalar.

Como alternativa a los paquetes específicos de cada sistema operativo existe además
un [__script__ de instalación](https://dlang.org/install.html) para usar con cualquier
sistema operativo compatible con POSIX (Linux, FreeBSD, Mac OS X) que puede instalar
las diferentes implementaciones (incluyendo varias versiones de los mismos) sin
requerir privilegios de administración. Puedes ver la [documentación de este script
de instalación](https://dlang.org/install.html) si necesitas más detalles.

## Configura tu editor

La belleza de D es que no se necesita un sofisticado entorno integrado de desarrollo (IDE)
ya que el código repetitivo es muy raro. Sin embargo, usar D es mejor cuando se está en una
zona confortable con el editor favorito.
Además, hay __plugins__ para desarrollar D al menos para los siguientes editores:

- [Atom](https://github.com/Pure-D/atomize-d)
- [Eclipse](http://ddt-ide.github.io)
- [Emacs](https://github.com/Emacs-D-Mode-Maintainers/Emacs-D-Mode)
- [IntelliJ](https://github.com/intellij-dlanguage/intellij-dlanguage)
- [Sublime Text](https://github.com/yazd/DKit)
- [Vim](https://wiki.dlang.org/D_in_Vim)
- [VS Code](https://marketplace.visualstudio.com/items/webfreak.code-d)
- [__Visual Studio__](http://rainers.github.io/visuald/visuald/StartPage.html)

Además, puedes querer probar un IDE dedicado específicamente a D:

- [Coedit](https://github.com/BBasile/Coedit)
- [Dlang IDE](https://github.com/buggins/dlangide)

La wiki de D contiene información detallada sobre los [editores disponibles](https://wiki.dlang.org/Editors) y los [IDE](https://wiki.dlang.org/IDEs).

