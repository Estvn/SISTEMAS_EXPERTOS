
- Anteriormente se realizó la configuración de un ETL con la herramienta de Azure Data Factory para poder hacer migración de data, y poder usarla para tener un Endpoint encargado de traer una carga masiva de información.

Haremos dos configuraciones:
1. Diseñar el Endpoint encargado de traer una carga masiva de datos. 
	- Por el momento no vamos a normalizar el modelo de datos.
	- Solo queremos mostrar la funcionalidad de las bases de datos de caché y poder monitirizar los tiempos de la mejor manera posible.
2. La implementación del modelo de datos que nos ayudará a leer la data de la tabla de la base de datos.

# Implementación de Modelo de Datos

Esta es la implementación del modelo de datos de la tabla:

```
from pydantic import BaseModel, Field, field_validator
from typing import Optional

class SeriesCatalog(BaseModel):

	id: Optional[int] = Field(
		default=None,
		ge=1,
		description="Auto-generated ID, optional for input"
	)

	name: Optional['str'] = Field(
		default=None
	)

	original_name: Optional['str'] = Field(
		default=None
	)

	overview: Optional['str'] = Field(
		default=None
	)

	status: Optional['str'] = Field(
		default=None
	)

	original_language: Optional['str'] = Field(
		default=None
	)
```

- Las clases de modelos de datos heredan o extienden de la lase Basemodel de Pydantic.
- Field es una función de Pydantic donde se escribe la estructura de un campo de una tabla en la bade de datos.

> [!NOTA]
> La ejecución de las consultas en FastAPI tiene que declararse de forma asíncrona.

- Algunos Endpoint no son comerciales, por ejemplo, un Endpoint para obtener catálogos de películas no tiene que tener seguridad o autorización de uso, si los clientes finales deben tener acceso a esta información.

- **Puede haber endpoints que son consumidos por admininstradores, otros que son consumidos por usuarios autenticados.**
- El nivel de validación de loss Endpoint dependerá del tipo de usuario que lo use.

- Si los recursos no permiten la creación de varias APIs, si es su responsabilidad como programador agregar validaciones a los Endpoints.

- **En ninguna aplicación es factible que un Endpoint traiga una cantidad masiva de data.**
- Lo ideal es paginar la los Endpoints de las APIs, o realizar otra estrategia para garantizar que no se va a saturar el servidor con Endpoints mal implementados.

# Herramientas de Monitorero de Salud de Endpoints

> [!NOTA]
Cuando implementamos Endpoints, queremos monitirizar la salud de los servidores que estamos implementando

- Este curso está más orientado a infraestructura y buenas prácticas de programación y gestión de proyectos.
- Necesitamos hacer la implementación de alguna herramientas que nos ayude a monitorizar y poder ver como están consumiendo nuestra API en particular

- **DataDog** tiene una capa gratuita de 7 días donde uno puede levantar el agente que va a estar escuchando a la API y todos los logs que se van generando.
- Esta herramienta nos ayudará a monitorear la salud del proyecto que estamos realizando 

- **Grafana** es otra herramienta que nos ayudaría a dar seguimiento a la salud de nuestro servidores y como se están ejecutando.

# Creación del Servicio Azure Application Insigths

> [!NOTA]
> - En este proyecto usaremos el servicio **Azure Application Insights** para generar telemétricas de los proyectos que queramos hacer el Attach y hacer un monitoreo de los servicios que vamos implementando.
> - Puede ver la documentación con Terraform para la implementación de este servicio de telemétrica.

- Cuando cree el servicio Insights, generará otro servicio llamado **Los Analytics Workspace**, que es otro recurso propio de Log Analytics que va de la mano con el servicio de telemétrica.
- Una vez creado el Azure Application Insights, nos devolverá el **Connection String** para agregarlo en las variables de ambiente

![[Pasted image 20250713234912.png]]

![[Pasted image 20250713234932.png]]

- **Normalmente cuando levantamos APIs, casi siempre hay que configurar Endpoints de salud y Endpoints de Telemétrica.**
- Esos son los Endpoints que constantemente se están ejecutando y están chequeando la salud de nuestra aplicación ya montada en el recurso que hallamos configurado.

- En cualquier nube, se configuran estos tipos de Endpoints para estar monitoreando la salud de los Endpoints.
- Va a depender después donde se querrá serializar esa telemétrica de la API y la tecnología que se haya elegido en particular.

- **Casi siempre con todas las herramientas de telemétrica se tiene que crear un archivo para el manejo de las configuraciones de la telemétrica, inicializarlo junto con la App, etc., esto será más de configuración.**

> [!NOTA]
> El archivo telemetry.py del vídeo solo es una configuración genérica de la telemétrica, pues la mayoría de las herramientas de telemétrica piden configuraciones generales de la herramienta dentro de la aplicación.
> Este archivo es solamente configuraciones, no contiene lógica de negocio.

**En el proyecto se instalan dos librerías para el manejo de telemétrica**

```
pip install azure-monitor-opentelemetry
pip install opentelemetry-instrumentation-fastapi
```

- La librería opentelemetry tiene una implementación para FastAPI.

![[Pasted image 20250714000240.png]]

# Manejo de Logs

- Normalmente una de las prácticas que realizamos en la programación, es llenar nuestra aplicación de Logs.
- **Lo correcto es manejar los logs con un LOGGER**
- El Logger hace la serialización o la salida para que el programador sepa hasta que punto se está manejando o ejecutando la aplicación.

- **Aquí hace más sentido el Servicio de monitoreo, porque todos nuestro Logs y serialización sucede dentro del Application Insights cuando se implementa.**

> [!NOTA]
> - En el archivo de telemetry se han importado unas librerías y agregado configuraciones para llevar de manera más formal los logs de nuestra API.
> - Se crean ciertas funciones para llevar la ejecución síncrona de las telemétricas que vamos a estar capturando.

![[Pasted image 20250714002921.png]]

El servicio de telemétrica ofrece varias gráficas para ver el estado de respuesta de los Endpoints

![[Pasted image 20250714005057.png]]
![[Pasted image 20250714005114.png]]
![[Pasted image 20250714005224.png]]

- Estas herramientas son potentes y buenas para llevar la gestión de los recursos que vamos levantando, y poder ver la salud de los Endpoints que vamos desarrollando.

> [!WARNING]
> En el main.py se agregaron algunas funciones para el manejo de las telemétricas.

- Los servicios de telemétrica suelen ser costosos.
- Se puede ver tardanza de solicitudes y errores.

- Se usan bases de datos de caché, porque a nivel de performance o a nivel de monitoreo de los Endpoints que implementamos comenzamos a ver comportamientos no deseados o comportamientos que deseamos manejar.

- **No podemos hacer cache de llamadas muy personalizadas para un usuario, o de Endpoints con información muy cambiante**
- Hay que ser muy selectivos para saber que Endpoints son los que se tienen que cachear.

































































