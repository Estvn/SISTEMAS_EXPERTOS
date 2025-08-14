
- En este video se mostrará como se hará la autenticación desde la aplicación, tablas a usar, y el primer SP que nos ayudará a hacer las inserciones de las peticiones.

- Haremos un modelo que nos va a servir para poder interactuar con los Endpoints, se conoce normalmente como un **payload**
- **Un payload es la parte de la información que se transmite entre el cliente y el servidor, especificamente el cuerpo de la solicitud o respuesta.**

- En pyodbc los parámetros se manejan desde params "?" para evitar inyecciones SQL.

Librerías usadas en la API

```
fastapi
uvicorn
python-dotenv
pyodbc
azure-storage-queue==12.11.0
```

Librería de python para usar Queue Storage

```
azure-storage-queue==12.11.0
```

- Para conectar el Queue con la aplicación tome el Connection String del Storage Account donde estará el Queue, e ingreselo en el .env para conectar el servicio con la aplicación.

- La key se ingresa dentro de comillas dobles porque el String tiene muchos iguales que pueden generar conflicto.

```
AZURE_SAK=DefaultEndpointsProtocol="https;AccountName=saccountpokequeuedev;AccountKey=uJV6PVx/9hq8++G7wgg450UJBhTmtpi7zNqYJSWvWPEIiCzcC1AsftzuZGp76alr5rP6DFzP8qXO+AStUPZOYA==;EndpointSuffix=core.windows.net"

QUEUE_NAME=request
```

![[Pasted image 20250804201316.png]]
# Código para conexión con el Queue

```
import os
from azure.storage.queue import QueueClient, BinaryBase64EncodePolicy, BinaryBase64DecodePolicy
from dotenv import load_dotenv

load_dotenv()

class AQueue:

	def __init__(self):
		self.azure_sak = os.getenv("AZURE_SAK")
		self.queue_name = os.getenv("QUEUE_NAME")
		self.queue_client = QueueClient.from_connection_string(self.azure_sak, self.queue_name)
		self.queue_client.message_decode_policy = BinaryBase64DecodePolicy()
		self.queue_client.message_encode_policy = BinaryBase64EncodePolicy()

  
	async def insert_message_on_queue(self, message: str) -> None:
		message_bytes = message.encode('utf-8')
		self.queue_client.send_message(
		self.queue_client.message_encode_policy.encode(message_bytes)
	)
```

- Los mensajes una vez agregados en el Queue, ahora tiene que ser tomado y procesado por el Azure Function App.














