# Docker


### Comandos para Docker

    sudo chmod 666 /var/run/docker.sock   --> para linux dejar de usar "sudo"


### Comandos de Imagenes 

    Es una capa de solo lectura, es la definición o plantilla de un contenedor


    Creación de imagenes

    docker build -t name .                      --> para crear una imagen a partir de un dockerfile
    docker build --tag tag_name .               --> (el punto es dockerfile)
    docker build -t nameImage:tag .             --> para indicar un tag
    docker build -t nameImage:tag -f dockerfile --> para indicar un dockerfile especifico


    docker history -H imageName:tag --> muestra la historia de la imagen


    docker rmi  imageName           -> eliminar imagen
    docker rmi imageName imageName2 -> elimnar mas de una imagen 

    Danglin images --> no tiene nombre ni tag, imagen no referenciada


### Multistage 

    El ultimo FROM sera el valido

    FROM maven:3.5-alpine         as builder  // palabra clave para establer un stage
    COPY app /app
    RUN  cd /app && mvn package

    FROM openjdp:8-alpine
    COPY --from=builder /app/target/my-app-1.0-SNAPSHOT.jar /opt/app.jar
    CMD java -jar /opt/app.jar


    el primer Stage es de construcción por lo regular


### Dockerfile 

    El archivo en el cual se hace la especificación de la imagen

    FROM     --> se especifica desde donde empezar                               : FROM ubuntu
    RUN      --> ejecutar un comando de liux                                     : RUN apt-get update && \ apt-get install -y default-jre


    COPY     --> copiar archivos                                                 : COPY docker-ajmg-0.0.1-SNAPSHOT.jar .
    ADD      --> agregar urls en la imagen, lo descarga y lo pone en la imagen

    ENV      --> variables de entorno
    WORKDIR  --> directorio de trabajo                                           : WORKDIR /app
    EXPOSE   --> exponer puertos

    LABEL    --> Etiqueta para dar información
    USER     --> Que usario esta ejecutando la tarea
    VOLUME   --> data persistente del contenedor
    CMD      --> mantiene vivo al contenedor, o correr un script
                en el cmd también se corren cosas cuando el contenedor exista
    .dockerignore --> Sirve para omitir data en el contexto de Docker




### Contenedores 

    Una imagen en ejecución


    docker run --name nameContainer -p HostPort:ContainerPort image
    : -d (detach)  para correr en backgorund
    -p indicar el puerto a exponer

    docker ps                             --> listar contenedores activos
    docker ps -a                          --> lista cotenedores activos y no activos
    docker rename actualName newNAme      --> renombrar un contenedor
    docker stop containerName/id          --> Parar un contenedor
    docker restart containerName/id       --> detiene y reinicia el contenedor
    docker exec -ti -u containerName bash --> ingresar a un contenedor


    docker rm -fv container  --> eliminar un contenedor
    docker ps -a             --> muestra los contenedores activos y no


    docker ps -q             --> id de los Contenedores
    docker ps -q | xargs docker rm -f --> eliminar todos los contenedores
    docker inspect nameContainer/id -> muestra detalles del contenedor

### variables de entorno
    docker run -dti -e "prueba=1234" --name containerNanme image  --> -e : indica una variable de entorno


    docker stats nameContainer/idcontainer  --> estatus de un container
    docker  exec -u user -ti containerName bash --> Se ingresa al contenedor con un usuario 

### limitar recursos a un contenedor
    docker run -m  "600mb" --name nameContainer image --> limitar el uso de ram, puede ser "XXmb" o "XXgb"
    docker run -d -m "2gb" --cpuset-cpus 0-n --name nameContainer image --> (0-n) el numero de nucleos

### copiar archivos de un host a un contenedor y de un contenedor a un host
    docker cp file  contenedorName:path
    docker cp app.config db:/var/log --> copiar un archivo de un host a un contenedor

    docker cp container:path pathHost
    docker cp apache:/var/log/file.log . -->copiar un archivo de un contenedor a un host


### Volúmenes
    Nos permite persistir data de un contenedor
        Host         : se alamacenan en el dockerhost
        Anónimo      : no definimos una carpeta pero docker la genera
        NamedVolumes : volumenes que creamos y son administradas por docker

    * volumen host:
    Example: docker run -d --name nameContainer -p HostPort:ContainerHost -e "enviromentVariable" -v pathHost:pathContainer
             docker run -d --name mysql-volumen -p 3306:3306 -e "MYSQL_ROOT_PASSWORD=12345678" -v /home/aztlan/Documentos/tmp/docker-volumenes/mysql-volumen-hst:/var/lib/mysql mysql:5.7

             docker rm -fv mysql-volumen
             # En la carpeta del host se almaceran la info de la bd, y si el contenedor es eliminado y se ejecuta nuevamente la data persistirá y será tomada de nuevo

    * volumen anónimo
             docker info | grep -i root -> para ver donde se encuentra docker
    Example: docker run -d --name nameContainer -p HostPort:ContainerHost -e "enviromentVariable" -v pathContainer
             docker run -d --name mysql-volumen-anonimo -p 3306:3306 -e "MYSQL_ROOT_PASSWORD=12345678" -v /var/lib/mysql mysql:5.7

             # La data será persisitida (opciinal) : /vat/lib/docker/volumes
             Notas : No es recomendable dada que reuslta dificil identificar el volumen
                     docker rm -fv mysql-volumen  -> con este comenado se elimina todo hasta el volumen, si omitimos v , no se elminiará el volumen