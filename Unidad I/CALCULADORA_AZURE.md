
https://portal.azure.com

- Azure ofrece un servicio de autorización y autenticación que se usa tanto para el Outlook y parte ofigmática.

#### Resource Groups

- Tienden a ser conceptualizados o creados en función de querer contener toda una aplicación, en la que su ciclo de vida sea el ciclo de vida de todo el software.
- Si ha creado un servicio contable y quiere borrar o desactivarlo, en teoría no debería poder borrar solo algo en específico, eso se considera una mala práctica.

- Los grupos de recursos se usan para crear proyectos, es una unidad lógica que me va a agrupar todas las bases de datos, todos los servidores web, todos los kubernetes, container registrers, queue storage, servicios de machine learning, etc., que estén bajo la misma idea de un proyecto o idea que estamos trabajando.

- Los grupos de recursos se usan también para diferenciar los proyectos y ambientes.
- Ejemplo, tenemos un proyecto contable y queremos tener dos instancias de todo el producto contable porque uno será para QA, otro para el desarrollo, y el último será para el ambiente productivo.

- **Todos los recursos que se crean, bases de datos o WebApps van a estar contenidos dentro de un grupo de recurso, y puedo seleccionar en que lugar geográfico quiero que todos los recursos que esté levantando sean configurados.**
- En la ubicación geográfica East US2 es más barato (por centavos), porque en esa ubicación se encuentran los servidores más "legacy".
- Legacy refiere a los servidores un poco más más lentos, aunque para los tipos de desarrollo o implementaciones sencillas está bien esta ubicación.

- Es muy importante saber en que región conviene montar nuestro grupo de recursos para su configuración y uso.
- Es importante la elección de la región cuando se está definiendo la arquitectura.

- **La región donde haremos el despliegue de nuestra aplicación va a depender de dos decisiones:**

	1. Dependerá de la latencia que vamos a tener, o el sector más grave de nuestros clientes.
		- Si yo sé que la mayoría de nuestros clientes sabemos que están en CA o en Honduras, es más conveniente usar un servidor de México.
		- Físicamente vamos a estar más cerca de los servidores de México.
		- La ubicación va a influir mucho la latencia de como recibimos la respuesta de los servicios.
	2. El nivel de privacidad que existen en los datos
		- Por ejemplo, el nivel de los datos que existe en Europa es muy estricto.
		- La data de los europeos no debería salir de Europa, físicamente y geográficamente, los datos de Europa no deberían salir de la región.
		- **Si tienes la oportunidad de crear una aplicación para un público europeo y creas tu grupo de recursos en Estados Unidos, pueden enfrentarte a problemas legales.**
		
#### **Single-Tenant (Un único inquilino)**

-  Para regulaciones estrictas (GDPR, HIPAA) o necesidades únicas.
- **Definición:** Cada cliente (inquilino) tiene su propia instancia exclusiva del software, infraestructura y base de datos.
    
- **Características:**
    
    - **Aislamiento total:** Los datos y la configuración no se comparten.
        
    - **Mayor seguridad y personalización:** Ideal para empresas con requisitos estrictos de compliance (ej. bancos, salud).
        
    - **Costo más alto:** Requiere más recursos dedicados.
        
    - **Mantenimiento individual:** Las actualizaciones se aplican por separado.
        

**Ejemplo:** Un servidor privado para una sola empresa.

#### **Multi-Tenant (Múltiples inquilinos)**

- **Definición:** Varios clientes comparten la misma instancia del software, infraestructura y base de datos (con datos lógicamente separados).
    
- **Características:**
    
    - **Economías de escala:** Más eficiente en costos (ej. SaaS como Salesforce, Slack).
        
    - **Actualizaciones centralizadas:** Todos los inquilinos reciben las mismas mejoras.
        
    - **Menor personalización:** Limitado por la arquitectura compartida.
        
    - **Riesgo potencial de "ruido del vecino":** Un inquilino puede afectar el rendimiento de otros.
        

**Ejemplo:** Un edificio de apartamentos donde los residentes comparten la misma estructura, pero tienen espacios privados.

#### ¿Qué pasa si mi aplicación es usada en América y en Europa?

 - En este punto, entra el concepto de single-tenant y multi-tenant.
 - O puede haber un híbrido, donde se crean grupos de recursos en servidores de Asia, Europa y América que sirve a las regiones donde ha sido colocado.

- Supongamos que tenemos una aplicación que requiere de 8 servicios, si tenemos que satisfacer a varios clientes se tiene que levantar los recursos en distintas regiones (**implementación del modelo single-tenant**), **al hacerlo a mano estamos atados a los errores humanos.**

