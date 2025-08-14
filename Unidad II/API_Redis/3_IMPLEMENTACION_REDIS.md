
- En el vídeo anterior se hizo la implementación de un Applicaction Insights para poder medir de una manera más profesional la salud de nuestra API
- Cuando hacemos la implementación de telemétricas para poder llevar la trazabilidad del servicio y como está corriendo en particular, se empieza a usar librerías de telemétricas dentro del proyecto de FastAPI.

En el video se usa la versión 5.0.1:

```
pip install redis
```

- **En el servicio creado de redis tiene que desmarcar la opción "Disable Access Key Authentication"** en la sección de Authentication
- Tenemos que deshabilitar la opción, porque vamos a usar otro tipo de autenticación, extrayendo la data de otra sección de Authentication para agregarlo en nuestra API.

- **En el servicio de Redis se configuró el Firewall para que permita cambios en la configuración del servicio solo desde una red.**

- Agregue en el .env la llave de acceso al servicio Redis

# Configuración de Redis

- En el archivo de redis_cache.py se encuentra toda la configuración de uso de redis.
- En el video solo se hizo un copy-paste del código, este archivo solo tiene configuraciones del servicio.

- La información de las bases de datos de caché pueden iniciarse con un tiempo de vida.
- Las DB de caché son volátiles, se puede delimitar un determinado periodo de tiempo para eliminar ciertos datos de la caché.

- Redis almacena los datos en forma de key-value, y tiene una infraestructura bastante robusta donde permiten gran cantidad de conexiones, y response super rápido, porque la información ya está calculada y no hay que hacer ninguna petición a la base de datos.


- Si el cálculo que estamos haciendo es muy complejo, y a nivel de servidor de DB SQL lleva su tiempo, una opción sería la creación de índices, meintras no afectemos el proceso de escritura.
- **La caché sería la segunda o tercera opción para optimizar la consulta de datos del negocio.**

> [!NOTA]
Las DB de Caché deben ser usada con información de catálogos, donde estamos seguros que la información no cambia dentro de una hora o un tiempo prolongado.

Estas son las funciones utilizadas para interactuar con la DB Redis

```
from utils.redis_cache import get_redis_client, store_in_cache, get_from_cache, delete_cache
```

Este es un ejemplo del nombre de Key que se creará en la Redis

```
SERIES_CACHE_KEY = "series:catalog:all"
```

- El estándar es "nombre_proyecto:que_informacion:filtro"
- series:catalog:all

Este es un ejemplo del Time To Live de una Key en la DB cache

```
CACHE_TTL = 1800
```

- 1800 segundos

**Un ejemplo de uso de la variable de Key y TTL:**

```
async def get_series_catalog() -> list[SeriesCatalog]:

	redis_client = get_redis_client()
	cached_data = get_from_cache( redis_client , SERIES_CACHE_KEY )

	if cached_data:
		return [SeriesCatalog(**item) for item in cached_data]

	query = "select top 10000 * from series.catalogs"
	result = await execute_query_json(query)
	dict = json.loads(result)

	if not dict:
		raise HTTPException(status_code=404, detail="Series catalog not found")

	store_in_cache( redis_client , SERIES_CACHE_KEY , dict , CACHE_TTL )
	return [SeriesCatalog(**item) for item in dict]
```

- Con este tipo de implementaciones hay ventajas y desventajas
- **La desventaja esque la función tiene que buscar en la cache y si no hay nada, es procesamiento de computo pérdido, pero la ventaja es cuando hay información para retornar en la cache y no es necesario ir a buscar en la base de datos.**

# Pruebas de la DB Cache Redis

- No puedo usar consola de redis del serivicio
- Me conecte al servicio desde mi terminal

Comando para conectarse al servicio de Redis desde Bash:

```
stvn@stvn-Precision-M2800:~$ redis-cli -h SeriesCache.redis.cache.windows.net -p 6380 -a 'qE2JAHcFDpWRFnjsw42hExb5FhtxRZhvqAzCaOinhdc=' --tls
```

```
redis-cli -h redis-animeproject-dev.redis.cache.windows.net -p 6380 -a 'TXCmEJAUT9cHiKq3193fG7szLI04yjlDEAzCaJPwoGk=' --tls
```

**Resultados del comando "SCAN 0" para ver la información en la DB Redis:**

![[Pasted image 20250715141026.png]]


Borrando todo de la cache para hacer una prueba de 0:

![[Pasted image 20250715141938.png]]

Primera solicitud y su tiempo de respuesta (11 segundos):
 
![[Pasted image 20250715142024.png]]

Segunda solicitud y su tiempo de respuesta (1.99 segundos):

![[Pasted image 20250715142057.png]]

- Según las configuraciones de código, después de 30 minutos, la información se va a eliminar de la base de datos de caché.
- **La buena práctica esque si vamos a hacer un POST, deberíamos eliminar lo que está en la caché porque un nuevo dato fué agregado, y se vuelve a insertar la información en la caché cuando se vuelve a llamar la información.**

# Headers de la API

- Los Headers de las APIs ayudan a autenticar y autorizar como ese Endpoint va a ser consumido y quien lo va a consumir.
- Ver el catálogo de series puede ser todo usuario, calificar una serie puede ser un usuario registrado, y crear una nueva serie debería se un usuario administrador.

- **Los correcto es separar las APIs**
- Debería haber un repositorio solo para tareas administrativas, y un repositorio con tareas de usuario.

- Si no hay presupuesto para la separación de APIs, entonces se tiene que agregar más seguridad en los Endpoints.

Enviando el Token en el header

![[Pasted image 20250715145606.png]]










