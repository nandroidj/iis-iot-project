Infraestructura Lógica
=======================

=======================
+ **análisis** completo de la infraestructura necesaria para el despliegue de una red de 10 (diez) nodos sensores en un entorno:

  * 5 nodos en la ciudad de Maimará

  * 5 nodos en la ciudad de Tilcara


+ **sensado** de los nodos:

  * temperatura ambiente

  * humedad de suelo
=======================

* componentes en la nube
* conectividad a Internet
* forma de almacenamiento de la información
* forma de acceso a la información
 
* Se deberán justificar las elecciones adoptadas:
  - [ ] evaluar las velocidades de datos necesarias
  - [ ] los volúmenes de datos a traficar y almacenar
  - [ ] los cálculos de los radioenlaces que pudieran existir,
  - [ ] la ubicación de los elementos en función del entorno (por ejemplo las alturas del terreno)
  - [ ] los servicios a contratar
  - [ ] los recursos en nube
  - [ ] redundancias 


+ **relacionar** los aspectos prácticos con los aspectos teóricos (como por ejemplo indicar las distintas partes de la solución adoptada con las capas del modelo de los sistemas IoT).

+ **AWS Calculator** considerar exhaustivamente el factor técnico-económico de la solución: cuantificar los costos y la elección de los elementos que mejor relación presente entre los aspectos técnicos y económicos

+ **realizar** una planilla incluyendo los elementos que pudieran resultar significativos para el proyecto, ya sea por sus costos o implicancias técnicas, con un resumen de costos y elecciones estratégicas que pudieran presentarse (aquí podría incluirse la redundancia del sistema como uno de los factores, la operación en bandas de frecuencias de uso compartido o exclusivo, o la contratación de algún servicio que pudiera llevar asociado costos de abonos, modelo de servicios en la nube, etc.).

## Introducción

El objetivo del documento es exponer la disposición de la infraestructura, la arquitectura informática y las medidas de seguridad necesarias para el despliegue de una red de diez nodos sensores en un entorno situado en la región norte de la República Argentina. 


## 1. Arquitectura General


El diagrama general propuesto para el montaje *end-to-end* que permita visulizar métricas proveniente de una red inalámbrica de sensores se encuentra separada en dos secciones:

  * *Frontend*: se desarrolla con la herramienta de visualización *Grafana* y se ejecutra sobre el hardware del usuario que desee observar métricas de la red dispuesta.

  * *Backend*: opera sobre la infraestructura de *Amazon Web Services (AWS)*. Se selecciona este proveedor ya que es el líder del mercado en el área de *Cloud Computing* proveyendo una gran cantidad de servicios de alta calidad con altos estándares de seguridad y disponibilidad, además de disponer de buena documentación para su uso y configuración.

### Proveedor de infraestructura

En base a una evaluación sobre las distintas tecnologías existentes en el mercado, se selecciona *AWS* como proveedor de infraestructura, ya que en comparación a ofrecer una solución *on-premise*, los servicios de *Cloud Computing* nos permiten un mayor desempeño, reducción de costos, facilidad en su configuración y sobre todo mejora en gran medida la planificación, adquisición, implementación y escalabilidad de una arquitectura compleja, más allá del mantenimiento que implica mantener servidores locales.

Se elige *AWS* por sobre cualquier otro servicio de la nube (por ejemplo Azure o Google Cloud), ya que es particularmente dominante en el mercado, ofreciendo un amplio conjunto de herramientas que continúan en crecimiento y resulta la red más completa de centros de datos en todo el mundo.

