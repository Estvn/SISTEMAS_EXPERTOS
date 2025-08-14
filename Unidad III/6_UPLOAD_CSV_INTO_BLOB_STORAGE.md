
# Configuraciones en las funciones Serverless para subir CSV a BLOB Storage

- En este vídeo se harán todas las configuraciones relacionadas al BLOB Storage.
- Convirtiendo el arreglo de Pokemones en arreglos de Bytes que es la estructura que se necesita para hacer el submit dentro de los BLOBs dentro del Container donde estarán los CSV.

Librerías usadas en proyecto Serverless

```
azure-functions
requests
python-dotenv

pandas
azure-storage-blob==12.22.0
```

```
pip install pandas
pip install azure-storage-blob==12.22.0
```

![[Pasted image 20250805141413.png]]

Código para convertir los datos en CSV

![[Pasted image 20250805141616.png]]

- El CSV se almacenará temporalmente en el servicio de la Function App 
- Se escribirá como una entrada temporal en memoria para poder procesarlo

Para escribir el CSV temporalmente en la memoria del Serverless usamos la librería:

```
import io
```

![[Pasted image 20250805142412.png]]

- Al intentar descargar el archivo csv con la URL que se muestra para el archivo no se podrá descargar, porque se necesita algo llamado **signature access token** para poder descargarlo.
- Pero esa es la URL que queremos almacenar en nuestro BLOB Storage.

```
upload_csv_to_blob(blob_name, pokemon_bytes)
logger.info(f"CSV uploaded to blob storage: {blob_name}")

complete_url = f"https://{AZURE_STORAGE_ACCOUNT}.blob.core.windows.net/{AZURE_STORAGE_CONTAINER_NAME}/{blob_name}"

update_request(id, "deliver", complete_url)
```

# Subiendo cambios al Azure Functions

Publicando cambios al Azure Functions

```
func azure functionapp publish serverlesspokequeuedev
```

![[Pasted image 20250805154054.png]]

![[Pasted image 20250805155212.png]]

Cambios en el Server

![[Pasted image 20250805155306.png]]

> [!NOTA]
> La variables de entorno del proyecto Serverless agreguelas en el Azure Functions 

# Subiendo cambios al ACR

```
docker buildx build --platform linux/amd64 -t pokeapi-dev:latest . --load
```

```
docker buildx build --platform linux/amd64 -t pokeapi-dev:0.2.0 . --load

```

![[Pasted image 20250805164137.png]]

![[Pasted image 20250805164626.png]]

```
docker tag pokeapi-dev:latest acrpokequeuedev.azurecr.i0/pokeapi-dev:0.2.0

docker tag pokeapi-dev:latest acrpokequeuedev.azurecr.i0/pokeapi-dev:latest
```

Loggeando en el servicio

```
az acr login --name acrpokequeuedev
```

```
docker push acrpokequeuedev.azurecr.io/pokeapi-dev:0.2.0
docker push acrpokequeuedev.azurecr.io/pokeapi-dev:latest
```

![[Pasted image 20250805165850.png]]
![[Pasted image 20250805220355.png]]













