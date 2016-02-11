
<properties 
   pageTitle="Notas de la versión de SDK de Azure para .NET 2.7 y .NET 2.7.1" 
   description="Notas de la versión de SDK de Azure para .NET 2.7 y .NET 2.7.1" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="01/19/2016"
   ms.author="juliako"/>


# Notas de la versión de SDK de Azure para .NET 2.7 y .NET 2.7.1

##Información general

Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.7.

Este documento también contiene las notas de la versión de SDK de Azure para la versión .NET 2.7.1.

Azure SDK 2.7 solo es compatible en Visual Studio 2015 y Visual Studio 2013. [SDK de Azure 2.6](https://azure.microsoft.com/downloads/) es el último SDK compatible para Visual Studio 2012.

Para obtener información detallada acerca de esta publicación, consulte la [Publicación de anuncio de SDK de Azure 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) y [Publicación de anuncio de SDK de Azure 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

##SDK de Azure para .NET 2.7

###Mejoras en el inicio de sesión de Visual Studio de 2015

Azure SDK 2.7 para Visual Studio 2015 es compatible con las nuevas características de administración de identidades en Visual Studio 2015. Esto incluye la compatibilidad para las cuentas de acceso a Azure mediante control de acceso basado en rol, proveedores de soluciones en la nube, DreamSpark y otros tipos de cuenta y suscripción.

Las mejoras en el inicio de sesión incluidas en SDK de Azure 2.7 solo están disponibles en Visual Studio 2015. Compatibilidad con Visual Studio 2013 se incluye en SDK de Azure 2.7.1.


###SDK de dispositivos móvil

Plantillas de **Aplicaciones móviles** para reflejar el [paquete de NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) y el proceso de configuración más actualizados

###Bus de servicio 

Mejoras y correcciones de errores generales. Para obtener información detallada sobre las actualizaciones y otras características, consulte las notas de la versión del [NuGet de bus de servicio](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) más reciente.

###Herramientas de HDInsight 

En esta versión se han realizado las siguientes actualizaciones. Estas actualizaciones se encuentran en vista previa. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Gráficos de Hive para Hive en trabajos de Tez
- Compatibilidad completa de IntelliSense de DML de Hive
- Compatibilidad con plantillas de Pig
- Plantillas de Storm para servicios de Azure

####Cambios drásticos

- El proyecto de **Storm** antiguo debe actualizarse cuando se usa esta versión de las herramientas. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express ya no es compatible. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###Herramientas del Servicio de aplicaciones de Azure

En esta versión se han realizado las siguientes actualizaciones en Web Tools Extensions. Para obtener más información, consulte [este](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog.

- Compatibilidad agregada para las cuentas de DreamSpark
- Cambio completo en Azure Tools realizado para admitir las nuevas API de Administración de recursos de Azure
- Compatibilidad de Servicio de aplicaciones de Azure agregado a [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

####Problemas conocidos

Los nodos de ranura de implementación de aplicación web no aparecen en el nodo de ranura en Explorador de servidores los nodos secundarios de ranura de implementación de aplicación web no se cargan en Cloud Explorer. Este problema se ha resuelto y preparado en la próxima versión SDK.


###<a id="cloud_explorer"></a>Cloud Explorer para Visual Studio de 2015

Azure SDK 2.7 incluye Cloud Explorer para Visual Studio de 2015, que le permitirá ver los recursos de Azure, inspeccionar sus propiedades y realizar acciones de desarrollador clave desde dentro de Visual Studio.

Cloud Explorer admite lo siguiente:

- Vistas de grupo de recursos y tipo de recurso de los recursos de Azure 
- Búsqueda de recursos por nombre (disponible en la vista de tipo de recurso)
- Compatibilidad con las suscripciones y los recursos que tienen aplicado el control de acceso basado en rol (RBAC) 
- Panel de acción integrado que muestra las acciones orientadas a desarrolladores específicas para los recursos seleccionados. Por ejemplo: adjunte el depurador remoto para las máquinas virtuales creadas con la pila del Administrador de recursos, Ver datos del diagnóstico para máquinas virtuales, etc.
- Panel de propiedades integrado que muestra propiedades orientadas a desarrolladores normalmente necesarias durante el desarrollo y las pruebas 
- Cambio rápido de la cuenta que se utilizará al enumerar los recursos (utilice el comando Configuración de la barra de herramientas) 
- Filtrado de suscripciones que se utilizará al enumerar los recursos (utilice el comando Configuración de la barra de herramientas) 
- Vínculos profundos en el Portal de Azure para administración de recursos y grupos de recursos 
 
 
###Herramientas del Administrador de recursos de Azure 

Las herramientas del Administrador de recursos de Azure se han actualizado para trabajar con el control de acceso basado en rol (RBAC) y nuevos tipos de suscripción. Estos cambios incluyen la capacidad de usar nuevas cuentas de almacenamiento, además de almacenamiento clásico para almacenar artefactos durante la implementación.

Si está utilizando un proyecto del grupo de recursos de Azure desde una versión anterior del SDK con SDK 2.7, es necesario un nuevo script de implementación para implementar mediante una nueva cuenta de almacenamiento en lugar de almacenamiento clásico. Se le solicitará antes de realizar cambios en el proyecto que agregue el nuevo script. Se cambiará el nombre del script anterior y tendrá que realizar modificaciones manualmente en el nuevo script.
 
 
###Herramientas del explorador de almacenamiento 

- Compatibilidad con la visualización de los blobs de anexión. Puede obtener más información en [esta entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Soporte técnico para ver las cuentas de almacenamiento premium a través del Explorador de servidores. El Explorador de servidores solo mostrará los blobs de página de cuentas de almacenamiento premium como si fuera el único tipo compatible para cuentas de almacenamiento premium.

###Herramientas de Factoría de datos de Azure para Visual Studio 

Introducción a **Herramientas de Factoría de datos de Azure** para Visual Studio. A continuación se muestran las características habilitadas. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Creación basada en plantillas**: seleccione plantillas de procesamiento de datos, de movimiento de datos o basadas en casos de uso para implementar una solución de integración de datos completa e iniciar la experiencia práctica rápidamente con Factoría de datos. 
- **Integración con el Explorador de soluciones para la creación e implementación de entidades de Factoría de datos**: cree e implemente canalizaciones y las entidades relacionadas como proyectos de Visual Studio. 
- **Integración con vista de diagrama de interacción visual durante la creación**: cree visualmente las canalizaciones y conjuntos de datos con ayuda de la vista de diagrama. 
- **Integración con el Explorador de servidores para la exploración y la interacción con entidades ya implementadas**: aproveche el Explorador de servidores para examinar las factorías de datos ya implementadas y las entidades correspondientes. Importe una factoría de datos implementada o cualquier identidad (canalización, servicios vinculados o conjuntos de datos) en el proyecto. 
- **Edición de JSON con IntelliSense enriquecido y validación de esquema**: configure y edite de forma eficiente documentos JSON de entidades de Factoría de datos con IntelliSense enriquecido y validación de esquema 
- **Publicación en varios entornos**: publique canalizaciones creadas para desarrollar, probar o producir un entorno mediante la creación de archivos de configuración para cada entorno.
- **Soporte técnico de procesamiento de datos basado en Pig, Hive y .Net**: soporte técnico para scripts de Pig y Hive en el proyecto de Factoría de datos. Soporte técnico para hacer referencia al proyecto de C# para la administración de .Net Activity.

##SDK de Azure para .NET 2.7.1

La sección siguiente contiene las actualizaciones que se introdujeron con SDK de Azure para la versión .NET 2.7.1.
###Herramientas de HDInsight 

Para obtener una explicación más detallada acerca de las actualizaciones de herramientas de HDInsight, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Vista de operador de trabajos de Hive (nueva característica)

	Para ayudarle a entender mejor la consulta de Hive, se agregó la característica Vista de operador de Hive. Para ver todos los operadores dentro de un vértice, haga doble clic en los vértices del gráfico de trabajo. Para ver más detalles acerca de un operador determinado, mantenga el mouse sobre ese operador.
- Marcador de Error de Hive (nueva característica)

	Para que pueda ver los errores gramaticales al instante, se agregó la característica marcador de Error de Hive. También se mejoraron los mensajes de error y ahora puede ver los errores gramaticales con detalle instantáneamente (hasta esta versión, tenía que enviar un script de Hive al clúster y esperar un tiempo antes de obtener detalles sobre el mensaje de error).  
- Gráfico de topología Storm (nueva característica)

	La visualización es muy importante cuando desee comprobar si su topología funciona según lo esperado. En esta versión agregamos la visualización de gráficos Storm. Puede visualizar las métricas importantes para su topología (por ejemplo, un color indica si un Bolt está "ocupado" o no). También puede hacer doble clic en el Bolt/Spout para ver más detalles.

- Compatibilidad con clústeres de HDInsight que se crearon en el Portal de Azure (corrección de un error)

	Ahora puede usar Visual Studio para ver y enviar trabajos a todos los clústeres de HDInsight independientemente de dónde se creó el clúster.

- Mayor compatibilidad con IntelliSense y carga más rápida de metadatos de Hive (mejora)

	Hemos mejorado IntelliSense agregando más sugerencias que facilitan su uso. Por ejemplo, el alias de tabla ahora también puede sugerirse en IntelliSense para que pueda escribir la consulta más fácilmente. Además, hemos mejorado la carga de metadatos de Hive, de forma que solo tarda unos segundos para enumerar todas las bases de datos, tablas y columnas de la tienda de metadatos de Hive.

Para obtener una explicación más detallada acerca de las actualizaciones de herramientas de HDInsight, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###Mejoras en Visual Studio 2013

- SDK de Azure 2.7.1 permite que Visual Studio 2013 acceda a suscripciones y cuentas de Azure a través del control de acceso basado en rol, proveedores de soluciones en la nube y Dreamspark.
- Con Azure SDK 2.7.1, la nueva ventana de herramientas de Cloud Explorer ahora también está disponible en Visual Studio 2013.

###Problemas conocidos

Instalación de SDK de Azure 2.6 o 2.7.1 para Visual Studio Community 2013 en un SO en un idioma diferente del inglés mostrará una advertencia informando de que los recursos en inglés y los que no estén en inglés de Visual Studio pueden no coincidir. Esta advertencia se puede descartar sin más preocupaciones. Solo se producirá si la máquina no contenía una instalación anterior de Visual Studio Community 2013 y va a instalar el SDK en un SO con un idioma diferente al inglés. La advertencia se muestra después de que el paquete de idioma aplica los recursos RTM a Visual Studio, pero antes de que aplique la actualización 4. Descartar la advertencia permitirá al paquete de idioma continuar y completar la aplicación de la versión 4 de actualización del contenido del paquete de idioma.

Los proyectos de LightSwitch no son compatibles con esta versión. Este problema se resolverá con la próxima versión del SDK.

##Consulte también:
[Publicación de anuncio de SDK de Azure 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850)

[Publicación de anuncio de Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Información de compatibilidad y retirada del SDK de Azure para .NET y API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

<!---HONumber=AcomDC_0128_2016-->