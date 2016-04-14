<properties 
	pageTitle="Ofertas de Aplicaciones web del Servicio de aplicaciones de Azure para empresas" 
	description="Muestra cómo usar Aplicaciones web del Servicio de aplicaciones de Azure para crear soluciones de sitio web de empresa para su negocio" 
	services="app-service\web" 
	documentationCenter="" 
	authors="apwestgarth" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2016" 
	ms.author="anwestg"/>

# Notas del producto sobre ofertas de Aplicaciones web del Servicio de aplicaciones de Azure para empresas #

La necesidad de reducir costos y ofrecer soluciones de TI más rápidas en un entorno en constante evolución supone nuevos desafíos para desarrolladores, profesionales de TI y administradores. Los usuarios buscan con más insistencia que sus aplicaciones web de línea de negocio (LOB) sean rápidas, tengan capacidad de respuesta y estén disponibles desde cualquier dispositivo. Al mismo tiempo, las empresas intentan aprovechar la mayor productividad y eficacia que proporciona la integración con servicios en la nube y móviles, lo que puede ser algo tan sencillo como el inicio de sesión único en todos los dispositivos utilizando Active Directory para colaboración en Office365 usando datos extraídos de una aplicación LOB interna que, a su vez, extrae datos de la implementación del personal de ventas de la empresa. [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) es un servicio en la nube de clase empresarial para desarrollar, probar y ejecutar aplicaciones móviles y aplicaciones web, API web y sitios web genéricos. Se puede utilizar para ejecutar sitios web corporativos, sitios de intranet, aplicaciones empresariales y campañas de marketing digital en una red global de centros de datos optimizados para escalabilidad y disponibilidad, y con compatibilidad para la integración continua y modernas prácticas de DevOps.

Estas notas del producto resalta las funcionalidades del servicio de [Aplicaciones web](/services/app-service/web/) y se centra específicamente en la ejecución de aplicaciones web LOB, describiendo la migración de aplicaciones web existentes y la implementación de nuevas aplicaciones web LOB en la plataforma.

## Público ##

Administradores, arquitectos y profesionales de TI que desean migrar a la nube cargas de trabajo web que ahora se ejecutan localmente. Las cargas de trabajo web pueden abarcar tanto aplicaciones web de tipo empresa a empleado como de tipo empresa a asociados.

## Introducción ##

Aplicaciones web del Servicio de aplicaciones es una plataforma ideal en la que hospedar servicios y aplicaciones web externos e internos porque ofrece una solución rentable, altamente escalable y administrada que le permite concentrarse en proporcionar valor empresarial para los usuarios en lugar de dedicar una cantidad importante de tiempo y dinero a mantener entornos independientes y dar soporte a los mismos. Aplicaciones web ofrece una plataforma flexible en la que implementar sus aplicaciones web empresariales proporcionando la capacidad de seguir autenticando en Active Directory local mediante la integración con Microsoft Azure Active Directory y la compatibilidad con implementaciones sencillas y rápidas a través de prácticas de integración e implementación continuas internas, permitiendo al mismo tiempo el escalado para crecer con las necesidades de la empresa, y todo ello en una plataforma administrada que le permite centrarse en su aplicación y no en su infraestructura.

## Definición del problema ##

El entorno de TI está cambiando rápidamente, pasando del hospedaje en servidores tradicionales, con sus altos costos de capital en tiempos de espera muy largos, a otro que emplea el uso a petición de servicios que se escalan automáticamente para administrar la carga. Los departamentos de TI se enfrentan al reto de reducir el costo y el espacio de utilización en infraestructura y mantenimiento, centrándose en reducir las inversiones de capital, aumentando al mismo tiempo la agilidad. El final del ciclo de vida de las plataformas con infraestructura más antigua, como por ejemplo Windows Server 2003, está conduciendo a los departamentos de TI a considerar la migración a nube como una manera potencial de evitar nuevos costos de capital a largo plazo. En el pasado, los directores generales financieros tomaban decisiones de compra para otros departamentos; sin embargo, cada vez son más los directores generales de marketing y otros jefes de unidades de negocio que están asumiendo un rol más activo en cómo se gastan sus presupuestos y cuál es el retorno de sus inversiones. Cada vez son más las empresas que necesitan que sus recursos sean más móviles que nunca, con empleados que trabajen de forma remota, dedicando más tiempo a los clientes que necesitan acceso a sistemas sin problemas.

