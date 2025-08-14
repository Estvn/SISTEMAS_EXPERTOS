
# Preparando servicio de Azure Functions

- Una vez que ya se pueden mandar mensajes en el Queue Storage, ya se pueden empezar a procesar los procesos Background con la Function App para poder tomar el mensaje que se escribe en la cola y poder hacer una operación o transacción con el mensaje que se está depositando.

La Function App tiene que tener una configuración con la versión de Python que se utilizará

```
site_config {
	application_stack {
		python_version = "3.12"
	}
}
```

# Creando un ambiente nuevo de Python

- En el video se creó un ambiente donde se hará el desarrollo del código Background del proyecto
- Se crea el proyecto serverless en un entorno de python diferente porque Azure Functions soporta esta versión de Python sin errores

```
pyenv virtualenv 3.11.8 poke.function.env
```

- Ya configurado el entorno habría que instalar las CLIs de las FunctionApps y los Cores que se estarán configurando para poder hacer toda la creación de funciones dentro del proyecto

# Instalando Core y CLI de Azure Functions

Creando entorno de Python para el código Background

```
python3 -m venv serverless-env
. serverless-env/bin/activate
```

Instalar Azure Functions mediante el comando brew
Este comando de usa en MAC

```
brew tap azure/functions
```

Esta es la documentación para instalar Azure Functions en Windows

https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-python

**Estos son los comandos que yo usé para instalar Azure Functions en Linux**

```
wget -q https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
sudo apt-get install azure-functions-core-tools-4
```

Evidencia de Azure Functions instalado (Este entorno serverless-env era parte un proyecto que cree previo a entender que serverless inicializa un nuevo proyecto)

![[Pasted image 20250804234431.png]]

# Creando proyecto Serverless (Azure Functions)

Comando para inicializar proyecto de Azure Functions (Serverless)

```
func init poke.queue.function --python
```

![[Pasted image 20250804234656.png]]

Librerías usadas en proyecto Serverless 

```
azure-functions
requests
python-dotenv

pandas
azure-storage-blob==12.22.0
```

------------------------

> [!NOTA]
> Una vez ya hechos todos los pasos anteriores e inicializado el proyecto Serverless queda agregar la función que se usará al momento de interactuar con las Function Apps

# Creando Función Serverless

Usar este comando para crear funciones Serveless
Tiene que estar ubicado dentro de la carpeta del proyecto

```
func new --name QueueTriggerReport
```

- Le va a salir una lista de opciones, preguntando si la función se va a ejecutar en función de algún evento de uno de los servicios de la lista.

![[Pasted image 20250805001232.png]]

- En el tutorial de Azure Functions de Windows, ellos enseñan como usar Serverless con HTTP como disparador de eventos.
- Incluso podemos hacer que la función Serverless se ejecute cada cierto tiempo con Timer Trigger.

- **En nuestro proyecto usaremos Queue Trigger donde un evento de Queue será el disparador de evento que ejecutará la función.**

![[Pasted image 20250805002006.png]]

- **Los valores que pide durante la creación de la función pueden ser modificados más adelante dentro del código.** 
- Todo este proceso agrega automáticamente una función en el archivo `function_app.py` 

------------------------

**Algo importante sobre el valor "AzureWebJobsStorage"**

![[Pasted image 20250805002507.png]]

- Este valor no es recomendable asignar valores en el dentro del código
- Este AzureWebJobsStorage ya se encuentra configurado en la Function App

![[Pasted image 20250805002744.png]]

- El Connection String asociado a la variable es el Connection String del Storage Account que se creó y se relacionó al Azure Functions (Servicio Serverless)

**Ahora agregamos el Connection String del Queue Storage**

![[Pasted image 20250805003101.png]]

En la función se agrega la variable con el Connection String asociado

![[Pasted image 20250805003246.png]]

**Comando para iniciar la función y proyecto de Azure Functions:**

```
func start
```

Probé ejecutando un Endpoint que impacta en la DBy la Queue, y el evento de la Queue activa la función Serverless

![[Pasted image 20250805003927.png]]

![[Pasted image 20250805003946.png]]

> [!WARNING]
> - Cuando se realiza la escritura de un mensaje en la cola, Azure Functions toma el mensaje y lo procesa, una vez terminado hace unqueue / elimina el mensaje del Queue Storage
> - Casi nunca podrá ver el primer mensaje en el Queue Storage, a menos que haya una cantidad masiva de mensajes si podrá ver algunos en el almacenamiento.

- Cuando un proceso Backgroud está escuchando la Queue podrá procesar los mensajes y realizar unqueue del Queue Storage.

# Publicar Función en Servicio Azure Functions

Conectese a Azure desde la terminal ubicado en el proyecto Serverless

```
az login
```

Publique la función en el Azure Functions con el siguiente comando (use el nombre del Servicio):

```
func azure functionapp publish serverlesspokequeuedev
```

Ejecutando comando de publicación de función:

![[Pasted image 20250805011601.png]]

Publicación de función finalizado:

![[Pasted image 20250805011621.png]]

> [!NOTA]
> No olvide agregar la variable de Connection String del Queue en las variables de entorno de Azure Functions

Agregando variable de entorno en Azure Functions

![[Pasted image 20250805011245.png]]

Función publicada en el servicio de Azure Functions:

![[Pasted image 20250805011720.png]]
![[Pasted image 20250805011915.png]]

- Una vez publicada tarda un tiempo considerable en ser utilizable 
- Como usamos servicios básicos y de prueba, se demora un poco en terminar sus configuraciones.

- **En la primera prueba realizada de la función tarda un poco más a diferencia del entorno local.**
- Se tendrían que analizar los temas de latencia 

![[Pasted image 20250805012835.png]]













