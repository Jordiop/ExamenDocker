# Examen Docker

## 1. Introducción

## 2. Configuración del archivo docker-compose.yml

Para poder desplegar nuestra aplicación correctamente en docker, se hará uso de Docker-compose. Docker-compose es una herramienta que te ayuda a definir aplicaciones multi-contenedor. 

### ¿Cómo se usa?

Para usarlo necesitaremos crear un archivo .yml. Al archivo docker-compose.yml le daremos la configuración necesaria para que nuestro proyecto se pueda alojar en docker. 

### Contenido del Docker-compose y explicación del mismo.

```
version: '3.3'
services:
   db:
     image: mysql:8.0.29
     volumes:
       - /opt/test:/var/lib/mysql
       - ./mysql-dump/:/docker-entrypoint-initdb.d
     environment:
       MYSQL_DATABASE: gai
       MYSQL_ROOT_PASSWORD: 8018
       MYSQL_USER: jordi
       MYSQL_PASSWORD: 8018
     ports:
       - 3306:3306
   phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin/phpmyadmin
    ports:
      - '8081:80'
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: 8018
   web:
    depends_on:
      - db
    image: tomcat
    volumes:
      - /home/jordiop/Desktop/Docker/GAI.war:/usr/local/tomcat/webapps/GAI.war
    ports:
      - '8082:8080'
    environment:
      MYSQL_ROOT_PASSWORD: 8018
      MYSQL_USER: jordi
      MYSQL_PASSWORD: 8018
```

#### MySQL 

Como vemos, toda la configuración está dentro de services. Dentro de services le daremos configuración a cada apartado de la webapp. 
* Lo primero que configuraremos es el MySQL en su versión 8.0.29. 
* En images le daremos el nombre de la imagen que se tiene que descargar para configurar. 
* En volumes le daremos datos para que ejecute nuestra base de datos al iniciarse.
* En environment le daremos la configuración personalizada al MySQL.
* Y por último le daremos los puertos. MySQL tiene como puertos por defecto el 3306, así que tras asegurar que no hay ninguna aplicación en esos puertos abierta, podremos ejecutar este archivo.

#### PHPMyAdmin

Para tener cierto control sobre la base de datos, instalaremos un gestor de base de datos con "GUI". Como podemos observar, tiene la etiqueta de "depends on db". Eso es porque básicamente le estamos dando una instrucción a phpmyadmin para indicarle dónde tiene que mandar su información cuando esté en uso.

En este caso, al ser una imagen relativamente sencilla, no necesitaremos configurarla muy explícitamente.
* En image le daremos el nombre de la imagen de PHPMyAdmin que queramos utilizar.
* En ports, esta vez sí, es muy importante darle un puerto libre y asegurarnos de ello, porque de este puerto dependeremos para poder gestionar luego el MySQL.
* Por útlimo, el environment. Aquí hay que prestar atención al nombre que se le da y a la contraseña porque si no PHPMyAdmin no funcionará.

#### Tomcat

En nuestro proyecto, tomcat es utilizado para alojar tanto el Java (backend) como la página web (frontend).
Para configurarlo debemos tener en cuenta una vez más "db". Eso es porque tomcat tendrá que enviar y recibir datos de nuestra base de datos previamente configurada. De ahí el `depends_on: db`

* En image simplemente pondremos tomcat, es decir, la predeterminada sin elegir versión. Este hecho no es muy relevante.

* En volumes le daremos la instrucción para que el .WAR extraído previemente de nuestro proyecto WebApp vaya a la carpeta correcta para que se despliegue nuestra aplicación 



## 3. Pasos para el despliegue de la aplicación.

## 4. Preparación y subida de la imagen a dockerhub.

## 5. Conclusiones