| Proveedor   | Ventajas   | Desventajas  |
|:-:|:-:|:-:|
| AWS | * Posición dominante en el mercado * Ofertas amplias y maduras * Soporte para grandes organizaciones * Entrenamiento extensivo * Alcance global | * Mayor complejidad * Manejo de costos * Opciones abrumadoras |
| Microsoft Azure | * Segundo proveedor más grande * Integración con herramientas y software de Microsoft * Amplio conjunto de funciones * Nube híbrida * Soporte para código abierto | * Problemas con la documentación * Herramientas de gestión incompletas |
| Google Cloud Platform | * Compromiso con el código abierto y la portabilidad * Grandes descuentos y contratos flexibles * Experiencia en DevOps | * Participante tardío en el mercado IaaS * Menos funciones y servicios * Históricamente no tan centrado en la empresa |




## 2. Infraestructura 

En esta sección se detallan los servicios y alcances que ofrece *AWS*. Cabe destacar que la mayoría de las especificaciones de seguridad y arquitectura son resueltas con herramientas administradas y ampliamente configurables por este proveedor.

En la siguiente figura se puede observar el diagrama de la arquitectura a montar, con el prestador del servicio previamente mencionado, para un entorno productivo con el fin de facilitar la comprensión del sistema.


        ADD IMAGE: WSN END-TO-END ARCHITECTURE


### Servicios de AWS utilizados

* **Route 53**: las solicitudes *DNS* de un usuario son atendidas por *Route 53*, el servicio de amazon para *DNS* de alta disponibilidad. El tráfico entrante por el puerto *443 (SSL)* se enruta a la infraestructura que se ejecuta en *Amazon Web Services*.

* **Content Delivery Network (CDN)**: es el servicio encargado de proporcionar el contenido estático, en particular, de la aplicación Web. Las solicitudes se enrutan automáticamente a la ubicación del borde más cercano, aumentando el rendimiento de la aplicación.

* **Simple Cloud Storage (S3)**: los recursos y los contenidos estáticos (archivos, imágenes, documentos) se almacenan con el servicio de *Amazon S3*, tanto para datos de la aplicación como archivos de los usuarios finales. Estos datos se encriptan para su almacenamiento con claves administradas por el *Key Management Service (AWS KMS)* cifrados con *AES-256*. *Amazon* garantiza una alta disponibilidad con su infraestructura multizona.

* **Elastic Load Balancing (ELB)**: las solicitudes *HTTP* son administradas por el servicio *ELB* encargado de distribuir el tráfico a las distintas instancias *EC2*, impidiendo el congestionamiento del tráfico.

* **CloudTrail**: se utiliza este servicio para visualizar los movimientos de los usuarios administradores dentro de una instancia o en toda la plataforma AWS, teniendo una auditoría de los procesos modificados.


* **Elastic Kubernetes Service (EKS)**: Se utiliza *EKS* como servicio de *Kubernetes*. Kubernetes es un sistema de código abierto que automatiza la implementación, escalado y administración de aplicaciones en contenedores. 


### Networking

Parte crucial de ofrecer un servicio Web a clientes externos es definir  la seguridad de las distintas capas del modelo OSI sin generar pérdidas ni retardos a los usuarios, para ello hacemos uso de distintos servicios de AWS que se encargan de administrar y definir las políticas de seguridad.

                    
        ADD IMAGE: WSN END-TO-END ARCHITECTURE


En la figura se muestra un esquema de la red de la plataforma, considerando que cada pool es escalable horizontalmente, es decir podemos tener tantos nodos como se desee.


<details><summary><b>Desglose de capas</b></summary><br>

1. Los nodos se albergan dentro de los pools de nodos, a los cuales se les especifica el tipo de servidor EC2 que deben iniciar y la cantidad por poola

2. A cada pool de servidores entre sus múltiples configuraciones, se le especifica su security group, el cual a su vez define  el firewall y nos proporciona un label para la tabla de ruteo dentro de la red.

3. A los pool, a su vez, se les define la zona en las que están habilitados a levantar sus nodos, especificando en general todas las de South America, São Paulo, sa-east (tres en total).

