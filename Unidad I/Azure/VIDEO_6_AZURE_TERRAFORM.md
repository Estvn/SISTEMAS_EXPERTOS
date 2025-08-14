
# Configuración del Storage Account en Azure

- Dependiendo de la naturaleza del proyecto, si necesitamos almacenar imágenes que el usuario quiera subir, o la evidencia de una documentación, todo esto depende del análisis y diseño de sistemas, los features que compongan el proyecto que se esté trabajando.

- Pueda que requiera el tipo de almacenamiento especial de **BLOBS** donde se serializa mediante BYTES, este tipo de archivos no estructurados los guardados en el tipo de bases de datos de BLOBS.
- Otro caso es cuando se crean colas para procesar ciertos mensajes destinados para resetear passwords, escalar imágenes, y procesos background podrían ser procesados mediante un mensaje de una cola, y posteriormente con un serverless o una functionapp hacer cierto tipo de ejecución o cierto tipo de implementación.

- Un BLOB Storage puede ser un Data Lake. 
- Un Data Lake es un conjunto de información (puede ser no estructurada) sin un orden.
- **LRS** Son las siglas de redundancia local.
- El servicio de BLOB Storage suele crearse más rápido que una base de datos relacional porque no tiene la lógica transaccional ni de constraints.

Dentro de este servicio se pueden crear estos tipos de almacenamiento: 

![[Pasted image 20250610085429.png]]










