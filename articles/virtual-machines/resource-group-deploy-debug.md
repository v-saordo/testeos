<properties
   pageTitle="Solución de problemas de implementaciones de grupo de recursos | Microsoft Azure"
   description="Describe los problemas comunes al implementar los recursos creados mediante el modelo de implementación del Administrador de recursos y muestra cómo detectar y corregir estos problemas."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="01/28/2016"
   ms.author="tomfitz;rasquill"/>

# Solución de problemas de implementaciones de grupo de recursos en Azure

Cuando se produce un problema durante la implementación, necesita descubrir qué salió mal. El Administrador de recursos ofrece dos maneras de averiguar qué ocurrió y por qué. Puede usar comandos de implementación para recuperar información sobre implementaciones concretas en un grupo de recursos. O bien, puede usar los registros de auditoría para recuperar información sobre todas las operaciones realizadas en un grupo de recursos. Con esta información, puede corregir el problema y reanudar las operaciones de la solución.

Este tema se centra principalmente en el uso de comandos de implementación para solucionar problemas de implementaciones. Para obtener información sobre el uso de los registros de auditoría para realizar el seguimiento de todas las operaciones en los recursos, consulte [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md).

En este tema se muestra cómo recuperar información de solución de problemas a través de Azure PowerShell, CLI de Azure y API de REST. Para obtener información sobre el uso del portal para solucionar problemas de implementación, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

En este tema también se describen las soluciones a los errores comunes que los usuarios encuentran.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica. No puede crear grupos de recursos con el modelo de implementación clásica.


## Solución de problemas de PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Puede obtener el estado general de una implementación con el comando **Get-AzureRmResourceGroupDeployment**. En el ejemplo siguiente la implementación produjo un error.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

Cada implementación normalmente se compone de varias operaciones y cada operación representa un paso en el proceso de implementación. Para detectar qué salió mal con una implementación, normalmente es necesario ver los detalles de las operaciones de implementación. Puede ver el estado de las operaciones con **Get-AzureRmResourceGroupDeploymentOperation**.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

Muestra dos operaciones en la implementación. En una, el estado de aprovisionamiento es de error y en la otra, es de correcto.

Puede recuperar el mensaje de estado con el comando siguiente:

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Solución de problemas de CLI de Azure

Puede obtener el estado general de una implementación con el comando **azure group deployment show**. En el ejemplo siguiente la implementación produjo un error.

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


Puede obtener más información acerca de por qué falló la implementación en los registros de auditoría. Para ver los registros de auditoría, ejecute el comando **azure group log show**. Puede incluir la opción **--last- deployment** para recuperar solo el registro de la implementación más reciente.

    azure group log show ExampleGroup --last-deployment