4. Los pools son definidos dentro de un contexto EKS, el cual se encuentra dentro de una VPC, como se puede observar tenemos una VPC que posee los servidores en distintas availability Zones, y se define una regla (por medida de seguridad) en la que solo los servidores que se encuetren dentro del Security Group “EKS PROD” pueden acceder a la base de datos de producción (aislando de esta forma produccion de desarrollo), generando un túnel entre estas dos VPC, con un puerto específico. Estos servidores dentro del pool de producción se encuentran totalmente aislados, sin acceso ssh, solo el nodo maestro del cluster de kubernetes puede acceder a estos servidores y solo desde estos servidores se puede acceder a la base de datos de producción.

</details>


#### Virtual Private Cloud (VPC)

La subred privada donde se encuentran los servidores Web y Base de datos (10.0.0.0/16), está aislada mediante la creación de una Virtual Private Cloud (VPC), permitiendo la configuración de tablas de rutas y puertas de enlace de red. Tenemos distintas subredes tanto para desarrollo como para la base de datos, independientes unas de otras, y definiendo solo para la subred publicada a la nube (o Internet) reglas y políticas configurables, tales como permitir solo el acceso a los servidores mediante TCP 443 (HTTPS).


#### Firewall y Security Groups

Una vez definidas todas las VPC y las salidas a internet enrutadas para los usuarios finales, nos permite definir tanto el Firewall y los Security Groups. Ambos cumplen un rol similar, el Firewall controla el tráfico entre las distintas VPC y el exterior, mientras que los Security Groups controlan el tráfico interno dentro de nuestra red. Estas políticas nos  brindan un control detallado sobre el tráfico de la red, definiendo reglas para permitir únicamente el tráfico HTTPS permitiendo el acceso de cualquier usuario por el puerto TCP 443 hacia el servidor Web y el backend, las propias comunicaciones entre un Servidor y su base de datos permitiendo el acceso TCP 5432 desde el servidor hacia la BD.

#### AWS Shield

Todos los clientes de AWS se benefician de las protecciones automáticas de AWS Shield Standard, el cual ofrece servicios de protección contra los ataques más comunes como denegación de servicio distribuido (Distributed Denial of Service o DDoS). Estos servicios brindan protección contra todos los ataques de infraestructura conocidos (Capa 3 y 4). 


#### Certificados HTTPS

Todo tráfico HTTP (puerto 80) es redirigido a HTTPS (puerto 443) para encriptar los datos en tránsito de los usuarios que incluyen contraseñas y datos personales. Utilizamos el servicio AWS Certificate Manager para obtener certificados SSL firmados por Amazon, los cuales son aceptados por todos los navegadores Web más utilizados. Esta configuración se aplica a Amazon Route 53.

### Procesamiento

Se utilizan instancias Elastic Compute Cloud (EC2) como servidores del cluster de la solución a resolver, en los cuales se ejecutan los servicios requeridos para el backend, esta topología permite que en cada VPC se puedan escalar a tantas instancias como sean necesarias, teniendo de esta forma un autoescalado sencillo de administrar para garantizar disponibilidad. 

El acceso de los desarrolladores, se realiza mediante permisos de AWS (IAM) hacia el cluster de Kubernetes de briken, desde el cual es posible acceder a  los servicios del backend que se haya permitido y a sus respectivos servidores que albergan estos servicios.

### Disponibilidad

Para lograr alta disponibilidad de los servidores, disponemos de instancias y servidores (replicados) en las distintas regiones y zonas de AWS. Dentro de una región hay 3 o más zonas de disponibilidad con fuentes de energía y conectividad a internet  independientes, por lo que se puede brindar un servicio de alta disponibilidad a los clientes de forma sencilla. Actualmente tenemos configurada redundancia de nuestros servicios en las zonas sa-east-1a  y sa-east-1b de la región de Sao Paulo. 

### Nivel de servicio

Existe un acuerdo de nivel de servicio de Amazon RDS ("SLA"), es una política que rige el uso del servicio de base de datos relacional de Amazon. Aplicadas a las instancias Multi-AZ tal que estén disponibles con un porcentaje de tiempo de actividad mensual de al menos el 99,95% durante cualquier ciclo de facturación mensual. Este nivel de servicio se aplica sobre:

