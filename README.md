# Examen Docker

## 1. Introducción

Esta práctica consiste en realizar la actividad del examen valorada en 5 puntos desplegando nuestro propio proyecto en Docker. Nuestro proyecto consiste en 3 imágenes: una con mysql, una con phpmyadmin y otra con tomcat. 

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
       - ./mysql-dump:/docker-entrypoint-initdb.d
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

* En ports le asignaremos un puerto diferente al de tomcat normal. ¿Por qué? Pues para no tener ningún conflicto con un posible tomcat ajeno a Docker.

* Y finalmente en environment le daremos una configuración personal al tomcat para gestionarlo también. Para gestionar tomcat se podría hacer uso de tomcat-admin o cosas por el estilo, pero no es estrictamente necesario.

#### Pasos finales

Finalmente guardaremos nuestro archivo que se llamará docker-compose.yml. Seguidamente pondremos en el mismo directorio el proyecto.war y el script de creación de bases de datos.

## 3. Pasos para el despliegue de la aplicación.

Tras guardar el archivo, desplegar la aplicación es muy sencillo. Colocaremos la ruta de la terminal sobre el directorio dónde tengamos el docker-compose.yml y otros componentes y realizaremos el comando `docker-compose up -d` dónde el -d es para que lo abra como demonio y nosotros podamos seguir utilizando la consola.

### Conexión a tomcat
![imagen](https://user-images.githubusercontent.com/95173613/172451794-0415afbd-ffdf-4e41-bc72-32bd008e2c48.png)

### Conexión a phpmyadmin
![imagen](https://user-images.githubusercontent.com/95173613/172452453-b13933f4-159e-46f2-b979-1945059fcbfc.png)

## 4. Preparación y subida de la imagen a dockerhub.

Lo primero que debemos hacer es realizar el inicio de sesión en dockerhub. Para ello debemos usar el comando `docker login`

Tras hacer login debemos tener claro qué imágenes son las que necesitamos para nuestro proyecto. Para saberlo tendremos que hacer un análisis en `docker ps`.

![imagen](https://user-images.githubusercontent.com/95173613/172452805-200149fe-2232-477e-afca-0ef8ba4d7aad.png)

En nuestro caso, debemos tener en cuenta tomcat, mysql y phpmyadmin.

Haremos uso del comando `docker tag *nombre_imagen* *nombre_usuario/*nombre_repositorio*`.
Y también uso del comando `docker push *nombre_usuario*/*nombre_repositorio*.

![imagen](https://user-images.githubusercontent.com/95173613/172454921-adf1c8cd-c7ea-4291-b994-8f4c801ca4dd.png)

![imagen](https://user-images.githubusercontent.com/95173613/172455093-062b1cf4-f636-4e5d-b478-dc1ccbdb7648.png)

![imagen](https://user-images.githubusercontent.com/95173613/172455325-2a4b56b6-aeaa-4cd3-a1e1-8c758ba43750.png)

Podremos apreciar en dockerhub como efectivamente, se ha subido correctamente nuestro contenido.

![imagen](https://user-images.githubusercontent.com/95173613/172455417-4c832ff2-b2eb-4857-a599-cf5782ee1c24.png)


## 5. Conclusiones

Sin ser la manera más eficiente para desplegar nuestro proyecto, por magnitud o por desconocimiento, docker te aporta una manera fácil de poder acceder a aplicaciones sin tener instalaciones complicadas. Seguramente, cuánta más gente tenga el proyecto trabajando en él, más útil nos parecerá. 

## 6. Anexos
Imágenes del examen:
https://hub.docker.com/u/jordiop

Comandos de descarga de las imágenes 
`docker pull jordiop/examenp`
`docker pull jordiop/examenm`
`docker pull jordiop/exament`
