
- Implementación de bases de datos de caché con la implementación de nuestras APIs.

- Para el uso de almacenamiento de cache, una de las primeras cosas que necesitamos es una base de datos en donde por lo menos una de las tablas tenga un volúmen considerable de datos.
- La primera parte de estos videos va a consistir en cómo vamos a hacer la carga de data masiva dentro de la base de datos que ya debería estar creada.

Los recursos que vamos a necesitar para hacer este experimento serán:

- Un Azure Container Registry para poder guardar la imagen de la API que vamos a estar cargando con FastAPI.
- Una WebApp para hacer el release de la imagen o el contenedor que va a estar ejecutándose dentro de este recurso.
- El servidor de la base de datos junto con la base de datos donde migraremos la data en particular.
- El servidor Web con la WebApp
- Un Azure Data Factory como herramienta ETL para poder hacer el movimiento de la data
	- Al final será un pipeline supremamente sencillo que nos ayudará a hacer la carga de la data que usaremos para experimentar el uso de la DB de cache

- Azure Storage Account para poder almacenar la data cruda/row data/ source data
- Base de datos de caché

> [!NOTA]
> A este punto el tema del release de los servicios ya debería estar resuelto

- Como este proyecto será sobre el consumo de catálogos de datos no se requiere de autenticación no se necesita como tal.

- **No se necesita validaciones, ya que las reglas de las RESTFull APIs dice que los Endpoints que son de uso comercial, como mostrar información a los clientes, son Endpoints que no necesitan que autenticaciones en particular.**

- Pero se recomienda hacer un Endpoint con autenticación, que sirva para agregar un nuevo elemento al catálogo.

# Pasos Previos para la Configuración de la Caché

> [!NOTA]
> Use Kaggle para usar datasets ya definidos

- Tiene datasets tanto con simulaciones tanto buenas como malas.
- Tengo que buscar un dataset diferente al que sale en los vídeos
- En el vídeo se eligió un dataset de pleícular y series con más de 250,000 registros.

- En el video se cargaron solo 8 columnas de las 180 del dataset por medio de un notebook de python, cargando la data en un csv.
- **Este ejercicio del video busca algo un poco más masiva para hacer el experimento de como poder hacer el caché de las respuestas de las llamadas que se hagan de los Endpoints.**

- Una vez que ya tenga el dataset descargado en la máquina y limpiado con la data que quiero almacenar, lo siguiente es crear un contenedor en el BLOB Storage, y en el contenedor subir el archivo CSV con la data limpia.
- El CSV en el BLOB Storage se usará para importar la data a una tabla de la base de datos.

- Tiene que crear una tabla en la base de datos, y tenerla lista y con los campos adecuados para hacer la carga de la información del CSV a la tabla de la DB.

# Configuración de Azure Data Factory

- Azure Data Factory ofrece una interfaz donde se pueden configurar pipelines sencillos como copiar datos, tomar decisiones o hacer iteraciones.
- También puede crear Data Flows, si ya ha usado herramientas como SSAS, se parece mucho en el sentido que puede hacer JOINS, INSERTS, UPSETS, etc.
- **Azure Data Factory puede funcionar para crear ETLs.**


![[Pasted image 20250713175322.png]]


- Como vamos a construir un ETL, lo primero que haremos es definir los **Link Services**

# Creación de un ETL 

- Nos dirijimos a la sección de **"Manage"** y presionamos **New**
- Cuando presionamos New, nos aparecen todos los Link Services que Azure Data Factory tiene activos y nos ofrece para hace conexión a fuentes de datos.

- Podemos por ejemplo, conectarnos con una RESTFULL API y leer y migrar los resultados de una API hacia una tabla.
- Hay actividades que especifican que es una URL lo que queremos consumir
- **Podemos hacer conexión a un servicio en particular sin ningún problema.**