Las necesidades empresariales cambian mensualmente, todas las semanas, a diario; las empresas buscan una escala global instantánea con servicios actualizados con regularidad repletos de características nuevas, ya sea proporcionados por terceros o internamente. En algunos casos las empresas también buscan las capacidades para aislar las aplicaciones y el acceso a recursos al mismo tiempo que utilizan servicios de la nube pública. Los usuarios tienen expectativas mayores, muchos de los cuales usan servicios en sus vidas privadas, como Office365, y esperan tener acceso a servicios similares y actualizados repletos de funciones en su vida profesional. Para hacer frente a esta demanda, TI debe ayudar a las empresas a posibilitar todo esto a través de la selección de servicios de terceros y de la integración con estos, y de la selección cuidadosa de plataformas que puedan adaptarse a las necesidades empresariales, siendo al mismo tiempo confiables con un costo total de propiedad reducido.

Los equipos de desarrollo buscan ofrecer ventajas empresariales inmediatas, proporcionando nuevas características con cierta frecuencia. Buscan una plataforma económica y confiable que se integre con sus herramientas y prácticas existentes (desarrollo, prueba y lanzamiento); y trabajando conjuntamente con los departamentos de TI, se automatiza la implementación, administración y alerta, todo ello con el objetivo de conseguir un tiempo de inactividad nulo.

<a href="highlevel" />
## Solución de alto nivel ##

Los marcos de trabajo y las plataformas web cada vez se usan más para desarrollar, probar y hospedar aplicaciones de línea de negocio. Una aplicación de línea de negocio típica, como un sistema de gastos de empleados internos, suele estar compuesta únicamente de una aplicación web con una base de datos auxiliar para almacenar los datos relacionados con la aplicación.

Aplicaciones web del Servicio de aplicaciones es una buena opción para hospedar tales aplicaciones, ya que ofrece una infraestructura escalable y confiable que se administra y revisa prácticamente sin intervención manual y que tiene un tiempo de inactividad casi nulo. La plataforma Microsoft Azure proporciona muchas opciones de almacenamiento de datos para admitir aplicaciones web hospedadas en Aplicaciones web, desde Base de datos SQL de Microsoft Azure (una base de datos como servicio relacional escalable administrada) a servicios populares de nuestros asociados, como por ejemplo Base de datos MySQL de ClearDB y MongoDB.

Un enfoque alternativo es hacer uso de su inversión existente local. En el escenario de ejemplo, un sistema de gastos de empleados, puede que le interese mantener el almacén de datos dentro de su propia infraestructura interna. Esto podría ser para la integración con sistemas internos (informes, nóminas, facturación, etc.) o para satisfacer un requisito de administración de TI. Aplicaciones web proporciona una serie de métodos que le permiten conectarse a su infraestructura local:

- [Entornos del servicio de aplicaciones](app-service-app-service-environment-intro.md) : los entornos del servicio de aplicaciones (ASE) son una nueva característica premium que se agregó recientemente a la oferta del servicio de aplicaciones de Microsoft Azure. ASE proporcionan un entorno plenamente aislado y dedicado para ejecutar aplicaciones del Servicio de aplicaciones de Azure de forma segura a gran escala, al mismo tiempo que ofrece aislamiento y acceso seguro a redes   
- [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md): Conexiones híbridas es una característica de Servicios de BizTalk de Microsoft Azure que permite a Aplicaciones web conectarse a recursos locales de forma segura, por ejemplo SQL Server, MySQL, API web y servicios web personalizados. 
- [Integración de red virtual](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/): Integración de Aplicaciones web con Red virtual de Azure le permite conectar su aplicación web a una red virtual de Azure que, a su vez, se puede conectar a la infraestructura local través de una VPN de sitio a sitio. 

