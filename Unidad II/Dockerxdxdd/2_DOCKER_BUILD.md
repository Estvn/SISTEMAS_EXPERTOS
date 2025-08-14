
# Prueba del Proyecto de FastAPI

- Descargué el código del GitHub del ingeniero
- Agregué el .env con las credenciales de mi servicio de base de datos.
- Cree un nuevo entorno virtual 
- Agregué las dependencias necesarias dentro del entorno.

Probando conexión con la base creada:

![[Pasted image 20250702101849.png]]

Prueba desde la API:

![[Pasted image 20250702104614.png]]

# Docker

Video:
- https://www.youtube.com/watch?v=Pl0raLHQavA&t=1022s

- El cambio de la versión de la imagen de Docker usada para Python es un proceso delicado porque puede generar incompatibilidades con las librerías o lógica de programación del proyecto.

- **En las imágenes de Docker Hub usted puede buscar imagen de cualquier lenguaje, Framework o sistemas operativo que desee.**
- Por ejemplo, en el vídeo se busca una imagen de Python con un SO por defecto.
	- Puede ver la versión de las Layers/elementos que conforman la imagen.
	- Puede ver las vulnerabilidades de las Layers de la imágenes.
	- Puede ver el peso y el SO donde puede correr la imagen de Docker.
	
# Comandos de Docker

- Tiene que crear un archivo llamado **Dockerfile** en la carpeta raíz del proyecto que desea agregar en el contenedor que va a crear.
- Tiene que crear un archivo .txt (requirements.txt) con las librerías que quiere instalar en la imagen.
- Tiene que tener cuidado al cambiar versiones de las imágenes en Docker.

![[Pasted image 20250702113438.png]]

**Los comandos a usar en el Dockerfile son los siguientes:**

- **FROM** -> Indica la imagen de DockerHub que ha seleccionado para el proyecto.
- **WORKDIR** -> Indica la ruta del directorio del SO de la imagen donde trabajará el proyecto.
- **RUN** -> Ejecuta comandos de la terminal del SO de la imagen (puede actualizar el sistema o instalar bases de datos, herramientas, librerías, etc.).
- **COPY {ruta1} {ruta2}** -> Copia archivos de una ruta a otra
- **EXPOSE** -> Expone el puerto que usará la aplicación.
- **CMD** -> Este el comando que se había mencionado que ejecutará la aplicación (es el último comando del archivo Dockerfile) 
	- Mientras haya una instancia viva del contenedor o el servicio del contenedor está corriendo, **esta comando siempre se estará ejecutando.**

> [!DANGER]
> El comando COPY de Docker tomar como ruta 1 la ubicación del archivo Dockerfile, y la ruta 2 es la ruta de trabajo del SO dentro de la imagen.

> [!IMPORTANT]
> El Build para la creación de la imagen la pueden hacer dos agentes: La máquina local o un servicio en la nube

> [!DANGER]
> El archivo .env se borra de la imagen para que no se cargue en el repositorio, pero se tiene que buscar la forma de volver a agregarlo para que la imagen funcione en producción.
> 


Imágenes del archivo Dockerfile:

![[Pasted image 20250702114556.png]]

![[Pasted image 20250702114619.png]]

- De último se encuentra el comando que ejecuta la aplicación.

# Comando para realizar Build de la imagen (ejecute el comando en la terminal)

- **Lo primero que se hace para ejecutar Dockerfile, es levantar la aplicación de Docker.**
- Cuando se ejecute el comando, desde la UI de Docker se podrá ver que el comando se está ejecutando

```
docker buildx build --platform linux/amd64 -t expertos-sabado:latest . --load
```

- Donde haremos release de nuestras aplicaciones, los procesadores tienen arquitectura AMD64.
- Si yo no hago Build de mi aplicación con el comando **buildx** lo que pasaría esque va a construir la aplicación sobre la arquitectura de la máquina donde se hizo el build (da problemas si su maquina es Apple y tiene procesador Sylicon).

