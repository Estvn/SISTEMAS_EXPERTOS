
- Al intentar descargar el archivo CSV sin el Signature Token, no se podrá acceder a el por la seguridad ofrecida por el BLOB Storage
- **Necesita el Signatue Access Token para descargar archivos del BLOB**
- El SAT se obtiene al habilitar una sesión y un cliente de BLOB en la API y usando la función de Azure `generate_blob_sas`

- Una vez obtenido el Token, se concatena con las URL de descarga de los archivos que se almacenó en la DB

La estructura concatenada lucirá de esta forma:

```
saccountpokequeuedev.blob.core.windows.net/reportes/poke_report_56.csv?se=2025-08-06T15%3A25%3A15Z&sp=r&sv=2024-08-04&sr=b&sig=DiclVKJ//P%2B1wUFwT4zIwpU7sXDQ6YAF%2Bnwwf%2BizltI%3D
```

> [!NOTA]
> El Signature Access Token se crea con un tiempo de expiración.

```
SELECT * FROM pokequeue.requests;

SELECT * FROM pokequeue.status;

DELETE FROM pokequeue.requests where url = '';

SELECT * from pokequeue.[status];

ALTER TABLE pokequeue.requests ADD active BIT DEFAULT 1;

UPDATE pokequeue.requests SET [active] = 1 where id BETWEEN 70 and 73;

select id, [type], active from pokequeue.requests where id = 64;

ALTER TABLE pokequeue.requests add sample_size int default null;
```

