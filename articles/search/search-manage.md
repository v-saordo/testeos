<properties 
	pageTitle="Administración del servicio de búsqueda en Microsoft Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Administración de Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="heidist"/>

# Administración del servicio de búsqueda en Microsoft Azure
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API DE REST](search-get-started-management-api.md)

Búsqueda de Azure es un servicio basado en la nube con una API basada en HTTP que puede usarse en aplicaciones de búsqueda personalizadas. Nuestro servicio de búsqueda proporciona el motor para análisis de texto de búsqueda de texto completo, características de búsqueda avanzadas, almacenamiento de datos de búsqueda y una sintaxis de comando de consulta.

En este artículo se explica cómo administrar un servicio de búsqueda en el [Portal de Azure](https://portal.azure.com). También puede usar la nueva función de análisis de tráfico de búsqueda para obtener información sobre la actividad en el nivel de índice. Visite [Análisis de tráfico de búsqueda para Búsqueda de Azure](search-traffic-analytics.md) para comenzar.

Como alternativa, puede utilizar la API de REST de administración. Consulte [Introducción a la API de REST de administración de la Búsqueda de Azure](search-get-started-management-api.md) y [Referencia a la API de REST de administración de la Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn832684.aspx) para obtener información detallada.

<a id="sub-1"></a>
## Adición del servicio de búsqueda a su suscripción

Como administrador de configuración de un servicio de búsqueda, uno de sus primeras decisiones es elegir un nivel de precios. Las opciones incluyen los niveles de precios Gratuito y Estándar.

Sin cargo alguno para los suscriptores existentes, puede seleccionar un servicio compartido, recomendado con fines didácticos, evaluación de prueba de concepto y pequeños proyectos de desarrollo. El servicio compartido incluye un espacio de almacenamiento de 50 MB, tres índices y recuento de documentos (un límite máximo de 10.000 documentos, incluso si el consumo de almacenamiento es inferior a los 50 MB totales permitidos). No hay garantías de rendimiento con el servicio compartido, de modo que si crea una aplicación de búsqueda de producción, considere la búsqueda estándar en su lugar.

Las búsquedas Estándar y Básica son facturables porque se está registrando para obtener recursos dedicados y una infraestructura que solo usará su suscripción. La búsqueda Estándar y Básica se asigna en agrupaciones de particiones (almacenamiento) definidas por el usuario y réplicas (cargas de trabajo de servicio) y los precios se establecen por unidad de búsqueda. Puede escalar en particiones o réplicas de forma independiente, agregando más de cualquiera que sea el recurso requerido.

Para planificar la capacidad y entender el impacto de facturación, recomendamos estos vínculos:

+	[Límites y restricciones](search-limits-quotas-capacity.md)
+	[Detalles de precios](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Cuando esté listo para suscribirse, consulte [Creación de un servicio de búsqueda en el portal](search-create-service-portal.md).

##Análisis de búsqueda

Puede habilitar la recopilación de datos a través de la actividad de búsqueda del usuario para comprender cómo funciona el servicio de búsqueda, los términos que se utilizan y si esos términos devuelven coincidencias. La mejor manera de analizar y visualizar estos datos a través de un paquete de contenido de Power BI. El primer paso es habilitar el análisis de tráfico de búsqueda. Consulte [Análisis del tráfico de Búsqueda de Azure](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/) para obtener información sobre el procedimiento.

<a id="sub-2"></a>
## Tareas administrativas

Aunque algunos servicios pueden tener coadministradores, un servicio de búsqueda de Azure tiene un administrador por suscripción. Tiene que ser un administrador para realizar las tareas descritas en esta sección. Además de agregar la búsqueda a la suscripción, el administrador es responsable de estas tareas adicionales:

+	Distribución de la URL de servicio (definida durante el aprovisionamiento del servicio).
+	Administración y distribución de las claves de API.
+	Supervisar el uso de recursos
+	Escalado o reducción vertical (se aplica solo a la búsqueda estándar)
+	Inicio o interrupción del servicio
+	Definición de roles para controlar el acceso administrativo

<a id="sub-3"></a>
## URL de servicio

La URL de servicio se define como una propiedad fija cuando crea el servicio; no se puede cambiar más adelante.

Los desarrolladores que crean aplicaciones de búsqueda deberán conocer la URL de servicio para las solicitudes de HTTP. Puede buscar con rapidez la URL de servicio mediante el panel de servicios.

Para obtener la URL de servicio del panel de servicios:

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **Examinar** | **Todo** | **Servicios de búsqueda**.
3.	Haga clic en el nombre de su servicio de búsqueda para abrir el panel.
4.	Haga clic en **PROPIEDADES** para deslizar una página de propiedades al abrirla. La URL de servicio se encuentra en la parte superior de la página. Puede anclar esta página para obtener acceso rápidamente más adelante.

    ![][8]

Asimismo, es posible que los desarrolladores le pregunten sobre la versión de la API. Un requisito de codificación de la API de Búsqueda de Azure siempre es especificar la versión de la API en la solicitud. El fin de este requisito es que los desarrolladores puedan seguir usando una versión anterior y, a continuación, cambien a una versión posterior en el momento adecuado.

La versión de la API no se muestra en las páginas del portal, por lo que no se trata de información que puedas proporcionar. Para obtener información sobre versiones de la API actuales y anteriores, consulte la [API de REST de Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<a id="sub-4"></a>
## Administración de las claves de API

Los desarrolladores que crean aplicaciones de búsqueda deberán tener una clave de API para obtener acceso a Búsqueda. Cada una de las solicitudes de HTTP a su servicio de búsqueda necesitarán una clave de API generada de forma específica para su servicio. La clave de API es el único mecanismo para autenticarse en la URL de su servicio de búsqueda.

Se usan dos tipos de claves para obtener acceso a su servicio de búsqueda:

+	Administración (válida para cualquier operación)
+	Consulta (válida solo para solicitudes de consulta)

Una clave de API de administración se crea con el servicio. Existe una clave principal y otra secundaria. Puede usarlas indistintamente; ninguna refleja un nivel de acceso superior o inferior, lo que resulta útil si quiere sustituir claves. Puede volver a generar cualquiera de las claves de administración, pero no puede agregarla al recuento total de claves de administración. Puede haber un máximo de dos claves de administración por servicio de búsqueda.

Las claves de consulta están diseñadas para su uso en aplicaciones cliente que llaman a Búsqueda directamente. Puede crear hasta 50 claves de consulta.

Para obtener o volver a generar claves de API, abra el panel de servicios. Haga clic en **CLAVES** para deslizar la página de administración de claves al abrirla. Los comandos para volver a generar o crear claves están en la parte superior de la página.

 ![][9]


<a id="sub-5"></a>
## Supervisar el uso de recursos

En esta vista previa pública, la supervisión de recursos se limita a la información que se muestra en el panel de servicios y algunas métricas que puede obtener al consultar el servicio.

En el panel de servicios, en la sección Uso, podrá determinar rápidamente si los niveles de recursos de partición son adecuados para su aplicación.

Al usar la API del servicio de búsqueda, podrá obtener una recuento de los documentos e índices. Existen límites máximos asociados a estos recuentos basados en el nivel de precio. Consulte [Límites del servicio de búsqueda](search-limits-quotas-capacity.md) para obtener más información.

+	[Obtención de estadísticas de índice](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Recuento de documentos](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Los comportamientos de Almacenamiento en caché pueden sobrevalorar un límite temporalmente. Por ejemplo, al usarse el servicio compartido, es posible que vea un recuento de documentos sobre el límite máximo de 10.000 documentos. La sobrevaloración es temporal y se detectará en la próxima comprobación de aplicación de límite.


<a id="sub-6"></a>
## Escalado o reducción vertical

Cada uno de los servicios de búsqueda se inicia con una cantidad mínima de una réplica y una partición. Si se registró para obtener recursos dedicados con el [nivel de precios Básico o Estándar](search-limits-quotas-capacity.md), puede hacer clic en el icono **ESCALAR** en el panel de servicios para reajustar el número de particiones y réplicas que usa por su servicio.

Al agregar capacidad mediante cualquiera de los recursos, el servicio los usa automáticamente. No es necesario que haga nada más, pero habrá un ligero retraso antes de materializarse el impacto del nuevo recurso. Puede tardar 15 minutos o más en aprovisionar los recursos adicionales.

 ![][10]

### Adición de réplicas

La adición de réplicas permiten un aumento de las consultas por segundo (QPS) o la consecución de una alta disponibilidad. Cada réplica tiene una copia de un índice, de modo que la adición de una réplica más se traduce en un índice más que puede usarse para ofrecer solicitudes de consulta. En la actualidad, la regla general es que son necesarias al menos tres réplicas para obtener una alta disponibilidad (consulte [Planeación de la capacidad](search-capacity-planning.md) para más información).

Un servicio de búsqueda con más réplicas puede equilibrar la carga de solicitudes de consulta sobre un número de índices mayor. Una vez especificado un nivel del volumen de consultas, el rendimiento de las consultas va a ser más rápido cuando haya más copias del índice disponible para ofrecer la solicitud. En caso de experimentar una latencia de consulta, puede esperar que se produzca un impacto positivo en el rendimiento una vez que las réplicas adicionales estén en línea.

Aunque el rendimiento de las consultas aumente a medida que agrega réplicas, esto no lo dobla ni triplica precisamente a medida que agrega réplicas a su servicio. Todas las aplicaciones de búsqueda están sujetas a factores externos que pueden afectar al rendimiento de las consultas. Las consultas complejas y la latencia de red son dos factores que contribuyen a la existencia de variaciones en los tiempos de respuesta de las consultas.

### Adición de particiones

La mayoría de las aplicaciones de servicio tienen una necesidad integrada de más réplicas en lugar de particiones, ya que la mayor parte de las aplicaciones que usan la búsqueda pueden ajustarse fácilmente a una sola partición que puede admitir hasta 15 millones de documentos.

En los casos donde se requiere un mayor número de documentos, puede agregar particiones si se registró en un servicio Estándar. El nivel Básico no proporciona particiones adicionales.

En el nivel Estándar, las particiones se agregan en múltiplos de 12 (concretamente 1, 2, 3, 4, 6 o 12). Se trata de un artefacto de particionamiento; un índice se crea en 12 particiones que pueden almacenarse en su totalidad en una partición o dividirse de igual forma en 2, 3, 4, 6 o 12 particiones (una partición por partición).

### Eliminación de réplicas

Tras períodos de elevados volúmenes de consultas, es muy probable que elimine réplicas una vez que se hayan normalizado las cargas de consultas de búsqueda (por ejemplo, tras finalizar las ventas navideñas).

Para ello, basta con que mueva de nuevo el control deslizante de la réplica a un número inferior. No es necesario que haga nada más. Al reducir el recuento de réplicas, las máquinas virtuales se abandonan en el centro de datos. Ahora, sus operaciones de ingesta de consultas y datos se ejecutarán en menos VM que antes. El límite mínimo es una réplica.

### Eliminación de particiones

A diferencia de la eliminación de réplicas, que no requiere que haga nada más, es posible que tenga que hacer algo si usa más almacenamiento del que se puede reducir. Por ejemplo, si su solución usa tres particiones, intentar reducirla a una o dos particiones generará un error si usa más espacio de almacenamiento del que se puede almacenar en el reducido número de particiones. En este caso, las opciones con las que cuenta son eliminar índices o documentos en un índice asociado para liberar espacio o mantener la configuración actual.

No existe un método de detección que indique qué particiones de índice se almacenan en particiones concretas. Cada partición proporciona un espacio de almacenamiento de aproximadamente 25 MB, de modo que será necesario reducirlo a un tamaño al que pueda ajustarse su número de particiones. Si quiere volver a una partición, las 12 particiones deberán ajustarse.

Para ayudar en una futura planificación, es posible que quiera comprobar el espacio de almacenamiento (usando [Obtención de estadísticas de índice](http://msdn.microsoft.com/library/dn798942.aspx)) para ver cuánto usó en realidad.

### Prácticas recomendadas de implementación del servicio y de la escala en varios centros de datos (vídeo)

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="sub-7"></a>
## Inicio o interrupción del servicio

Puede iniciar, interrumpir o incluso eliminar el servicio usando comandos en el panel de servicios.

 ![][11]


Con la interrupción o inicio del servicio no se desactiva la facturación. Debe eliminar el servicio para evitar que se le cobren todas las cargas. Cualquier dato asociado a su servicio se eliminará cuando su servicio no esté disponible.

<a id="sub-8"></a>
## Definición de roles para el acceso administrativo

Azure proporciona un modelo de autorización global basado en roles para todos los servicios administrados a través del portal o en la API del Administrador de recursos de Azure si usa una herramienta de administración personalizada. Los roles de lector, colaborador y propietario establecen el nivel de administración del servicio para los usuarios, los grupos y las entidades de seguridad de Active Directory a los que asigna cada rol. Consulte [Control de acceso basado en roles en el Portal de Azure clásico](../active-directory/role-based-access-control-configure.md) para obtener información detallada sobre la pertenencia a roles.

En términos de Búsqueda de Azure, los controles de acceso basados en roles determinan las siguientes tareas administrativas:


Rol|Tarea
---|---
Propietario|Iniciar, detener o eliminar el servicio.<p>Generar y ver claves de administración y consulta.<p>Ver el estado del servicio, incluido el número de índice, los nombres de índice, el recuento de documentos y el tamaño de almacenamiento.<p>Agregar o eliminar miembros del rol (solo un propietario puede administrar la pertenencia a roles).<p>Los administradores de servicio y suscripción tienen pertenencia automática en el rol de propietarios.
Colaborador|Tiene el mismo nivel de acceso que el propietario, excepto para la administración de roles. Por ejemplo, un colaborador puede ver y regenerar `api-key`, pero no puede modificar pertenencias a roles.
Lector|Ver las claves de estado y consulta de servicio. Los miembros de este rol no pueden iniciar o detener un servicio, ni pueden ver claves de administrador.

Tenga en cuenta que los roles no otorgan derechos de acceso al extremo de servicio. Las operaciones del servicio de búsqueda, como la administración de índices, el rellenado del índice y las consultas en datos de búsqueda, se controlan mediante claves de API, no a través de roles. Consulte "Autorización para administración frente a operaciones de datos" en [Control de acceso basado en roles en el Portal de Azure](../active-directory/role-based-access-control-configure.md) para obtener más información.

Los roles proporcionan control de acceso después de crear el servicio. Solo los administradores de la suscripción pueden agregar un servicio de búsqueda a una suscripción.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7
[Set roles to control administrative access]: #sub-8

<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


 

<!---HONumber=AcomDC_0302_2016-->