En los diagramas siguientes se describe un ejemplo de solución de alto nivel con opciones de conectividad para recursos locales. En el primer ejemplo se muestra cómo se puede conseguir esto con características estándar del Servicio de aplicaciones de Azure y, en el segundo, cómo se puede conseguir con la oferta premium de los entornos del Servicio de aplicaciones.

Uso de características del Servicio de aplicaciones estándar: ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Uso de características del Servicio de aplicaciones estándar")

Uso de un entorno del Servicio de aplicaciones: ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Uso de un entorno del Servicio de aplicaciones")

## Ventajas empresariales ##

Aplicaciones web del Servicio de aplicaciones proporciona numerosas ventajas empresariales que permiten que su función sea mucho más económica y ágil a la hora de satisfacer las necesidades empresariales.

### Modelo PaaS ###

Aplicaciones web del Servicio de aplicaciones se basa en un modelo de plataforma como servicio que proporciona una serie de ahorros de costo y eficacia. Ya no es necesario pasar horas administrando máquinas virtuales ni aplicando revisiones a sistemas operativos ni marcos. Aplicaciones web es un entorno automáticamente revisado que le permite centrarse en la administración de las aplicaciones web y no en las máquinas virtuales, dejando a los equipos libres para proporcionar valor empresarial adicional.

El modelo PaaS que sustenta Aplicaciones web permite a los profesionales de la metodología de operaciones de desarrollo cumplir sus objetivos. Como empresa, esto significa la administración e integración completas durante todo el ciclo de vida de la aplicación, incluido el desarrollo, pruebas, lanzamiento, supervisión, administración y soporte técnico.

Para los equipos de desarrollo, se pueden configurar flujos de trabajo de integración e implementación continuos desde Visual Studio Team Services, GitHub, TeamCity, Hudson o BitBucket, habilitando así la compilación, prueba e implementación automáticas que permiten ciclos de lanzamiento más rápidos; asimismo, al mismo tiempo se reduce la problemática que supone el lanzamiento en una infraestructura existente. Aplicaciones web también admite la creación de varios entornos de prueba y ensayo para el flujo de trabajo de lanzamiento. Ya no necesitará reservar o asignar hardware para estas finalidades; puede crear tantos entornos como desee y definir su propia promoción al flujo de trabajo de lanzamiento. Como empresa, podría decidir el lanzamiento a un espacio de prueba desde el control de código fuente, realizar una serie de pruebas y, tras la finalización correcta, promover a un espacio de ensayo; finalmente podría cambiar a producción sin tiempo de inactividad, con la ventaja agregada de que las aplicaciones web hospedadas en Aplicaciones web están previamente cargadas y activas para proporcionar la mejor experiencia posible al cliente. Asimismo, las empresas pueden usar las capacidades de las pruebas en producción de las aplicaciones web del Servicio de aplicaciones para dirigir una sección del tráfico a una ranura diferente, validar los cambios, antes de cambiar todo el tráfico a la nueva implementación o de revertir todo el tráfico a la implementación anterior.

Los equipos de operaciones pueden estar seguros de que se encuentran en la mejor posición posible para reaccionar ante un problema con cualquiera de sus aplicaciones web hospedadas en Aplicaciones web con las características de supervisión y alerta integradas. Los equipos de operaciones ya debieran haber invertido en soluciones de supervisión y análisis como Microsoft Visual Studio Application Insights, New Relic y AppDynamics. También son compatibles con Aplicaciones web, lo que permite la continuidad y un entorno familiar desde donde supervisar sus aplicaciones web.

Por último, Aplicaciones web proporciona funcionalidad para realizar automáticamente copias de seguridad de los sitios y las bases de datos hospedadas directamente en un contenedor de almacenamiento de blobs de Azure. Todo ello le proporciona una forma sencilla y un método muy económico con los que recuperarse ante desastres, lo que reduce la necesidad de hardware y software locales complejos.

### Facilidad de migración ###

