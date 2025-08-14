
# Descripción del Sistema E-commerce

- Infraestructura escalable y robusta en la nube.
- Gestión de operaciones de venta en línea.
- Soportar alto volumen de transacciones.
- Proporcionar experiencia de usuario fluida a través de un portal principal.
- Permitir la gestión interna de productos y pedidos desde un BackOffice.
- Ofrecer capacidades analíticas avanzadas sobre los datos de venta para la toma de decisiones estratégicas.
- Plataforma capaz de manejar tareas intensivas o que no requieren una respuesta inmediata al usuario de forma eficiente en segundo plano.
- Asegurar que la experiencia de usuario no se vea afectada por procesos de larga duración, como confirmación de pedidos, actualización de inventarios complejos o generación de notificaciones masivas.

# Servicios identificados a usar

- Azure App Service - Webapp FrontOffice
- Azure App Service - WebApp BackOffice
- Azure App Service - WebApp API
- Queue storage - Cola de tareas sin respuesta inmediata
- Azure Functions - Serverless para ejecución de tareas en segundo plano

- Azure SQL Database - DB OLTP
- Storage Account - Almacenamiento de archivos
- Azure Cache for Redis - Almacenamiento de datos volátiles

- Azure SQL Database - DW
- Azure Data Factory - Orquestador del ETL

# Diseño de la Solución 


![[Arquitectura_Nube_Ecommerce.drawio.png]]

## Justificación de los componentes arquitectónicos

- **Dos WebServer para UI**. Separar responsabilidades entre un FrontOffice, BackOffice, y mejorar la seguridad.
- **DB SQL OLTP**. Alojar la información transaccional del negocio.
- **Storage Account.** Almacenar archivos (imágenes, pdf, etc) del negocio.
- **Webserver para API.** Maneja el acceso a los almacenamientos de datos del negocio. Una API compartida por ambas UI, ya que la UI del negocio no exige un costo computacional tan grande como para realizar una API única.
- **Queue Storage y Azure functions.** Manejar tareas en segundo plano y que no requieren respuesta inmediata, para evitar una alta carga de trabajo en el servidor que aloja la API.
- **Azure Cache for Redis.** Para alojar data frecuentemente visitada, carrito de compras, sesión del cliente, para lograr una UX fluida.
- **Azure Data Factory y DW**. Un orquestador para manejar el proceso ETL entre la DB OLTP del negocio y el DW que guardará los datos listos para realizar análisis estratégico.

#### Justificación más detallada del uso de los servicios (opcional)

- Cada tipo de usuario (clientes, editores) podrá acceder desde una **UI** en un servidor dedicado (**FrontOffice y BackOffice**).

- Las **UI** toman información de la **base de datos SQL** por medio de una única **API** (se ha decidido dejar una sola API, porque la gestión del negocio requiere de un costo computacional mucho menor que el realizado por los clientes).

- La UI de cliente prioriza la eficiencia y experiencia de usuario fluida, entonces la UI de cliente podrá enviar mensajes en **Queue storage** para pedidos, transacciones, etc., que luego serán ejecutadas en segundo plano por un **Serverless**.

- UI de cliente usará el **almacenamiento en caché (Azure Cache for Redis)** para alojar información como el carrito de compras, datos de sesión e información frecuentemente visitada, asegurando una **experiencia de usuario sin retardos**.

- Para el análisis de negocio se usa el **orquestador Data Factory**, para hacer el **proceso ETL** entre la **base de datos OLTP y el Datawarehouse**, para que luego la información del Datawarehouse pueda ser solicitada por la API o una herramienta externa (Power BI), y que la gestión del negocio pueda **realizar el análisis estratégico**.

# Evidencias de Despliegue

### Despliegue de los servicios de la infraestructura de aplicaciones

Creación del grupo de recursos: 

![[Pasted image 20250620120733.png]]

![[Pasted image 20250620120909.png]]

Creación de los tres Service Plan y sus respectivas Web Apps:

![[Pasted image 20250620144536.png]]

![[Pasted image 20250620144813.png]]

![[Pasted image 20250620144929.png]]

Creación de los servicios Queue Storage y Azure Functions (Servicios Serverless):

![[Pasted image 20250620212226.png]]

![[Pasted image 20250620212324.png]]

### Despliegue de los servicios de la infraestructura de almacenamiento

Creación de la DB OLTP 

![[Pasted image 20250620215008.png]]

![[Pasted image 20250620215100.png]]

Creación del Storage Account

![[Pasted image 20250620220716.png]]


![[Pasted image 20250620220749.png]]

Creación de Azure Redis for Cache:

![[Pasted image 20250620223418.png]]

![[Pasted image 20250620223447.png]]

Creación de la DB para Datawarehouse

![[Pasted image 20250620230219.png]]

![[Pasted image 20250620230240.png]]

Creación de Azure Data Factory (Orquestador ETL)

![[Pasted image 20250620231349.png]]

![[Pasted image 20250620231709.png]]


# Evidencias de Configuraciones y Conexión con los Servicios de Almacenamiento

## Servicio de Bases de datos

Configuración de reglas de Firewall del Servicio de DB

![[Pasted image 20250702015350.png]]

![[Pasted image 20250702020101.png]]

## Base de datos OLTP

Conexión con ecommerce_db desde Azure Data Studio

![[Pasted image 20250702020216.png]]

Creación de una tabla en ecommerce_db:

![[Pasted image 20250702020723.png]]

## Datawarehouse

