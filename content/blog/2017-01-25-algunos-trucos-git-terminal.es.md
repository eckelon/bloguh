---
title: Algunos trucos git en la terminal
date: 2017-01-25T15:40:24.000+06:00
author:
- JA Samitier
categories:
- development
tags:
- productivity
- work
- zsh
- ohmyzsh
- git
description: Trucos fáciles para empezar a usar la terminal de forma productiva
---

El otro día enseñé a [Jorge](http://jorgeatgu.com) un par de funciones de mi [`.zshrc`](https://github.com/eckelon/dotfiles/blob/master/zshrc) que me facilitan la vida gestionando repositorios git desde la terminal y le gustaron bastante. He pensado que igual también resultan interesantes por aquí.

- **Cómo generar un `.gitignore` automáticamente**

```
function gi() {
	 curl -L -s https://www.gitignore.io/api/$@ >> .gitignore;
}
```

de esta forma se puede ejecutar `gi vagrant java intelliJ` para descargar el .gitignore resultado de esa búsqueda en [Gitignore.io](http://gitignore.io)

- **Cómo listar todos los repositorios (con sus respectivas ramas actuales) presentes en un directorio**

```

function gls() {
    for d in *; do
        if [[ -d "$d" && -e "$d/.git" ]]; then
            echo "$d -> $(cd "$d" && git_prompt_info | sed 's/%//g;s/{//g;s/}//g')"
        fi
    done
}
```

En uno de los proyectos en los que me encuentro tenemos que trabajar con varios módulos, cada uno de los cuales está en un repositorio git distonto. Muchas veces necesitaba poder saber de un vistazo en qué rama estaba cada uno de estos módulos. Se ve así:

```
~/.vim/plugged
▶ gls
YouCompleteMe -> master ✔
csapprox -> master ✔
ctrlp.vim -> master ✔
ferret -> master ✔
goyo.vim -> master ✔
jshint.vim -> master ✗
nerdcommenter -> master ✔
nerdtree -> master ✔
plaintasks.vim -> master ✗
tagbar -> master ✔
vim-airline -> master ✔
vim-airline-themes -> master ✔
vim-colorschemes -> master ✔
vim-fugitive -> master ✔
vim-gitgutter -> master ✔
vim-hybrid-material -> master ✔
vim-javascript -> master ✔
vim-jsx -> master ✔
vim-sensible -> master ✔
vim-strip-trailing-whitespaces -> master ✔
```

Requisito: la función `git_prompt_info` del [plugin git](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh) de ohmyzsh

- **Cómo hacer `git pull` a sus ramas actuales en todos los repositorios dentro de un directorio**

```
function gupd() {
    current_directory=$PWD
    for d in *; do
        if [[ -d "$d" && -e "$d/.git" ]]; then
            cd $d
            echo "$d -> $(git_prompt_info | sed 's/%//g;s/{//g;s/}//g')"
            git fetch
            git pull origin $(git_current_branch)
            cd $current_directory
        fi
    done
}
```

Por lo mismo que he contado en el punto anterior, a veces necesito bajarme los cambios de unos cuantos repositorios, para lo cual esta función me resulta muy cómoda. Además de listar los repositorios, también los actualiza. Evidentemente para poder hacer esto no puedo tener cambios sin commitear en ninguno de los repositorios.

Requisito: las funciones `git_current_branch` y `git_prompt_info` del [plugin git](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh) de ohmyzsh

Estoy seguro de que puede hacerse mejor, por lo que cualquier comentario o idea será bien recibida :)
