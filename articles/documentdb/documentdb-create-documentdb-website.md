<properties 
	pageTitle="Implementación de DocumentDB y aplicaciones web del Servicio de aplicaciones de Azure mediante una plantilla del Administrador de recursos de Azure | Microsoft Azure" 
	description="Aprenda a implementar una cuenta de DocumentDB, aplicaciones web del Servicio de aplicaciones de Azure y una aplicación web de ejemplo mediante una plantilla del Administrador de recursos de Azure." 
	services="documentdb, app-service\web" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="ryancraw"/>

# Implementación de DocumentDB y aplicaciones web de servicio de aplicación de Azure mediante una plantilla del Administrador de recursos de Azure #

En este tutorial se muestra cómo usar una plantilla del Administrador de recursos de Azure para implementar e integrar [DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/), una aplicación web del [servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y una aplicación web de ejemplo.

Después de completar este tutorial, podrá responder a las preguntas siguientes:

-	¿Cómo puedo usar una plantilla del Administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB y una aplicación web en el Servicio de aplicaciones de Azure?
-	¿Cómo puedo usar una plantilla del Administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB, una aplicación web de Servicio de aplicaciones de Azure y una aplicación Webdeploy?

<a id="Prerequisites"></a>
## Requisitos previos ##
> [AZURE.TIP] Si bien en este tutorial no se supone que se deba tener experiencia anterior con plantillas del Administrador de recursos de Azure, JSON o Azure PowerShell, si desea modificar las plantillas a las que se hace referencia o las opciones de implementación, sí se requerirá entonces el conocimiento de cada una de estas áreas.

Antes de seguir las instrucciones de este tutorial, asegúrese de contar con lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para miembros](https://azure.microsoft.com/pricing/member-offers/) o [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Una cuenta de almacenamiento de Azure. Para obtener instrucciones, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).
- Una estación de trabajo con Azure PowerShell 0.9.8. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Este tutorial aún no se ha actualizado para la versión preliminar de Azure PowerShell 1.0. 

##<a id="CreateDB"></a>Paso 1: Descarga y extracción de los archivos de ejemplo ##
Vamos a empezar descargando los archivos de ejemplo que usaremos en este tutorial.

1. Descargue el [ejemplo Creación de una cuenta de DocumentDB y aplicaciones web, e implementación de una aplicación de demostración](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) en una carpeta local (por ejemplo, C:\\DocumentDBTemplates) y extraiga los archivos. En este ejemplo se implementará una cuenta de DocumentDB, un sitio web de servicio de aplicación y una aplicación web. También se configurará automáticamente la aplicación web para conectar con la cuenta de DocumentDB.

2. Descargue el [ejemplo Creación de una cuenta de DocumentDB y aplicaciones web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) en una carpeta local (por ejemplo, C:\\DocumentDBTemplates) y extraiga los archivos. En este ejemplo se implementará una cuenta de DocumentDB y una aplicación web de servicio de aplicación, y se modificará la configuración de la aplicación web para exponer fácilmente la información de conexión de DocumentDB, pero no se incluye una aplicación web.

> [AZURE.TIP] Tenga en cuenta que en función de la configuración de seguridad de su equipo, puede que tenga que desbloquear los archivos extraídos; para ello, haga clic en ellos con el botón derecho, haga clic en **Propiedades** y haga clic en **Desbloquear**.

![Captura de pantalla de la ventana Propiedades con el botón Desbloquear resaltado](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Paso 2: Implementación del ejemplo de cuenta de documento, aplicación web de servicio de aplicación y aplicación de demostración ##

Ahora vamos a implementar nuestra primera plantilla.

> [AZURE.TIP] La plantilla no valida que el nombre de la aplicación web y el nombre de la cuenta de DocumentDB especificados a continuación sean a) válidos y b) estén disponibles. Es muy recomendable que compruebe la disponibilidad de los nombres que planea suministrar antes de ejecutar el script de implementación de PowerShell.