Conexión con dw_ecommerce_db desde Azure Data Studio

![[Pasted image 20250702020847.png]]

Creación de tabla y campos para dw_ecommerce_db

![[Pasted image 20250702021028.png]]

## Storage Account

Elección de Key para conexión a sageneralecommerceenv 

![[Pasted image 20250702022349.png]]

Conexión e interacción desde Microsoft Azure Storage Explorer

![[Pasted image 20250702022958.png]]

![[Pasted image 20250702023030.png]]

## Storage Account para Queue y Azure Functions

Elección de Key para conexión

![[Pasted image 20250702023816.png]]

Conexión e interacción desde Microsoft Azure Storage Explorer

![[Pasted image 20250702024219.png]]

![[Pasted image 20250702024301.png]]

# Código en Tres Repositorios Independientes

Se ha decidido separar los recursos en módulos del negocio, en tres repositorios diferentes:

- Módulo de aplicaciones 
- Módulo de almacenamiento
- Módulo de analítica

Al descubrir que se puede optar por compartir recursos (e.g. servicio de base de datos puede ser compartido por el DW y OLTP, servicio de cuentas de almacenamiento puede ser compartido por Queue Storage y Azure Functions), la separación de los repositorios depende de más comunicación sobre los servicios que se crean.

### Repositorio Infraestructura de Aplicaciones

UI y tareas asíncronas

- Azure App Service - Webapp FrontOffice
- Azure App Service - WebApp BackOffice
- Azure App Service - WebApp API
- Queue storage - Cola de tareas sin respuesta inmediata
- Azure Functions - Serverless para ejecución de tareas en segundo plano

### Repositorio Infraestructura de Almacenamiento

Todo lo que tiene que ver con datos en tiempo real y caché.

- Azure SQL Database - DB OLTP
- Storage Account - Almacenamiento de multimedia
- Azure Cache for Redis - Almacenamiento de datos volátiles

### Repositorio Infraestructura de Analítica

Para Datawarehouse y orquestación ETL.

- Azure SQL Database - DW
- Azure Elastic Jobs - Orquestador del ETL


# Enlaces a los Repositorios

- https://github.com/Estvn/ecommerce-infra-applications
- https://github.com/Estvn/ecommerce-infra-storage
- https://github.com/Estvn/ecommerce-infra-analytics

# Reflexiones Finales

### Separación de la Infraestructura en Repositorios

- Inicialmente se consideró la idea de hacer la separación estrictamente por los servicios ofrecidos (e.g. todo lo relacionado con almacenamiento en un repositorio, todo lo relacionado con UI en otro repositorio y todo lo relacionado con funcionalidad serverless en el tercer repositorio).
- Finalmente los servicios se agruparon en tres categorías de actividades realizadas (UI, almacenamiento y analítica), de esta forma cada repositorio tiene una responsabilidad única.
- Con este tipo de separación entre los repositorios, habían recursos que se deseaba ser compartidos, pero estaba en repositorios distintos. Esta problemática fue el escenario perfecto para poner en práctica la referencia de servicios desde un repositorio diferente.

### Sistema implementado para el procesamiento asíncrono de tareas en segundo plano

- La aplicación está compuesta por una sola API que es consumida por dos UI, se ha tomado esta decisión porque la UI BackOffice no representará una carga computacional tan grande como la UI FrontOffice.
- Para reducir aún más el consumo de la API por parte del FrontOffice se hace uso de un servicio de Redis Cache. Puede ser usado para manejar la sesión, el carrito de compras e incluso data frecuentada por el cliente.
- También se hace uso de servicio Queue Storage, para guardar en cola las tareas de los usuarios que no exigen una respuesta inmediata (e.g. confirmación de compra, transacciones). Estas tareas o mensajes luego serán ejecutadas por el servicio Azure Functions, que funciona de manera serverless logrando mitigar la carga innecesaria en la API general de la aplicación.

### Uso de un Datawarehouse y herramienta de orquestación ETL

- Una base de datos OLTP donde se guarda toda la información transaccional del negocio, un servicio llamado Azure Data Factory (orquestador) y el Datawarehouse para guardar la información procesada para análisis del negocio, son las herramientas involucradas para la toma de decisiones a partir de los datos.
- Los procedimientos almacenados que forman parte del ETL están alojados en el Datawarehouse, el orquestador simplemente se encarga del flujo de trabajo realizado por el ETL.
- El uso de estos elementos para el desarrollo de un DW es una opción excelente y más económica que otras opciones (desarrollo de IA para análisis de negocio o Azure Synapse Analytics), además, se puede identificar con precisión las estadísticas del negocio por medio de la data procesada.

# Experiencia Personal Sobre el Proyecto

- Personalmente, es la primera vez que utilizo servicios en la nube profesionales. 
- El desarrollo de una infraestructura en la nube y la creación de los servicios en Terraform para un proyecto de este tipo, es sencillo cuando ya se conocen los servicios que se necesitan crear.

- El análisis de los servicios requeridos es un proceso complejo, ya que existen muchas opciones y muchas tecnologías (algunas podrían ser las indicadas, pero la inexperiencia en ella la puede descartar como posible opción) para satisfacer las necesidades de los negocios.
- Como opinión personal, la clave para saber que servicios se pueden usar es identificar el costo de los servicios que esté dentro de las posibilidades de los clientes, y también conocer como funciona una tecnología e identificar si es la correcta para cubrir una necesidad en la aplicación que se va a implementar.



































