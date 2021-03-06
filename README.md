# Taller de programación I

Sitio no oficial de la materia 7542 - Taller de programación I

## Como contribuir

### Clone

Primero clonamos el sitio

```shell
$ git clone https://github.com/Taller-de-Programacion/Taller-de-Programacion.github.io.git
Cloning into 'Taller-de-Programacion.github.io'...
remote: Counting objects: ...
...
Checking connectivity... done.
```

o bien, lo actualizamos si ya lo teníamos clonado localmente

```shell
$ cd Taller-de-Programacion.github.io
$ git pull
Already up-to-date.
```

### Agregar un post

Simplemente escribir un post dentro de la carpeta ```_posts/[año]``` con un nombre sin espacios con el formato ```[año]-[mes]-[dia]-[nombre-del-post].md```.
Cada post debe tener un encabezado de la forma

```yaml
---
layout: post
title: titulo-aqui
author: tu-nombre
date: la-fecha-de-publicacion

---

```

### Verificar localmente el post

Estando dentro de la carpeta ```Taller-de-Programacion.github.io```, levantamos el sitio con docker
Se utilizará la imagen ```jekyll/jekyll:pages``` del proyecto [jekyll/docker](https://github.com/jekyll/docker) ya que es la más similar a github-pages. 

El comando ```run``` creara un container a partir de la imagen. Es necesario hacerlo una sólo vez: con los comandos ```start``` y ```stop``` podremos iniciar y detener el container sin necesidad de crearlo desde cero.

```shell
$ JEKYLL_VERSION=3.6
$ sudo docker run --name TallerPages \
                  -v $(pwd):/srv/jekyll -p 127.0.0.1:4000:4000 \
                  jekyll/jekyll:$JEKYLL_VERSION \
                  jekyll serve --watch
Unable to find image 'jekyll/jekyll:3.6' locally
3.6: Pulling from jekyll/jekyll
1160f4abea84: Pull complete
7e624458e890: Pull complete
...
Status: Downloaded newer image for jekyll/jekyll:3.6
Fetching gem metadata from https://rubygems.org/...........
Fetching gem metadata from https://rubygems.org/.
Resolving dependencies....
Installing ...
....
Installing github-pages
Bundle complete! 2 Gemfile dependencies, 64 gems now installed.
Bundled gems are installed into `/usr/local/bundle`
Configuration file: /srv/jekyll/_config.yml
            Source: /srv/jekyll
       Destination: /srv/jekyll/_site
      Generating...
                    done.
 Auto-regeneration: enabled for '/srv/jekyll'
Configuration file: /srv/jekyll/_config.yml
    Server address: http://0.0.0.0:4000/
  Server running... press ctrl-c to stop.
```

Este último comando:
 1. descarga de la internet la *imagen* ```jekyll/jekyll:3.6```. Si la imagen ya esta descargada este paso se ignora. Actualmente la version de ```jekyll``` usada por ```github``` es la 3.6.
 1. crea un *container* de docker tal que:
    - ```--name TallerPages``` es el nombre único y simbólico del container.
    - ```-v $(pwd):/srv/jekyll``` indica que la carpeta virtual dentro del container ```/srv/jekyll``` debe en realidad mapearse a la carpeta real ```$(pwd)``` (el directorio actual).
    - ```-p 127.0.0.1:4000:4000``` indica que el puerto virtual dentro del container ```4000``` debe mapearse al puerto real ```4000``` sobre la interfaz ```127.0.0.1```. Cambiar esta interfaz por ```0.0.0.0``` para exportar el servicio hacia afuera del host.
    - ```jekyll/jekyll:$JEKYLL_VERSION``` indica que imagen usar como punto de partida para el container
    - ```jekyll serve --watch``` En este caso, jekyll servirá el sitio (y lo actualizará si hay un cambio en los fuentes). Agregar ```--drafts``` para que se sirvan tambien los drafts de jekyll.
 1. finalmente se ejecuta el comando ```jekyll serve --watch```. Este a su vez
    1. Instala ciertos paquetes con ```apk``` (son los "fetchs" de la salida del comando anterior).
    1. Instala ciertas gemas de ruby con ```bundle```, entre ellas ```github-pages``` (son los "Installing" de la salida anterior).
    1. Corre finalmente jekyll con los parámetros pasados.

> Nota: A veces los permisos se alteran y pueden terminar mapeandose a un grupo inválido con GID 65533, asegurarse de corregirlo
```chown``` para evitar errores al levantar el servidor.

El container puede ser detenido y reiniciado con:

```shell
$ sudo docker stop TallerPages
```

```shell
$ sudo docker start -a TallerPages
```

> Nota: el flag ```-a``` es opcional y permite *attachear* la consola del container a la terminal actual, útil para ver los logs de jekyll.

> Nota: uno puede crear un container temporal agregando el parametro ```--rm``` al comando ```docker run``` pero crear un container no temporal y detenerlo es mas eficiente a la larga: al reiniciar el container ninguna de las dependencias de debe ser descargada ni instalada de nuevo.

> Nota: si tienes problemas con la creacion del sitio prueba en borrar el contenido de ```_site``` y de borrar el archivo ```Gemfile.lock```. Lo primero forzara a ```jekyll``` a recrear todo el sitio de nuevo mientras que lo segundo forzara a instalar las ultimas versiones de las dependencias.

### Proponer el post

Habiendo probado localmente el post es hora de proponerlo para que sea integrado al sitio oficialmente.
Basta con solo commitearlo y hacer un Pull Request.