1. Abra Microsoft Azure PowerShell y vaya a la carpeta en la que ha descargado y extraído el [ejemplo Creación de una cuenta de DocumentDB y una aplicación de servicio web, e implementación de una aplicación de demostración](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (por ejemplo, C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo).


2. Vamos a ejecutar el script de PowerShell CreateDocDBWebsiteTodo.ps1. El script toma los siguientes parámetros obligatorios:
	- WebsiteName: especifica el nombre de la aplicación web del servicio web y se usa para crear la URL que se usará para el acceso a la aplicación web (por ejemplo, si especifica "mydemodocdbwebsite", la URL por la que tendrá acceso a la aplicación web será mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName: especifica el nombre del grupo de recursos de Azure que se implementará. Si el grupo de recursos especificado no existe, se creará.

	- docDBAccountName: especifica el nombre de la cuenta de DocumentDB que se creará.

	- location: especifica la ubicación de Azure en la que se crearán los recursos de DocumentDB y de la aplicación web. Valores válidos son Asia Oriental, Sudeste de Asia, Este de Estados Unidos, Oeste de Estados Unidos, Europa del Norte, Europa Occidental (tenga en cuenta que el valor de ubicación proporcionado distingue mayúsculas de minúsculas).


3. Este es un comando de ejemplo para ejecutar el script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Tenga en cuenta que se le pedirá que escriba su nombre de usuario y contraseña de Azure como parte de la ejecución del script. La implementación completa tardará entre 10 y 15 minutos en completarse.

4. A continuación se muestra un ejemplo de la salida resultante:

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Antes de examinar nuestra aplicación de ejemplo, es necesario comprender qué hace la implementación de la plantilla:

	- Se ha creado una aplicación web del Servicio de aplicaciones.

	- Se ha creado una cuenta de DocumentDB.

	- Un paquete de implementación web se ha implementado en la aplicación web del Servicio de aplicaciones

	- La configuración de la aplicación web se ha modificado de forma que el extremo de DocumentDB y la clave maestra principal se han expuesto como configuración de la aplicación.

	- Se ha creado una serie de reglas de supervisión predeterminadas.

	
6. Para usar la aplicación, vaya simplemente a la URL de la aplicación web (en el ejemplo anterior, la URL sería http://mydemodocdbwebapp.azurewebsites.net). Verá la siguiente aplicación web:

	![Aplicación de tareas pendientes de ejemplo](./media/documentdb-create-documentdb-website/image2.png)

7. Siga adelante y cree un par de tareas, y posteriormente se abrirá el [Portal de Microsoft Azure](https://portal.azure.com).

8. Elija examinar los grupos de recursos y seleccione el grupo de recursos que hemos creado durante la implementación (en el ejemplo anterior, myDemoResourceGroup).

	![Captura de pantalla del Portal de Azure clásico con myDemoResourceGroup resaltado](./media/documentdb-create-documentdb-website/image3.png)
9.  Observe que el mapa de recursos del modo Resumen muestra todos nuestros recursos relacionados (cuenta de DocumentDB, aplicación web del Servicio de aplicaciones, Supervisión).

	![Captura de pantalla del modo de resumen](./media/documentdb-create-documentdb-website/image4.png)
10.  Haga clic en su cuenta de DocumentDB e inicie el Explorador de consultas (cerca de la parte inferior de la hoja de la cuenta).

	![Captura de pantalla de las hojas del Grupo de recursos y Cuenta con el icono Explorador de consultas resaltado](./media/documentdb-create-documentdb-website/image8.png)

11. Ejecute la consulta predeterminada, "SELECT * FROM c" e inspeccione los resultados. Observe que la consulta ha recuperado la representación JSON de los elementos todo creados en el paso 7 anterior. No dude en experimentar con las consultas; por ejemplo, intente ejecutar SELECT * FROM c WHERE c.isComplete = true para devolver todos los elementos todo marcados como completos.


	![Captura de pantalla de las hojas del Explorador de consultas y Resultados en la que se muestran los resultados de la consulta](./media/documentdb-create-documentdb-website/image5.png)
12. No dude en explorar la experiencia del portal de DocumentDB o modificar la aplicación Todo de ejemplo. Cuando esté preparado, implementaremos otra plantilla.
	
<a id="Build"></a>
## Paso 3: Implementación de la cuenta de documentos y del ejemplo de aplicación web ##

Ahora implementaremos nuestra segunda plantilla.

> [AZURE.TIP] La plantilla no valida que el nombre de la aplicación web y el nombre de la cuenta de DocumentDB especificados a continuación sean a) válidos y b) estén disponibles. Es muy recomendable que compruebe la disponibilidad de los nombres que planea suministrar antes de ejecutar el script de implementación de PowerShell.

1. Abra Microsoft Azure PowerShell y vaya a la carpeta en la que descargó y extrajo el [ejemplo Creación de una cuenta de DocumentDB y un ejemplo de aplicación web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (por ejemplo, C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Vamos a ejecutar el script CreateDocDBWebsite.ps1 de PowerShell. El script toma los mismos parámetros que la primera plantilla que hemos implementado, a saber:
	- WebsiteName: especifica el nombre de la aplicación web del servicio web y se usa para crear la URL que se usará para el acceso a la aplicación web (por ejemplo, si especifica "myotherdocumentdbwebapp", la URL por la que tendrá acceso a la aplicación web será myotherdocumentdbwebapp.azurewebsites.net).

	- ResourceGroupName: especifica el nombre del grupo de recursos de Azure que se implementará. Si el grupo de recursos especificado no existe, se creará.

	- docDBAccountName: especifica el nombre de la cuenta de DocumentDB que se creará.

	- 	location: especifica la ubicación de Azure en la que se crearán los recursos de DocumentDB y de la aplicación web. Valores válidos son Asia Oriental, Sudeste de Asia, Este de Estados Unidos, Oeste de Estados Unidos, Europa del Norte, Europa Occidental (tenga en cuenta que el valor de ubicación proporcionado distingue mayúsculas de minúsculas).

3. Este es un comando de ejemplo para ejecutar el script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Tenga en cuenta que se le pedirá que escriba su nombre de usuario y contraseña de Azure como parte de la ejecución del script. La implementación completa tardará entre 10 y 15 minutos en completarse.

4. El resultado de la implementación será muy parecido al primer ejemplo de plantilla.
5. Antes de abrir el Portal de Azure, se debe comprender qué se logró con la implementación de esta plantilla:

	- Se ha creado una aplicación web del Servicio de aplicaciones.

	- Se ha creado una cuenta de DocumentDB.

	- 	La configuración de la aplicación web de Azure se ha modificado de forma que el extremo de Azure DocumentDB, la clave maestra primaria y la clave maestra secundaria se expusieron como configuración de la aplicación.

	- 	Se ha creado una serie de reglas de supervisión predeterminadas.

6. Abra el [Portal de Azure](https://portal.azure.com), elija examinar los grupos de recursos y seleccione el grupo de recursos creado durante la implementación (en el ejemplo anterior, myOtherDemoResourceGroup).
7. En el modo Resumen, haga clic en la aplicación web que acaba de implementar.

	![Captura de pantalla del modo de resumen con la aplicación web myotherdocumentdbwebapp resaltada](./media/documentdb-create-documentdb-website/image6.png)
8. En la hoja de la aplicación web, haga clic en **Toda la configuración**, luego en **Configuración de la aplicación** y observe que existen valores de configuración de aplicación para el extremo de DocumentDB y para cada una de las claves maestras de DocumentDB.

	![Captura de pantalla de la aplicación web, la configuración y las hojas de configuración de aplicación](./media/documentdb-create-documentdb-website/image7.png)
9. Explore libremente el Portal de Azure o siga uno de nuestros [ejemplos](http://go.microsoft.com/fwlink/?LinkID=402386) de DocumentDB para crear su propia aplicación de DocumentDB.

	
	
<a name="NextSteps"></a>
## Pasos siguientes

¡Enhorabuena! Ha implementado DocumentDB, una aplicación web del Servicio de aplicaciones y una aplicación web de ejemplo mediante plantillas del Administrador de recursos de Azure.

- Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).
- Para obtener más información acerca de aplicaciones de servicio web de la aplicación de Azure, haga clic en [aquí](http://go.microsoft.com/fwlink/?LinkId=325362).
- Para obtener más información sobre las plantillas del Administrador de recursos de Azure, haga clic [aquí](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
 

<!---HONumber=AcomDC_0204_2016-->