- **Terraform** es una herramienta que sirve para automatizar la configuración de los servicios y grupos de recursos.
- **Terraform** sirve para realizar el despliegue de la infraestructura, que va a soportar los proyectos que se montarán encima de la infraestructura.

- La región seleccionada dependerá mucho de la seguridad y la privacidad de los datos.

- Las VPN apuntan a infraestructuras con contenido que no está disponible en las infraestructuras de la región en la que se encuentra.
- Si usa una VPN para ver contenido de streaming que no está en los servidores de su región, podrá experimentar baja latencia por la distancia a la que se encuentra el contenido.

#### Resource Groups

- **Los grupos de recursos son unidades lógica que van a contener todos los recursos que estaremos creando y están agrupados bajo la lógica de un proyecto.**
- Los grupos de recursos no generan un costo.

- Las Tags de los grupos de recursos ayudan a identificar quien creó los grupos de recursos y para que son.

```
createdBy {estiven mejia}
env {dev | QA | production}
date {fecha}
```

**Recursos de Azure:**

- Refiere a servidores, bases de datos, servicios de Machine Learning, etc.
- Todos los recursos tienen que ir atados a un grupo de recursos, que conceptualmente denota una aplicación general que se desea desarrollar. 
- A que considerar donde se va localizar geográficamente el grupo de recursos, para evitar una alta latencia, problemas legales y privacidad de datos.

### **Frontoffice (o Front Office)**

Es el área de la empresa que tiene contacto directo con los clientes o usuarios finales. Su función principal es la atención al cliente, ventas y gestión de relaciones externas.

**Ejemplos de funciones del Frontoffice:**

- **Atención al cliente** (atención en mostrador, call center, chat en línea).
- **Ventas y asesoramiento comercial** (ejecutivos de ventas, agentes comerciales).
- **Relaciones públicas y marketing** (atención a proveedores o socios estratégicos).
- **Trading financiero** (en bancos o fondos de inversión, los traders que operan en mercados).

**Características clave:**

- Interacción constante con clientes.
- Enfoque en generar ingresos y fidelizar clientes.
- Suele ser la "cara visible" de la empresa.

### **Backoffice (o Back Office)**

Es el área que da soporte interno a la empresa, sin contacto directo con el cliente. Su función es garantizar que las operaciones y procesos administrativos funcionen correctamente.

**Ejemplos de funciones del Backoffice:**

- **Contabilidad y finanzas** (gestión de facturas, nóminas).
- **Recursos Humanos** (selección de personal, gestión laboral).
- **Operaciones y logística** (gestión de inventarios, procesamiento de pedidos).
- **Tecnología y sistemas** (soporte IT, desarrollo de software).
- **Cumplimiento normativo y legal** (auditorías, regulaciones).

**Características clave:**

- Trabajo administrativo y operativo.
- Soporte a las áreas de frontoffice.
- Enfoque en eficiencia y cumplimiento de procesos.

# Calculadora de Azure

- Antes de levantar los presupuestos se puede usar la **calculadora de Azure** para poder gestionar el presupuesto.

- Multi-tenant refiere a todos los recursos que yo levante van a ser para atender a distintos clientes.
- Puedo tener n cantidad de clientes que quieren operar con el servicio que ofrezco.

- Va a ser un servicio multi-tenant y voy a tener distintos clientes, puede haber ciertos clientes que no requieran de algunos módulos de la aplicación y otros requerirán soporte.

> [!IMPORTANT] IMPORTANTE
> **En este tipo de sistemas es buena práctica separar el backOffice del frontOffice.** 

- Cuando hablo del backOffice, me refiero a todas las tablas de mantenimiento, todos esos valores con transaccionalidad baja o datos sensitivos porque es lo que la gente consume a través de los formularios.

- BackOffice tiende a estar en servidores a parte por cuestiones de seguridad y performance.
- Un BackOffice tiende a tener menor cantidad de recursos y su transaccionalidad no tiene que ver con la transaccionalidad de un sistema contable que atiende a una cartera de 100 clientes.

- **Manejaremos diferentes App Service Plan.**
- Es un recursos de Azure que contiene diferentes WebApp que tendré para mi base de datos.

- Azure container registry
- App Service. Las horas de servicio ofrecidas por defectos pueden ser reevaluadas, ya que existen horas muertas donde no se usa la aplicación.
	- En App Service va a estar el código que hemos desarrollado (FrontEnd, BackEnd, Modelo de IA)
- Azure SQL DataBase.













