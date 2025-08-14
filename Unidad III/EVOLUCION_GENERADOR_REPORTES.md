
# Objetivo del Proyecto

- Extender y adaptar esta aplicación base, añadiendo nuevas funcionalidades y demostrando habilidad para modificar y mejorar un sistema existente que debe permanecer desplegado y funcional en Azure.

# Arquitectura del Sistema

![[DiagramaPokeQueue.svg]]

# Tareas a Realizar (Total: 90 Puntos)

## Tarea 1: Implementar Eliminación Completa de Reportes (25 Puntos)

### Frontend (NextJs)

- Se ha añadido un botón de eliminar en cada fila de reporte, añadiendo una modal de confirmación como paso extra de seguridad, y al confirmar la eliminación se hace una llamada a la API que borra los registros de reporte de la Base de Datos y el BLOB Storage. Al llegar la confirmación al Front se refresca la lista de los reportes.

![[Pasted image 20250807205501.png]]

![[Pasted image 20250807205549.png]]

![[Pasted image 20250807205605.png]]
El registro 65 se ha eliminado:

![[Pasted image 20250807205642.png]]

Lista refrescada:

![[Pasted image 20250807205728.png]]

### Backend (FastAPI)

- Se ha creado un Endpoint de la API que recibe una solicitud HTTP con el método DELETE con el ID del elemento a eliminar.
- Este Endpoint desencadena una serie de acciones:
	- Primero, revisa si el elemento está "eliminado en la base de datos", si lo está, lanza un estado 404, si el elemento no está "eliminado", entonces se elimina de la base.
	- Una vez eliminado el elemento de la base de datos, va a eliminar el archivo CSV del BLOB Storage, y una vez realizado se envía la respuesta del Endpoint.

En esta demostración "eliminamos" el elemento 64:

Demostrando registro en la DB:

![[Pasted image 20250807211011.png]]

Demostrando registro en el BLOB:

![[Pasted image 20250807211031.png]]

Enviando petición:

![[Pasted image 20250807210835.png]]

Elemento eliminado:

![[Pasted image 20250807211100.png]]

![[Pasted image 20250807211118.png]]

![[Pasted image 20250807211138.png]]

La captura anterior no estaba actualizada y solo debían estar el reporte 63 y 64

![[Pasted image 20250807211157.png]]

Procedimiento almacenado para eliminar reportes:

```
ALTER PROCEDURE pokequeue.delete_poke_request(@id int)
AS
SET NOCOUNT ON;
BEGIN

	UPDATE pokequeue.[requests]
		SET [active] = 0
	where id = @id AND [active] != 0;

	IF @@ROWCOUNT > 0
		SELECT 1 AS response;
	ELSE
		SELECT 0 AS response;
END;

EXEC pokequeue.delete_poke_request 70;
```

## Tarea 2: Enriquecer Reporte con Detalles del Pokémon (40 Puntos)

### Worker (Azure Function - Python)

- Los archivos CSV que contenían como datos relevantes el nombre y la URL de información del Pokemon, ahora han sido actualizados agregando más campos con datos relevantes como hp, ataque, defensa, ataque-especial, defensa-especial, velocidad y un campo donde se guardan varias habilidades. 
- Estos datos han sido obtenidos realizando otro consumo a la PokeAPI por medio de un URL único por cada Pokemon del tipo que se obtuvo.

- Las funciones que viven en el Azure Functions como servicio "Serverless" han sido actualizadas para poder generar archivos CSV con más contenido.

![[Pasted image 20250808003850.png]]

## Tarea 3: Reportes con Muestreo Aleatorio (25 Puntos)

### Frontend (Next Js)

- En la UI antes de generar un nuevo reporte se solicita ingresar un número para la cantidad de registros deseados en el CSV del reporte

![[Pasted image 20250808111909.png]]

### Backend (API FastAPI)

- Se ha modificado el Endpoint de Backend para la creación de reportes para que se pueda obtener el número de Pokemons.
- Se ha modificado la base de datos añadiendo un campo de la cantidad de muestras que se desean registrar en el reporte

![[Pasted image 20250808114147.png]]

### Worker (Azure Function - Python)

- La Function App que hace el proceso Background ha sido modificada para poder obtener el número de registros por reporte, y a partir de ese número filtrar la cantidad de Pokemon que se va a registrar en el reporte.

En este ejemplo se pidieron 20 registros para el reporte:

![[Pasted image 20250808114429.png]]

En la UI se ha agregado el campo de la cantidad de registros en el reporte:

![[Pasted image 20250808114504.png]]

# Repositorios

- https://github.com/Estvn/ui-pokequeue
- https://github.com/Estvn/api-pokequeue
- https://github.com/Estvn/serverless-pokequeue
- https://github.com/Estvn/infraestructura-pokequeue
- https://github.com/Estvn/db-pokequeue




















































