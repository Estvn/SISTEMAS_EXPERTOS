
# Configuración de Azure Container Registry (ACR)

- Así como configuramos las Webapps, también podemos configurar otro tipo de recursos, como servidores de bases de datos y **Storage Account**, donde podemos almacenar la información de las colas o BLOB Service, o un **Azure Container Registry**.

#### ¿Qué es un Azure Container Registry?

- De la misma forma que Docker tiene su DockerHub donde podemos tener imágenes tanto privadas como públicas, podríamos levantar un contenedor de Docker que ya va a contener un Ubuntu o un Redis que es una base de datos de caché.

- DockerHub es un tipo de almacenamiento que se dedica a almacenar imágenes de Docker y versionarlas.
- Al igual que Github que se dedica a almacenar código versionable.
- **En Dockerhub se puede almacenar imágenes y pueden ser versionadas.**

- ¿Qué pasa si tu no quieres que el código que estás desarrollando se encuentre en Docker?
- **Puede configurar la imagen de Docker de forma privada o pública. **
- **La cuenta de Dockerhub puede ser vinculada con una cuenta institucional.**
- Si usted va a usar Dockerhub, básicamente tendría que gestionar una plataforma más.

> [!WARNING]
> En lugar de usar Dockerhub o Docker de forma separada en Azure, usted puede usar **Azure Container Registry.**

- **Azure Container Registry** es parecido a Dockerhub, pero vive directamente dentro de la plataforma de Azure.
- Tendrá como repositorio las imágenes que usted vaya publicando.
- Al usar **Azure Container Registry** tendrá un entorno mucho más controlado y mucho más privado.

- Puede crear el archivo de Azure Container Registry dentro del mismo archivo de las webapps, porque esto al final será utilizado para almacenar las webapps.

> [!DANGER]
> Maneje los nombres de los servicios, entornos y usuarios en minúscula para evitar problemas futuros en los loggeos.

- Tenemos tres opciones de registro con Docker:
	- Azure Container Registro
	- Docker Hub
	- Private Registry

- Cuando seleccionemos una de estas opciones y depleguemos la imagen, podemos seleccionar la opción de **"Continuous deployment"** 
- Al encender el depliegue continuo, se va a generar un **Webhook**,  esto garantiza que cada vez que realicemos un cambio en la imagen de nuestro **Container Registry**, la imagen del servidor también se refresque.

> [!IMPORTANT]
> - La diferencia que tenemos entre el modelo Basic y Estandar de Container Registry, es que en el modelo Estandar se encuentra la opción para activar el Webhook y reflejar los cambios realizados en el servidor. En el modelo Basic no tenemos esta opción.
> 
> - El modelo Basic ofrece menos **Webhooks** que el modelo Estandar, por ejemplo, si el modelo Basic ofrece cuatro Webhooks, usted puede hacer seguimiento de los cambios en cuatro imágenes diferentes.








































