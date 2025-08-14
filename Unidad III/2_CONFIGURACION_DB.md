AZ LOGIN
- La idea es mandar peticiones desde el Front la petición de generar un CSV con todos los Pokemones de una categoría, y guardar la petición dentro de un Queue Storage
- Mediante el Servicio Serverless se hará el consumo de la PokeAPI para poder tomar todos los Pokemones y guardarlos en CSVs dentro del BLOB Storage.

- Para poder llevar la transaccionabilidad de las peticiones, estado de la petición hecha por la UI se necesitará la base de datos.

Script para la creación de los primero objetos de la DB

```
CREATE SCHEMA pokequeue;

create table pokequeue.messages (
	id INTEGER IDENTITY(1,1) PRIMARY KEY,
	message VARCHAR(255) NOT NULL
);

INSERT INTO pokequeue.messages([message]) values ('POKE QUEUE REQUEST');

SELECT * FROM pokequeue.messages;
```

Script para la configuración de los usuarios de DB

```
-- Ejecute este comando en la BD master
CREATE LOGIN PokeQueueApp WITH PASSWORD = 'Password1234';

-- Ejecute estos comandos en la DB pokequeue_db
CREATE USER PokeQueueAppUser FOR LOGIN PokeQueueApp;

GRANT SELECT ON SCHEMA :: pokequeue to PokeQueueAppUser;

GRANT INSERT, UPDATE, DELETE ON SCHEMA :: pokequeue to PokeQueueAppUser;

GRANT EXECUTE ON SCHEMA :: pokequeue to PokeQueueAppUser;
```

> [!IMPORTANT]
> La buena práctica es crear un usuario diferente por aplicación. Esto es parte del trabajo de los DBA.

- **La creación de los objetos en la DB siempre los haremos con el usuario Admin.**

- El diagrama E-R del sistema es muy sencillo, básicamente se tendrán los estados de  las peticiones de los reportes, que son nuestra entidad fuerte o tabla de mantenimiento/catálogo y tendremos la tabla transaccional donde tendremos las relaciones donde la tabla de peticiones tendrá relaciones 1:1 con la tabla de status, y la tabla de status con la de peticiones será de 1:M

Creación de tablas:

```
CREATE TABLE pokequeue.[status](
	id INT IDENTITY(1,1) PRIMARY KEY,
	[description] VARCHAR(255) NOT NULL
);

INSERT INTO pokequeue.[status] ([description])
VALUES ('sent'),('inprogress'),('deliver'),('failed');

SELECT * FROM pokequeue.[status];

CREATE TABLE pokequeue.[requests](
	id INT IDENTITY(1,1) PRIMARY KEY,
	[type] NVARCHAR(255) NOT NULL,
	id_status INT NOT NULL,
	[url] NVARCHAR(1000) NOT NULL,
	created DATETIME DEFAULT GETDATE(),
	updated DATETIME DEFAULT GETDATE(),
	FOREIGN KEY (id_status) REFERENCES pokequeue.[status](id)
);

SELECT * FROM pokequeue.[requests];
```

- Como el procedimiento almacenado se ejecutará desde la API, el OUTPUT de hacer un INSERT, UPDATE o DELETE es diferente a hacer un SELECT. 
- Por estándar, todas las consultas realizadas desde la API deben terminar con un SELECT
- **SET NOCOUNT ON** si hay un INSERT, UPDATE o DELETE dentro de la lógica del Script que generamos, que no imprima o lo mande como parte del resultado del arreglo, lista, colección o tupla de objetos que Pyodbc va a poder recolectar cuando se ejecute el procedimiento almacenado.
- **SET NOCOUNT ON** es para que el output principal siempre tenga un SELECT

- Hasta que no se ejecute un SELECT dentro del SP no deja que se ejecute otro proceso.

```
SET NOCOUNT ON;
```

Script del procedimiento almacenado

```
ALTER PROCEDURE pokequeue.create_poke_request(
@type NVARCHAR(255), @sample_size int)
AS
SET NOCOUNT ON;

INSERT INTO pokequeue.[requests](
	[type],
	[url],
	id_status,
	sample_size
) VALUES (
	@type,
	'',
	(select id from pokequeue.[status] where description = 'sent'),
	@sample_size
)

SELECT MAX(id) as id from pokequeue.[requests];
```

Ejecutar el procedimiento almacenado:

```
exec pokequeue.create_poke_request 'fire', 15;
```

- **El output del procedimiento almacenado es el mensaje que enviaremos por la cola.**

