La rotación y el mantenimiento de hardware es un factor clave para las empresas a medida que los ciclos de lanzamiento de hardware y sistemas operativos se aceleran. ¿Quizá tenga una serie de servidores Windows Server 2003 R2 que están llegando al final del período de soporte en 2015 pero aún hospedan aplicaciones web clave para su empresa? Aplicaciones web del Servicio de aplicaciones es un excelente candidato en el que hospedar esas aplicaciones y con el que racionalizar el estado de hardware empresarial. Aplicaciones web proporciona acceso a una amplia gama de especificaciones de hardware que se administran y mantienen como parte del servicio, eliminando la necesidad de tener en cuenta los costos de reemplazo y administración como parte de su presupuesto en infraestructura. La migración puede ser tan simple como una operación de copiar y pegar desde la implementación existente a Aplicaciones web o una migración más compleja, donde el uso del Asistente para migración de Aplicaciones web agregará valor. Las aplicaciones web migradas disfrutan de infinidad de servicios de Azure, integrando servicios adicionales en las aplicaciones web. Por ejemplo, se puede considerar la incorporación de Azure Active Directory para controlar el acceso a la aplicación basándose en la asociación de los usuarios a grupos de seguridad. Otro ejemplo puede ser agregar servicios de caché para mejorar el rendimiento y reducir la latencia, proporcionando una mejor experiencia de usuario global.

### Hospedaje de clase empresarial ###

