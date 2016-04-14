<properties 
	pageTitle="Azure PowerShell con el Administrador de recursos | Microsoft Azure" 
	description="Introducción al uso de Azure PowerShell para implementar varios recursos como un grupo de recursos en Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Uso de Azure PowerShell con Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)

Administrador de recursos de Azure presenta un concepto totalmente nuevo acerca de los recursos de Azure. En lugar de crear y administrar recursos individuales, empiece por imaginar una solución entera, como un blog, una galería de fotos, un portal de SharePoint o un wiki. Use una plantilla, una representación declarativa de la solución, para crear un grupo de recursos que contiene todos los recursos que necesita para respaldar la solución. Luego, administre e implemente ese grupo de recursos como una unidad lógica.

En este tutorial, aprenderá a usar Azure PowerShell con el Administrador de recursos de Azure. Se explica el proceso de creación e implementación de un grupo de recursos para una aplicación web hospedada en Azure con una base de datos SQL, con todos los recursos que necesita para su funcionamiento.

## Requisitos previos

Para completar este tutorial, necesita:

- Una cuenta de Azure
  + Puede [abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) - Obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Sitios web. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.
  
  + Puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Su suscripción a MSDN le proporciona crédito todos los meses que puede usar con servicios de Azure de pago.
- Azure PowerShell 1.0. Para obtener más información sobre esta versión y cómo instalarla, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

Este tutorial está diseñado para los principiantes de PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones.

## Lo que implementará

En este tutorial, usará Azure PowerShell para implementar una aplicación web y una base de datos SQL. Sin embargo, esta solución de aplicación web y base de datos SQL se compone de varios tipos de recursos que funcionan conjuntamente. Los recursos reales que va a implementar son:

- Servidor SQL: para hospedar la base de datos.
- Base de datos SQL: para almacenar los datos.
- Reglas de firewall: para permitir que la aplicación web se conecte a la base de datos.
- Plan de servicio de aplicaciones: para definir las capacidades y el costo de la aplicación web.
- Sitio web: para ejecutar la aplicación web.
- Configuración web: para almacenar la cadena de conexión en la base de datos. 

## Obtención de ayuda sobre los cmdlets

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda con el cmdlet Get-AzureRmResource, escriba:

	Get-Help Get-AzureRmResource -Detailed

Para obtener una lista de los cmdlets del módulo Recursos con una sinopsis de ayuda, escriba:

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

El resultado será similar al siguiente extracto:

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Para obtener toda la ayuda posible para un cmdlet, escriba un comando con el formato:

	Get-Help <cmdlet-name> -Full
  
## Inicio de sesión en la cuenta de Azure

Antes de trabajar en la solución, debe iniciar sesión en su cuenta.

Para iniciar sesión en su cuenta de Azure, use el cmdlet **Login-AzureRmAccount**.

    PS C:\> Login-AzureRmAccount

El cmdlet pide las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

La configuración de la cuenta caduca, por lo que necesita actualizarla ocasionalmente. Para actualizar la configuración de cuenta, vuelva a ejecutar el cmdlet **Login-AzureRmAccount**.

>[AZURE.NOTE] Los módulos del Administrador de recursos requieren Login-AzureRmAccount. No basta con un archivo de configuración de publicación.

## Encontrar las ubicaciones de los tipos de recursos

Al implementar un recurso debe especificar la ubicación donde desea hospedarlo. No todas las regiones admiten todos los tipos de recursos. Antes de implementar la aplicación web y la base de datos SQL, debe averiguar qué regiones admiten dichos tipos. Un grupo de recursos puede contener recursos ubicados en diferentes regiones; sin embargo, siempre que sea posible, debe crear recursos en la misma ubicación para optimizar el rendimiento. En concreto, será conveniente asegurarse de que la base de datos está en la misma ubicación que la aplicación que accede a ella.

Para obtener las ubicaciones que admiten cada tipo de recurso, deberá usar el cmdlet **Get-AzureRmResourceProvider**. En primer lugar, veamos lo que devuelve este comando:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace representa una colección de tipos de recursos relacionados. Estos espacios de nombres normalmente coinciden correctamente con los servicios que quiere crear en Azure. Si desea usar un proveedor de recursos que se muestra como **No registrado**, puede registrarlo ejecutando el cmdlet **Register-AzureRmResourceProvider** y especificando el espacio de nombres del proveedor. Lo más probable es que el proveedor de recursos que use en este tutorial ya esté registrado en su suscripción.

