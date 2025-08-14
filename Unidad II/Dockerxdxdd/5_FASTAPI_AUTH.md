
> [!DANGER]
> Antes de seguir con esta parte 4 tiene que ver este vídeo:
> - https://www.youtube.com/watch?v=ACCNL9l2VCU

```
pip install fastapi
pip install uvicorn
pip install pyrebase
pip install firebase-admin
pip install python-dotenv
pip install pyodbc
pip install redis
pip install azure-monitor-opentelemetry
```

- **En este vídeo se hará cacheo de nuestra API con Redis.**
- Usaremos Datadog para análisis de datos en el Backend.

 - El punto es ver como se hace el consumo o las peticiones de cada uno de los Endpoints que se van desarrollando.
 - Vamos a usar el Token que nos entrega la aplicación para autenticar los Tokens.

- **En POSTMAN la asignación de las variables en un nuevo proyecto es últil para usar valores que se repiten en las rutas.**

```
{{domain}}/
```

# Decoradores en FastAPI

- Lo más fácil sería definir los headers y como mandaremos nuestro JWT y agregar validaciones en los Endpoints, pero esto es justo lo que no queremos hacer.
- **Necesitamos agregar decoraciones en cada una de las funciones que se ejecutan cuando se llama a un Endpoint.**

- Estos decoradores podemos irlos agregando en todos los Endpoint en los que queramos agregar esa capa de validación o autenticación de datos.

- Un decorador tiene una sintaxis que empieza con `@`, tipo `@app.get("/")`
- El decorador recibe como parámetro una función, que vienen siendo la función que está justo debajo del decorador (la función que se ejecuta al llamado de un Endpoint).

- Los decoradores vienen de un concepto de la programación funcional, algo conocido como funciones de orden superior. 
- **Las funciones de orden superior son básicamente funciones que reciben como parámetro otra función, alteran la estructura de esa función y retornan una nueva función.**

> [!NOTA]
> En la carpeta de security están diseñados los decoradores.

- Hacemos nuestra propia lógica de los decoradores, porque podemos hacer nuestra propia lógica, como autorizar a un usuario a utilizar un Endpoint.
- Los usuarios pueden recibir Tokens, pero en el payload del Token estará definido si un usuario puede usar un Endpoint o no.

- **Los decoradores de FastAPI se decoran con el decorador wrap.**

```
def validate(func):
	@wraps(func)
	async def wrapper( *args, **kwargs ):
		request = kwargs.get('request')
		if not request:
		raise HTTPException(status_code=400, detail="Request object not found" )
```

- Los parámetros \*args y \*kwargs nos ayudan a acceder a los parámetros de ejecución y los parámetros de configuración.

```
@app.post("/series", response_model=SeriesCatalog, status_code=201)
@validateadmin
	async def create_new_series(request: Request, response: Response, series_data: SeriesCatalog) -> SeriesCatalog:
		cs = await create_serie(series_data)
		return cs
```

- El decorador personalizado va a recibir la modificación de los decoradores `@app`
- Los decoradores `@app` tratan a las funciones como si fuesen un llamado HTTP que estamos haciendo a un servidor Web.

- En el momento que hacemos un llamado a HTTP, automáticamente obtenemos objetos por defecto (request, header, response, etc.)
- **Los decoradores hacen que las funciones se comporten como peticiones HTTP**
- **Por eso las funciones tiene los objetos que se crean durante la ejecución de peticiones HTTP**.

> [!NOTA]
> Los decoradores hacen que las funciones debajo de ellos se comporten como peticiones HTTP.

- Por esta razón, en nuestro archivo security donde estamos definiendo los decoradores, tenemos la certeza que vamos a poder invocar uno de los atributos u objetos que son propios de una petición HTTP, como por ejemplo, el objeto request

# Agregando Validaciones en los Decoradores

- Si el objeto como el request no existe, significa que la función no fué decorada para comportarse como una petición HTTP como tal.
- Si la función que decoramos no es una función pensada para un llamado HTTP, podemos agregar validaciones buscando objetos request, y si no existe lanzar una excepción.

- Normalmente los request, parte de lo que tienen es el atributo de la autorización

> [!NOTA]
> En postman, las autorizaciones se pueden agregar en la sección de autorizaciones, o directamente en el header.

- En los decoradores de security podemos buscar valores de validaciones en el header del request que tiene la función decorada.

```
def validate(func):
@wraps(func)

	async def wrapper( *args, **kwargs ):
	request = kwargs.get('request')
	
	if not request:
		raise HTTPException(status_code=400, detail="Request object not found" )

	authorization: str = request.headers.get("Authorization")
	if not authorization:
		raise HTTPException(status_code=400, detail="Authorization header missing" )

```

