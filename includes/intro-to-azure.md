# Introducción a Microsoft Azure

Microsoft Azure es la plataforma de aplicaciones de Microsoft para la nube pública. El objetivo de este artículo es proporcionarle los cimientos para entender los conceptos básicos de Azure, incluso si no sabe nada de la informática en la nube.

**Cómo leer este artículo**

Azure está en crecimiento constante, por ello es fácil sentirse abrumado. Los servicios básicos son los que se presentan en primer lugar en este documento. Empiece por ahí y pase luego a los servicios adicionales. Esto no quiere decir que no se puedan utilizar únicamente los servicios adicionales; no obstante, los servicios básicos conforman el núcleo de una aplicación que se ejecuta en Azure.

**Envíenos sus comentarios**

Sus comentarios son importantes. Este artículo debería proporcionarle una eficaz visión global de Azure. De no ser así, háganoslo saber por medio de la sección de comentarios que se encuentra en la parte inferior de la página. Detalle que es aquello que esperaba encontrar y cómo podemos mejorar el artículo.
   

## Tabla de contenido

**Servicios básicos** - [Componentes de Azure](#components) - [Portal de administración](#portal) - [Proceso](#compute) - [Administración de datos](#data) - [Redes](#networking) - [Servicios para desarrolladores](#DevService)

**Servicios adicionales** - [Identidad y acceso](#identity) - [Móvil](#mobile) - [Copia de seguridad](#backup) - [Mensajería e integración](#messaging) - [Asistencia para procesos](#ComputeAssist) - [Rendimiento](#Performance) - [Big Compute y Big Data](#BigStuff) - [Multimedia](#media) - [Comercio](#commerce)

[Introducción](#start)

 
<h2><a id="components">Componentes de Azure</a></h2>

Azure agrupa los servicios en categorías en el Portal de administración y en varios elementos visuales de ayuda como la [infografía sobre qué es Microsoft Azure](http://azure.microsoft.com/documentation/infographics/azure/ "Qué es la infografía del póster de Microsoft Azure"). El Portal de administración es lo que se utiliza para administrar la mayoría de servicios en Azure (pero no todos).

En este artículo se usará **otra organización** para hablar de los servicios basados en una función similar y para llamar a subservicios importantes que forman parte de otros más grandes.

![Componentes de Azure](./media/intro-to-azure/AzureComponentsIntroNew780.png)**Ilustración: Azure proporciona servicios de aplicaciones accesibles desde Internet que se ejecutan en centros de datos de Azure.**

<h2><a id="portal"></a>Portal de administración</h2>
Azure cuenta con una interfaz web denominada [Portal de administración](http://manage.windowsazure.com) que permite a los administradores tener acceso a la mayoría de características de Azure (pero no a todas) y administrarlas. Microsoft normalmente publica el portal de la IU más reciente en beta antes de retirar uno antiguo. La más reciente se llama ["Portal de vista previa de Azure"](https://portal.azure.com/).

Normalmente hay un largo periodo de solapamiento en el que los dos portales están activos. Aunque los servicios fundamentales aparecerán en los dos portales, es posible que no toda la funcionalidad esté disponible en ambos. Puede que los servicios más recientes aparezcan primero en el portal más nuevo y que la funcionalidad y los servicios más antiguos existan solo en el anterior. Lo que tiene que tener en cuenta es que, si no encuentra algo en el portal más antiguo, debe comprobar el más reciente (y viceversa).


<h2><a id="compute"></a>Proceso:</h2>

Una de las funciones más básicas de una plataforma de nube es la ejecución de aplicaciones. Azure ofrece estas opciones:

1.	Máquinas virtuales le da el control sobre su propia máquina virtual, incluido el sistema operativo. 
2.	Sitios web ofrece una variedad de aplicaciones, marcos de trabajo y plantillas para la creación rápida de aplicaciones web escalables de gran tamaño y sitios web de presencia y la posterior administración de desarrollos, pruebas y operaciones de manera eficiente.
3.	Servicios en la nube es una opción de plataforma como servicio (PaaS) ajustada para crear aplicaciones muy escalabes y resistentes a errores, pero con más flexibilidad que Sitios web. 

Cada uno de los modelos de ejecución de Azure desempeña su propia función.

Estas tecnologías se pueden usar por separado o combinarlas como sea necesario para crear la base perfecta para su aplicación. El enfoque que elija depende del problema que intente resolver.


###Máquinas virtuales de Azure###

![Máquinas virtuales de Azure](./media/intro-to-azure/VirtualMachinesIntroNew.png) **Ilustración: Máquinas virtuales de Azure ofrece un control total sobre las instancias de máquina virtual en la nube.**

La posibilidad de crear una máquina virtual a petición, ya sea a partir de una imagen estándar o de una que suministre el usuario, puede resultar muy útil. Este enfoque, conocido comúnmente como Infraestructura como servicio (IaaS), es lo que proporciona Máquinas virtuales de Azure. La ilustración 2 muestra una combinación de cómo se ejecuta una máquina virtual y cómo crear una desde un disco duro virtual (VHD).

Para crear una VM, es necesario especificar qué VHD se va a utilizar y el tamaño de la VM. Luego, se paga por el tiempo que se ejecuta la VM. Se le cobra por minuto y solo mientras esté en ejecución, aunque hay un cargo mínimo de almacenamiento por mantener el VHD disponible. Azure ofrece una galería de VHD en existencias (llamados "imágenes") que contienen un sistema operativo de arranque desde el que se puede iniciar. Entre ellos se incluyen opciones de Microsoft y sus asociados, como Windows Server y Linux, SQL Server, Oracle y muchos más. Sin embargo, puede crear usted mismo VHD e imágenes y cargarlos personalmente. Puede incluso cargar VHD que contengan únicamente datos y luego obtener acceso a ellos desde sus máquinas virtuales en ejecución.

Con independencia del lugar de donde provenga el VHD, puede almacenar repetidamente los cambios realizados mientras la máquina virtual esté en ejecución. La siguiente vez que cree una VM a partir de ese VHD, las cosas estarán donde las dejó. Los VHD que respaldan las Máquinas virtuales están almacenados en blobs de Almacenamiento de Azure, de los que hablaremos más adelante. Esto significa que obtiene redundancia para asegurarse de que sus máquinas virtuales no desaparezcan debido a errores en el hardware o el disco. También es posible copiar el VHD modificado fuera de Azure y luego ejecutarlo localmente.
 
Su aplicación se ejecuta dentro de una o varias Máquinas virtuales, dependiendo de cómo la creara antes o si decidió crearla ahora desde el principio.

Este enfoque bastante general de la informática en nube se puede utilizar para solucionar muchos problemas diferentes.

**Escenarios de máquina virtual**

1.	**Desarrollo y pruebas**: puede usarlos para crear una plataforma de desarrollo y prueba económica que puede apagar cuando haya terminado de usarla. O también, podría crear y ejecutar aplicaciones que utilicen los lenguajes y las bibliotecas que prefiera. Esas aplicaciones pueden utilizar cualquiera de las opciones de administración de datos que proporciona Azure, y también puede elegir usar SQL Server u otro DBMS que se ejecute en una o varias máquinas virtuales. 
2.	**Trasladar aplicaciones a Azure (levantar y mover)**: "levantar y mover" recibe este nombre porque es como si trasladara una aplicación usando una carretilla elevadora para mover objetos de gran tamaño. "Levanta" el VHD desde su centro de datos local y lo "mueve" a Azure para ejecutarlo ahí. Normalmente tendrá que efectuar algunas operaciones para quitar las dependencias de otros sistemas. Si hay muchas, quizá le convenga más utilizar la tercera opción.  
3.	**Ampliar el centro de datos**: use máquinas virtuales de Azure como extensión de su centro de datos local, ejecutando SharePoint u otras aplicaciones. Para permitir esto, es posible crear dominios de Windows en la nube ejecutando Active Directory en las VM de Azure. Puede usar Red virtual de Azure (que se menciona más adelante) para conectar su red local con su red de Azure.
 


###Sitios web###

![Sitios web Azure](./media/intro-to-azure/AzureWebsitesIntroNew.png) **Ilustración: Sitios web de Azure ejecuta una aplicación de sitios web en la nube sin que haga falta administrar el servidor web subyacente.**

Una de las cosas más importantes que se hacen en la nube es ejecutar sitios y aplicaciones web. Máquinas virtuales de Azure le permite hacer esto, pero le deja la responsabilidad de administrar una o varias VM y los sistemas operativos subyacentes. Los roles web de servicios en la nube pueden hacerlo, pero su implementación y mantenimiento conlleva también trabajo administrativo. ¿Y si lo que desea es un sitio web donde sea otro quien se encargue del trabajo administrativo?

Esto es exactamente lo que proporciona Sitios web Azure. Este modelo de proceso ofrece un entorno web administrado utilizando el Portal de administración de Azure y las API. Puede mover una aplicación de sitio web existente a Sitios web Azure sin cambios, o puede crear una directamente en la nube. Cuando un sitio web está en ejecución, puede agregar o quitar instancias de forma dinámica y recurrir a Sitios web Azure para equilibrar las solicitudes de carga entre ellos. Sitios web Azure ofrece tanto una opción compartida, en la que el sitio web se ejecuta en una máquina virtual con otros sitios, como una opción estándar, que permite que un sitio se ejecute en su propia VM. La opción estándar también le permite aumentar el tamaño (potencia informática) de las instancias, si es necesario.

Para el desarrollo, Sitios web es compatible con .NET, PHP, Node.js, Java y Python, junto con Base de datos SQL y MySQL (de ClearDB, un socio de Microsoft) para el almacenamiento relacional. También ofrece compatibilidad integrada con varias aplicaciones conocidas, como WordPress, Joomla y Drupal. El objetivo es proporcionar una plataforma de bajo coste, escalable y enormemente útil para la creación de sitios y aplicaciones web en la nube pública.


**Escenarios de sitio web**

Sitios web va a resultar útil para corporaciones, desarrolladores y agencias de diseño web. En el caso de las corporaciones, constituye una solución fácil de administrar, escalable, muy segura y de alta disponibilidad para la ejecución de sitios web de presencia. Cuando necesite configurar un sitio web, es mejor empezar por Sitios web Azure y seguir por Servicios en la nube si requiere una característica que no esté disponible en Sitios web. Consulte el final de la sección "Proceso" para ver más vínculos que pueden ayudarle a elegir entre las opciones.

###Servicios en la nube###
![Servicio en la nube de Azure](./media/intro-to-azure/CloudServicesIntroNew.png) **Ilustración: Servicios en la nube de Azure ofrece un lugar para ejecutar código personalizado de alta escalabilidad en un entorno de Plataforma como servicio (PaaS).**

Supongamos que desea crear una aplicación en la nube que pueda admitir varios usuarios de manera simultánea, que no requiera mucha administración y que siempre esté disponible. Podría ser, por ejemplo, un proveedor de software consolidado que ha decidido embarcarse en el Software como servicio (SaaS) mediante la creación de una de sus aplicaciones en la nube. O bien, podría ser un principiante que desea crear una aplicación de consumidor que espera que crezca rápido. Si fuera a crear sobre Azure, ¿qué modelo de ejecución debería usar?

Sitios web Azure le permite crear esta clase de aplicación web, pero existen algunas limitaciones. No dispone de acceso administrativo, por ejemplo. Esto significa que no puede instalar software arbitrario. Máquinas virtuales de Azure le proporciona mucha flexibilidad, incluso acceso administrativo, y puede usarlo desde luego para crear una aplicación muy escalable. Sin embargo, tendrá que gestionar muchos aspectos de confiabilidad y administración por sí mismo. Lo que desea es una opción que le otorgue el control que necesita, pero que además gestione la mayoría del trabajo relacionado con dichos aspectos.

Pues esto es exactamente lo que proporciona Servicios en la nube de Azure. Esta tecnología está diseñada expresamente para permitir aplicaciones escalables, confiables y de bajo coste administrativo, y es un ejemplo de lo que comúnmente se denomina plataforma como servicio (PaaS). Para utilizarla, debe crear una aplicación mediante la tecnología que elija, por ejemplo C#, Java, PHP, Python, Node.js o alguna otra. A continuación, el código se ejecuta en máquinas virtuales (conocidas como instancias) que ejecutan una versión de Windows Server.

Pero estas VM son distintas de aquellas que se crean con Máquinas virtuales de Azure. Y la diferencia es esta: Azure es quien las administra. Así, realiza tareas como instalar revisiones de sistemas operativos e implementar automáticamente las nuevas imágenes revisadas. Esto implica que su aplicación no debe mantener el estado en las instancias de rol web o de trabajo, sino que, por el contrario, se mantiene en una de las opciones de administración de datos de Azure que se describen en la siguiente sección. Azure también supervisa las VM y las reinicia en caso de error. Puede configurar los servicios en la nube para que creen automáticamente más o menos instancias según la demanda. Esto le permite atender un aumento en el uso y reducir de nuevo para no pagar tanto cuando el uso descienda.

A la hora de crear una instancia, dispone de dos roles para elegir, ambos basados en Windows Server. La principal diferencia entre los dos es que una instancia de un rol web ejecuta IIS, mientras que la de un rol de trabajo no. No obstante, ambas se administran de la misma forma, y lo normal en una aplicación es usar las dos. Por ejemplo, una instancia de rol web podría aceptar solicitudes de los usuarios, y luego pasarlas a una instancia de rol de trabajo para procesarlas. Para escalar o reducir verticalmente la aplicación, puede solicitar que Azure cree más instancias de uno de los roles o apagar las instancias existentes. Y como sucede con Máquinas virtuales de Azure, solo paga por el tiempo que se ejecuta cada instancia de rol web o de trabajo.

**Escenarios de Servicios en la nube**

Servicios en la nube es una opción ideal para el escalado horizontal masivo cuando necesite un control sobre la plataforma superior al que ofrece Sitios web Azure pero no requiera controlar el sistema operativo subyacente.

####Selección de un modelo de proceso####
La página de comparación entre Sitios web Azure, Servicios en la nube y Máquinas virtuales de Azure (http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/) proporciona información más detallada sobre cómo seleccionar un modelo de proceso.




<h2><a id="data"></a>Administración de datos</h2>

Las aplicaciones necesitan datos, y diferentes tipos de aplicaciones necesitan diferentes tipos de datos. Como consecuencia, Azure ofrece varias formas de almacenar y administrar los datos. Azure proporciona muchas opciones de almacenamiento, todas ellas diseñadas para ofrecer un rendimiento muy duradero. Con cualquiera de estas opciones, hay siempre 3 copias de sus datos sincronizados entre los centros de datos de Azure (6 si permite que Azure utilice la redundancia geográfica para hacer una copia de seguridad en otro centro de datos que esté al menos a 450 kilómetros de distancia).


###En Máquinas virtuales###
La posibilidad de ejecutar SQL Server u otro DBMS en una VM creada con Máquinas virtuales de Azure ya se ha mencionado. Recuerde que esta opción no se limita a los sistemas relacionales; también tiene la libertad de ejecutar tecnologías NoSQL como MongoDB y Cassandra. Ejecutar su propio sistema de base de datos es sencillo, es una réplica de lo que estamos acostumbrados a hacer en nuestros centros de datos, pero también exige tratar la administración de ese DBMS. En otras opciones, Azure controla más administración (o toda) en su lugar.

De nuevo, el estado de la Máquina virtual y cualquier disco de datos adicional que cree o cargue están respaldados por el almacenamiento de blobs (del que hablaremos más adelante).


###Base de datos SQL de Azure###
![Base de datos SQL de almacenamiento de Azure](./media/intro-to-azure/StorageAzureSQLDatabaseIntroNew.png) **Base de datos SQL de Azure ofrece un servicio de base de datos relacional administrado en la nube.**

Para el almacenamiento relacional, Azure proporciona la característica Base de datos SQL. No se deje engañar por el nombre. Se trata de algo diferente de la típica Base de datos SQL que ofrece SQL Server y que se ejecuta sobre Windows Server.

Antes llamada SQL Azure, Base de datos SQL de Azure ofrece todas las características principales de un sistema de administración de base de datos relacional, lo que incluye transacciones atómicas, acceso simultáneo a los datos por parte de varios usuarios con integridad de datos, consultas SQL ANSI y un modelo de programación conocido. Al igual que SQL Server, se puede obtener acceso a Base de datos SQL mediante Entity Framework, ADO.NET, JDBC y otras conocidas tecnologías de acceso a datos. También es compatible con la mayoría del lenguaje T-SQL, junto con herramientas de SQL Server como SQL Server Management Studio. A todos aquellos que estén familiarizados con SQL Server (u otra base de datos relacional), el uso de Base de datos SQL les resultará muy sencillo.

Pero Base de datos SQL no es simplemente un DBMS en la nube, es un servicio PaaS. En este sentido, sigue teniendo el control sobre sus datos y sobre quién puede tener acceso a ellos, pero Base de datos SQL se ocupa del repetitivo trabajo administrativo, como administrar la infraestructura de hardware y mantener actualizado el software de base de datos y del sistema operativo. Base de datos SQL ofrece también una alta disponibilidad, copias de seguridad automática y capacidad de restauración en el momento, y puede replicar copias entre regiones geográficas.

Existe también una opción Premium por la que paga un poco más para disponer de su propio servidor dedicado. Con la opción Estándar, la base de datos se ejecuta en un hardware compartido, lo cual podría limitar las consultas de base de datos si se encontrara en un servidor especialmente ocupado.

**Escenarios para Base de datos SQL**

Si va a crear una aplicación de Azure (usando uno de los modelos de proceso) que necesita almacenamiento relacional, Base de datos SQL puede ser una buena opción. Las aplicaciones que se ejecutan fuera de la nube pueden utilizar también este servicio, aunque hay multitud de otros escenarios de uso. Por ejemplo, se puede tener acceso a los datos almacenados en Base de datos SQL desde diferentes sistemas cliente, como equipos de escritorio, equipos portátiles, tabletas y teléfonos. Y como proporciona alta disponibilidad integrada a través de la replicación, el uso de Base de datos SQL puede ayudar a reducir el tiempo de inactividad.


###Tablas###
![Tablas de almacenamiento de Azure](./media/intro-to-azure/StorageTablesIntroNew.png) **Ilustración: Las Tablas de Azure proporcionan un modelo plano NoSQL para almacenar datos.**

Esta característica recibe en ocasiones nombres diferentes porque forma parte de otra más amplia denominada "Almacenamiento de Azure". Si ve "tablas", "tablas de Azure" o "tablas de almacenamiento", todo hace referencia a lo mismo.

Y no se preocupe por el nombre: esta tecnología no proporciona almacenamiento relacional. De hecho, es un ejemplo de un enfoque NoSQL conocido como almacén de clave/valor. Tablas de Azure permite a una aplicación almacenar propiedades de diversos tipos, como cadenas, enteros y fechas. Luego, la aplicación puede recuperar un grupo de propiedades proporcionando una clave única para ese grupo. Aunque no se admiten operaciones complejas, como uniones, las tablas ofrecen acceso rápido a datos con tipo. También son muy escalables, una sola tabla puede contener hasta un terabyte de datos. Y dada su simplicidad, las tablas son habitualmente menos caras de utilizar que el almacenamiento relacional de Base de datos SQL.

**Escenarios para tablas**

Supongamos que desea crear una aplicación de Azure que necesita acceso rápido a datos con tipo, quizás a una gran cantidad de ellos, pero no es necesario realizar consultas SQL complejas en estos datos. Por ejemplo, imagine que va a crear una aplicación de consumidor que necesita almacenar información de perfil de cliente para cada usuario. Su aplicación va a ser muy conocida, así que tiene que permitir muchos datos, pero prácticamente lo único que hará con esos datos es almacenarlos y recuperarlos de formas muy sencillas. Esta es exactamente la clase de escenario donde las tablas de Azure tienen sentido.


###Blobs###
![Blobs de almacenamiento de Azure](./media/intro-to-azure/StorageBlobsIntroNew.png) **Ilustración: Los Blobs de Azure proporcionan datos binarios sin estructurar.**

Blobs de Azure (como antes, "Almacenamiento en blobs" y "Blobs de almacenamiento" hacen referencia a lo mismo) está diseñado para almacenar datos binarios sin estructurar. Al igual que las Tablas, los Blobs proporcionan almacenamiento económico, y un solo blob puede tener un tamaño hasta de 1 TB (un terabyte). Las aplicaciones de Azure pueden utilizar también unidades de Azure, que permiten a los blobs proporcionar almacenamiento persistente para un sistema de archivos de Windows montado en una instancia de Azure. Aunque la aplicación ve archivos ordinarios de Windows, el contenido está realmente almacenado en un blob.

El Almacenamiento de blobs se utiliza por muchas otras características de Azure (incluidas Máquinas virtuales), así que sin duda puede manejar también sus cargas de trabajo.

**Escenarios para Blobs**

Una aplicación que almacena vídeo, archivos masivos u otra información de tipo binario puede utilizar blobs como método de almacenamiento sencillo y barato. Los Blobs suelen utilizarse junto con otros servicios como la Red de entrega de contenido, de la que hablaremos más adelante.

###Importación / Exportación###
![Azure Import Export Service](./media/intro-to-azure/ImportExportIntroNew.png)
 
**Ilustración: Importación y exportación de Azure ofrece la posibilidad de enviar una unidad de disco duro física a o desde Azure para la importación o exportación de datos masivos más rápida y barata.**

Es posible que alguna vez desee trasladar una gran cantidad de datos a Azure. Esto tomaría mucho tiempo, quizá días, y utilizaría mucho ancho de banda. En estos casos, puede utilizar Importación/Exportación de Azure, que le permite enviar unidades de disco duro SATA de 3.5" cifradas por Bitlocker directamente a centros de datos de Azure, donde Microsoft transferirá los datos al almacenamiento de blobs en su lugar. Una vez completada la carga, Microsoft le devuelve las unidades. También puede solicitar que se exporten grandes cantidades de datos desde el Almacenamiento de blobs a unidades de disco duro y que se le envíen de vuelta por correo.

**Escenarios para la importación y exportación**

- **Migración de datos de gran tamaño**: si tiene grandes cantidades de datos (terabytes) que quiere cargar en Azure, el servicio de importación y exportación es a menudo mucho más rápido y quizá más barato que la transferencia por Internet. Una vez que los datos se encuentren en blobs, puede procesarlos en otros fomatos como Almacenamiento en tablas o Base de datos SQL.
 
- **Recuperación de datos archivados**: puede usar la importación y exportación para que Microsoft transfiera grandes cantidades de datos almacenados en Almacenamiento de blobs de Azure a un dispositivo que envíe y solicitar que le devuelvan dicho dispositivo a donde desee. Como esto puede tomar algún tiempo, no es una buena opción para la recuperación ante desastres. Es mejor para datos archivados a los que no necesita obtener un acceso rápido.


###Servicio de archivos de Azure###
![Servicio de archivos de Azure](./media/intro-to-azure/FileServiceIntroNew.png) **Ilustración: los Servicios de archivo de Azure ofrecen rutas de acceso \servidor\recurso compartido de SMB para aplicaciones que se ejecutan en la nube.**

En un entorno local, es habitual usar grandes cantidades de almacenamiento de archivos al que se puede obtener acceso a través del protocolo Bloque de mensajes del servidor (SMB) utilizando un formato \Servidor\recurso compartido. Azure cuenta ahora con un servicio que le permite utilizar este protocolo en la nube. Las aplicaciones que se ejecutan en Azure pueden usarlo para compartir archivos entre máquinas virtuales utilizando las API de sistemas de archivos familiares como ReadFile y WriteFile. Además, es posible obtener acceso a los archivos al mismo tiempo a través de una interfaz REST, lo que hace posible obtener acceso a los recursos compartidos desde instalaciones locales, donde puede configurar además una red virtual. Archivos de Azure se crea sobre un servicio de blobs; por tanto, hereda la misma disponibilidad, durabilidad, escalabilidad y redundancia geográfica del Almacenamiento de Azure.

**Escenarios para Archivos de Azure**

- **Migración de aplicaciones existentes a la nube**: es más sencillo migrar aplicaciones locales a la nube que usar recursos compartidos de archivos para compartir datos entre diferentes partes de la aplicación. Cada máquina virtual se conecta al recurso compartido de archivos y así puede leer y escribir archivos del mismo modo que lo haría sobre un recurso compartido de archivos local.

- **Configuración de aplicaciones compartidas**: algo recurrente entre las aplicaciones distribuidas es contar con archivos de configuración en una ubicación centralizada que permite tener acceso a ellos desde muchas máquinas virtuales diferentes. Estos archivos de configuración se pueden almacenar en un recurso compartido de archivos de Azure para que lo lean todas las instancias de la aplicación. La configuración se puede administrar también a través de la interfaz REST, que permite un acceso a los archivos de configuración desde cualquier parte del mundo.

- **Recurso compartido de diagnóstico**: puede guardar y compartir archivos de diagnóstico como registros, métricas y volcados de memoria. El hecho de poder tener acceso a estos archivos a través tanto de SMB como de la interfaz REST permite a las aplicaciones utilizar una variedad de herramientas de análisis para procesar y analizar los datos de diagnóstico.

- **Desarrollo, prueba y depuración**: cuando desarrolladores o administradores están trabajando en máquinas virtuales en la nube, a menudo necesitan diversas herramientas o utilidades. La instalación y distribución de estas utilidades en cada una de las máquinas virtuales requiere mucho tiempo. Con Archivos de Azure, un desarrollador o administrador puede almacenar sus herramientas favoritas en un recurso compartido de archivos y conectarse a ellas desde prácticamente cualquier máquina virtual.










<h2><a id="networking"></a>Redes</h2>

En la actualidad, Azure se ejecuta en muchos centros de datos repartidos por todo el mundo. Cuando ejecute una aplicación o almacene datos, puede seleccionar uno o varios de estos centros de datos. También puede conectar con ellos de varias formas utilizando los servicios que se presentan a continuación.


###Red virtual###
![VirtualNetwork](./media/intro-to-azure/VirtualNetworkIntroNew.png)

**Ilustración: Las redes virtuales ofrecen una red privada en la nube para permitir que diferentes servicios se comuniquen entre sí, o con recursos locales si configura una conexión VPN (una conexión entre locales).**


Una manera útil de usar la nube pública es considerarla una extensión de su propio centro de datos.
 
Como puede crear VM a petición y luego eliminarlas (y dejar de pagar) cuando ya no las necesite, puede contar con el poder de la informática solo cuando lo desee. Y como Máquinas virtuales de Azure le permite crear VM que se ejecutan en SharePoint, Active Directory y otros programas de software local conocido, este enfoque puede funcionar con las aplicaciones que ya tiene.

Si bien, para que esto resulte realmente útil, sería aconsejable que sus usuarios trataran estas aplicaciones como si se ejecutaran en su propio centro de datos. Esto es exactamente lo que permite Red virtual de Azure. Mediante un dispositivo de puerta de enlace de VPN, un administrador puede configurar una red privada virtual (VPN) entre la red local y las máquinas virtuales que se implementan en una red virtual de Azure. Como asigna sus propias direcciones IP v4 a las VM en la nube, estas parecen estar en su propia red. De este modo, los usuarios de su organización pueden tener acceso a las aplicaciones que dichas VM contienen como si se estuvieran ejecutando localmente.

Para obtener más información sobre la planificación y la creación de una red virtual idónea para usted, consulte [Red virtual](http://msdn.microsoft.com/library/azure/jj156007.aspx).

###ExpressRoute###

![ExpressRoute](./media/intro-to-azure/ExpressRouteIntroNew.png) **Ilustración: ExpressRoute usa una red virtual de Azure, pero enruta conexiones a través de líneas dedicadas más rápidas en lugar de usar la red pública de Internet.**

Si necesita más ancho de banda o seguridad que la que puede ofrecer Red virtual de Azure, puede fijarse en ExpressRoute. En algunos casos, ExpressRoute puede ahorarrle también algún dinero. Sigue necesitando una red virtual en Azure, pero el vínculo entre Azure y su sitio utiliza una conexión dedicada que no vaya por la red pública de Internet. A fin de utilizar este servicio, necesitará un contrato con un proveedor del servicio de red o un proveedor de Exchange.

La configuración de una conexión ExpressRoute requiere más tiempo y planificación; por ello es posible que prefiera empezar con una red privada virtual y migrar luego a una conexión ExpressRoute.

Para obtener más información sobre ExpressRoute, consulte [ Información general técnica sobre ExpressRoute](http://msdn.microsoft.com/library/azure/dn606309.aspx).

###Administrador de tráfico###

![TrafficManager](./media/intro-to-azure/TrafficManagerIntroNew.png) **Ilustración: El Administrador de tráfico de Azure permite enrutar el tráfico global a su servicio en función de reglas inteligentes.**
 
Si su aplicación de Azure se va a ejecutar en varios centros de datos, puede utilizar el Administrador de tráfico de Azure para enrutar las solicitudes de los usuarios de forma inteligente entre instancias de la aplicación. También puede enrutar tráfico a servicios que no se ejecuten en Azure siempre y cuando se pueda obtener acceso a ellos desde Internet.

Una aplicación de Azure con usuarios en una única parte del mundo podría ejecutarse en un solo centro de datos de Azure. Sin embargo, una aplicación con usuarios repartidos por todo el mundo es más probable que se ejecute en varios centros de datos, quizás incluso en todos ellos. En este segundo caso, se enfrenta a un problema: ¿cómo dirigir a los usuarios de forma inteligente a las instancias de la aplicación? La mayor parte del tiempo, deseará que cada usuario tenga acceso al centro de datos más próximo, dado que de este modo obtendrá el mejor tiempo de respuesta. ¿Pero qué sucede si esa instancia de la aplicación experimenta sobrecarga o no está disponible? En ese caso, sería mejor dirigir la solicitud automáticamente a otro centro de datos. Pues esto es exactamente lo que hace el Administrador de tráfico de Azure.

El propietario de la aplicación define las reglas que especifican cómo se deben dirigir las solicitudes de los usuarios a los centros de datos y, luego, confía en el Administrador de tráfico para hacer cumplir esas reglas. Por ejemplo, los usuarios se dirigirían normalmente al centro de datos de Azure más próximo, pero se les enviaría a otro cuando el tiempo de respuesta de su centro de datos predeterminado superase el tiempo de respuesta de otros centros de datos. En el caso de aplicaciones distribuidas globalmente con muchos usuarios, contar con un servicio integrado para solucionar problemas como estos resulta muy útil.

El Administrador de tráfico utiliza Directory Name Service (DNS) para dirigir usuarios a extremos del servicio, pero el tráfico deja de pasar por el Administrador de tráfico una vez que se establece la conexión. Esto impide que el Administrador de tráfico se convierta en un cuello de botella que podría ralentizar las comunicaciones de sus servicios.


<h2><a id="DevService"></a>Servicios para desarrolladores</h2>
Azure ofrece diversas herramientas para ayudar a los desarrolladores y profesionales de TI a crear y mantener aplicaciones en la nube.

###SDK de Azure###
En 2008, la primera versión preliminar de Azure solo admitía el desarrollo con .NET. En la actualidad, sin embargo, puede crear aplicaciones de Azure prácticamente con cualquier lenguaje. Microsoft proporciona actualmente SDK específicos del lenguaje para .NET, Java, PHP, Node.js, Ruby y Python. También hay un SDK general de Azure que proporciona compatibilidad básica con cualquier lenguaje, como C++.

Estos SDK le ayudan a crear, implementar y administrar aplicaciones de Azure. Se pueden encontrar en [www.microsoftazure.com](http://azure.microsoft.com/downloads/) o GitHub, y se pueden usar con Visual Studio y Eclipse. Azure ofrece también herramientas de línea de comandos que los desarrolladores pueden utilizar con cualquier editor o entorno de desarrollo, como herramientas para implementar aplicaciones en Azure desde sistemas Linux y Macintosh.

Además de ayudarle a crear aplicaciones de Azure, estos SDK también proporcionan bibliotecas de clientes que le ayudan a crear software que utiliza servicios de Azure. Por ejemplo, puede crear una aplicación que lea y escriba blobs de Azure, o crear una herramienta que implemente aplicaciones de Azure a través de la interfaz de administración de Azure.

###Visual Studio Online###

Visual Studio Online es una nombre comercial que abarca diversos servicios que ayudan a desarrollar aplicaciones en Azure.

Para evitar confusiones: no ofrece una versión hospedada o basada en web de Visual Studio. Seguirá siendo necesario que disponga de una copia local de Visual Studio en ejecución. Sin embargo, pone a disposición del usuario muchas otras herramientas que pueden resultar de gran utilidad.

Sí incluye un sistema de control de código fuente denominado Team Foundation Service, que ofrece un control de las versiones y el seguimiento de elementos de trabajo. Aún así, puede utilizar Git para el control de versiones si lo prefiere. Y puede variar el sistema de control de código fuente utilizado por proyecto. Puede crear proyectos de equipo privados sin límite a los que se puede obtener acceso desde cualquier parte del mundo.

Visual Studio Online ofrece un servicio para prueba de carga. Puede ejecutar pruebas de carga creadas en Visual Studio en máquinas virtuales en la nube. Especifique el número total de usuarios con los que quiere llevar a cabo la prueba de carga y Visual Studio Online determinará automáticamente cuántos agentes se necesitan, pondrá a punto las máquinas virtuales requeridas y ejecutará las pruebas de carga. Si está suscrito a MSDN, podrá disfrutar de miles de minutos de usuario gratuitos para prueba de carga al mes.

Visual Studio Online ofrece también un servicio denominado Application Insights, que le ofrece un análisis de toda la aplicación. Proporciona estadísticas sobre el rendimiento y el uso que se está haciendo de la aplicación. Si ya está utilizando System Center Operations Manager, también puede enlazarse a él y emitir alertas cuando ocurra algún problema.

Además, ofrece soporte para el desarrollo ágil con características como compilaciones de integración continua, tableros kanban y salas para equipos virtules.

**Escenarios de Visual Studio Online**

Visual Studio Online supone una buena opción para empresas que tienen que colaborar a nivel mundial y no disponen todavía de una infraestructura establecida para ello. Puede instalarla en minutos, seleccionar un sistema de control de código fuente y empezar a escribir código y compilar ese mismo día. Las herramientas de equipo permiten la coordinación y la colaboración y las herramientas adicionales suministran el análisis requerido para probar y ajustar su aplicación rápidamente.

No obstante, las organizaciones que tengan ya un sistema local pueden probar nuevos proyectos en Visual Studio Online para ver si es más eficaz.

###Automatización###
A nadie le gusta malgastar el tiempo haciendo los mismos procesos manuales una y otra vez. Automatización de Azure ofrece un método para crear, supervisar, administrar e implementar recursos en su entorno de Azure.

Automatización utiliza "runbooks", que a su vez utiliza flujos de trabajo de Windows PowerShell (en lugar de simplemente PowerShell) a nivel más profundo. Los runbooks están diseñado para ejecutarse sin interacción del usuario. Los flujos de trabajo de PowerShell permiten guardar el estado de un script en puntos de control a lo largo del proceso. De este modo, si se produce un error, no tiene que iniciar un script desde el principio, sino que puede hacerlo desde el último punto de control. Esto le ahorra mucho trabajo intentando que el script controle todos los posibles errores.

**Escenarios de Automatización**

Automatización de Azure es un buena opción para automatizar las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia de Azure.


###Administración de API###

La creación y publicación de interfaces de programación de aplicaciones (API) en Internet es un método muy utilizado para ofrecer servicios a las aplicaciones. Si esos servicios se pueden volver a vender (por ejemplo, los datos del tiempo), una organización puede conceder acceso a esos mismos servicios a otros terceros a cambio de una cuota. A medida que aumentan los socios, seguramente tendrá que optimizar y controlar el acceso. Es posible incluso que algunos socios necesiten los datos en un formato distinto.

Administración de API de Azure facilita a las organizaciones la publicación de API a socios, empleados y desarrolladores de terceros de manera segura y a escala. Proporciona un extremo API diferente y actúa como proxy para llamar al extremo real al tiempo que ofrece servicios como almacenamiento en caché, transformación, control de acceso y agregado de analíticas.

**Escenarios de Administración de API**

Imagine que su empresa tiene un conjunto de dispositivos que necesitan devolver la llamada a un servicio central para obtener datos (por ejemplo, una empresa de transporte que tenga dispositivos en todos los camiones que están en carretera). Seguro que la empresa querrá poner en marcha un sistema que le permita realizar un seguimiento de sus propios camiones a fin de predecir y actualizar las horas de entrega. Es posible conocer cuántos camiones tiene y planificar en consonancia. Cada camión necesitará un dispositivo que devuelva la llamada a una ubicación central con su posicionamiento y datos de velocidad, y quizá más información.

Un cliente de la empresa de transportes probablemente se beneficiará también del hecho de conocer estos datos de posicionamiento. El cliente podría utilizarlo para conocer la distancia que tienen que recorrer los productos, dónde se quedan retenidos y cuánto pagan a lo largo de determinadas rutas (si se combina esta información con lo que se pagó por el envío). Si la empresa de transporte agrega ya estos datos, muchos clientes pagarían por disponer de ellos. Pero entonces la empresa de transporte tendría que encontrar una forma de hacer llegar la información a los clientes. Una vez que los clientes tuvieran acceso a ella, perdería el control sobre la frecuencia con que se consultan esos datos. Tendría que proporcionar reglas acerca de quién podría tener acceso a qué datos. Todas esas reglas tendrían que compilarse en su API externa. Es ahí donde Administración de API puede resultar útil.


 

<h2><a id="identity"></a>Identidad y acceso</h2>
El trabajo con identidades forma parte de la mayoría de las aplicaciones. Por ejemplo, saber quién es un usuario permite que una aplicación decida cómo debe interactuar con ese usuario. Azure ofrece servicios para ayudar a realizar un seguimiento de la identidad así como integrarla con almacenes de identidad que ya esté utilizando.


###Active Directory###

Como la mayoría de los servicios de directorio, Azure Active Directory almacena información acerca de los usuarios y de las organizaciones a las que pertenecen. Permite que los usuarios inicien sesión y les proporciona tokens que pueden presentar a las aplicaciones para demostrar su identidad. También permite sincronizar la información del usuario con Windows Server Active Directory al ejecutarse en la red local. Aunque los mecanismos y los formatos de datos que emplea Azure Active Directory no son idénticos a los usados en Windows Server Active Directory, las funciones que realiza son bastante parecidas.
 
Es importante comprender que Azure Active Directory está diseñado principalmente para su uso en aplicaciones en la nube. Así por ejemplo, se puede utilizar en aplicaciones que ejecutan Azure o en otras plataformas de nube. También se usa en aplicaciones de nube de Microsoft, como las de Office 365. Sin embargo, si desea extender su centro de datos en la nube mediante Máquinas virtuales de Azure y red Virtual de Azure, Azure Active Directory no es la elección adecuada. En su lugar, deberá ejecutar Windows Server Active Directory en Máquinas virtuales.

Para que las aplicaciones puedan tener acceso a la información que contiene, Azure Active Directory proporciona una API de RESTful conocida como Azure Active Directory Graph. Esta API permite que las aplicaciones que se ejecutan en cualquier plataforma tengan acceso a los objetos de directorio y a las relaciones entre ellos. Por ejemplo, una aplicación autorizada podría utilizar esta API para obtener información acerca de un usuario, los grupos a los que pertenece, etc. Las aplicaciones también pueden ver las relaciones entre los usuarios (su gráfico social), lo que les permite trabajar de una forma más inteligente con las conexiones entre la gente.

Otra funcionalidad de este servicio, el Servicio de control de acceso de Azure AD , permite que una aplicación pueda aceptar fácilmente información de identidad de Facebook, Google, Windows Live ID y otros proveedores de identidad conocidos. No es necesario que la aplicación comprenda los diversos formatos de datos y protocolos que utiliza cada uno de estos proveedores, el Control de acceso convierte todos ellos en un único formato común. También permite que una aplicación acepte inicios de sesión de uno o varios dominios de Active Directory. Por ejemplo, un proveedor que proporciona una aplicación SaaS podría utilizar el Servicio de control de acceso de Azure AD para dar a los usuarios de cada uno de sus clientes un inicio de sesión único en la aplicación.

Los servicios de directorio son uno de los pilares de la informática local, por lo que no debe sorprendernos de que sean también importantes en la nube.

###Multi-Factor Authentication###
![Azure Multi-Factor Authentication](./media/intro-to-azure/MFAIntroNew.png) **Ilustración: Multi-Factor Authentication dota a su aplicación de la funcionalidad para verificar más de una forma de identificación**
 
La seguridad siempre es importante. Multi-factor authentication (MFA) ayuda a garantizar que sean solo los usuarios quienes obtienen acceso a sus propias cuentas. El servicio MFA (conocido también como autenticación de dos factores o "2FA") requiere que los usuarios proporcionen dos de estos tres métodos de verificación de identidades para los inicios de sesión y las transacciones de usuario.

- Un elemento que conoce (normalmente una contraseña).
- Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
- Un elemento físico que le identifica (biométrica).

Así, cuando un usuario inicia sesión, puede solicitarle que verifique también su identidad con una aplicación móvil, una llamada telefónica o un mensaje de texto en combinación con su contraseña. De manera predeterminada, Azure Active Directory admite el uso de contraseñas como único método de autenticación para inicios de sesión de usuario. Puede utilizar MFA junto con Azure AD o aplicaciones y directorios personalizados mediante el SDK de MFA. Puede utilizarlo junto con aplicaciones locales utilizando el Servidor Multi-Factor Authentication.

**Escenario de MFA**

Protección de inicios de sesión sobre cuentas con información delicada como inicios de sesión de bancos y acceso de código fuente donde una entrada no autorizada podría tener un elevado coste propietario intelectual o financiero.







<h2><a id="Mobile"></a>Móvil</h2>

Si va a crear una aplicación para un dispositivo móvil, Azure puede ayudar a almacenar datos en la nube, autenticar a los usuarios y enviar notificaciones de inserción sin tener que escribir una gran cantidad de código personalizado.

Si bien es cierto que puede crear el back-end de una aplicación móvil mediante Máquinas virtuales, Servicios en la nube o Sitios web, puede ahorrar mucho tiempo en escribir los componentes de servicio subyacentes utilizando los servicios de Azure.


###Servicios móviles###

![MobileServices](./media/intro-to-azure/MobileServicesIntroNew.png) **Ilustración: Servicios móviles ofrece una funcionalidad habitualmente requerida por aplicaciones que interactúan con dispositivos móviles.**

Servicios móviles de Azure ofrece muchas funciones útiles que le permitirán ahorrar tiempo al generar un back-end para una aplicación móvil. Le permite realizar sencillas tareas de aprovisionamiento y administración de los datos almacenados en una Base de datos SQL. Con código del lado servidor puede utilizar fácilmente opciones adicionales de almacenamiento de datos como almacenamiento de blobs o MongoDB. Servicios móviles brinda soporte para las notificaciones, aunque en algunos casos puede utilizar también para ello los Centros de notificaciones (como se describe a continuación). El servicio tiene además una API de REST a la que su aplicación móvil puede llamar para que realice el trabajo. Servicios móviles ofrece también la posibilidad de autenticar usuarios a través de Microsoft y Active Directory, así como otros populares proveedores de identidades como Facebook, Twitter y Google.


Puede utilizar otros servicios de Azure, como Bus de servicio y roles de trabajo, y conectarlos a sistemas locales. Puede incluso consumir complementos de terceros desde la Tienda Azure (como SendGrid para el correo electrónico) a fin de ofrecer una funcionalidad adicional.


Las bibliotecas de cliente nativas para Android, iOS, HTML/JavaScript, Windows Phone y la Tienda Windows facilitan el desarrollo de aplicaciones en todas las principales plataformas móviles. Una API de REST le permite usar la funcionalidad de datos y autenticación de Servicios móviles con aplicaciones en diferentes plataformas. Un único servicio móvil puede respaldar varias aplicaciones cliente; de este modo, puede proporcionar una experiencia de usuario coherente entre dispositivos.

Dado que Azure ya admite la escala masiva, puede controlar el tráfico a medida que aumente la popularidad de su aplicación. También se pueden utilizar la supervisión y el registro para ayudar a solucionar problemas y administrar el rendimiento.


###Centros de notificaciones###

![NotificationHubs](./media/intro-to-azure/NotificationHubsIntroNew.png) **Ilustración: Los Centros de notificaciones ofrecen una funcionalidad que requieren habitualmente las aplicaciones que interactúan con dispositivos móviles.**

Aunque puede escribir código para crear notificaciones en Servicios móviles de Azure, los Centros de notificaciones están optimizados para difundir millones de notificaciones de inserción en pocos minutos. No tiene que precuparse por detalles como el operador de telefonía móvil o el fabricante del dispositivo. Puede dirigirse a individuos o millones de usuarios con una sola llamada de API.

El diseño de Centros de notificaciones le permite funcionar con cualquier back-end. Puede utilizar Servicios móviles de Azure, un back-end personalizado en la nube que se ejecute en cualquier proveedor o un back-end local.

**Escenarios de Centros de notificaciones** Si estuviera programando un juego para móviles en el que los jugadores participasen por turnos, seguramente tendría que buscar la manera de notificar al jugador 2 que el jugador 1 terminó su turno. Si fuera eso todo lo que necesita, podría utilizar Servicios móviles. Pero si tuviera a 100.000 usuarios jugando y quisiera enviar una oferta en la que fuera importante el factor tiempo, Centros de notificaciones sería una mejor opción.

Puede enviar noticias de última hora, eventos deportivos y notificaciones sobre anuncios de productos a millones de usuarios con baja latencia. Las empresas pueden hacer llegar a sus empleados comunicaciones novedosas en las que el tiempo es un factor relevante, como clientes potenciales, de manera que los empleados no tengan que estar consultando el correo u otras aplicaciones constantentemente para mantenerse informados. También puede enviar contraseñas válidas una sola vez requeridas para la autenticación multifactor.
   



<h2><a id="Backup"></a>Copia de seguridad</h2>
Todas las empresas necesitan hacer copias de seguridad de los datos y restaurarlos. Puede utilizar Azure para hacer copias de seguridad de su aplicación y restaurarla tanto en la nube como en un entorno local. Azure ofrece diferentes opciones para ayudar en función del tipo de copia de seguridad.

###Recuperación de sitios###

 
Azure Site Recovery (anteriormente Administrador de recuperación de Hyper-V) puede ayudarle a proteger aplicaciones importantes mediante la coordinación de la replicación y la recuperación entre sitios. Site Recovery proporciona capacidad para proteger las aplicaciones basadas en Hyper-v, VMWare o SAN en su propio sitio secundario, en el sitio del anfitrión o en Azure y evitar los gastos y la complejidad de crear y administrar su propia ubicación secundaria. Azure cifra los datos y las comunicaciones y el usuario tiene la opción de habilitar también el cifrado de los datos en reposo.

Asimismo, supervisa el estado de los servicios continuamente y ayuda a automatizar la recuperación ordenada de servicios en caso de que se produzca una interrupción del sitio en el centro de datos principal. Las máquinas virtuales se pueden preparar de manera organizada para ayudar a restaurar las aplicaciones rápidamente, incluso para cargas de trabajo complejas de varios niveles.

Recuperación de sitios funciona con tecnologías existentes como Hyper-V Replica, System Center y SQL Server AlwaysOn. Consulte [Información general sobre Azure Site Recovery](hyper-v-recovery-manager-overview.md) para obtener más detalles.

###Copia de seguridad de Azure###
![Copia de seguridad de Azure](./media/intro-to-azure/AzureBackupIntroNew.png) **Ilustración: Copia de seguridad de Azure realiza copias de seguridad de los datos desde servidores de Windows locales a la nube.**

Copia de seguridad de Azure realiza copias de seguridad de los servidores locales que ejecutan Windows Server a la nube. Puede administrar sus copias de seguridad directamente desde las herramientas de copia de seguridad en Windows Server 2012, Windows Server 2012 Essentials o System Center 2012 - Data Protection Manager. También es posible utilizar un agente especializado de copia de seguridad.

Los datos están más seguros porque las copias de seguridad se cifran antes de la transmisión y se almacenan cifradas en Azure con la protección de un certificado que cargue. El servicio utiliza la misma protección de datos redundante y altamente disponible presente en Almacenamiento de Azure. Puede hacer copias de seguridad de archivos y carpetas de manera programada o de inmediato, ejecutando copias de seguridad completas o incrementales. Una vez que se realiza la copia de seguridad de los datos en la nube, los usuarios autorizados pueden recuperar fácilmente copias de seguridad en cualquier servidor. Ofrece también directivas de retención de datos configurables, compresión de datos y limitación a la transferencia de datos para poder administrar el coste de almacenamiento y transferencia de datos.

**Escenarios de Copia de seguridad de Azure**

Si ya utiliza Windows Server o System Center, Copia de seguridad de Azure es la solución natural para hacer una copia de seguridad del sistema de archivos de servidores, máquinas virtuales y bases de datos de SQL Server. Funciona con archivos cifrados, dispersos y comprimidos. Existen algunas limitaciones; para conocerlas, [consulte primero los requisitos previos de Copia de seguridad de Azure](http://technet.microsoft.com/library/dn296608.aspx).



<h2><a id="messaging"></a>Mensajería e integración</h2>

Con independencia de lo que esté haciendo, con frecuencia el código necesita interactuar con otro código. En algunos casos, todo lo que hace falta es mensajería básica en cola. En otros, se requieren interacciones más complejas. Azure ofrece algunas formas diferentes de resolver estos problemas. La ilustración 5 muestra las opciones.

###Colas###
![Retransmisión de bus de servicio de Azure](./media/intro-to-azure/QueuesIntroNew.png) **Ilustración: Las colas permiten un acoplamiento flexible entre las partes de una aplicación y facilitan el escalado.**

Su concepto es sencillo: una aplicación coloca un mensaje en una cola y al final otra aplicación lee ese mensaje. Si su aplicación solo necesita este sencillo servicio, las colas de Azure podrían ser la mejor opción.

Debido a la manera en que ha crecido Azure con el tiempo, Colas de Almacenamiento de Azure y Colas de Bus de servicio ofrecen servicios similares de puesta en cola. Los motivos que pueden llevarle a usar uno u otro se explican en el documento bastante técnico [<LINK>](http://msdn.microsoft.com/library/azure/hh767287.aspx "Colas de Service Bus y Colas de Azure: comparación y diferencias"). En la mayoría de escenarios, cualquiera de los dos funcionará.

**Escenarios de Colas**

Actualmente, uno de los usos más comunes de las colas es dejar que una instancia de rol web se comunique con una instancia de rol de trabajo dentro de la misma aplicación de Servicios en la nube.

Por ejemplo, supongamos que crea una aplicación de Azure para compartir vídeo. La aplicación consta de código PHP que se ejecuta en un rol web que permite a los usuarios cargar y ver vídeos, junto con un rol de trabajo implementado en C# que convierte el vídeo cargado a varios formatos.

Cuando una instancia de rol web recibe un nuevo vídeo de un usuario, puede almacenarlo en un blob y enviar un mensaje a un rol de trabajo a través de una cola para indicar dónde encontrar este nuevo vídeo. A continuación, la instancia de rol de trabajo, no importa cuál, lee el mensaje de la cola y lleva a cabo las conversiones de vídeo necesarias en segundo plano.

Estructurar una aplicación de esta forma permite el procesamiento asincrónico, y también que la aplicación sea más sencilla de escalar, dado que el número de instancias de rol web y de rol de trabajo se puede variar de forma independiente. También puede utilizar el tamaño de una cola como desencadenador para escalar verticamente el número de roles de trabajo. Si sube demasiado, agrega más roles. Si cae, puede reducir el número de roles en ejecución para ahorrar dinero.

Puede utilizar este mismo modelo entre muchas partes diferentes de su aplicación incluso aunque no utilicen roles web y de trabajo. Le permite escalar o reducir verticalmente las partes en cualquier lado de la cola según requieran la demanda y el tiempo de procesado.


###Bus de servicio###
Tanto si se ejecutan en la nube, en el centro de datos, en un dispositivo móvil o en cualquier otra parte, las aplicaciones necesitan interactuar. El objetivo del Bus de servicio de Azure es permitir el intercambio de datos entre aplicaciones que se ejecutan prácticamente en cualquier parte.

Además de las colas descritas antes (una a una), el Bus de servicio ofrece también otros métodos de comunicación.


![Retransmisión de bus de servicio de Azure](./media/intro-to-azure/ServiceBusRelayIntroNew.png) **Ilustración: La Retransmisión de bus de servicio permite la comunicación entre aplicaciones en diferentes lados de un firewall.**

El Bus de servicio permite la comunicación directa por medio de su servicio de retransmisión, y proporciona una forma de interactuar a través de los firewalls. Las retransmisiones del Bus de servicio permiten que las aplicaciones se comuniquen mediante el intercambio de mensajes a través de un punto final hospedado en la nube, en lugar de localmente.

**Escenarios de Retransmisión de bus de servicio**

Las aplicaciones que se comunican a través del Bus de servicio pueden ser aplicaciones de Azure o software que se ejecute en alguna otra plataforma de nube. También pueden ser aplicaciones que se ejecutan fuera de la nube. Por ejemplo, piense en una línea aérea que implementa servicios de reservas en equipos dentro de su propio centro de datos. La línea aérea necesita exponer estos servicios a muchos clientes, como los terminales de facturación de los aeropuertos, los terminales del agente de reserva y, quizás, hasta los teléfonos de los clientes. Para ello, se podría utilizar un Bus de servicio y crear interacciones sin conexión directa entre las diversas aplicaciones.

![Temas de Service Bus de Azure](./media/intro-to-azure/ServiceBusTopicsSubsIntroNew.png) **Ilustración: Los temas de Service Bus permiten a varias aplicaciones publicar mensajes y a otras aplicaciones suscribirse para recibir mensajes que respondan a un criterio específico.**

Bus de servicio ofrece un mecanismo de publicación y suscripción denominado Temas y Suscripciones. Con el mecanismo de publicación y suscripción, una aplicación puede enviar mensajes a un tema, mientras que otras aplicaciones pueden crear suscripciones a ese tema. Este permite la comunicación uno a varios entre un conjunto de aplicaciones, lo que hace posible que varios destinatarios lean el mismo mensaje.

**Escenarios de Retransmisión de bus de servicio** Si está con alguna configuración en la que existen muchos mensajes que son importantes, pero varios sistemas situados por debajo en la línea de comunicación necesitan escuchar solo diferentes subconjuntos de esas comunicaciones, los temas y las suscripciones de Bus de servicio suponen una buena opción.
  

###Servicios de BizTalk###
![Servicios de BizTalk](./media/intro-to-azure/BizTalkServicesIntroNew.png) **Ilustración: Servicios de BizTalk ofrece la posibilidad de transformar formatos de mensajes XML en la nube.**

En ocasiones requiere conectar sistemas que se comunican mediante diferentes formatos de mensajería. Es habitual en las empresas contar con diferentes esquemas de bases de datos y formatos de mensajería XML, incluso si hay un estándar común disponible. En lugar de escribir mucho código personalizado, puede utilizar BizTalk Server en un sistema local para integrar varios sistemas. Servicios de BizTalk de Azure ofrece el mismo tipo de servicio, pero en la nube. Puede pagar solo por lo que utiliza y no preocuparse por la escalación como haría en el entorno local.
 

**Escenarios de Servicios de BizTalk** Las interacciones negocio a negocio (B2B) requieren habitualmente este tipo de conversión. Por ejemplo, una empresa constructora de aviones necesita solicitar piezas de diversos proveedores. Seguramente tendrá muchos proveedores de piezas. Estas solicitudes deberían automatizarse para ir directamente desde los sistemas de los constructores de aviones hasta los sistemas de los proveedores. Ninguna de las empresas desea cambiar sus sistemas principales y formatos de mensajes, y es muy poco probable que esos formatos coincidan. Servicios de BizTalk puede tomar los mensajes y convertirlos al nuevo formato en cualquiera de los dos sentidos. El trabajo de convertir puede correr por cuenta tanto del proveedor de aviones como de los diversos provedores, dependiendo de quién desee más control y la cantidad de conversión requerida.


<h2><a id="ComputeAssist"></a>Asistencia de proceso</h2>
Azure ofrece asistencia para los servicios que no necesitan ejecutarse todo el tiempo.

###Programador###

![Programador de Azure](./media/intro-to-azure/SchedulerIntroNew.png) **Ilustración: Programador de Azure ofrece un método para la programación de trabajos en un momento específico y con una duración concreta.**

A veces, las aplicaciones necesitan ejecutarse solo en un momento determinado. En Azure, puede ahorrar dinero con este tipo de aplicación en lugar de permitir que una aplicación se esté ejecutando constantemente a la espera de datos que procesar. Programador de Azure le permite programar en qué momento debe ejecutarse una aplicación en función de un intervalo de tiempo o calendario. Es confiable y verificará que un proceso se ejecuta incluso si existen errores de red, equipo o centro de datos. Use la API de REST de Programador para administrar estas acciones.

Cuando se produce una alarma programada, el Programador envía mensajes HTTP o HTTPS a un extremo específico o puede colocar un mensaje en una Cola de almacenamiento. Por tanto, es necesario que se preocupe por que su aplicación tenga un extremo accesible o supervise una cola de almacenamiento. Una vez que tenga el mensaje, puede realizar la acción para la que esté programada.

**Escenarios del Programador**

- Acciones de aplicación periódicas: por ejemplo, un servicio puede obtener periódicamente datos desde Twitter y recopilarlos en una fuente regular.
- Mantenimiento diario: procesamiento o eliminación de registros, realización de copias de seguridad y otras tareas de programación a intervalos.
- Tareas que se ejecutan de noche. 
- Tareas de aplicaciones web, como eliminación diaria de registros, realización de copias de seguridad y otras tareas de programación a intervalos. Un administrador puede optar por hacer una copia de seguridad de su base de datos a la 1 AM todos los días durante los 9 meses siguientes, por ejemplo.

La API de Programador permite crear, actualizar, eliminar, ver y administrar recopilaciones de trabajos y trabajos programados mediante programación.





<h2><a id="Performance"></a>Rendimiento</h2>

El rendimiento es siempre importante para una aplicación. Las aplicaciones tienden a obtener acceso a los mismos datos una y otra vez. Una manera de mejorar el rendimiento es mantener una copia de esos datos más cerca de la aplicación, y así reducir el tiempo necesario para recuperarlos. Azure proporciona servicios diferentes para este fin:


###Almacenamiento en caché de Azure###
![Almacenamiento en caché de Azure](./media/intro-to-azure/AzureCacheIntroNew.png) **Ilustración: Una aplicación de Azure puede almacenar datos de caché en la memoria e incluso dividirlos entre muchos roles de trabajo**

Tener acceso a los datos almacenados en alguno de los servicios de administración de datos de Azure (Base de datos SQL, tablas o blobs) es bastante rápido. Pero tener acceso a los datos almacenados en memoria es incluso más rápido. Como consecuencia, mantener en memoria una copia de los datos a los que se tiene acceso con mayor frecuencia puede mejorar el rendimiento de la aplicación. Para tal fin, puede utilizar el almacenamiento en la caché de memoria interna de Azure.


Una aplicación de Servicios en la nube puede almacenar datos en esta caché y, luego, recuperarlos directamente sin necesidad de obtener acceso a almacenamiento persistente. La caché se puede mantener dentro de las VM de su aplicación o la pueden proporcionar las VM dedicadas exclusivamente al almacenamiento en caché. En uno u otro caso, la caché se puede distribuir, y expandirse los datos que contiene entre varias VM de un centro de datos de Azure.

Azure cuenta con varias tecnologías diferentes de caché que han cambiado con el tiempo. Si seguimos el orden en que se presentaron, encontramos la caché compartida, la caché en rol, la caché administrada y la caché en Redis. La caché compartida es una tecnología antigua y se debería tratar de evitar la creación de nuevas implementaciones con ella. La caché administrada presenta las mismas características que la caché en rol, pero como servicio administrado fuera del Portal de administración de Azure. La caché de Redis se encuentra actualmente en su versión de vista previa. La implementación en Redis es la que tiene el número mayor de características y está recomendada para escribir nuevo código en caché.


**Escenarios de Caché de Azure**

Por ejemplo, una aplicación que lee repetidamente un catálogo de productos podría beneficiarse del uso de este tipo de almacenamiento en caché, dado que los datos que necesita estarán disponibles más rápidamente. La tecnología también admite el bloqueo, de forma que se puede utilizar con datos de lectura/escritura y de solo lectura. Y las aplicaciones de ASP.NET pueden usar el servicio para almacenar datos de sesión con un simple cambio de la configuración.

###Servicio CDN###
![Red CDN de Azure](./media/intro-to-azure/CDNIntroNew.png) **Ilustración: Las copias de un blob se pueden almacenar en caché en sitios de todo el mundo.**

Supongamos que necesita almacenar datos de blob a los que van a tener acceso usuarios de todo el mundo. Puede ser un vídeo del último partido de la Copa del Mundo, por ejemplo, o actualizaciones de controladores o un conocido libro electrónico. Almacenar una copia de los datos en varios centros de datos de Azure puede ayudar, pero si hay muchos usuarios, es probable que no sea suficiente. Para conseguir un mejor rendimiento, puede utilizar el servicio CDN de Azure.

Este servicio CDN dispone de sitios en todo el mundo, cada uno de los cuales es capaz de almacenar copias de blobs de Azure. La primera vez que un usuario de cualquier parte del mundo tiene acceso a un blob en particular, la información que contiene se copia desde un centro de datos de Azure hasta el almacenamiento de CDN local de ese lugar geográfico. A partir de entonces, los accesos desde esa parte del mundo usarán la copia del blob almacenada en el CDN (no tendrán que recorrer todo el camino hasta llegar al centro de datos de Azure más cercano). El resultado es un acceso más rápido a los datos a los que usuarios de cualquier parte del mundo tienen acceso con mayor frecuencia.

**Escenarios de red CDN**

Es habitual utilizar CDN con Servicios multimedia para emitir vídeo en todo el mundo. El vídeo normalmente tiene un gran tamaño y requiere mucho ancho de banda. Ya se ha hablado también de Servicios multimedia en esta página.







<h2><a id="BigStuff"></a>Big Data y Big Compute</h2>

###HDInsight (Hadoop)###
![HDInsight](./media/intro-to-azure/HDInsightIntroNew.png) **Ilustración: HDInsight ayuda con el procesamiento masivo de grandes cantidades de datos**

Durante muchos años, el grueso de los análisis de datos se ha llevado a cabo en datos relacionales almacenados en un almacén de datos creado con un DBMS relacional. Este tipo de análisis de negocios es aún importante, y lo seguirá siendo durante mucho tiempo. Pero, ¿y si los datos que desea analizar fueran tan grandes que las bases de datos relacionales no pudieran gestionarlos? Y suponga que los datos no son relacionales. Podrían ser, por ejemplo, registros del servidor o datos de sucesos históricos de sensores o de alguna otra cosa. En este caso, tiene lo que se conoce como un problema de Big Data. Y necesita otro enfoque.

La tecnología dominante en la actualidad para analizar Big Data es Hadoop. Esta tecnología, que es un proyecto de código abierto de Apache, almacena los datos mediante el sistema de archivos distribuidos Hadoop (HDFS, Hadoop Distributed File System), y permite a los desarrolladores crear trabajos de MapReduce para analizar esos datos. HDFS distribuye los datos entre varios servidores y, a continuación, ejecuta fragmentos del trabajo de MapReduce en cada uno, de forma que los Big Data se pueden procesar en paralelo.

HDInsight es el nombre del servicio de Azure basado en Hadoop de Apache. HDInsight permite a HDFS almacenar los datos en el clúster y distribuirlos entre varias VM. También extiende la lógica de un trabajo de MapReduce entre esas VM. Lo mismo que sucede con Hadoop local, los datos se procesan localmente (la lógica y los datos en los que se trabaja están en la misma VM) y en paralelo para obtener mejor rendimiento. HDInsight también puede almacenar los datos en Azure Storage Vault (ASV), que utiliza blobs. ASV le permite ahorrar dinero dado que puede eliminar su clúster de HDInsight cuando no lo utilice y seguir manteniendo los datos en la nube.
 
HDinsight también admite otros componentes del ecosistema Hadoop, como Hive y Pig. Microsoft ha creado también componentes que facilitan el trabajo con los datos producidos por HDInsight usando herramientas BI tradicionales, como el adaptador HiveODBC y el Explorador de datos que funcionan con Excel.

###Informática de alto rendimiento (Big Compute)###

Una de las formas más atractivas de utilizar una plataforma en la nube es ejecutar informática de alto rendimiento (HPC) y otras aplicaciones "Big Compute". Por ejemplo, aplicaciones especializadas de ingeniería creadas para utilizar la interfaz de paso de mensajes (MPI) típica del sector, así como las llamadas aplicaciones embarazosamente paralelas (como los modelos de riesgo financiero).

La esencia de Big Compute es ejecutar código en muchos equipos al mismo tiempo. En Azure, esto significa ejecutar muchas máquinas virtuales a la vez, trabajando todas en paralelo para resolver algún problema. Esto requiere de alguna manera los recursos y la programación de aplicaciones; es decir, distribuir su trabajo entre estas instancias. El HPC Pack gratuito de Microsoft y otras soluciones de clúster de proceso pueden lograr un buen desempeño en Azure, sacando partido de los servicios de infraestructura y proceso de Azure para agregar capacidad a petición a un clúster de computación local o ejecutar aplicaciones de Big Compute por completo en la nube.

Azure ofrece diversos tamaños de instancia de máquina virtual con diferentes configuraciones de núcleos de CPU, memoria, capacidad de disco y otras características para cumplir con los requisitos de diferentes aplicaciones. Las recientes instancias A8 y A9 funcionan bien en muchas cargas de trabajo que consumen numerosos recursos, y aplicaciones MPI paralelas en particular, porque cuentan con CPU de varios núcleos y alta velocidad e importantes cantidades de memoria. En determinadas configuraciones, las instancias se benefician de una red de aplicaciones de baja latencia y alto rendimiento en la nube que incluye la tecnología de acceso directo a memoria remota (RDMA) para una máxima eficiencia de las aplicaciones MPI paralelas.

Asimismo, Azure ofrece a socios y desarrolladores de aplicaciones de Big Compute un completo conjunto de funcionalidades de proceso, servicios, opciones de arquitectura y herramientas de desarrollo. Azure admite flujos de trabajo de Big Compute personalizados que impliquen flujos de trabajo de datos especializados y modelos de programación de tareas y trabajos que puedan escalarse a miles de núcleos de proceso.



<h2><a id="media"></a>Multimedia</h2>

![Servicios multimedia de Azure](./media/intro-to-azure/MediaServicesIntroNew.png) **Ilustración: Servicios multimedia es una plataforma para aplicaciones que proporcionan vídeo y otro contenido multimedia a clientes de todo el mundo.**

Gran parte del tráfico de Internet actual lo constituye el vídeo, y ese porcentaje será incluso mayor en el futuro. Pero proporcionar vídeo en la Web no es tan sencillo. Depende de muchas variables, como el algoritmo de codificación y la resolución de pantalla del monitor del usuario. El vídeo también suele experimentar una demanda a ráfagas, como los picos del sábado por la noche cuando cientos de personas desean ver una película en Internet.

Dada su popularidad, es seguro que se van crear muchas aplicaciones que utilicen vídeo. Pero todas ellas necesitarán resolver algunos de los mismos problemas, y hacer que cada una resuelva esos problemas por su cuenta no tiene sentido. Un enfoque mejor es crear una plataforma que proporcione soluciones comunes para su uso por muchas aplicaciones. Y crear esta plataforma en la nube tienen algunas ventajas obvias. Puede estar ampliamente disponible según un criterio de pago por uso, y también puede gestionar la demanda variable a la que con frecuencia se enfrentan las aplicaciones de vídeo.

Servicios multimedia de Azure soluciona este problema al proporcionar un conjunto de componentes de nube que permiten que la gente pueda crear y ejecutar fácilmente aplicaciones que utilizan vídeo y otro contenido multimedia.

Como muestra la ilustración, Servicios multimedia ofrece un conjunto de componentes para aplicaciones que trabajan con vídeo y otro contenido multimedia. Por ejemplo, incluye un componente de ingestión multimedia para cargar vídeo en Servicios multimedia (donde se almacena en blobs de Azure), un componente de codificación que es compatible con diversos formatos de audio y vídeo, un componente de protección de contenido que permite la administración de los derechos digitales, un componente para insertar anuncios en una secuencia de vídeo, componentes para la transmisión por secuencias y mucho más. Los socios de Microsoft también proporcionan componentes para la plataforma y luego piden a Microsoft que los distribuya y facture en su nombre.

Las aplicaciones que utilizan esta plataforma se pueden ejecutar en Azure o en cualquier otra plataforma. Por ejemplo, una aplicación de escritorio para una productora de vídeo podría permitir que sus usuarios descargaran vídeo de Servicios multimedia para luego procesarlo de varias formas. De manera alternativa, un servicio de administración de contenido basado en la nube que se ejecuta en Azure podría confiar en Servicios multimedia para procesar y distribuir vídeo. Con independencia de dónde se ejecute y para qué sirva, cada aplicación elige los componentes que necesita utilizar, y obtiene acceso a ellos a través de interfaces de RESTful.

Para distribuir lo que produce, una aplicación puede utilizar CDN de Azure, otro CDN, o simplemente enviar bits directamente a los usuarios. Sea cual sea la forma en que llegue allí, el vídeo creado usando Servicios multimedia se puede consumir a través de varios sistemas cliente, como Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash y Silverlight. El objetivo es facilitar la creación de aplicaciones multimedia modernas.

**Referencias**

Para obtener una imagen de cómo funciona Servicios multimedia, descargue el [póster de Servicios multimedia de Azure][Azure Media Services Poster].






<h2><a id="commerce"></a>Comercio</h2>

El surgimiento del software como servicio está transformando el modo en que creamos las aplicaciones. Y también está transformando el modo en que las vendemos. Dado que una aplicación SaaS reside en la nube, tiene sentido que sus clientes potenciales deban buscar soluciones en línea. Y este cambio se aplica tanto a los datos como a las aplicaciones. ¿Por qué no buscar en la nube conjuntos de datos comercialmente disponibles? Microsoft aborda ambas cuestiones con [Azure Marketplace](http://datamarket.azure.com/) y [Tienda de Azure](../articles/overview.md).

![Comercio de Azure](./media/intro-to-azure/CommerceIntroNew.png) **Ilustración: Azure Marketplace y la Tienda de Azure le permiten buscar y comprar aplicaciones y conjuntos de datos comerciales de Azure y usarlos como parte de las aplicaciones de Azure.**

La diferencia entre los dos es que Marketplace se encuentra fuera del Portal de administración de Azure, mientras que a la Tienda se puede tener acceso desde el portal. Los potenciales clientes pueden buscar para encontrar aplicaciones de Azure que satisfagan sus necesidades. Los clientes también pueden buscar en cualquiera de ellos conjuntos de datos comerciales, como datos demográficos, datos financieros, datos geográficos y muchos más. Y, si encuentran algo que les guste, pueden tener acceso a ello desde el proveedor, directamente a través de las ubicaciones web de Marketplace o de la Tienda o, en algunos casos, desde el Portal de administración. Las aplicaciones pueden utilizar también la API de Búsqueda de Bing a través de Marketplace, que les proporciona acceso a los resultados de las búsquedas web.


**Escenarios de Comercio**

SendGrid es una aplicación de la Tienda Azure que le permite enviar correo electrónico. Ofrece funcionalidad adicional como estadísticas y entrega confiable. Puede comprar esta aplicación y los servicios relacionados en vez de tratar de crear usted mismo una infraestructura de este tipo.


<h2><a id="start"></a>Introducción</h2>

Ahora que ha captado la idea general, el siguiente paso es programar su primera aplicación de Azure. Elija su lenguaje, [obtenga el SDK adecuado](/downloads/) y ¡a por ello!. La informática en nube es el nuevo estándar, comience ya.



[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/

<!---HONumber=July15_HO1-->