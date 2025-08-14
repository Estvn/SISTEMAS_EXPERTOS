
- Se hará práctica sobre nuestros conocimientos en el desarrollo de APIs, UIs, e implementación y desarrollo de cola mediante un servicio Serverless, que será la lógica que realizará procesos mediante los mensajes que lleguen a las colas.
- Se usará la API pública de Pokemon para obtener una lista de datos y almacenarlos en un archivo CSV.

- Haremos que nuestro servicio Serverless tome el mensaje con la petición del reporte y lo guarde en un Storage Account
- Usaremos el BLOB Storage para tener la persistencia del archivo.
- En la UI vamos a tener una tabla con el status de como va la generación del reporte, y una vez hecho vamos a poder descargarlo.

> [!NOTA]
> Tendremos dos Webapps donde crearemos la UI con NextJs y la API con FastAPI.

- Usaremos el servicio de BLOB para guardar toda la información no estructurada.
- En un solo Storage Account podemos guardar los servicios de BLOB y Queue Storage.

- El proceso Background (Serverless) que estaremos implementando en un App Service Plan propio, porque Serverless se crea en un subtipo de App Service Plan que es un poco más demandador la manera en como se consume como tal.

- La interación con el Serverless/Worker es que la API entrega mensajes o tareas a la Queue y la Function App/Worker las ejecutará automáticamente por su orden de llegada.
- **El Worker podría ocupar información de la Base de Datos, entonces podríamos hacer que el Worker haga peticiones a la Base de Datos por medio de la API.**

> [!NOTA]
> El worker puede hacer peticiones a la API, pero la API no debe hacer peticiones hacia el Worker

- El Worker también estará conectado con la PokeAPI para que pueda hacer consultas a los datos de esa API.
- **El worker básicamente se está conectando a las API del sistema.**
- En un solo ACR tendremos dos repositorios o dos contenedores respectivos para la API y la UI.

- **La Function App necesita de un App Service con configuraciones específicas y de un Sotage Account para poder ser desplegado y funcionar.**
- La Funcion App la crearemos con FastAPI.

> [!DANGER]
> En la creación de imágenes de Docker, el comando de CMD debe tener las siguientes caraterísticas:
> ```
> CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
> ```

> [!IMPORTANT]
> Al vincular nuestro contenedor con el Container Regsitry de Azure, tenemos que loggearnos en Azure desde la terminal del proyecto que Dockerizamos.

Comando para crear imágen

```
docker buildx build --platform linux/amd64 -t pokeapi-dev:latest . --load
```

Comando para crear contenedor

```
docker run -d -p 8000:8000 --name pokeapi-container --env-file .env pokeapi-dev:latest
```

Comando para loggear y vincular proyecto con Container Registry de Azure

```
az acr login --name acrpokequeuedev
```

Comando para relacionar nuestra imagen con el repositorio de ACR
Creando tags para relacionar nuestra imagen con el ACR
Comandos para crear las imágenes que enviaremos al ACR

```
docker tag pokeapi-dev:latest acrpokequeuedev.azurecr.oi/pokeapi-dev:latest
docker tag pokeapi-dev:latest acrpokequeuedev.azurecr.oi/pokeapi-dev:0.0.0
```

Command to send/push our Docker imagen to the ACR

```
docker push acrpokequeuedev.azurecr.io/pokeapi-dev:0.0.0
docker push acrpokequeuedev.azurecr.io/pokeapi-dev:latest
```

Se realizó también la vinculación de la Webapp con el ACR 

[Archivo con pasos para relacionar Webapp con ACR](../../Unidad II/Docker/3_PUSH_ACR_AZURE_WEBAPPS.md)

**El Driver para conexión con la DB utilizado es:**

```
SQL_DRIVER=/opt/homebrew/lib/libmsodbcsql.17.dylib
```

- Cuando instalemos la librería para la conexión con la DB, en mi máquina local el Driver se instalará en esa dirección.
- Sin embargo cuando se haga un nuevo release, tendrá que ser la dirección de la máquina de Linux donde trabajaremos.

> [!IMPORTANT]
> - Una buena práctica de bases de datos es no crear objetos sobre dbo
> - dbo es un esquema sensible porque ehí tenemos los objetos clave de nuestra base de datos.
> 

Creación de esquema en DB:

```
CREATE SCHEMA pokequeue;

create table pokequeue.messages (
	id INTEGER IDENTITY(1,1) PRIMARY KEY,
	message VARCHAR(255) NOT NULL
);

INSERT INTO pokequeue.messages([message]) values ('POKE QUEUE REQUEST');

SELECT * FROM pokequeue.messages;
```

> [!NOTA]
> - Si su Webapp relacionada con el ACR no reacciona, puede ver los Logs de la sección de Deployment Center para ver que está pasando con la imagen.
> - Según mi investigación, la Webapp debería correr en el puerto 80, pero la imagen corre en el puerto 8000:8000
> - **En las variables de entorno de la Webapp estaba una variable WEBSITES_PORT = 80, se elimina para que la Webapp pueda correr en el puerto indicado desde la imagen.**

Hechas las configuraciones ya debería funcionar:

![[Pasted image 20250729115915.png]]

- Ahora en el Dockerfile hay que hacer configuraciones para poder instalar el Driver SQL en la imagen

Actualmente las configuraciones sin el Driver son:

```
FROM python:3.12.6-slim-bullseye

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN rm .env

EXPOSE 80

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```


> [!NOTA] Investigar
> - Automatización de variable de entorno - DevOps
> - Azure Secrets
> - Github Actions - Automatización de publicación de imágenes
> 