Ya agregamos dos validaciones:
- La primera validación es para ver si la función está decorada para tener comportamiento de solicitud HTTP.
- La segunda es para validar si en los headers de la petición HTTP están los valores de autorización.

**Ahora sigue la implementación del estándar del Bearer Token**

# Implementación del Estándar de Bearer Token

- Es un esquema en el que asociamos un Token para hacer validaciones
- Es una práctica bastante común en la implmentación de Endpoints.

- Para agregar esta validación se usan dos token o palabras, el primero es el bearer y el segundo es el Token que se usará para autenticar los Endpoints.

Aquí se valida si viene bearer:

```
schema, token = authorization.split()
	if schema.lower() != "bearer":
		raise HTTPException(status_code=400, detail="Invalid auth schema" )
```

- Lo siguiente es tomar nuestro Token y decodificarlo para acceder al Payload de nuestro JWT
- El Payload contiene la información que nos proveen desde el JWT.

Podemos hacer validaciones de invalidación o expiración de los datos:

```
try:
	payload = jwt.decode( token , SECRET_KEY , algorithms=["HS256"] )
	email = payload.get("email")
	firstname = payload.get("firstname")
	lastname = payload.get("lastname")
	active = payload.get("active")
	exp = payload.get("exp")
	
	if email is None or exp is None or active is None:
		raise HTTPException(status_code=400, detail="Invalid token 3" )
	
	if datetime.utcfromtimestamp(exp) < datetime.utcnow():
		raise HTTPException( status_code=401, detail="Expired token" )
	
	if not active:
		raise HTTPException( status_code=403, detail="Inactive user" )

request.state.email = email
request.state.firstname = firstname
request.state.lastname = lastname

except PyJWTError:
	raise HTTPException( status_code=401 , detail="Invalid token or expired token" )
return await func( *args, **kwargs )
```

- Los datos validados son los más importantes y que forzosamente tienen que existir.
- Se actualiza el estado del request con los datos ya validados
- En el retorno entregamos el flujo a la función, junto con los mismos parámetros de invocación o estado.

En la creación completa del decorador, al final retornamos el wrapper 

```
def validate(func):
	@wraps(func)
	async def wrapper( *args, **kwargs ):

		request = kwargs.get('request')

		if not request:
			raise HTTPException(status_code=400, detail="Request object not found" )

  

		authorization: str = request.headers.get("Authorization")

		if not authorization:
			raise HTTPException(status_code=400, detail="Authorization header missing" )

  
		schema, token = authorization.split()

		if schema.lower() != "bearer":
			raise HTTPException(status_code=400, detail="Invalid auth schema" )

		try:

			payload = jwt.decode( token , SECRET_KEY , algorithms=["HS256"] )
			email = payload.get("email")
			firstname = payload.get("firstname")
			lastname = payload.get("lastname")
			active = payload.get("active")
			exp = payload.get("exp")

			if email is None or exp is None or active is None:
				raise HTTPException(status_code=400, detail="Invalid token 3" )
	
			if datetime.utcfromtimestamp(exp) < datetime.utcnow():
				raise HTTPException( status_code=401, detail="Expired token" )
	
			if not active:
				raise HTTPException( status_code=403, detail="Inactive user" )
			
			request.state.email = email
			request.state.firstname = firstname
			request.state.lastname = lastname

		except PyJWTError:
			raise HTTPException( status_code=401 , detail="Invalid token or expired token" )

	return await func( *args, **kwargs )
return wrapper
```

- El wrapper es el decorador que se ha creado.

**La declaración del decorador tiene que ser exactamente de la siguiente forma:**

```
@app.post("/series", response_model=SeriesCatalog, status_code=201)
@validateadmin
async def create_new_series(request: Request, response: Response, series_data: SeriesCatalog) -> SeriesCatalog:
	cs = await create_serie(series_data)
return cs
```

- Porque el decorador validate ya tiene que ser una función filtrada y tratada como HTTP request.
- Tiene que estar antes de @app para filtrar los request, y entregarlos validados a la función con comportamiento de solicitud HTTP

# Pasando Parámetros para el uso de Decoradores

- Si agregar un decorador para una función en FastAPI, tiene que asignar los siguientes parámetros a la función 

```
@app.post("/series", response_model=SeriesCatalog, status_code=201)
@validateadmin

async def create_new_series(request: Request, response: Response, series_data: SeriesCatalog) -> SeriesCatalog:
	cs = await create_serie(series_data)
return cs
```

- Por temas de reducción de consultas en la base de datos, es ideal manejar los tiempos de acceso de los Tokens, para que un token funcione por un determinado tiempo para hacer múltiples consultas, y cuando se expire hacer nuevamente un conexión a la base y generar un nuevo Token.
- En lugar de abrir una conexión a la base cada que se accede a un Endpoint que tiene seguridad de autorizaciones.









































