- Cuando suba la imagen al contenedor de la máquina o servicio que la contiene, no podrá entender la imagen.

- **Es muy importante saber cual es la arquitectura de la computadora en la que se está trabajando, y cuando generemos el Build de la aplicación (porque estamos hablando de un sistema operativo), tiene que coincidir también con la arquitectura de la máquina donde haremos nuestro release.**
- Se usa el comando buildx para cambiar la arquitectura del SO que estará dentro de la imagen.

### Explicación de los comandos

- **buildx** -> Se usa para cambiar la arquitectura de la imagen
- **--platform linux/amd64** -> Se usa para cambiar la arquitectura del procesador.
- **-t expertos-sabado:latest** -> Define el nombre del repo y una etiqueta

- **.** -> El punto indica que en la raíz del proyecto encontrará el Dockerfile
- **--load** -> Se usa para ir viendo los logs o warnings o información generada durante el proceso de creación de la imagen de Docker a la que haremos push para publicar nuestra solución.

Aquí tengo el comando de build:

![[Pasted image 20250702232524.png]]

Aquí empieza a ejecutar los comandos del Dockerfile:

![[Pasted image 20250702233038.png]]

El la UI de Docker se puede ver el proceso:

![[Pasted image 20250702233209.png]]

Todos los comandos ejecutados:

![[Pasted image 20250702233727.png]]

- Durante el proceso de Build se traen las definiciones de los Layers de la imagen del repositorio público de DockerHub para que esas configuraciones formen parte del empaquetado de nuestra imagen.
- **Luego sigue ejecutando todos los demás comandos.**

En el programa de Docker para ver las imagenes creadas puede ver esto:

![[Pasted image 20250702234010.png]]

![[Pasted image 20250702234023.png]]

> [!IMPORTANT]
> ¡Podemos correr la imagen de Docker en local para ver nuestro proyecto desde el navegador!

# Comando para Correr la Imagen de Docker

```
docker run -d -p 8000:80 --name api-cafeteria-expertos --env-file .env api-cafeteria-expertos:latest
```

- Vamos a poder ver nuestro contenedor corriendo, y podremos entrar a su terminal desde Docker Desktop.

> [!IMPORTANT] ¿Donde ejecuto el comando para crear el contenedor?
> El comando para crear/correr el contenedor tiene que ser en la ubicación del proyecto.
> Por ejemplo, el comando indica el `.env` del proyecto que se ha creado una imagen.

- **-d** -> Se usa cuando no quiero que corra el contenedor y que automáticamente se ingrese a la terminal del SO del contenedor que ejecutaremos ahorita.
- **-d** -> Se usa cuando quiero que el proyecto se ejecute sin tanto protocolo.

- **-p** -> Se usa para indicar cuáles son los puertos en los que voy a trabajar.
- `-p 8000:80` -> Estoy indicando primero el puerto de mi máquina, y de último el puerto del contenedor.
	- Este parámetro del comando es para conectar los puertos del contenedor con los puertos de mi máquina.
	- Quiero conectar el puerto 8000 de mi máquina con el puerto 80 del contenedor.

- **--name** -> Este comando simplemente es para asignar un nombre al contenedor que vamos a usar.

**Necesitamos indicar las variables de entorno al contenedor, ya que las borramos al crear la imagen.**

- **--env-file .env** -> este comando va a indicar que el archivo `.env` contiene las variables de entorno

![[Pasted image 20250703001842.png]]

--------------

- En Docker Desktop, en la parte de contenedores podemos ver el contenedor que está corriendo
- Podemos ver los Logs del SO del contenedor
- Podemos ver los directorios de carpetas del SO dentro del contenedor
- **Dentro de la carpeta app del SO podemos ver nuestro proyecto.**
- Podemos ver desde stats cuanta memoria está usando.

- Si no agregas los Drivers necesarios en la imagen, saltarán errores en el proyecto
- Los proyectos pueden usar Drivers instalados en el SO de nuestra máquina, si no los agregamos en el Dockerfile para instalarlos en la imagen, habrán errores.