- También se puede hacer conexiones con otros servidores en la nube como AWS
- Hay que definir que se usará con el equipo de infraestuctura, definir las redes privada o públicas, gateways para hacer las conexiones, etc.

El primer servicio que vamos a conectar con Data Factory es el BLOB Storage:

![[Pasted image 20250713185159.png]]

![[Pasted image 20250713185340.png]]

El segundo servicio para crear Link es una Azure SQL DB

![[Pasted image 20250713185548.png]]

![[Pasted image 20250713185629.png]]

Los dos enlaces a los servicios ya están creados

![[Pasted image 20250713185714.png]]
# Creación de los Datasets

- Un dataset va a conectar con el CSV que tengo en el servicio BLOB Storage.
- Vamos a crear el dataset que va a estar conectado también con la base de datos.

- **En la sección de Author de Data Factory es donde se crean los datasets**
- Puede crear Datasets ordenados por carpetas, porque pueda que un Data Factory le sirva para muchos proyectos o configurar muchos pipelines.

## Crear Dataset para el BLOB

- **El Dataset será de tipo BLOB Storage, y y tenemos que indicar que dentro del BLOB Storage queremos comunicarnos con un archivo CSV.**

![[Pasted image 20250713190731.png]]

- Es importante seleccionar la opción de **First row as header**, porque un csv es texto plano, y la primera fila solo indica los nombres de las columnas

Puede ver la data con Preview Data

![[Pasted image 20250713191046.png]]

Aquí puede ver cada cambio de configuración en el Data Factory

![[Pasted image 20250713191211.png]]

## Crear Dataset para la DB

- Una vez creado el Dataset para el BLOB Storage, sigue la creación del dataset para la DB
- **Sigue los mismos pasos que la configuración del dataset del BLOB.**

![[Pasted image 20250713191802.png]]

# Creación del Pipeline

- En la sección de Author se puede acceder a la creación de un pipeline
- En la creación de pipeline, se puede automatizar la ejecución con decenas de herramientas que ofrece Data Factory.

- Seleccionamos la opción de Copy Data
- En General agregamos el nombre del elemento
- En Source referenciamos al CSV
- En Sink referenciamos a la tabla donde se enviarán los datos.
	- En Sink se puede elegir la opción de Insert, Upsert y Stored Procedure
	- Upsert sirve para buscar llaves primarias y hacer actualizaciones sobre esas filas

- En la sección de Mapping click en import schemas para verificar si los campos entre el csv y la tabla de la DB hacen Match.
- En Setting podemos definir el límite de datos a transferir, pero lo dejaremos por defecto

- Por último solo queda publicar los cambios.

![[Pasted image 20250713203624.png]]

- **Una vez configurado el pipeline y publicado los cambios, puede iniciar el ETL presionando Debug.**

> [!DANGER]
> - El error que puede surgir durante la ejecución del pipeline, esque las comas de los textos de la data hacen escape en los CSV, y esto lo considera como una nueva columna, con cada coma encontrada
> - Si surge un error durante la ejecución del pipeline, no hay ningún cambio en el almacenamiento de destino.

> [!DANGER]
> Recuerde en el servicio de DB permitir acceso a otros servicios de Azure

Configuración en el dataset del CSV para evitar errores

![[Pasted image 20250713204834.png]]


- Tuve otro error de llave duplicada, pero fue porque tenia 5 datos en la tabla, solo los borré.

**Ejecución del pipeline en progreso:**

![[Pasted image 20250713205200.png]]
**Información en la tabla de la DB:**

![[Pasted image 20250713205232.png]]

# Resumen

- Lo realizado en este vídeo solo fue una preparación del ambiente para que las pruebas con la base de datos Redis puedan mostrarse, simulando la situación de una aplicación real con cientos de miles de datos.
- **La cache lo que realiza es no ir a hacer consultas a la base de datos, sino que la respuesta se guarda en la DB de caché para hacer lecturas super rápidas de datos.**











