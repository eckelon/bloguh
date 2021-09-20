+++
author = ["JA Samitier"]
categories = ["development"]
date = 2017-01-21T23:00:00Z
description = ""
tags = ["unix", "terminal", "zsh", "terminal"]
title = "Cómo uso la terminal"

+++
En el trabajo, las dos aplicaciones en las que más centro mi atención son la terminal, donde hago cosas, y el navegador, donde las pruebo. No me gusta usas demasiadas aplicaciones porque eso me supone tener que aprender configuraciones y formas de trabajar que realmente no aportan demasiado. Además, como en el trabajo uso Mac OS y en casa Ubuntu, es un lío tener que usar distintas aplicaciones para lo mismo solo porque no estén en un sistema operativo u otro. En esta entrada me gustaría describir cómo y por qué uso la terminal.

![Imagen de un ordenador ejecutando una terminal](/uploads/farzad-nazifi-p-xsl33wxyc-unsplash.jpg)

<span class='image-credit'>Photo by <a href="https://unsplash.com/@euwars?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Farzad Nazifi</a> on <a href="https://unsplash.com/collections/1111107/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></span>

Primero decir que las dos máquinas donde trabajo son:

* Mountain Nickel 13", corriendo Ubuntu 16.04 "Xenial Xerus", mi portátil personal.
* MacBook Pro 13" Retina (2015) corriendo Mac OS 10.11 "El Capitán", mi portátil _corporativo_

Ambas máquinas disponen de terminales _*nix_, que desde mi punto de vista es la terminal ideal para un desarrollador. A partir de aquí, intento que el funcionamiento de ambas terminales (y todo el software que corro en ellas) sea lo más similar posible. Además de las herramientas que proporcionan _out of the box_, yo también incluyo:

* [Zsh](http://www.zsh.org): shell alternativa a bash que tiene algunos detalles muy útiles para los que usamos mucho la terminal. En la presentación [Why Zsh is Cooler than your Shell](http://es.slideshare.net/jaguardesignstudio/why-zsh-is-cooler-than-your-shell-16194692) se pueden ver alguna de estas ventajas.
* [Oh My ZSH](http://ohmyz.sh): conjunto de configuraciones y plugins construidas alrededor de zsh. Es algo pesada, pero aporta configuraciones muy convenientes con solo un par de clicks.
* [Vim](http://vim.org): Editor de texto _extremadamente_ configurable y eficiente. Lo uso para ~~todo~~ casi todo (vale, para proyectos Java empleo [IntelliJ IDEA](https://www.jetbrains.com/idea)).
* [Tmux](https://tmux.github.io): Multiplexador de la terminal que además permite crear sesiones accesibles por ssh. Es ideal para hacer _pair programming_.
* [The Silver Searcher](https://github.com/ggreer/the_silver_searcher): Una herramienta para buscar texto alternativa a _grep_. Es [muy muy muy](http://geoff.greer.fm/ag/speed/) rápida y sencilla de utilizar. La empleo para buscar y reemplazar código de forma masiva. Un _must_ clarísimo.
* [Git](https://git-scm.com): El sistema de control de versiones distribuído que uso para todos los proyectos, tanto profesionales como personales. Además de eficiente, también tiene un _cli_ que permite hacer todo en la terminal fácilmente. Dejo por aquí la [configuración que uso normalmente](https://github.com/eckelon/dotfiles/blob/master/gitconfig).

Estas son las herramientas que yo añado, pero uso muchas otras que me provee el sistema, algunas de ellas:

* `tail`
* `find`
* `grep`
* `sed`
* `curl`
* `ssh`
* `top`
* `ps`

Creo que no me dejo nada.

[Nestor](http://nestorsalceda.com) suele decir que él no usa un IDE, porque unix es su IDE. Un entorno de desarrollo integrado es una aplicación que agrupa un montón de herramientas (de búsqueda, refactoring, edicion de texto, linters...) que usar conjuntamente para desarrollar software. Unix provee muchas herramientas que si se saben usar a la vez, hacen que no necesites un IDE concreto. Por ejemplo, así es como reemplazo texto masivamente, sin usar el _reemplazar_ de ningún IDE.

    ag miVar ~/Development/miProyecto/ -l | xargs sed -i 's/miVar/miNuevaVar/g'

Con `ag -l` listo todos los ficheros donde encuentro la ocurrencia buscada. Con `xargs`, le paso este listado a `sed`, el editor de texto en memoria de unix, al que también le paso la orden de reemplazar la ocurrencia por la nueva palabra a todo el fichero. Lo que gano con esto es:

* Trabajar en una sola herramienta, la terminal.
* Mantener una [configuración sencilla de mantener](https://github.com/eckelon/dotfiles).
* Tener una forma de trabajo portable en cualquier momento a otra máquina.