El comando **azure group log show** puede devolver mucha información. Para solucionar problemas, lo habitual es centrarse en las operaciones que produjeron un error. El script siguiente usa la opción **--json** y **jq** para buscar el registro de errores de implementación. Para obtener información sobre herramientas como **jq**, consulte [Herramientas útiles para interactuar con Azure](#useful-tools-to-interact-with-azure)

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

Puede ver que **properties** incluye información de json sobre la operación con error.

Puede usar las opciones **--verbose** y **-vv** para ver más información de los registros. Use la opción **--verbose** para mostrar los pasos que recorren las operaciones en `stdout`. Para ver un historial completo de la solicitud, use la opción **-vv**. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

## Solución de problemas de la API de REST

La API de REST de Administrador de recursos proporciona identificadores URI para recuperar información sobre una implementación, las operaciones de implementación y los detalles sobre una operación determinada. Para consultar descripciones completas de estos comandos, consulte:

- [Obtener información acerca de una implementación de plantilla.](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Lista de todas las operaciones de implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Obtener información acerca de una operación de implementación de plantilla.](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Actualización de credenciales expiradas

La implementación producirá un error si las credenciales de Azure expiraron o si no se suscribió a su cuenta de Azure. Las credenciales pueden expirar si la sesión está abierta demasiado tiempo. Puede actualizar las credenciales con las siguientes opciones:

- Para PowerShell, use el cmdlet **Login-AzureRmAccount**. Las credenciales de un archivo de configuración de publicación no son suficientes para los cmdlets del módulo AzureResourceManager.
- Para CLI de Azure, use **azure login**. Para obtener ayuda con errores de autenticación, asegúrese de que ha [configurado correctamente la CLI de Azure](../xplat-cli-connect.md).

## Comprobación del formato de plantillas y parámetros

Si el archivo de plantilla o de parámetro no está en el formato correcto, se producirá un error en la implementación. Antes de ejecutar una implementación, puede probar la validez de la plantilla y los parámetros.

### PowerShell

Para PowerShell, use **Test-AzureRmResourceGroupDeployment**.

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### CLI de Azure

Para CLI de Azure, use **azure group template validate <resource group>**

En el ejemplo siguiente se muestra cómo validar una plantilla y los parámetros necesarios. La CLI de Azure solicita valores de parámetros que son necesarios.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### API de REST

Para la API de REST, consulte [Validación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Comprobación de las ubicaciones que admiten el recurso

Al especificar la ubicación para un recurso, debe usar una de las ubicaciones que admiten el recurso. Antes de especificar una ubicación para un recurso, use uno de los comandos siguientes para comprobar si la ubicación admite el tipo de recurso.

### PowerShell

Para las versiones de PowerShell anteriores a la versión 1.0, puede consultar la lista completa de recursos y ubicaciones con el comando **Get-AzureLocation**.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

Puede especificar un determinado tipo de recurso con:

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

Para la versión 1.0 de PowerShell, use **Get-AzureRmResourceProvider** para obtener ubicaciones admitidas.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Puede especificar un determinado tipo de recurso con:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

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

### Azure CLI

Para CLI de Azure, puede usar **azure location list**. Dado que la lista de ubicaciones puede ser larga y hay muchos proveedores, puede utilizar herramientas para examinar los proveedores y las ubicaciones antes de utilizar una ubicación que aún no esté disponible. El siguiente script usa **jq** para detectar las ubicaciones donde está disponible el proveedor de recursos para Máquinas virtuales de Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API de REST

Para la API de REST, consulte [Obtención de información sobre un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Creación de nombres de recursos únicos

Para algunos recursos, sobre todo cuentas de almacenamiento, servidores de base de datos y sitios web, debe proporcionar un nombre para el recurso que sea único en todo Azure. Actualmente, no hay ninguna manera de probar si un nombre es único. Se recomienda usar una convención de nomenclatura cuyo uso sea improbable en otras organizaciones.

## Problemas de autenticación, suscripción, rol y cuota

Puede haber uno o varios problemas que impiden la correcta implementación y que guardan relación con la autenticación y la autorización y Azure Active Directory. Sin importar cómo administra sus grupos de recursos de Azure, la identidad que usa para iniciar sesión en su cuenta debe ser un objeto de Azure Active Directory. Esta identidad puede ser una cuenta profesional o educativa que ha creado o que se le ha asignado, o puede crear una entidad de servicio para aplicaciones.

Sin embargo, Azure Active Directory permite al usuario o al administrador controlar qué identidades pueden tener acceso a qué recursos con un alto grado de precisión. Si se producen errores en las implementaciones, examine las propias solicitudes en busca de indicios de problemas de autenticación o autorización, y examine también los registros de implementación para el grupo de recursos. Observará que, aunque tiene permisos para algunos recursos, no tiene permisos para otros. Con la CLI de Azure, puede examinar los inquilinos de Azure Active Directory y los usuarios que usen los comandos `azure ad`. (Para obtener una lista completa de los comandos de CLI de Azure, consulte [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md)).

También puede tener problemas cuando una implementación llega a cuota predeterminada, que podría haberse establecido por grupo de recursos, suscripciones, cuentas, etc. Confirme que dispone de los recursos disponibles para implementar correctamente. Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

Para examinar las cuotas de su propia suscripción para núcleos, debería usar el comando `azure vm list-usage` en la CLI de Azure y el cmdlet **Get-AzureRmVMUsage** de PowerShell. A continuación se muestra el comando en la CLI de Azure y la cuota de núcleos para una cuenta de evaluación gratuita es 4:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si tuviera que intenta implementar una plantilla que crea más de 4 núcleos en la región occidental de Estados Unidos en la suscripción anterior, obtendría un error de implementación que podría ser similar al siguiente (en el portal o investigando los registros de implementación):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

En estos casos, debe ir al portal y archivar un problema de soporte técnico para aumentar su cuota para la región en la que desea realizar la implementación.

> [AZURE.NOTE] Recuerde que para los grupos de recursos, la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en el oeste de Estados Unidos, debe pedir 30 núcleos de administrador de recursos en el oeste de Estados Unidos. Si necesita implementar 30 núcleos en cualquiera de las regiones para las que tiene acceso, debe pedir 30 núcleos de administrador de recursos en todas las regiones. 
<!-- --> 
Para ser específicos sobre núcleos, por ejemplo, puede comprobar las regiones para las que debe solicitar la cantidad adecuada de cuota mediante el comando siguiente, que se canaliza en **jq** para el análisis de json. El proveedor de Azure de
<!-- -->
		muestra Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' 
		{ 
		 "name": "virtualMachines", 
		  "apiVersions": [ 
		    "2015-05-01-preview", 
		    "2014-12-01-preview" 
		],
		"locations": [
		  "Este de Estados Unidos", 
		  "Oeste de Estados Unidos", 
		  "Europa occidental", 
		  "Asia oriental", 
		  "Sudeste de Asia" 
		 ]
        }


## Comprobación del registro del proveedor de recursos

Los recursos son administrados por los proveedores de recursos, y es posible que una cuenta o suscripción esté habilitada para utilizar un proveedor determinado. Si están habilitadas para utilizar un proveedor, también se deben registrar para su uso. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos.

### PowerShell

Para obtener una lista de proveedores de recursos y su estado de registro, use **Get-AzureProvider** para las versiones de PowerShell anteriores a la versión 1.0.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Para registrar un proveedor, use **Register-AzureProvider**.

Para la versión 1.0 de PowerShell, use **Get-AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Para registrar un proveedor, use **Register-AzureRmResourceProvider**.

### Azure CLI

Para ver si el proveedor está registrado para su uso con la CLI de Azure, use el comando `azure provider list` (lo siguiente es un ejemplo truncado del resultado).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Nuevamente, si desea obtener más información sobre los proveedores, incluida su disponibilidad regional, escriba `azure provider list --json`. Lo siguiente selecciona solo la primera de ellas en la lista que se va a ver:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

Si un proveedor requiere el registro, use el comando `azure provider register <namespace>`, donde el valor *namespace* proviene de la lista anterior.

### API de REST

Para obtener el estado de registro, consulte [Obtención de información sobre un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Para registrar un proveedor, consulte [Registro de una suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Descripción de una implementación correcta para las plantillas personalizadas

Si utiliza las plantillas que ha creado, es importante comprender que el sistema de Administrador de recursos de Azure informa de que una implementación se ha completado correctamente cuando todos los proveedores terminan la implementación correctamente. Esto significa que todos los elementos de plantilla se implementaron para su uso.

Sin embargo, tenga en cuenta que esto no significa necesariamente que el grupo de recursos esté "activo y listo para sus usuarios". Por ejemplo, la mayoría de las implementaciones solicitan a la implementación que descargue actualizaciones, que espere en otros recursos no son plantillas o que instale complejos scripts o alguna otra actividad ejecutable que Azure no conoce porque no es una actividad de la que un proveedor esté realizando seguimiento. En estos casos, puede trascurrir algo de tiempo antes de que los recursos estén disponibles para su utilización por parte de todos los usuarios. Por ello, debe esperar a cierto tiempo tras la confirmación del estado de implementación antes de que la implementación se pueda utilizar realmente.

Sin embargo, puede evitar que Azure informe de que la implementación se produjo correctamente mediante la creación de un script personalizado para su plantilla personalizada (usando por ejemplo [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)) que sepa cómo supervisar toda la implementación para la preparación de todo el sistema y se devuelva correctamente solo cuando los usuarios puedan interactuar con toda la implementación. Si desea asegurarse de que su extensión es la última en ejecutarse, utilice la propiedad **dependsOn** de la plantilla. Se puede ver un ejemplo al [crear implementaciones de plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Herramientas útiles para interactuar con Azure
Cuando trabaje con los recursos de Azure desde la línea de comandos, recopilará herramientas que le ayudarán a realizar su trabajo. Las plantillas de grupo de recursos de Azure son documentos JSON, y la API de administración de recursos de Azure acepta y devuelve JSON; así pues, las herramientas de análisis de JSON son uno de los primeros utensilios que podrán ayudarle a navegar por la información sobre los recursos, así como diseñar o interactuar con plantillas y archivos de parámetros de plantilla.

### Herramientas de Windows, Linux y Mac
Si usa la interfaz de línea de comandos de Azure para Mac, Linux y Windows, probablemente esté familiarizado con las herramientas estándar de descarga como **[curl](http://curl.haxx.se/)** y **[wget](https://www.gnu.org/software/wget/)** o **[Resty](https://github.com/beders/Resty)**, y las utilidades de JSON como **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** y las bibliotecas de lenguaje que sirven para administrar JSON correctamente. (Muchas de estas herramientas también tienen puertos para Windows, como [wget](http://gnuwin32.sourceforge.net/packages/wget.htm); de hecho, hay varias formas de conseguir que Linux y otras herramientas de software de código abierto se ejecuten también en Windows).

Este tema incluye algunos comandos de CLI de Azure que puede utilizar con **jq** para obtener exactamente la información que desee de forma más eficaz. Debe elegir el conjunto de herramientas que le resulte cómodo para comprender el uso de los recursos de Azure.

### PowerShell

PowerShell tiene varios comandos básicos para realizar las mismas acciones.

- Utilice el cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** para descargar archivos como plantillas de grupo de recursos o archivos JSON de parámetros.
- Use el cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** para convertir una cadena JSON en un objeto personalizado ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) que tiene una propiedad para cada campo de la cadena JSON.


## Pasos siguientes

Para dominar la creación de plantillas, lea [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md) y visite el [repositorio de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) para obtener ejemplos implementables. Un ejemplo de la propiedad **dependsOn** es [Creación de una máquina virtual con varias NIC y con acceso a RDP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!----HONumber=AcomDC_0204_2016-->