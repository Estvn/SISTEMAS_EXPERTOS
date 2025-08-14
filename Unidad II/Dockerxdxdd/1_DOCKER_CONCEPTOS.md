
- En Docker tenemos el código o manifiesto de nuestra solución.
- El lugar donde va a vivir nuestro código será GitHub.
- El lugar donde estaremos codificando será nuestra máquina.

- Normalmente este código o Docker File va a formar parte de un proyecto completo.
- Una vez que tenemos nuestro Docker File y nuestro repositorio, el siguiente paso es generar el Build de nuestra solución.
- Al realizar el proceso de Build a nuestro Docker File, vamos a generar **la imagen** de nuestra solución (Docker Image).

- En Docker Hub puede encontrar una gran cantidad de imágenes que la comunidad ha estado compartiendo.
- En Docker Hub encuentra una gran cantidad de repositorios que son imágenes con todas las 
- herramientas instaladas que usted necesita para empezar su proyecto.

## Creación de imagen de Docker y Layers

> [!DANGER]
> -  **La imagen** va a estar compuesta por Layers.
> - La definición de Layer es la definición de variables de ambiente, instrucciones de como correr ciertas instrucciones a nivel de terminal, etc.
> - **Las imágenes trabajan bajo un Sistema Operativo en concreto.**

- La manera en la que vamos a interpretar los Layers de nuestras imágenes va a ser mediante un diggest sobre la definición de los componentes que forman parte de nuestra imagen.
- **Cada Layer es una capa que contiene una herramienta dentro de la imagen.**
- Una Layer podría tener el SO, otra Layer podría tener un lenguaje, otra Layer podría tener a Pytorch y otro Layer el código de nuestro proyecto.

-  Cuando hacemos Build de nuestra imagen de Docker, internamente se empiezan a generar las capas que representan distintas partes del SO y otras librerías instaladas para que nuestro proyecto comience a ejecutarse o comience a correr como tal.

- Cuando hacemos el Build basado en Docker, **generamos el Docker Image**, que a su vez genera las Layers, donde ciertos Layers van a representar partes de un SO o librerías.

- Tenemos los artefactos que nos sirven para hacer un Deploy (código), y tenemos la parte del Storage (Repositorio).

> [!NOTA]
> DockerHub nos ayuda a llevar el versionamiento de nuestros repositorios de imágenes de Docker.
> También podemos llevar el versionamiento de las Layers de las imagesnes a las que hacemos el Build.

- Se puede pushear las imágenes con sus Layers a Docker Registry o Azure Container Registry.

¿Qué sucede dentro de las tecnologías que funcionan como repositorios?

- No es un repositorio de código como tal.
- Tiene una DB generalizada de imágenes, y está la división de cada una de las capas de cada una de las imágenes que tenemos.
- Va a estar el repositorio de la API. El código de la API estará identificado por una Tag, y también tendrá sus Layers.
- **Cada Layer del código de la API tendrá una dirección que apunta a una Layer de la imagen ubicada en la base de datos generalizada.**
- Es muy eficiente la manera en la que se genera el código para referenciar.

- **Si queremos agregar otro código/API en el repositorio DockerHub, las referencias de este código se harán hacia las Layers de la imagen que ya existía. La imagen no se duplica.**

> [!NOTA]
> Toda la configuración dentro de DockerHub puede realizarse en local o el agente DevOps que se encargó de administrar la imagen de Docker.

El Build lo pueden hacer dos agentes:
- Local
- DevOps. El agente en la nube, que se conecta a nuestro repositorio, o mediante un WebHook escucha que hay una nueva versión en la rama donde tenemos el proyecto, y el mismo se encarga de realizar el Build y hacer el push de las imágenes actualizadas de nuestra solución en contenedor. 

## Levantar el Contenedor de Docker

**Una vez creada la imagen y conectada a un repositorio con sus Layers, el siguiente paso es levantar el contenedor.**

- Levantar el contenedor simplemente es tomar la imagen de la cual ya hicimos Build.
- Al momento de levantar el contenedor va a ser el SO prácticamente el que va a empezar a correr, a tener su directorio de archivos, su terminal, ejecutar servicios del proyecto que nosotros queremos correr.
- Levantar un contenedor, a parte de dar de alta a los servicios, es cual es el demonio que se quiere ejecutar por detrás.

¿Donde va a estar corriendo nuestro contenedor?

 - Explícitamente hay un Storage, pero es a nivel muy operativo.
 - Puede ser una máquina virtual que puede tener un IIS (Web Server que corre en Windows Server) corriendo o un NGINX o Python corriendo o cualquier servidor que se le ocurra dentro de la máquina virtual.
- **Se debe configurar el contenedor dentro de la máquina virtual.**
- También podemos tener una Lambda Function de AWS que hará correr al contenedor y los servicios dentro de él.

- Va a existir un comando que ejecutará ese demonio o proceso del servicio que queremos levantar.
- El comando puede ser "python main.py", "npm run start", etc.
- **Tendremos un comando que hará correr nuestra solución de manera productiva.**

- **El contenedor se puede levantar directamente en una MV, Lambda Function o WebApp.**

Arquitectura de Docker

![[Pasted image 20250627112751.png]]

Funcionamiento en repositorio de imágenes

![[Pasted image 20250627112041.png]]

- **Todo lo mencionado es la explicación de lo que necesitamos saber cuando queremos empaquetar una solución.**
- Se define SO, librerías pre-instaladas, y podemos instalar nuevas librerías en la imagen.
- 











































