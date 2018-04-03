# Marco de desarrollo web Vibe.d

[Vibe.d](http://vibed.org) es un potente marco de desarrollo web (*web
framework*) con el que se ha escrito este tour. Algunas de sus características
más destacables son:

* Vibe.d permite crear servidores web y servicios web de alto rendimiento
  gracias a que ha sido desarrollado usando fibras (*fibers* en inglés)
  para realizar E/S asíncrona. El código escrito parece como si fuera síncrono
  pero el marco realiza toda la ingrata gestión asíncrona en segundo plano.
  En la sección siguiente se muestra un ejemplo de ello.
* Generador de JSON e interfaz web fácil de usar.
* Soporte inmediato y listo para usar de Redis y MongoDB, con lo que crear
  sistemas de *backend* con buen rendimiento.
* Se pueden escribir clientes y servidores TCP y UDP genéricos.

Hay que tener en cuenta que los ejemplos de este capítulo no se pueden ejecutar
*online* ya que requiren conectividad de red que está deshabilitada por razones
obvias de seguridad.

La forma más sencilla de crear un proyecto con vibe.d es instalar el gestor de
paquetes `dub` y crear un nuevo proyecto especificando *vibe.d* como plantilla:

    dub init <project-name> -t vibe.d

El gestor de paquetes `dub` se asegurará de descargar vibe.d y de que este
esté disponible a la hora de construir el proyecto.

El libro [D Web development](https://www.packtpub.com/web-development/d-web-development)
proporciona una introducción completa a este gran marco de desarrollo web.
