
- **En la sección de "Deployment > Deployment Center" de la WebApp puede ver la configuración de la WebApp y que hay dentro de ella.**
- Hace sentido que al crear una imagen de Docker indiquemos el puerto 80 para exponer, si en la webapp de un app service indicamos también el puerto 80.

- Otra forma de poder manejar los proyectos de Terraform, es agregando una carpeta en el proyecto (API o UI) con los recursos de Terraform que se usarán en esa parte del proyecto.

# Subir Imagen de FastAPI a ACR de Azure

Para desplegar su proyecto de FastAPI a la WebApp de azure, desde la terminal del proyecto de FastAPI tiene que hacer login en Azure:

```
az login
```

Luego conectamos nuestro proyecto de FastAPI con el ACR creado con Terraform en Azure:

```
az acr login --name {nombre del ACR}
```

![[Pasted image 20250707091538.png]]

> [!NOTA]
> Recuerde que el nombre del ACR debe ir todo junto y en minúscula

- Todos estos comandos los tiene que hacer el agente de DevOps, que nos ayuda en todo nuestro proceso de release de nuestra aplicación.

# Crear Tags para Referenciar Imágenes al ACR de Azure

```
docker tag api-ecommerce:latest acrcafeteriaunahenv.azurecr.io/cafeteria-unah:0.1.0
docker tag api-ecommerce:latest acrcafeteriaunahenv.azurecr.io/cafeteria-unah:latest
```

- **api-ecommerce:latest** -> Es la **imagen** de Docker que queremos agregar al ACR.
- **acrcafeteriaunahdev.azurecr.io/** -> Indica hacia que ACR vamos a hacer push de las imágenes que tenemos de manera local.
- **cafeteria-unah:0.1.0** -> Con esto creamos un nuevo repositorio dentro del servicio de ACR

- latest será siempre reemplazado por la nueva versión funcional, y el repositorio versionado irá aumentando su versión.

> [!DANGER]
**Este comando tag tiene que hacerse después de haber ejecutado el comando "docker build" para crear la imagen.**

Después de ejecutar el comando para montar la imagen en el ACR, podrá verlas en Docker Desktop:

![[Pasted image 20250707101817.png]]

# Desplegar Imágenes Referenciadas al ACR de Azure

- El hecho de hacer un Docker Push, se entiende que se ha hecho login a un Hub privado de ACR.
- Al hacer docker push, el comando entiende que tiene que buscar la imagen que tiene las carácterísticas apropiadas para subir una imagen de docker a ACR (la imagen deben estar referenciada al recurso ACR, tener las etiquetas debidas, etc).

Una vez que se ha logeado a azure y logeado al recurso ACR, y crear las etiquetas de las imágenes que referencian al recurso ACR de Azure, ya puede hacer la publicación de las imágenes:

```
docker push acrcafeteriaunahenv.azurecr-io/cafeteria-unah:0.1.0
docker push acrcafeteriaunahenv.azurecr-io/cafeteria-unah:latest
```

![[Pasted image 20250707102141.png]]

Se monta otra imagen, pero como las capas ya existían, esta imagen hace referencia a las ya creadas:

![[Pasted image 20250707102304.png]]

**Podemos ver las imágenes publicadas en la sección de repositorios del recurso ACR:**

![[Pasted image 20250707102513.png]]
![[Pasted image 20250707102531.png]]

# Vincular la Imagen de ACR con una WebApp de Azure

**Dirijase a la WebApp donde montará el recurso ACR.**

- Antes de vincular el ACR con la WebApp, se tiene que hacer el seteo de las variables de ambiente de la WebApp.
- Agregue las variables de ambiente de la API manualmente en el recurso WebApp donde montará la imagen de la API
- **Vea las variables del `.env` y vaya a la sección de variables de ambiente de la WebApp.**

![[Pasted image 20250707103755.png]]
 
Ahora, en Deployment Center: 
- Registry source -> Azure Container Registry
- Registry -> {nombre del recurso ACR creado}
- Image -> {nombre del repositorio de la imagen en el ACR}
- Tag -> {tag de la versión de la imagen a publicar}
- Continuous deployment -> on

![[Pasted image 20250707104720.png]]

- **Reinicie la WebApp donde vinculó el ACR.**
- La tardanza de reinicio dependerá de como está la cola operativa dentro de la granja de servidores de Azure.
- La primera carga tiende a ser lenta, las demás no.

![[Pasted image 20250707105032.png]]

La velocidad del servicio no será la mejor por el servicio utilizado para la WebApp.

![[Pasted image 20250707105514.png]]

# Recomendaciones

- Podría crear un Endpoint para obtener un simple texto con la versión de la API.


### Nuevo Release del Proyecto

- Si el proyecto tiene una actualización, tiene que volver a hacer el docker build de la aplicación.

```
docker buildx build --platform linux/amd64 -t api-ecommerce:latest . --load
```

- Genere nuevamente los tags

```
docker tag api-ecommerce:latest acrcafeteriaunahenv.azurecr.io/cafeteria-unah:0.2.0
docker tag api-ecommerce:latest acrcafeteriaunahenv.azurecr.io/cafeteria-unah:latest
```

![[Pasted image 20250707112057.png]]

- En un escenario profesional de DevOps, las versiones anteriores de la imagen se terminan eliminando automáticamente.
- Se elimina en un periodo de tiempo, dependiendo de la configuración realizada.

- Ahora, genera un push

```
docker push acrcafeteriaunahenv.azurecr-io/cafeteria-unah:0.2.0
docker push acrcafeteriaunahenv.azurecr-io/cafeteria-unah:latest
```

El proceso es más rápido porque la imagen ya existe:

![[Pasted image 20250707112510.png]]

Mire la versión:

![[Pasted image 20250707112539.png]]

![[Pasted image 20250707112705.png]]