Puede obtener más detalles sobre un proveedor mediante la especificación de ese espacio de nombres:

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Para limitar la salida a las ubicaciones compatibles para un tipo específico de recurso, como sitios web, use:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La salida debe ser similar a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Las ubicaciones que vea pueden ser ligeramente diferentes que los resultados anteriores. Los resultados podrían ser diferentes debido a que un administrador de su organización ha creado una directiva que limita qué regiones pueden usarse en su suscripción, o puede haber restricciones relacionadas con las políticas de impuestos de su país.

Vamos a ejecutar el mismo comando para la base de datos:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Parece que estos recursos están disponibles en muchas regiones. En este tema, usaremos **Oeste de EE. UU.**, pero puede especificar cualquiera de las regiones admitidas.

## Crear un grupo de recursos

Esta sección del tutorial le guía por el proceso de creación de un grupo de recursos. El grupo de recursos servirá de contenedor para todos los recursos de la solución que comparten el mismo ciclo de vida de ejemplo. Más adelante en el tutorial, implementará la aplicación web y la base de datos SQL en este grupo de recursos.

Para crear un grupo de recursos, use el cmdlet **New-AzureRmResourceGroup**.

El comando usa el parámetro **Name** para especificar un nombre para el grupo de recursos y el parámetro **Location** para definir su ubicación. Según lo que hemos descubierto en la sección anterior, usaremos "Oeste de EE. UU." para la ubicación.

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Su grupo de recursos se ha creado correctamente.


## Obtención de las versiones de API disponibles para los recursos

Cuando implemente una plantilla, debe especificar una versión de API que se usará para crear el recurso. Las versiones de API disponibles corresponden a versiones de las operaciones de API de REST que publica el proveedor de recursos. A medida que los proveedores de recursos habiliten nuevas características, lanzarán nuevas versiones de la API de REST. Por lo tanto, la versión de la API que especifica en la plantilla afecta a las propiedades que están disponibles cuando crea la plantilla. En general, seleccionará la versión de la API más reciente al crear nuevas plantillas. Para las plantillas existentes, puede decidir si quiere continuar usando una versión de la API que sabe que no cambiará su implementación, o bien actualizar la plantilla para que la versión más reciente aproveche las nuevas características.

Si bien este paso puede parecer confuso, detectar las versiones de la API que están disponibles para su recurso no es difícil. Usará de nuevo el comando **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Como puede ver, esta API se ha actualizado con frecuencia. Normalmente, los mismos números de versión de API estarán disponibles para todos los recursos de un proveedor de recursos. La única excepción es si un recurso se ha agregado o quitado en algún momento. Supondremos que están disponibles las mismas versiones de API para el recurso serverFarms; sin embargo, puede comprobar si un determinado recurso tiene una lista distinta de versiones de API disponibles.

Para la base de datos, verá:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Creación de la plantilla

En este tema no se muestra cómo crear una plantilla ni se analiza su estructura. Para obtener esa información, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md). La plantilla que implementará se muestra a continuación. Observe que la plantilla usa las versiones de API que recuperó en la sección anterior. Para asegurarse de que todos los recursos se encuentran en la misma región, usamos la expresión de plantilla **resourceGroup().location** para usar la ubicación del grupo de recursos.

Observe también la sección de parámetros. Esta sección define los valores que puede proporcionar al implementar los recursos. Estos valores se usarán más adelante en el tutorial.

Puede copiar la plantilla y guardarla localmente como un archivo .json. En este tutorial, supondremos que la ha guardado en c:\\Azure\\Templates\\azuredeploy.json, pero puede guardarla en cualquier ubicación que le resulte adecuada y con el nombre que tenga sentido para sus necesidades.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Implementación de la plantilla

