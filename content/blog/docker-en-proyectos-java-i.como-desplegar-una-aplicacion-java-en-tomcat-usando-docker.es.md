+++
author = ["JA Samitier"]
categories = ["development"]
date = 2017-01-14T23:00:00Z
description = ""
tags = ["docker", "tomcat", "java"]
title = "Docker en proyectos Java (I). Cómo desplegar una aplicación Java en tomcat usando Docker"
+++

De todas las cosas que aprendí en 2016, la que más ha cambiado mi día a día ha sido Docker y, sobretodo, la capacidad orquestación con Docker Compose. La orquestación de máquinas es algo que ya me llamó la atención durante mi paso por \[Senpai Devs\](http://senpaidevs.com), cuando \[Néstor\](http://nestorsalceda.com) nos enseñaba las _virguerías_ que era capaz de hacer para montar diferentes entornos. Esta pretende ser la primera de las entradas sobre cómo monté mi entorno de desarrollo (Java EE + Elastic + Apache + tomcat + SQL Server) completamente sobre Docker. Empezaremos por tomcat.

\[Apache tomcat\](http://tomcat.apache.org) es el contenedor de _servlets_ mas común a la hora de desplegar aplicaciones Java. Es ligero, no demasiado complejo de configurar y está delante de una comunidad enorme de desarrolladores. Además es software libre, lo cual también es importante.

Antes de empezar la fiesta, hay que tener claro que no estamos levantando máquinas, sino aplicaciones. Si necesitamos levantar 3 aplicaciones java, entonces levantaremos tres tomcats, cada uno en su propio contenedor, con su propia configuración etc. Si una aplicación falla, paramos **esa** aplicación, dejando las demás funcionando. Es por esto que vamos a usar Docker Compose. Para una sola aplicación no sería necesario, ya que con un \`docker run\` podríamos tenerla levantada con un comando, pero la idea de esta serie de entradas es mostrar cómo levanto todo mi entorno de desarrollo, para lo cual si es necesaria la orquestación.

Esto es lo que vamos a hacer:

\- Crear un fichero de orquestación (\`docker-compose.yml\`) en el que estableceremos las aplicaciones que vamos a levantar (en este caso solo una llamada **portal**) y también cómo se construye esa aplicación (en este caso haremos que la construya con el fichero \`Dockerfile\` dentro del directorio \`tomcat\`).

\- Asociar los puertos del contenedor con los de nuestra máquina. El puerto 8080, el de aplicación, lo asociaremos al 3000, y el puerto 8000, el que usaremos para poder debuggar la ejecución del tomcat, lo asociaremos al 4000.

\- Establecer variables de entorno de nuestra aplicación: Le diremos que use 2Gb de ram.

Además, configuraremos el tomcat para que:

\- Utilice la imagen \[tomcat:7.0.73-jre7-alpine\](https://github.com/docker-library/tomcat/blob/1651e929e7d4c9785b602cb93cdd2503573c3834/7/jre7-alpine/Dockerfile)

\- Abra puerto de debug en el 8000

\- Lea la configuración de la carpeta \`conf\` dentro del directorio \`tomcat\`

Necesitamos un fichero \`docker-compose.yml\` que contenga lo siguiente:

\`\`\`

version: '2'

services:

  portal:

    build: tomcat/.

    volumes:

      - ./tomcat/conf:/usr/local/tomcat/conf

      - ./webapps_portal:/usr/local/tomcat/webapps

    environment:

      - 'JAVA_OPTS=-Xmx2g'

    ports:

      - 4000:8000

      - 3000:8080

\`\`\`

Además necesitaremos un directorio \`tomcat\` con el siguiente fichero:

\- \`Dockerfile\`:

\`\`\`

FROM tomcat:7.0.73-jre7-alpine

ENV JPDA_ADDRESS=8000

ENV JPDA_TRANSPORT=dt_socket

VOLUME \["/usr/local/tomcat/conf"\]

VOLUME \["/usr/local/tomcat/webapps"\]

EXPOSE 8009

EXPOSE 8000

CMD \["catalina.sh", "jpda", "run"\]

\`\`\`

y el directorio \`conf\` del tomcat, donde podremos modificar la configuración del tomcat (context, logging...). Además, en el directorio principal necesitaremos también el directorio \`webapps_portal\`, donde dejaremos el war que desplegará el tomcat.

La estructura queda así:

\`\`\`

.

├── docker_compose.yml

├── tomcat

│   ├── Dockerfile

│   └── conf

└── webapps_portal

\`\`\`

Ahora solo nos queda levantarlo con \`docker-compose up --build -d\` (El \`--build\` solo lo necesitaremos la primera vez que lo levantemos, o cuando hagamos cambios en la configuración).

En la siguiente entrada veremos cómo meterle un apache por delante para poder decidir qué peticiones del apache van al tomcat y cuáles no.