
- Lo que se hará en este vídeo es construir el Core principal del proyecto.
- Casi todo lo realizado en el vídeo son configuraciones, la lógica compleja del proyecto va a estar en la función serverless.

- Una vez leído del mensaje debemos actualizar la base de datos indicando que el mensaje de la Queue está en estado "inprogress"
- Luego se hará el consumo de la API de Pokemon, generar el CSV y escribirlo en el Storage Account, por último hacer el release de la aplicación.

Creando procedimiento para actualizar el estado de las peticiones:

```
CREATE PROCEDURE pokequeue.update_poke_request(
	@id int,
	@status NVARCHAR(255),
	@url NVARCHAR(1000)
)
AS
SET NOCOUNT ON;

UPDATE pokequeue.requests
	SET id_status = (select id from pokequeue.status where description = @status),
	url = @url,
	updated = GETDATE()
WHERE id = @id

SELECT 1 AS completed;
```

- Hasta este punto del vídeo hicimos 2 endpoints, uno para crear un tipo de Pokemon y otro para actualizar el estado del tipo del pokemon, dos funciones serverless, una que captura el mensaje en la cola (insertado por el endpoint de la creación del tipo de pokemon) y otra funcion que actualiza el estado del tipo del pokemon haciendo consumo del endpoint de actualización.

------------------------------

Probé la API de Pokemon desde Google Collab

![[Pasted image 20250805122442.png]]

![[Pasted image 20250805122717.png]]

Cuando hacía la insersión de type de Pokemon plant, da error porque ese tipo de Pokemon no existe:

![[Pasted image 20250805133057.png]]

Con type fire si funciona:

![[Pasted image 20250805134534.png]]