Aplicaciones web del Servicio de aplicaciones proporciona una plataforma estable y confiable que ha demostrado ser capaz de controlar una amplia variedad de necesidades empresariales, desde pequeñas cargas de trabajo de desarrollo y prueba internas hasta sitios web con mucho tráfico altamente escalados. Al usar Aplicaciones web está haciendo uso de la misma plataforma de hospedaje de clase empresarial que Microsoft, como empresa, usa para cargas de trabajo web de alto valor. Aplicaciones web, y todos los servicios de la plataforma de Azure, se crean con seguridad y conforme a las disposiciones legales (como por ejemplo ISO (ISO/IEC 27001:2005), certificaciones SSAE 16 y ISAE 3402 SOC1 y SOC2, BAA HIPAA, PCI y Fedramp) como parte esencial de cada elemento y característica. Para obtener más información, consulte [http://aka.ms/azurecompliance](/support/trust-center/compliance/).

La plataforma de Microsoft Azure permite controles de autorización basados en rol que posibilitan niveles de control empresarial a recursos dentro de Aplicaciones web. RBAC ofrece a las empresas la capacidad de implementar sus propias directivas de administración de acceso para todos sus recursos en el entorno de Azure, asignando usuarios a grupos y, a su vez, asignando los permisos necesarios a esos grupos en el recurso como una aplicación web. Para obtener más información sobre RBAC en Azure, consulte [http://aka.ms/azurerbac](../role-based-access-control-configure/). Mediante el uso de Aplicaciones web, puede estar seguro de que las aplicaciones web se implementan en un entorno seguro y de que tiene control total sobre en qué territorio se implementan sus recursos.

Los Entornos del Servicio de aplicaciones de Azure [http://aka.ms/aseintro](http://aka.ms/aseintro) son una nueva opción del plan de servicio premium para los clientes de empresa que deseen usar el Servicio de aplicaciones de Azure y ofrecen un entorno totalmente aislado y dedicado. Esto permite a los clientes de empresa implementar aplicaciones que pueden sacar provecho de muy alta escala, al mismo tiempo que les permite ejercer el pleno control del tráfico de red de entrada y salida; además, los Entornos del servicio de aplicaciones permiten a las aplicaciones establecer conexiones seguras de alta velocidad a recursos locales a través de redes virtuales.

Las Aplicaciones web del Servicio de aplicaciones también pueden usar íntegramente sus inversiones locales ofreciendo la posibilidad de conectarse a los recursos internos, como el almacenamiento de datos o el entorno de SharePoint. Como se analizó en la sección [Solución de alto nivel](#highlevel), puede hacer uso de conexiones híbridas y conectividad de redes virtuales para establecer conexiones con servicios e infraestructuras locales.

### Escala global ###

Aplicaciones web del Servicio de aplicaciones es una plataforma global y escalable que permite que sus aplicaciones web crezcan y se adapten a las necesidades de una empresa en rápida expansión y con una planeación y un coste a largo plazo mínimos. En escenarios de infraestructura local típicos, la expansión y aumento a petición tanto local como geográficamente requerirían una gran cantidad de administración, planeación y gastos para aprovisionar y administrar infraestructuras adicionales. Aplicaciones web ofrece la capacidad de escalar las aplicaciones web con el flujo y reflujo de los requisitos. Por ejemplo, usando la aplicación de gastos como ejemplo, durante la mayoría del mes los usuarios hacen poco uso de la aplicación, pero a medida que se acerca la fecha límite de cada mes para introducir el envío de gastos y el uso aumenta en la aplicación, Aplicaciones web tiene la capacidad de aprovisionar automáticamente más infraestructura para su aplicación y, después, una vez que el uso ha vuelto a decrecer, puede recuperar la escala de la infraestructura de línea base definida.

Aplicaciones web está disponible globalmente en 24 centros de datos en todo el mundo y sigue creciendo. Para obtener la lista más actualizada de regiones y ubicaciones, consulte [http://aka.ms/azlocations](http://aka.ms/azlocations). Con Aplicaciones web, su empresa puede conseguir fácilmente un alcance y escala globales. A medida que su empresa se expande por nuevas regiones, los paneles de la aplicación de informes que utiliza y hospeda en Aplicaciones web se pueden implementar fácilmente en centros de datos adicionales y dar servicio a los usuarios locales mucho más rápido mediante la combinación de Aplicaciones web y el Administrador de tráfico de Azure, todo ello con la ventaja adicional de la infraestructura escalable subyacente que es capaz de contraerse y expandirse conforme a las necesidades de cambio de las oficinas regionales.
 
## Detalles de la solución ##

Veamos un ejemplo de un escenario de migración de aplicación. Aquí se describen los detalles de cómo las características de Aplicaciones web del Servicio de aplicaciones se unen para proporcionar una solución y un valor empresarial excelentes.
 
A lo largo de este ejemplo la aplicación de línea de negocio que analizaremos es una aplicación de informe de gastos que permite a los empleados enviar sus gastos para reembolsarlos. La aplicación se hospeda en un servidor Windows Server 2003 R2 con IIS6 y la base de datos es una base de datos SQL Server 2005. La razón por la que elegimos un servidor más antiguo reside en la proximidad del fin de servicio de Windows Server 2003 R2 y SQL Server 2005 y contamos con [herramientas](http://aka.ms/websitesmigration) e [instrucciones](http://aka.ms/websitesmigrationresources) para migrar automáticamente las cargas de trabajo a Azure. Con todo eso en mente, el modelo utilizado en este ejemplo se aplica a una amplia gama de escenarios de migración.

### Migrar aplicaciones existentes ###

El primer paso de toda la solución para mover una aplicación de línea de negocio a Aplicaciones web es identificar la arquitectura y los recursos de la aplicación existente. El ejemplo de este documento es una aplicación web ASP.NET hospedada en un único servidor IIS con la base de datos hospedada en un servidor SQL Server independiente, tal y como se muestra en la siguiente figura. Los empleados inician sesión en el sistema mediante una combinación de nombre de usuario y contraseña, escriben detalles de gastos y cargan copias digitalizadas de recibos en la base de datos para cada elemento de gastos.
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Elementos que se deben tener en cuenta ####

Cuando se migra una aplicación desde un entorno local, debe tener en mente algunas restricciones de Aplicaciones web. A continuación presentamos algunos temas clave que se deben tener en cuenta al migrar aplicaciones web a Aplicaciones web ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-	Enlaces de puerto: Aplicaciones web solo admite el puerto 80 para el tráfico HTTP y el puerto 443 para el tráfico HTTPS. Si su aplicación usa cualquier otro puerto, entonces una vez migrada la aplicación asegúrese de usar el puerto 80 para HTTP y el puerto 443 para el tráfico HTTPS. Este suele ser un problema sin consecuencias, ya que, en implementaciones locales, es habitual usar distintos puertos para evitar el uso de nombres de dominio, especialmente en entornos de desarrollo y pruebas.
-	Autenticación: Aplicaciones web admite la autenticación anónima de forma predeterminada y la autenticación mediante formularios según se identifica por una aplicación. Aplicaciones web puede ofrecer autenticación de Windows cuando la aplicación se integra con Azure Active Directory y ADFS solamente. Se trata de una característica que se describe con más detalle [aquí](http://aka.ms/azurebizapp) 
-	Ensamblados basados en caché global de ensamblados: Aplicaciones web no permite la implementación de ensamblados en la memoria caché global de ensamblados, por lo que si la aplicación o que se está migrando usa esta característica de forma local, considere la posibilidad de mover los ensamblados a la carpeta de la papelera de dicha aplicación.
-	Modo de compatibilidad con IIS5: Aplicaciones web no admite el modo de compatibilidad con IIS5 y, como tal, cada instancia de Aplicaciones web y todas las aplicaciones que se encuentran bajo la instancia primaria de Aplicaciones web se ejecutan en el mismo proceso de trabajo dentro de un único grupo de aplicaciones.
-	Uso de bibliotecas COM: Aplicaciones web no permite el registro de componentes COM en la plataforma, por lo tanto, si la aplicación usa cualquiera de los componentes COM, estos necesitarían reescribirse en código administrado e implementarse con la aplicación.
-	Filtros ISAPI: los filtros ISAPI se pueden admitir en Aplicaciones web y necesitarán implementarse como parte de la aplicación y registrarse en el archivo web.config de dicha aplicación web. Para obtener más información, consulte [http://aka.ms/azurewebsitesxdt](../web-sites-transform-extend/). 

Una vez que estos temas se han tenido en cuenta, la aplicación web deberá estar lista para la nube. Y no se preocupe si algunos temas no se cumplen totalmente; la herramienta de migración hará todo lo posible para la migración.

Los pasos siguientes en el proceso de migración consisten en crear una aplicación web del Servicio de aplicaciones y una Base de datos SQL de Azure. Hay varios tamaños de instancias de Aplicaciones web con un número variable de núcleos de CPU y cantidades de RAM disponibles para seleccionar según los requisitos de sus aplicaciones web. Para obtener más información y precios, consulte [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). Del mismo modo, Base de datos SQL de Microsoft Azure se encarga de todas las necesidades de una empresa con varios niveles de servicio y niveles de rendimiento para satisfacer los requisitos. Puede encontrar más información en [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/). Después de crearse, la aplicación se carga en Aplicaciones web del Servicio de aplicaciones, ya sea a través de FTP o WebDeploy y, a continuación, se mueve a la base de datos.

En esta migración la solución utiliza Base de datos SQL de Azure, pero no es la única base de datos que se admite en Azure. Las empresas también usan MySQL, MongoDB, Microsoft Azure DocumentDB, entre otros, a través de complementos que se pueden comprar en la [Tienda de Azure](/marketplace/partner-program/).

Al crear una base de datos SQL de Azure, hay una serie de opciones disponible para importar una base de datos existente desde un servidor local generando un script de una base de datos existente para usar la [exportación e importación de aplicaciones de capa de datos](http://aka.ms/dacpac).

La base de datos de aplicación de gastos se originó creando una base de datos SQL de Azure nueva, conectándose a la base de datos mediante SQL Server Management Studio y, a continuación, ejecutando un script para crear el esquema de base de datos y rellenarlo con datos de la base de datos local.

El paso final en esta primera fase de la migración requiere la actualización de las cadenas de conexión a la base de datos para la aplicación. Esto se consigue a través del Portal de Azure. Para cada aplicación web puede modificar la configuración específica de la aplicación, incluidas las cadenas de conexión que utiliza la aplicación para conectarse a cualquier base de datos que se va a utilizar.

### Alternativas al uso de Base de datos SQL de Azure ###

La plataforma de Azure ofrece varias alternativas al uso de Base datos SQL de Azure, como una base de datos primaria de aplicaciones web que permite habilitar diferentes cargas de trabajo, es decir, el uso de una solución no SQL, o habilitar la plataforma para satisfacer las necesidades de datos de una empresa. Por ejemplo, una empresa puede guardar datos que no deben almacenarse fuera del sitio o en un entorno en la nube pública y, por tanto, se preocuparía de mantener el uso su base de datos local.

#### Conectividad con recursos locales ####
Aplicaciones web del Servicio de aplicaciones ofrece varias opciones de conexión a los recursos locales, como por ejemplo bases de datos, que permiten la reutilización de la infraestructura existente de alto valor. Las opciones se enumeran a continuación:

- Los entornos de Servicio de aplicaciones están aislados y se crean dentro de una subred de una red virtual, permitiendo así que el entorno se comunique con puntos de conexión privados ubicados dentro de la misma red virtual - [http://aka.ms/appserviceasenetworking](http://aka.ms/appserviceasenetworking)
- Integración de red virtual de Aplicaciones web admite la integración entre Aplicaciones web y una red virtual de Azure, lo que permite el acceso a los recursos que se ejecutan en la red virtual que, si se conecta a la red local con VPN de tipo sitio a sitio, permite la conectividad directa a los sistemas locales.
- Conexiones híbridas es una característica de Servicios de BizTalk de Azure que proporciona una manera fácil de conectarse a recursos locales individuales como SQL Server, MySQL, API web HTTP y la mayoría de servicios web.

#### Escala y resistencia ####

A medida que una empresa aumenta sus recursos a través de adquisiciones o por crecimiento orgánico natural, las aplicaciones web también se deben escalar para satisfacer nuevas demandas. De hecho, hoy en día es habitual ver una diseminación aún mayor de empleados remotos y equipos co-ubicados, por ejemplo compañías con oficinas en Estados Unidos, Europa y Asia, con un equipo de ventas móvil en muchos más territorios. Aplicaciones web tiene la capacidad de controlar los cambios elásticos de escala de forma cómoda y automática.

Aplicaciones web del Servicio de aplicaciones permite configurar las aplicaciones web para que se escalen automáticamente mediante el Portal de Azure, según dos vectores: tiempos programados o por uso de CPU. El escalado automático de Aplicaciones web proporciona una manera económica y muy flexible de resolver los cambios más grandes en uso para todas las aplicaciones empresariales, desde aplicaciones web (como nuestro sistema de informes de gastos) hasta sitios web de marketing que experimentan una subida alta de tráfico durante un tiempo breve de promoción. Para obtener más información e instrucciones sobre cómo escalar sus aplicaciones web mediante Aplicaciones web, consulte [Escalación de sitios web](web-sites-scale.md).

Además de la flexibilidad de escalado de Aplicaciones web, la plataforma global permite la resistencia y continuidad empresarial a través de la distribución posible de aplicaciones web y sus recursos en varios centros de datos y regiones geográficas.

## Resumen ##
Aplicaciones web del Servicio de aplicaciones ofrece una solución flexible, económica y con capacidad de respuesta a las necesidades dinámicas de una empresa en un entorno que evoluciona rápidamente. Aplicaciones web ayuda a las empresas a aumentar la productividad y eficacia mediante una plataforma administrada con capacidades de operaciones de desarrollo y administración práctica reducida, proporcionando al mismo tiempo capacidades empresariales de escala, resistencia, seguridad e integración con activos locales.

## Pasar a la acción ##
Para obtener más información sobre el servicio Aplicaciones web del Servicio de aplicaciones de Azure, visite [http://aka.ms/enterprisewebsites](/services/websites/enterprise/), donde puede encontrar más información, y regístrese para obtener una versión de prueba hoy mismo en [http://aka.ms/azuretrial](/pricing/free-trial/) para evaluar el servicio y descubrir las ventajas para su empresa.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=AcomDC_0224_2016-->