Tiene el grupo de recursos y tiene la plantilla, así que ahora está listo para implementar en el grupo de recursos la infraestructura definida en la plantilla. Los recursos se implementan con el cmdlet **New-AzureRmResourceGroupDeployment**. La sintaxis básica se parece a esta:

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Especifique el grupo de recursos y la ubicación de la plantilla. Si la plantilla no es local, puede usar el parámetro **- TemplateUri** y especificar un URI para la plantilla. Puede establecer el parámetro **-Mode** en **Incremental** o **Complete**. De forma predeterminada, el Administrador de recursos realiza una actualización incremental durante la implementación; por lo tanto, no es esencial establecer **-Mode** cuando desee **Incremental**. Para comprender las diferencias entre estos modos de implementación, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

###Parámetros dinámicos de la plantilla

Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, la analiza y agrega los parámetros al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, PowerShell le pide dicho valor.

A continuación se muestra el comando completo con parámetros incluidos. Puede proporcionar sus propios valores para los nombres de los recursos.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

Al escribir el comando, se le pide el parámetro obligatorio que falta, que es **administratorLoginPassword**. Y, al escribir la contraseña, el valor de cadena segura se oscurece. Esta estrategia elimina el riesgo de ofrecer una contraseña en texto sin formato.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si la plantilla incluye un parámetro con un nombre que coincide con el de uno de los parámetros del comando utilizado para implementar la plantilla (como cuando incluye un parámetro denominado **ResourceGroupName** en la plantilla y este es el mismo que el parámetro **ResourceGroupName** del cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), se le pedirá que proporcione un valor para un parámetro con el sufijo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre de los parámetros utilizados para operaciones de implementación.

El comando se ejecuta y devuelve mensajes cuando se crean los recursos. En última instancia, verá el resultado de la implementación.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

Con tan solo unos pasos, hemos creado e implementado los recursos necesarios para un sitio web complejo.

## Obtención de información acerca de los grupos de recursos

Después de crear un grupo de recursos, puede usar los cmdlets del módulo Administrador de recursos para administrar los grupos de recursos.

- Para obtener todos los grupos de recursos de la suscripción, use el cmdlet **Get-AzureRmResourceGroup**:

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      Si desea obtener un grupo de recursos determinado, proporcione el parámetro **Name**.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- Para obtener los recursos del grupo de recursos, use el cmdlet **Get-AzureRmResource** y su parámetro **ResourceGroupNameContains**. Sin parámetros, Find-AzureRmResource obtiene todos los recursos de la suscripción de Azure.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- En la plantilla anterior se incluye una etiqueta en un recurso. Puede usar etiquetas para organizar lógicamente los recursos de la suscripción. Utilice los comandos **Find-AzureRmResource** y **Find-AzureRmResourceGroup** para consultar los recursos por etiquetas.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      Hay mucho más que puede hacer con las etiquetas. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

## Incorporación a un grupo de recursos

Para agregar un recurso al grupo de recursos, puede usar el cmdlet **New-AzureRmResource**. Sin embargo, agregar un recurso de esta manera puede dar pie a confusión en un futuro porque el nuevo recurso no existe en la plantilla. Si volviera a implementar la plantilla antigua, implementaría una solución incompleta. Si implementa con frecuencia, le resultará más fácil y más confiable agregar el recurso nuevo a la plantilla y volver a implementarla.

## Movimiento de un recurso

Puede mover recursos existentes a un nuevo grupo de recursos. Para ver ejemplos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

## Eliminación de un grupo de recursos

- Para eliminar un recurso del grupo de recursos, use el cmdlet **Remove-AzureRmResource**. Este cmdlet elimina el recurso, pero no el grupo de recursos.

	Este comando quita el sitio web TestSite del grupo de recursos TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para eliminar un grupo de recursos, use el cmdlet **Remove-AzureRmResourceGroup**. Este cmdlet elimina el grupo de recursos y sus recursos.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Pasos siguientes

- Para más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para obtener información sobre cómo implementar plantillas, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resource-group-template-deploy.md).
- Para ver un ejemplo detallado de cómo implementar un proyecto, consulte [Implementación predecible de microservicios en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para información sobre la solución de problemas de una implementación que da error, consulte [Solución de problemas de implementaciones de grupos de recursos en Azure](./virtual-machines/resource-group-deploy-debug.md).

<!---HONumber=AcomDC_0309_2016-->