<properties
	pageTitle="Aprovisionamiento e implementación predecibles de microservicios en Azure"
	description="Obtenga información sobre cómo implementar una aplicación formada por microservicios en el Servicio de aplicaciones de Azure como una sola unidad y de forma predecible con plantillas de grupo de recursos JSON y scripting de PowerShell."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cephalin"/>


# Aprovisionamiento e implementación predecibles de microservicios en Azure #

En este tutorial se explica cómo aprovisionar e implementar una aplicación formada por [microservicios](https://en.wikipedia.org/wiki/Microservices) en el [Servicio de aplicaciones de Azure](/services/app-service/) como una sola unidad y de forma predecible con plantillas de grupo de recursos JSON y scripting de PowerShell.

Al aprovisionar e implementar aplicaciones de gran escala que se componen de microservicios muy desacoplados, la repetibilidad y la previsión son fundamentales para que el proceso se realice correctamente. El [Servicio de aplicaciones de Azure](/services/app-service/) le permite crear microservicios que incluyen aplicaciones web, aplicaciones móviles, aplicaciones de API y aplicaciones lógicas. El [Administrador de recursos de Azure](../resource-group-overview.md) le permite administrar todos los microservicios como una unidad, junto con las dependencias de recursos, como la configuración del control de origen y la base de datos. Ahora, también puede implementar esta aplicación mediante plantillas JSON y scripting sencillo de PowerShell.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Lo que hará ##

En el tutorial, implementará una aplicación que incluye:

-	Dos aplicaciones web (es decir, dos microservicios)
-	Un base de datos SQL backend
-	Configuración de aplicaciones, cadenas de conexión y control de código fuente
-	Información sobre aplicaciones, alertas y configuración de autoescala

## Herramientas que va a utilizar ##

En este tutorial, utilizará las siguientes herramientas. Habida cuenta de que no se trata de un tema dedicado íntegramente a las herramientas, voy a presentar un escenario de un extremo a otro y a dar solo una breve introducción acerca de cada una, además de indicar dónde se puede encontrar más información al respecto.

### Plantillas del Administrador de recursos de Azure (JSON) ###
 
Cada vez que crea una aplicación web en el Servicio de aplicaciones de Azure, por ejemplo, el Administrador de recursos de Azure utiliza una plantilla JSON para crear el grupo de recursos completo con los recursos del componente. Una plantilla compleja de [Azure Marketplace](/marketplace) como la aplicación [WordPress escalable](/marketplace/partners/wordpress/scalablewordpress/) puede incluir la base de datos MySQL, cuentas de almacenamiento, el plan del Servicio de aplicaciones, la propia aplicación web, las reglas de alerta, la configuración de la aplicación, valores de escalado automático, etc., y todas estas plantillas están disponibles a través de PowerShell. Para obtener información acerca de cómo descargar y usar estas plantillas, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

Para obtener más información sobre las plantillas del Administrador de recursos de Azure, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

### SDK de Azure 2.6 para Visual Studio ###

El SDK más reciente contiene mejoras en la compatibilidad de la plantilla del Administrador de recursos en el editor de JSON. Puede utilizar esto para crear rápidamente una plantilla de grupo de recursos desde el principio o abrir una plantilla existente de JSON (por ejemplo, una plantilla descargada de la Galería) para modificarla, rellenar el archivo de parámetros e incluso implementar el grupo de recursos directamente desde una solución de grupo de recursos de Azure.

Para obtener más información, consulte [SDK de Azure 2.6 para Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### Azure PowerShell 0.8.0 o posterior ###

A partir de la versión 0.8.0, la instalación de Azure PowerShell incluye el módulo Administrador de recursos de Azure además del módulo de Azure. Este nuevo módulo permite crear un script de la implementación de grupos de recursos.

Para obtener más información, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

### Explorador de recursos de Azure ###

Esta [herramienta de vista previa](https://resources.azure.com) le permite explorar las definiciones de JSON de todos los grupos de recursos en la suscripción y los recursos individuales. En la herramienta, puede editar las definiciones de JSON de un recurso, eliminar una jerarquía completa de recursos y crear nuevos recursos. La información disponible en esta herramienta es muy útil para la creación de plantillas, ya que muestra las propiedades que debe establecer para un tipo determinado de recursos, los valores correctos, etc. Incluso puede crear el grupo de recursos en el [Portal de Azure](https://portal.azure.com/); a continuación, examinar sus definiciones de JSON en la herramienta de explorador a fin de facilitarle el uso de plantillas para el grupo de recursos.

### Botón Implementación en Azure ###

Si utiliza GitHub para el control de código fuente, puede colocar un [botón de implementación en Azure](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) en el archivo README.MD, que habilita una IU de implementación inmediata para Azure. Aunque puede hacerlo para cualquier aplicación web sencilla, se puede extender para habilitar la implementación de un grupo de recursos completo colocando un archivo azuredeploy.json en la raíz del repositorio. Este archivo JSON, que contiene la plantilla de grupo de recursos, lo utilizará el botón Implementación en Azure para crear el grupo de recursos. Para obtener un ejemplo, consulte el ejemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), que se utilizará en este tutorial.

## Obtención de la plantilla de grupo de recursos de ejemplo ##

Ahora vamos a centrarnos en ello.

1. 	Navegue hasta el ejemplo del Servicio de aplicaciones [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp).

2.	 En readme.md, haga clic en **Implementación en Azure**.
 
3.	Se le remite al sitio [deploy-to-azure](https://deploy.azure.com) de sitio y se le solicita que especifique los parámetros de implementación. Observe que la mayoría de los campos se rellenan con el nombre del repositorio y algunas cadenas aleatorias. Puede cambiar todos los campos si lo desea, pero lo único que tiene que escribir es el inicio de sesión administrativo de SQL Server y la contraseña y, a continuación, hacer clic en **Siguiente**.
 
	![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.	A continuación, haga clic en **Implementar** para iniciar el proceso de implementación. Una vez que el proceso se ejecuta hasta completarse, haga clic en el vínculo http://todoapp*XXXX*.azure.websites.net para examinar la aplicación implementada.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

	La interfaz de usuario podría ser un poco lenta la primera vez que obtiene acceso a ella porque las aplicaciones se acaban de iniciar, pero no le quepa duda de que se trata de una aplicación totalmente funcional.

5.	En la página Implementar, haga clic en el vínculo **Administrar** para ver la nueva aplicación en el Portal de Azure.

6.	En el menú desplegable **Essentials**, haga clic en el vínculo del grupo de recursos. Tenga en cuenta también que la aplicación web ya está conectada al repositorio de GitHub en **Proyecto externo**.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.	En la hoja del grupo de recursos, tenga en cuenta que ya existen dos aplicaciones web y una base de datos SQL en el grupo de recursos.

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
Todo lo que acabamos de ver en unos pocos minutos es una aplicación de dos microservicios totalmente implementada, con todos los componentes, las dependencias, la configuración, la base de datos y la publicación continua, configurada mediante una orquestación automatizada en el Administrador de recursos de Azure. Todo esto se realiza mediante dos elementos:

-	El botón Implementación en Azure
-	azuredeploy.json en la raíz del repositorio

Puede implementar la misma aplicación decenas, cientos o miles de veces y tener exactamente la misma configuración cada vez. La repetibilidad y la predicción de este enfoque le permite implementar aplicaciones de gran escala con facilidad y confianza.

## Examine (o edite) AZUREDEPLOY.JSON ##

Ahora veamos cómo se ha configurado el repositorio de GitHub. Va a utilizar el editor de JSON en el SDK .NET de Azure, por lo que si no ha instalado todavía [SDK .NET de Azure 2.6](/downloads/), hágalo ahora.

1.	Clone el repositorio [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) con su herramienta git favorita. En la siguiente captura de pantalla, hago esto en Team Explorer en Visual Studio 2013.

	![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.	Desde la raíz del repositorio, abra azuredeploy.json en Visual Studio. Si no ve el panel de esquema de JSON, deberá instalar el SDK .NET de Azure.

	![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

No voy a describir todos los detalles del formato JSON, pero en la sección [Más recursos](#resources) hay vínculos para obtener información sobre el lenguaje de la plantilla de grupo de recursos. Aquí, voy a mostrar las interesantes características que le ayudarán a empezar a crear su propia plantilla personalizada para la implementación de la aplicación.

### Parámetros ###

Eche un vistazo a la sección de parámetros para ver que la mayoría de ellos son los que el botón **Implementación en Azure** le pide que especifique. El sitio detrás del botón **Implementación en Azure** rellena la interfaz de usuario de entrada con los parámetros definidos en azuredeploy.json. Estos parámetros se usan en las definiciones de recursos, como los nombres de recursos, los valores de propiedad, etc.

### Recursos ###

En el nodo de recursos, puede ver que se definen cuatro recursos de nivel superior, que son una instancia de SQL Server, un plan del Servicio de aplicaciones y dos aplicaciones web.

#### Plan del Servicio de aplicaciones ####

Comencemos con un simple recurso de nivel de raíz en JSON. En el esquema de JSON, haga clic en el plan del Servicio de aplicaciones denominado **[hostingPlanName]** para resaltar el código correspondiente de JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Tenga en cuenta que el elemento `type` especifica la cadena para un plan del Servicio de aplicaciones (hace mucho tiempo se conocía como una granja de servidores), y otros elementos y propiedades se rellenan con los parámetros definidos en el archivo JSON, y este recurso no tiene ningún recurso anidado.

>[AZURE.NOTE] Tenga en cuenta también que el valor de `apiVersion` indica a Azure con qué versión de la API de REST utilizar la definición de recursos de JSON, y esto puede afectar a qué formato se le debe dar al recurso dentro de `{}`.

#### SQL Server ####

A continuación, haga clic en el recurso de SQL Server denominado **SQLServer** en el esquema de JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
Tenga en cuenta lo siguiente sobre el código resaltado de JSON:

-	El uso de parámetros garantiza que los recursos creados son denominados y configurados de manera que sean coherentes entre sí.
-	El recurso de SQL Server tiene dos recursos anidados, cada uno con un valor diferente para `type`.
-	Los recursos anidados dentro de `“resources”: […]`, donde se definen las reglas de firewall y de la base de datos, tienen un elemento `dependsOn` que especifica el identificador de recurso del recurso SQLServer en el nivel de raíz. Esto indica al Administrador de recursos de Azure que antes de crear este recurso, el otro recurso ya debe existir; y si ese otro recurso está definido en la plantilla, cree ese primero.

	>[AZURE.NOTE] Para obtener información detallada sobre cómo utilizar la función `resourceId()`, consulte [Funciones de la plantilla del Administrador de recursos de Azure](../resource-group-template-functions.md).

-	El efecto del elemento `dependsOn` es que el Administrador de recursos de Azure puede saber qué recursos se pueden crear en paralelo y qué recursos deben crearse de forma secuencial.

#### Aplicación web ####

Ahora vamos a pasar a las aplicaciones web, que son más complicadas. Haga clic en la aplicación web [variables(‘apiSiteName’)] en el esquema de JSON para resaltar su código JSON. Observará que las cosas se están poniendo mucho más interesantes. Para este propósito, hablaré de cada una de las características:

##### Recurso raíz #####

La aplicación web depende de dos recursos distintos. Esto significa que el Administrador de recursos de Azure creará la aplicación web sólo después de haber creado el plan del Servicio de aplicaciones y la instancia de SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### Configuración de la aplicación #####

La configuración de la aplicación también se define como un recurso anidado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

En el elemento `properties` para `config/appsettings`, tiene dos configuraciones para la aplicación con el formato `“<name>” : “<value>”`.

-	`PROJECT` es una [configuración de KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que indica a la implementación de Azure qué proyecto usar en una solución de Visual Studio de varios proyectos. Le mostraré más adelante cómo se configura el control de código fuente, pero dado que el código de ToDoApp se encuentra en una solución de Visual Studio de varios proyectos, necesitamos esta configuración.
-	`clientUrl` es simplemente una configuración de la aplicación que el código de la aplicación utiliza.

##### Cadenas de conexión #####

Las cadenas de conexión también se definen como un recurso anidado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

En el elemento `properties` para `config/connectionstrings`, cada cadena de conexión también se define como un par nombre-valor, con el formato específico de `“<name>” : {“value”: “…”, “type”: “…”}`. Para el elemento `type`, los valores posibles son `MySql`, `SQLServer`, `SQLAzure` y `Custom`.

>[AZURE.TIP] Para obtener una lista definitiva de los tipos de cadena de conexión, ejecute el siguiente comando de PowerShell de Azure: [Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### Control de código fuente #####

La configuración del control de código fuente también se define como un recurso anidado. El Administrador de recursos de Azure usa este recurso para configurar la publicación continua (consulte la advertencia en `IsManualIntegration` más adelante) y también para iniciar la implementación de código de la aplicación automáticamente durante el procesamiento del archivo JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` y `branch` deberían ser bastante intuitivos y deben apuntar al repositorio de Git y al nombre de la bifurcación desde la que realizar la publicación. De nuevo, se definen mediante parámetros de entrada.

Tenga en cuenta en el elemento `dependsOn` que, además del recurso de aplicación web, `sourcecontrols/web` depende también de `config/appsettings` y `config/connectionstrings`. Esto es porque una vez que `sourcecontrols/web` está configurado, el proceso de implementación de Azure intentará automáticamente implementar, generar e iniciar el código de aplicación. Por lo tanto, insertar esta dependencia ayuda a asegurarse de que la aplicación tenga acceso a la configuración de la aplicación y las cadenas de conexión necesarias antes de ejecutar el código de aplicación.

>[AZURE.NOTE] Tenga en cuenta también que `IsManualIntegration` está establecido en `true`. Esta propiedad es necesaria en este tutorial, ya que no posee realmente el repositorio de GitHub y realmente no puede conceder permiso a Azure para configurar la publicación continua de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (es decir, insertar actualizaciones de repositorio automáticas en Azure). Puede utilizar el valor predeterminado `false` para el repositorio especificado solo si ha configurado antes las credenciales de GitHub del propietario en el [Portal de Azure](https://portal.azure.com/). En otras palabras, si ha configurado el control de código fuente para GitHub o BitBucket para cualquier aplicación en el [Portal de Azure](https://portal.azure.com/) anteriormente, mediante las credenciales de usuario, Azure recordará estas credenciales y las usará para implementar cualquier aplicación de GitHub o BitBucket en el futuro. Sin embargo, si no ha hecho esto, se producirá un error en la implementación de la plantilla JSON cuando el administrador de recursos de Azure intente configurar el control de código fuente de la aplicación web porque no puede iniciar sesión en GitHub o BitBucket con las credenciales del propietario del repositorio.

## Comparación de la plantilla JSON con grupos de recursos implementados ##

Aquí puede consultar todas las hojas de la aplicación web en el [Portal de Azure](https://portal.azure.com/), pero hay otra herramienta también puede resultar útil, e incluso más. Vaya a la herramienta de vista previa [Explorador de recursos de Azure](https://resources.azure.com), que proporciona una representación JSON de todos los grupos de recursos en las suscripciones, tal y como existen en el backend de Azure. También puede ver cómo la jerarquía JSON del grupo de recursos en Azure se corresponde con la jerarquía del archivo de plantilla utilizado para crearlo.

Por ejemplo, si voy a la herramienta [Explorador de recursos de Azure](https://resources.azure.com) y expando los nodos en el explorador, puedo ver el grupo de recursos y los recursos de nivel de raíz que se engloban en sus respectivos tipos de recursos.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Si profundiza en una aplicación web, observará que los detalles de configuración de la misma son similares a los de la captura de pantalla siguiente:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Nuevamente, los recursos anidados deben tener una jerarquía muy similar a la de su archivo de plantilla JSON, y debería ver la configuración de la aplicación, las cadenas de conexión, etc. que se reflejan de forma adecuada en el panel JSON. La ausencia de configuración aquí puede indicar un problema con el archivo JSON y puede ayudarle a solucionar problemas con el archivo de plantilla JSON.

## Implementación de la plantilla de grupo de recursos manualmente ##

El botón **Implementación en Azure** es excelente, pero permite implementar la plantilla de grupo de recursos en azuredeploy.json sólo si ya se ha insertado azuredeploy.json en GitHub. El SDK .NET de Azure también proporciona las herramientas para implementar cualquier archivo de plantilla JSON directamente desde el equipo local. Para ello, siga estos pasos:

1.	En Visual Studio, haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2.	Haga clic en **Visual C#** > **Nube** > **Grupo de recursos de Azure** y, a continuación, haga clic en **Aceptar**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.	En **Seleccionar plantilla de Azure**, seleccione **Plantilla en blanco** y haga clic en **Aceptar**.

4.	Arrastre azuredeploy.json a la carpeta **Plantilla** del nuevo proyecto.

	![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.	En el Explorador de soluciones, abra el archivo azuredeploy.json copiado.

6.	Para ilustrar la demostración, vamos a agregar algunos recursos de Application Insights estándar a nuestro archivo JSON; para ello, hay que hacer clic en **Agregar recurso**. Si solo le interesa implementar el archivo JSON, omita los pasos de implementación.

	![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.	Seleccione **Application Insights para aplicaciones web**; a continuación, asegúrese de que hay un plan del Servicio de aplicaciones y una aplicación web seleccionados y, a continuación, haga clic en **Agregar**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

	Ahora ya podrá ver varios recursos nuevos que, en función del recurso de que se trate y de su función, dependerá del plan del Servicio de aplicaciones o de la aplicación web. Estos recursos no están habilitados por su definición existente y tiene que cambiar este ajuste.

	![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.	En el esquema de JSON, haga clic en **appInsights AutoScale** para resaltar su código JSON. Se trata de la configuración de escalado del plan del Servicio de aplicaciones.

9.	En el código resaltado de JSON, busque las propiedades `location` y `enabled` y configúrelas tal como se muestra a continuación.

	![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10.	En el esquema de JSON, haga clic en **CPUHigh appInsights** para resaltar su código JSON. Se trata de una alerta.

11.	Busque las propiedades `location` y `isEnabled` y configúrelas tal como se muestra a continuación. Haga lo mismo para las otras tres alertas (bombilla púrpura).

	![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12.	Ahora ya puede realizar la implementación. A continuación, haga clic con el botón secundario en el proyecto y seleccione **Implementar** > **Nueva implementación**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13.	Inicie sesión en su cuenta de Azure si aún no lo ha hecho.

14.	Seleccione un grupo de recursos existente en su suscripción o cree uno nuevo, seleccione **azuredeploy.json** y, a continuación, haga clic en **Editar parámetros**.

	![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

	Ahora podrá editar todos los parámetros definidos en el archivo de plantilla en una cómoda tabla. Los parámetros que definen los valores predeterminados ya tendrán sus valores predeterminados y los parámetros que definen una lista de valores permitidos se mostrarán como menús desplegables.

	![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15.	Rellene todos los parámetros vacíos y use la [dirección de repositorio de GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) en **repoUrl**. A continuación, haga clic en **Guardar**.
 
	![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

	>[AZURE.NOTE] El escalado automático es una característica que se ofrece en el nivel **estándar** o posterior, y las alertas para planes son características que se ofrecen en el nivel **básico** o posterior; deberá establecer el parámetro **sku** en **Estándar** o **Premium** para ver todos los nuevos detalles mejorados de App Insights.
	
16.	Haga clic en **Implementar**. Si ha seleccionado **Guardar contraseñas**, la contraseña se guardará en el archivo de parámetros como **texto sin formato**. De lo contrario, se le pedirá que escriba la contraseña de la base de datos durante el proceso de implementación.

¡Ya está! Ahora solo tiene que ir al [Portal de Azure](https://portal.azure.com/) y a la herramienta [Explorador de recursos de Azure](https://resources.azure.com) para ver la configuración de escalado automático y las nuevas alertas que se han agregado a la aplicación implementada con JSON.

Con los pasos de esta sección se consigue lo siguiente:

1.	Preparación del archivo de plantilla
2.	Creación de un archivo de parámetros con el archivo de plantilla
3.	Implementación del archivo de plantilla con el archivo de parámetros

El último paso se realiza fácilmente mediante un cmdlet de PowerShell. Para ver lo que Visual Studio hizo al implementar la aplicación, abra Scripts\\Deploy-AzureResourceGroup.ps1. Hay una gran cantidad de código, pero solo voy a resaltar todo el código pertinente que necesita para implementar el archivo de plantilla con el archivo de parámetros.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

El último cmdlet, `New-AzureResourceGroup`, es el que realmente realiza la acción. Todo esto debería demostrará que, con la ayuda de herramientas, es relativamente fácil implementar su aplicación de nube de manera predecible. Cada vez que ejecute el cmdlet en la misma plantilla con el mismo archivo de parámetros, obtendrá el mismo resultado.

## Resumen ##

En las operaciones de desarrollo, la repetibilidad y previsión son claves para cualquier implementación correcta de una aplicación de gran escala compuesta por microservicios. En este tutorial, ha implementado una aplicación de dos microservicios en Azure como un único grupo de recursos con la plantilla de Administrador de recursos de Azure. Espero que le haya aportado los conocimientos que necesita para iniciar la conversión de la aplicación de Azure a una plantilla a fin de que pueda aprovisionarla e implementarla de manera predecible.

## Pasos siguientes ##

Obtenga información sobre cómo [aplicar con facilidad metodologías ágiles y publicar continuamente la aplicación de microservicios](app-service-agile-software-development.md). Conozca además técnicas de impementación avanzadas como la [implementación de la distribución de paquetes piloto](app-service-web-test-in-production-controlled-test-flight.md).

<a name="resources"></a>
## Más recursos ##

-	[Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md)
-	[Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md)
-	[Funciones de la plantilla del Administrador de recursos de Azure](../resource-group-template-functions.md)
-	[Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../resource-group-template-deploy.md)
-	[Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)
-	[Solución de problemas de implementaciones de grupo de recursos en Azure](../virtual-machines/resource-group-deploy-debug.md)




 

<!---HONumber=AcomDC_0211_2016-->