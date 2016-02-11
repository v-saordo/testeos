<properties
	pageTitle="Comparación entre el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales"
	description="Aprenda cuándo usar el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales para hospedar aplicaciones web."
	services="app-service\web, virtual-machines, cloud-services"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/19/2015"
	ms.author="tdykstra"/>

# Comparación entre el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales

## Información general

Azure ofrece varias formas de hospedar sitios web: [Servicio de aplicaciones de Azure][], [Servicios en la nube][] y [Máquinas virtuales][]. Este artículo le ayuda a comprender las opciones y a tomar la decisión correcta para su aplicación web.

El Servicio de aplicaciones de Azure es la opción más adecuada para la mayoría de aplicaciones web. La implementación y la administración están integradas en la plataforma, los sitios pueden escalarse rápidamente para asumir altas cargas de tráfico y el equilibrio de carga y el administrador de tráfico incluidos ofrecen una gran disponibilidad. Puede mover los sitios actuales al Servicio de aplicaciones de Azure fácilmente con una [herramienta de migración en línea](https://www.migratetoazure.net/), utilizar una aplicación de código abierto de la galería de aplicaciones web o crear un sitio nuevo usando el marco y las herramientas que prefiera. La característica [Trabajos web][] facilita la tarea de agregar procesamiento de trabajo en segundo plano a su aplicación web del Servicio de aplicaciones.

Si necesita más control sobre el entorno del servidor web, como la posibilidad de tener acceso remoto al servidor o configurar las tareas de inicio del servidor, Servicios en la nube de Azure es por lo general la mejor opción.

Si tiene actualmente una aplicación que requiere cambios sustanciales para ejecutarse en el Servicio de aplicaciones de Azure o en Servicios en la nube de Azure, puede elegir Máquinas virtuales de Azure a fin de simplificar la migración a la nube. Sin embargo, se requiere más tiempo para configurar, proteger y mantener adecuadamente las Máquinas virtuales, además de mayores conocimientos de TI, en comparación con el Servicio de aplicaciones de Azure y Servicios en la nube. Si está considerando la opción de Máquinas virtuales Azure, tenga en cuenta el esfuerzo constante de mantenimiento requerido para aplicar revisiones al entorno de Máquina virtual, así como para actualizarlo y administrarlo.

El diagrama siguiente ilustra el control relativo de control frente a la facilidad de uso para cada una de estas opciones de hospedaje web en Azure.

![ChoicesDiagram][ChoicesDiagram]

##<a name="scenarios"></a>Escenarios y recomendaciones

Aquí se presentan algunas situaciones habituales de aplicaciones con recomendaciones acerca de la opción de hospedaje web de Azure que podría ser más apropiada en cada caso.

- [Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con recursos locales.](#onprem)
- [Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.](#corp)
- [Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.](#iis6)
- [Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.](#smallbusiness)
- [Soy diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes.](#designer)
- [Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.](#multitier)
- [Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.](#custom)
- [Mi sitio usa software de código abierto y deseo hospedarlo en Azure.](#oss)
- [Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa.](#lob)
- [Deseo hospedar una API de REST o un servicio web para los clientes móviles.](#mobile)


### <a id="onprem"></a> Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con recursos locales.

El Servicio de aplicaciones de Azure es una solución excelente para aplicaciones empresariales complejas. Le permite desarrollar aplicaciones que se escalan automáticamente en una plataforma con equilibrio de carga, se protegen con Active Directory y se conectan con sus recursos locales. Esta opción consigue que la administración de estas aplicaciones resulte sencilla gracias a un portal y unas API de categoría superior, y le permite obtener información acerca del uso que los clientes están haciendo de ellas con herramientas específicamente diseñadas. La característica [Trabajos web][] le permite ejecutar tareas y procesos en segundo plano en el marco de su nivel web, mientras que la conectividad híbrida y las [características de VNET](../fundamentals-introduction-to-azure.md#networking/)T facilitan la reconexión con los recursos locales. El Servicio de aplicaciones de Azure proporciona SLA con un tiempo activo garantizado del 99,9% para las aplicaciones web y le permite:

* Ejecutar sus aplicaciones de manera confiable en una plataforma en la nube que se mantiene por sí misma y aplica revisiones automáticamente.
* Escalar automáticamente entre una red global de centros de datos.
* Realizar copias de seguridad y restaurar para la recuperación ante desastres.
* Cumplir con ISO, SOC2 y PCI.
* Integrarse con Active Directory

### <a id="corp"></a> Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.

El Servicio de aplicaciones de Azure es una excelente solución para hospedar sitios web corporativos. Permite a las aplicaciones web escalarse de manera rápida y sencilla para atender con facilidad la demanda en una red global de centros de datos. Ofrece alcance local, tolerancia a errores y administración del tráfico inteligente. Todo en una plataforma que proporciona herramientas de administración de clase superior, con lo que podrá obtener información sobre el estado y el tráfico del sitio rápidamente y con toda facilidad. El Servicio de aplicaciones de Azure proporciona SLA con un tiempo activo garantizado del 99,9% para las aplicaciones web y le permite:

* Ejecutar sus sitios web de manera confiable en una plataforma en la nube que se mantiene por sí misma y aplica revisiones automáticamente.
* Escalar automáticamente entre una red global de centros de datos.
* Realizar copias de seguridad y restaurar para la recuperación ante desastres.
* Administrar registros y tráfico con herramientas integradas.
* Cumplir con ISO, SOC2 y PCI.
* Integrarse con Active Directory

### <a id="iis6"></a> Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.

El Servicio de aplicaciones de Azure permite evitar fácilmente los costes de infraestructura asociados a la migración de aplicaciones IIS6 antiguas. Microsoft ha creado [herramientas de migración fáciles de utilizar y una detallada guía sobre migración](https://www.movemetowebsites.net/) que le permiten comprobar la compatibilidad e identificar aquellos cambios que deban realizarse. La integración con Visual Studio, TFS y las herramientas CMS más habituales facilitan la implementación de aplicaciones IIS6 directamente en la nube. Una vez implementadas, el Portal de Azure proporciona potentes herramientas de administración que permiten reducir verticalmente para administrar costes y aumentar verticalmente para atender la demanda cuando sea necesario. Con la herramienta de migración puede hacer lo siguiente:

* Migrar con rapidez y sencillez su aplicación web de Windows Server 2003 heredada a la nube.
* Optar por dejar la base de datos SQL adjunta en el entorno local para crear una aplicación híbrida.
* Mover automáticamente su base de datos SQL junto con la aplicación heredada.

### <a id="smallbusiness"></a>Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.

El Servicio de aplicaciones de Azure es una solución excelente para este escenario, porque puede empezar a usarlo gratis y luego agregar más capacidades cuando las necesite. Cada aplicación web gratuita incluye un dominio proporcionado por Azure (*su\_compañía*.azurewebsites.net), y la plataforma incluye tanto herramientas de administración e implementación integradas como una galería de aplicaciones que permiten empezar a usarla sin complicaciones. Hay muchos otros servicios y opciones de escalado que permiten que el sitio evolucione con cuando aumente la demanda de los usuarios. Con el Servicio de aplicaciones de Azure, puede:

- Comenzar con el nivel gratis y luego escalar, según sea necesario.
- Usar la Galería de aplicaciones para configurar rápidamente aplicaciones web conocidas, como WordPress.
- Agregar servicios y características de Azure adicionales a su aplicación, según sea necesario.
- Proteger su aplicación web con HTTPS.

### <a id="designer"></a> Soy un diseñador gráfico o web y deseo diseñar y compilar sitios web para mis clientes.

Para diseñadores y desarrolladores web, el Servicio de aplicaciones de Azure se integra fácilmente con diversos marcos y herramientas, admite la implementación de Git y FTP y ofrece una integración estrecha con herramientas y servicios como Visual Studio y Base de datos SQL. Con el Servicio de aplicaciones, puede:

- Usar herramientas de línea de comandos para [tareas automatizadas][scripting].
- Trabajar con lenguajes populares como [.Net][dotnet], [PHP][], [Node.js][nodejs] y [Python][].
- Seleccionar tres niveles de escala diferentes para escalar hasta capacidades muy altas.
- Integrarse con otros servicios de Azure, como [Base de datos SQL][sqldatabase], [Bus de servicio][servicebus] y [Almacenamiento][] u ofertas de asociados de la [Tienda de Azure][azurestore], como MySQL y MongoDB.
- Integrarse con herramientas como Visual Studio, Git, WebMatrix, WebDeploy, TFS y FTP.

### <a id="multitier"></a>Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.

Si está ejecutando una aplicación de niveles múltiples, como por ejemplo un servidor web que se conecta con una base de datos, el Servicio de aplicaciones de Azure es una opción excelente que ofrece una integración estrecha con Base de datos SQL. Y puede utilizar la característica Trabajos web para ejecutar procesos de back-end.

Elija Servicios en la nube para uno o varios de sus niveles si necesita más control sobre el entorno del servidor, como la posibilidad de tener acceso remoto al servidor o configurar las tareas de inicio del servidor.

Elija Máquinas virtuales para uno o varios de sus niveles si desea utilizar su propia imagen de máquina o ejecutar servicios o software de servidor que no puede configurar en Servicios en la nube.

### <a id="custom"></a>Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.

Si su aplicación requiere una instalación o configuración compleja del software y el sistema operativo, Máquinas virtuales es probablemente la mejor solución. Con Máquinas virtuales, puede hacer lo siguiente:

- Usar la galería de Máquina virtual para que se inicie con un sistema operativo, como Windows o Linux y, a continuación, personalizarla según los requisitos de su aplicación.
- Crear y cargar una imagen personalizada de un servidor en un entorno local existente para que ejecute en una máquina virtual en Azure.

### <a id="oss"></a>Mi sitio usa software de código abierto y deseo hospedarlo en Azure.

Si el marco de código abierto se admite en el Servicio de aplicaciones, los lenguajes y los marcos requeridos por su aplicación se configuran automáticamente sin necesidad de que realice ninguna acción. El Servicio de aplicaciones le permite:

- Usar muchos lenguajes de código abierto populares, como [.NET][dotnet], [PHP][], [Node.js][nodejs] y [Python][].
- Configurar WordPress, Drupal, Umbraco, DNN y muchas otras aplicaciones web de terceros.
- Migrar una aplicación existente o crear una nueva a partir de la galería de aplicaciones.

Si no se admite el marco de código abierto en el Servicio de aplicaciones, puede ejecutar la aplicación en cualquiera de las dos otras opciones de hospedaje web de Azure. Con Servicios en la nube, se utilizan tareas de inicio para instalar y configurar cualquier software de código abierto requerido que se ejecute Windows. Con Máquinas virtuales, puede instalar y configurar el software en una imagen de la máquina, que puede basarse en Windows o Linux.

### <a id="lob"></a>Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa.

Si desea crear una aplicación de línea de negocio, puede que su sitio web requiera un acceso directo a los servicios o datos de la red corporativa. Es posible hacerlo en el Servicio de aplicaciones, Servicios en la nube y Máquinas virtuales mediante el servicio [Red virtual de Azure](/services/virtual-network/). En el Servicio de aplicaciones puede usar la nueva [característica de integración de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite la ejecución de las aplicaciones de Azure como si se encontraran en su red corporativa.

### <a id="mobile"></a>Deseo hospedar una API de REST o un servicio web para los clientes móviles.

Los servicios web HTTP le permiten admitir una amplia variedad de clientes, incluidos los clientes móviles. Los marcos como ASP.NET Web API se integran con Visual Studio para facilitar la creación y el consumo de los servicios REST. Estos servicios se exponen desde un extremo web, por lo que es posible usar cualquier técnica de hospedaje web en Azure para admitir este escenario. Sin embargo, el Servicio de aplicaciones es una elección excelente para hospedar las API de REST. Con el Servicio de aplicaciones, puede:

- Crear rápidamente una aplicación web para hospedar el servicio web HTTP en uno de los centros de datos globalmente distribuidos de Azure.
- Migrar los servicios existentes o crear otros nuevos.
- Conseguir contratos de nivel de servicio para disponibilidad con una sola instancia, o escalar horizontalmente a varias máquinas dedicadas.
- Usar el sitio publicado para proporcionar API de REST a cualquier cliente HTTP, incluidos los clientes móviles.

Además, el Servicio de aplicaciones de Azure tiene una nueva característica de vista previa de las API de REST: aplicaciones de API. Para obtener más información sobre las aplicaciones de API, consulte [Qué son las aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md).

##<a name="features"></a>Comparación de características

La siguiente tabla compara las capacidades del Servicio de aplicaciones, Servicios en la nube y Máquinas virtuales para ayudarle a tomar la mejor decisión. Para obtener más información acerca de los contratos de nivel de servicio para cada opción, consulte [Contratos de nivel de servicio de Azure](/support/legal/sla/).

Característica|Servicio de aplicaciones (aplicaciones web)|Servicios en la nube (roles web)|Máquinas virtuales|Notas
---|---|---|---|---
Implementación casi instantánea|X|||La implementación de una aplicación o la actualización de una aplicación a un Servicio en la nube, o la creación de una máquina virtual, toma varios minutos; la implementación de una aplicación a una aplicación web tarda segundos.
Escalado horizontal a máquinas más grandes sin volver a implementar|X|||
Las instancias de un servidor web comparten contenido y configuración; esto significa que no es necesario volver a implementar o configurar a medida que escale.|X|||
Varios entornos de implementación (producción y ensayo)|X|X||
Administración de actualización automática del SO|X|X||
Intercambio fluido entre plataformas (mover fácilmente entre 32 bits y 64 bits)|X|X||
Código de implementación con GIT, FTP|X||X|
Código de implementación con Web Deploy|X||X|Servicios en la nube admite el uso de Web Deploy para implementar actualizaciones en instancias de rol individuales. Sin embargo, no puede utilizarlo para la implementación inicial de un rol, y si utiliza Web Deploy para una actualización, tiene que realizar la implementación por separado para cada instancia de un rol. Se requieren múltiples instancias para optar al contrato de nivel de servicio de Servicio en la nube para entornos de producción.
Soporte para WebMatrix|X||X|
Acceso a servicios como Bus de servicio, Almacenamiento, Base de datos SQL|X|X|X|
Web de host o nivel de servicios web de una arquitectura multinivel|X|X|X|
Nivel medio del host de una arquitectura multinivel|X|X|X|Las aplicaciones web del Servicio de aplicaciones pueden hospedar con facilidad un nivel medio de la API de REST y la característica [Trabajos web](http://go.microsoft.com/fwlink/?linkid=390226) puede hospedar trabajos de procesamiento en segundo plano. Puede ejecutar Trabajos web en un sitio web dedicado para alcanzar una escalabilidad independiente para el nivel. La característica de [aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md) de vista previa proporciona incluso más características para hospedar servicios REST.
Soporte integrado de MySQL como servicio|X|X|X|Servicios en la nube puede integrar MySQL como servicio mediante las ofertas de ClearDB, pero no como parte del flujo de trabajo del Portal de Azure.
Soporte para ASP.NET, ASP clásico, Node.js, PHP, Python|X|X|X|
Escalado horizontal a varias instancias sin volver a implementar|X|X|X|Máquinas virtuales puede escalar horizontalmente hasta varias instancias, pero los servicios que se ejecutan en este servicio se deben escribir para controlar este escalado horizontal. Tiene que configurar un equilibrador de carga para que dirija solicitudes entre las máquinas y crear un Grupo de afinidad para evitar que todas las instancias se reinicien simultáneamente debido a errores de mantenimiento o hardware.
Soporte para SSL|X|X|X|En el caso de las aplicaciones web del Servicio de aplicaciones, solo se admite SSL para nombres de dominio personalizados para el modo Básico y Estándar. Para obtener más información sobre el uso de SSL con aplicaciones web, consulte [Configuración de un certificado SSL para un Sitio web Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Integración de Visual Studio|X|X|X|
Depuración remota|X|X|X|
Código de implementación con TFS|X|X|X|
Aislamiento de red con [Red virtual de Azure](/services/virtual-network/)|X|X|X|Consulte también [Integración de redes virtuales de Sitios web Azure](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Soporte técnico para el [Administrador de tráfico de Azure](/services/traffic-manager/)|X|X|X|
Supervisión de extremo integrado|X|X|X|
Acceso de escritorio remoto a los servidores||X|X|
Instalación de cualquier MSI personalizado||X|X|
Capacidad de definir/ejecutar tareas de inicio||X|X|
Puede atender eventos de ETW||X|X|


> [AZURE.NOTE]
Si desea empezar a usar el Servicio de aplicaciones de Azure antes de registrarse para crear una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita una aplicación básica de ASP.NET de corta duración en el Servicio de aplicaciones de Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


## <a id="nextsteps"></a> Pasos siguientes

Para obtener más información acerca de las tres opciones de hospedaje web, consulte los recursos siguientes:

* [Introducción a Azure](../fundamentals-introduction-to-azure.md)
* [Cálculo de las opciones de hospedaje proporcionadas por Azure](../fundamentals-application-models.md)

Para conocer con mayor profundidad las opciones que ha elegido para su aplicación, consulte los recursos siguientes:

* [Servicio de aplicaciones de Azure](/documentation/services/app-service/)
* [Servicios en la nube de Azure](/documentation/services/cloud-services/)
* [Máquinas virtuales de Azure](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Servicio de aplicaciones de Azure]: /services/app-service/
  [Servicios en la nube]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Máquinas virtuales]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [Trabajos web]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Almacenamiento]: http://www.windowsazure.com/documentation/services/storage/
 

<!---HONumber=AcomDC_0128_2016-->