* RDS (base de datos administrada)
* CloudFront : Contemplando el porcentaje de tiempo de actividad mensual como la tasa de error.
* Elastic Load Balancing Service
* EKS Service
* Compute Service


**Créditos de servicio**

      ADD TABLE: SERVICE BILLING


### Almacenamiento


#### Base de Datos Administrada
Para proporcionar alta disponibilidad, la base de datos que contiene los datos de la aplicación se aloja de forma redundante en una implementación Multi-AZ (Zonas de disponibilidad múltiple) de Amazon Relational Database Service (Amazon RDS), al crear una  instancia de base de datos Multi-AZ se crea automáticamente una instancia de base de datos principal y réplica de forma síncrona, los datos en una instancia en espera en una zona de disponibilidad (AZ) diferente. En caso de una falla en la infraestructura, se realiza una conmutación por error automática a la instancia de base de datos en espera. Dado que el punto final de su instancia de base de datos sigue siendo el mismo después de una conmutación por error, la aplicación reanuda sin inconvenientes ni cambios.

Este servicio administrado de base de datos nos ofrece conexiones estables, alta disponibilidad, escalado y backups periódicos programados con posibilidad de restauración point-in-time. Esto automatiza y simplifica las tareas de administración.

#### Encriptación
Para la encriptación de los datos tanto estáticos como dinámicos, se utiliza dos servicios de AWS combinados, el primero de ellos es para los archivos almacenados en S3, a los mismo se los encripta mediante claves gestionadas (AES-256) utilizando el SSE-S3 , el mismo se combina con SSE-KMS para crear las claves de encriptación y rotarlas cada cierto periodo de tiempo.

Con amazon SSE-S3 se cifra cada objeto con una clave única. Como una protección adicional, se cifra la clave con una clave maestra que gira regularmente. El cifrado del lado del servidor de Amazon S3 es uno  de los cifrados de bloque más fuertes disponible para cifrar los datos, estándar de cifrado avanzado de 256 bits (AES-256). Esto aplica tanto a los archivos almacenados en S3 como a la base de datos Postgres SQL en RDS.

#### Administración

El acceso de desarrolladores o administradores de la solución propuesta a AWS es administrado por el servicio Identity and Access Management (IAM) el cual ofrece distintas configuraciones para la gestión de usuarios y roles

* Creación de grupos
* Administración de Roles (a recursos de AWS para permitir el uso de otros recursos) 
* Políticas configurables
* Acceso multifactor


Para los usuarios con rol Domain Admin (DA) y en caso de ser necesario para usuarios con permisos críticos, se utiliza como política el acceso multifactor (MFA), mediante la cual un usuario con estas características deberá cumplir con los requisitos de “Algo que sabe” y “Algo que tiene”. Estos dos factores consisten en una contraseña y una aplicación con una contraseña de único uso (One Time Password, OTP).

Los accesos de los desarrolladores son limitados y controlados bajo el principio “Necesita Saber” (Need to Know Basis) limitando su acceso a zonas indebidas del sistema. El servicio IAM provee herramientas para el control granular de permisos necesario para lograr esto.

Una vez configurado los permisos de IAM (AWS) tanto para desarrolladores como para usuarios impersonados (roles utilizados para deploys, monitoreo, alertas, CI/CD, etc), se configuran los permisos dentro del cluster de kubernetes para cada uno de ellos, a partir del control de acceso basado en roles (RBAC , por sus siglas en inglés Role-Based Access Control), este servicio de kubernetes permite configurar políticas dinámicamente a través de la API, de esta forma se manejan políticas de seguridad para usuarios, grupos o pods, definiendo perfiles de usuarios y configurando permisos de:

  * Cluster-Role:  Para permisos del cluster (usuarios administradores ,impersonados de deploys y monitoreo),

  * Role: Para perfiles específicos, dando accesos a namespaces (de Kubernetes) específicos. 

