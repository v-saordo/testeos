
<properties 
   pageTitle="Notas de la versión de SDK de Azure para .NET 2.8." 
   description="Notas de la versión de SDK de Azure para .NET 2.8." 
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
   ms.date="01/31/2016"
   ms.author="juliako"/>

# Azure SDK para .NET 2.8, 2.8.1 y 2.8.2

##Información general
 
Este artículo contiene las notas de la versión (que incluyen los problemas conocidos y los cambios recientes) de Azure SDK para .NET, versiones 2.8, 2.8.1 y 2.8.2.

Para obtener una lista completa de nuevas características y actualizaciones realizadas en esta versión, consulte el anuncio de [SDK 2.8 de Azure para Visual Studio 2013 y Visual Studio de 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##  SDK de Azure para .NET 2.8

### Descarga del SDK de Azure para .NET 2.8

[SDK de Azure para .NET 2.8 para Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285)

[SDK de Azure para .NET 2.8 para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### Compatibilidad con .NET 4.5.2 

####Problemas conocidos

El SDK de Azure para .NET 2.8 le permite crear paquetes de servicios en la nube de .NET 4.5.2. Sin embargo .NET Framework 4.5.2 no se instalará en las imágenes del sistema operativo invitado predeterminado hasta el lanzamiento del sistema operativo invitado de 2016. Antes de eso, .NET Framework 4.5.2 estará disponible mediante una versión de sistema operativo invitado distinta (2 de noviembre de 2015). Consulte la página [Matriz de compatibilidad del SDK y lanzamientos del SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md) para comprobar cuándo se publicará la imagen. Después de que se publique la imagen del 2 de noviembre de 2015, tendrá la posibilidad de elegir esa imagen; para ello, actualice el archivo de configuración de servicios en la nube (.cscfg). En el archivo de configuración de servicio, establezca el atributo osVersion del elemento ServiceConfiguration en la cadena "WA-GUEST-OS-4.26\_201511-02". Si elige participar para usar esta imagen, ya no obtendrá las actualizaciones automáticas para el SO invitado. Para obtener las actualizaciones automáticas, osVersion se debe establecer en "*" y .NET 4.5.2 solo estará disponible a través de las actualizaciones automáticas en enero de 2016.

###Factoría de datos de Azure

####Problemas conocidos 

Durante la creación de un proyecto de **Plantilla de Factoría de datos** en el que intervienen datos de ejemplo, el script de Azure PowerShell puede dar error si la versión de Azure PowerShell instalada en la máquina es posterior a la 0.9.8.

Para crear correctamente este tipo de proyecto, debe instalar la [versión de Azure PowerShell 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### Herramientas del Administrador de recursos de Azure 

####Cambios drásticos

El script de PowerShell que se proporciona en el proyecto de Grupo de recursos de Azure se ha actualizado en esta versión para que funcione con los nuevos cmdlets de Azure PowerShell, versión 1.0. Este nuevo script no funcionará en Visual Studio cuando se usa una versión del SDK anterior a 2.8.

Los scripts de proyectos creados en versiones anteriores del SDK no se ejecutarán en Visual Studio al usar el SDK 2.8. Todos los scripts seguirán funcionando fuera de Visual Studio con la versión adecuada de los cmdlets de Azure PowerShell.

El SDK 2.8 requiere la versión 1.0 de los cmdlets de Azure PowerShell. Todas las demás versiones del SDK requieren la versión 0.9.8 de los cmdlets de Azure PowerShell. Para más información, consulte [este](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###Extensiones de herramientas web

####Problemas conocidos

En la siguiente versión se abordarán los siguientes problemas conocidos.

- El gesto del Explorador de servidores y la nube relacionado con el Servicio de aplicaciones en los entornos que no son de producción (como clientes de Azure China y Azure Stack) no funcionará. Para los clientes de estas áreas afectadas, la descarga del perfil de publicación del Portal de Azure ofrecerá la posibilidad de publicación. Una versión futura reparará gestos como "Adjuntar depurador" y "Ver registros de streaming" para los clientes de Azure China y Azure Stack. 
- Los clientes pueden ver errores durante la creación del Servicio de aplicaciones cuando la instancia de App Insights en la que van a realizar la implementación se encuentra en una región distinta al Este de Estados Unidos. En estos casos, la creación de un Servicio de aplicaciones en el portal y la descarga del perfil de publicación permitirán escenarios de publicación. 

###Herramientas de HDInsight de Azure

####Nuevas actualizaciones

- Puede ejecutar una consulta de Hive en el clúster a través de HiveServer2 con casi ninguna sobrecarga y ver los registros de trabajos en tiempo real.
- Con la nueva sección de visualización de ejecución de tareas de Hive puede profundizar más en el trabajo, encontrar más detalles e identificar posibles problemas.

Para obtener información, vea [SDK 2.8 de Azure para Visual Studio 2013 y Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## SDK de Azure para .NET 2.8.1

### Problemas conocidos de Visual Studio 2013 y Visual Studio 2015
 
1. Las publicaciones de trabajos web desencadenados en ranuras mostrarán un error y no establecerán una programación, pero el trabajo web se insertará en Azure. Los clientes que necesitan un trabajo programado pueden usar el Portal de Azure para configurar la programación del trabajo web. 
2. Los clientes de Python pueden experimentar problemas con el depurador. El equipo de servicio está implementando una solución para ello, pero si los clientes se ven afectados, informe a Microsoft en los foros o en las secciones de comentarios del blog de anuncios o las notas de la versión. 
3. Los clientes de determinadas regiones (por ejemplo, el sur de la India) experimentarán errores de aprovisionamiento del Servicio de aplicaciones. Esto es coherente con el portal y los clientes que experimenten este problema pueden usar el Portal de Azure para solicitar acceso para publicar en estas regiones geográficas. Cuando soliciten acceso a estas regiones, el uso del aprovisionamiento del Portal de Azure debería funcionar. 

##Azure SDK para .NET 2.8.2

Después de la instalación de la versión 2.8.2 de las herramientas, es posible que los clientes experimenten el problema siguiente.

- Si usa Windows 10 y no ha instalado Internet Explorer, puede que reciba un error de tipo "No se encontró Internet Explorer". Para resolver este problema, instale Internet Explorer mediante el cuadro de diálogo Agregar o quitar componentes de Windows.

Si observa este problema, use la característica Enviar una sonrisa para notificarlo.

Para más información, consulte [este](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) artículo.
##Otras actualizaciones

Para ver otras actualizaciones, vea la [publicación del anuncio de Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##Consulte también:

[Publicación de anuncio de SDK de Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Información de compatibilidad y retirada del SDK de Azure para .NET y API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

<!---HONumber=AcomDC_0204_2016-->