A cada uno de estos perfiles se le debe asignar un Role Binding para asociar el grupo de usuarios con el tipo de permiso y sus posibles acciones sobre el cluster, además de aprobar el control de admisión por grupo de usuarios.

En resumen se busca y configura que los administradores y desarrolladores definen el estado deseado del cluster y no las modificaciones necesarias para alcanzarlo, el mayor beneficio respecto al enfoque basado en la intención es permitir que el cluster sea resistente a fallas, debido a que Kubernetes sabe lo que debe estar haciendo cuando ocurren fallas y por ende como recuperarse.

#### Monitoreo

Para el monitoreo de los servicios y estado de los servidores se utilizan los operadores de Prometheus, el cual se inicia en el mismo cluster como pods en cada nodo para monitorear todos los servicios. Prometheus es un servicio de monitoreo estándar en Kubernetes, el cual genera métricas del resto de los pods y nodos (servicios y servidores), se utiliza este sistema de monitoreo por las siguientes razones:

* Recopilación de métricas : Prometheus utiliza el modelo de extracción para recuperar métricas a través de HTTP. Esta es la razón principal de utilizar prometheus, ya que estas métricas se utilizan tanto para el monitoreo y visualización y a su vez proporcionara otros servicios de Kubernetes esta recopilación de métricas y estados, para tomar decisiones, como es el caso de la escalabilidad o alertas.

* Punto final de métrica : los sistemas que se desean supervisar con Prometheus exponen sus métricas en un punto final, generalmente en “/metrics” (esto es configurable para cada caso).

* Exportadores: Al ser prácticamente un estándar de Kubernetes, existen diversas posibilidades para obtener y configurar las métricas de cualquier recurso, como bibliotecas que convierten la métrica existente de aplicaciones de terceros al formato de métrica de Prometheus.

* Exportador de nodos: Prometheus recupera métricas a nivel de máquina por separado de la información de la aplicación. La cual expone memoria, el espacio en disco, el uso de la CPU y las métricas de ancho de banda.
	
Para la visualización de métricas se utiliza Grafana  la cual se utiliza en conjunto con Prometheus, tomando las métricas del mismo para exponerlas al administrador de forma entendible. Grafana permite una amplia customización, brindando la posibilidad de crear dashboards completos.

Por otro lado, a partir de las métricas, se generan alertas de estados y posible criticidad en los servidores o servicios utilizando Alert Manager . Con el mismo es posible generar alertas de estados e informarlas por distintos medios (email, Slack, Telegram, etc).

La arquitectura de monitoreo se muestra en la siguiente gráfica:


      ADD IMAGE: MONITORING ARCHITECTURE

Como se puede observar en la figura, cada servicio, pod, nodo (servidor), balanceador de carga, etc. expone sus métricas y logs en una url específica (en general “/metrics”), las cuales son tomadas por el agente de Prometheus, que detecta estas métricas, las expone y guarda en un almacenamiento interno (de AWS) . Estas métricas luego son tomadas y analizadas para realizar distintas tareas administrativas:

* Visualización: A partir de Grafana, quien toma las métricas de Prometheus y las expone al administrador de forma personalizable.

* Alertas: A partir de Alert Manager, quien al configurar los umbrales superados, generan alertas por distintos medios.

* Correcto funcionamiento: Si detecta que un pod comienza a consumir más recursos o posee errores constantes, el pod es reiniciado, la misma práctica se aplica para los nodos.

* Escalabilidad: Mejora las métricas básicas de Kubernetes para generar políticas de escalabilidad, agregando nuevas variables. Detallado en la siguiente sección.

* Extras: Cada tipo de servicio (como el mismo backend), pueden generar en diversas urls, métricas o información específica del servicio, el cual puede ser configurado y monitoreado por Prometheus.






























