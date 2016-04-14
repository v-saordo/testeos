<properties
	pageTitle="Automatización de DocumentDB - Administrador de recursos - CLI | Microsoft Azure"
	description="Use la CLI o plantillas del Administrador de recursos de Azure para implementar una cuenta de base de datos de DocumentDB. DocumentDB es una base de datos NoSQL basada en la nube para datos JSON."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="mimig"/>

# Automatización de la creación de cuentas de DocumentDB con la CLI de Azure y plantillas del Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

En este artículo se muestra cómo crear una cuenta de DocumentDB con la interfaz de línea de comandos (CLI) de Azure o con plantillas del Administrador de recursos de Azure. Para crear una cuenta de DocumentDB mediante el Portal de Azure, consulte [Creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

- [Creación de una cuenta de DocumentDB con la CLI](#quick-create-documentdb-account)
- [Creación de una cuenta de DocumentDB con una plantilla ARM](#deploy-documentdb-from-a-template)

Las cuentas de base de datos de DocumentDB son actualmente el único recurso de DocumentDB que se puede crear con plantillas ARM y la CLI de Azure.

## Preparación

Para poder usar la CLI de Azure con grupos de recursos de Azure, necesitará la versión correcta de la CLI de Azure y una cuenta de Azure. Si no tiene la CLI de Azure, debe [instalarla](../xplat-cli-install.md).

### Actualización de la versión de la CLI de Azure

En el símbolo del sistema, escriba `azure --version` para ver si ya tiene instalada la versión 0.9.11 o una posterior.

	azure --version
    0.9.11 (node: 0.12.7)

Si la versión no es 0.9.11 o una posterior, tendrá que [instalar la CLI de Azure](../xplat-cli-install.md) o actualizarla mediante uno de los instaladores nativos o a través de **npm**. Escriba `npm update -g azure-cli` para actualizarla o `npm install -g azure-cli` para instalarla.

### Definición de su cuenta y suscripción de Azure

Si aún no tiene una suscripción a Azure pero sí una suscripción a Visual Studio, puede activar sus [ventajas para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). O puede suscribirse a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para usar plantillas de Administración de recursos de Azure, debe tener una identidad de cuentas Microsoft o de cuenta profesional o educativa. Si tiene una de estas cuentas, escriba el siguiente comando.

	azure login

Lo que genera el siguiente resultado:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE] Si no tiene una cuenta de Azure, verá un mensaje de error que indica que necesita un otro tipo de cuenta. Para crear una a partir su cuenta de Azure actual, consulte [Crear una identidad profesional o educativa en Azure Active Directory](../virtual-machines/resource-group-create-work-id-from-personal.md).

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en un explorador y escriba el código proporcionado en la salida del comando.

![Captura de pantalla que muestra la pantalla de inicio de sesión del dispositivo para la CLI de Microsoft Azure](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Una vez escrito el código, seleccione la identidad que quiere usar en el explorador y, si es necesario, indique su nombre de usuario y contraseña.

![Captura de pantalla que muestra en donde debe seleccionar la cuenta de identidad de Microsoft asociada a la suscripción de Azure que quiere usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Obtendrá la siguiente pantalla de confirmación cuando se haya conectado; luego puede cerrar la ventana del explorador.

![Captura de pantalla que muestra la confirmación del inicio de sesión en la interfaz de línea de comandos multiplataforma de Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

El shell de comandos también proporciona el siguiente resultado.

    -info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Además del método de inicio de sesión interactivo que se describe aquí, existen otros métodos de inicio de sesión en la CLI de Azure. Para más información sobre otros métodos y sobre el control de varias suscripciones, consulte [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md).

### Cambio al modo de grupo de recursos de CLI de Azure

De manera predeterminada, la CLI de Azure se inicia en el modo de administración de servicios (modo **asm**). Escriba lo siguiente para cambiar al modo de grupo de recursos.

	azure config mode arm

Lo que genera el siguiente resultado:

	info:    New mode is arm

Puede volver a cambiar al conjunto predeterminado de comandos escribiendo `azure config mode asm`.

## <a id="quick-create-documentdb-account"></a>Tarea: Creación de una cuenta de DocumentDB con la CLI de Azure

Siga las instrucciones de esta sección para crear una cuenta de DocumentDB con la CLI de Azure.

### Paso 1: Creación o recuperación del grupo de recursos

Para crear una cuenta de DocumentDB, necesita primero un grupo de recursos. Si ya conoce el nombre del grupo de recursos que quiere usar, vaya al [paso 2](#create-documentdb-account-cli).

Para revisar una lista de todos los grupos de recursos actuales, ejecute el siguiente comando y tome nota del nombre del grupo de recursos que quiere usar:

    azure group list

Para crear un grupo de recursos, ejecute el siguiente comando, especifique el nombre del nuevo grupo de recursos que se va crear y la región en la que se va a crear:

	azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto. 
 - `<resourcegrouplocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

Entrada de ejemplo:

	azure group create new_res_group westus

Lo que genera el siguiente resultado:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).

### <a id="create-documentdb-account-cli"></a> Paso 2: Creación de una cuenta de DocumentDB con la CLI

Cree una cuenta de DocumentDB en el grupo de recursos nuevo o existente escribiendo el siguiente comando en el símbolo del sistema:

> [AZURE.TIP] Si ejecuta este comando en Azure PowerShell o Windows PowerShell, recibirá un error sobre un token inesperado. En su lugar, ejecute este comando en el símbolo del sistema de Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto. 
 - En `<databaseaccountname>`, solo se pueden usar minúsculas, números y el carácter "-"; y debe tener entre 3 y 50 caracteres.
 - `<databaseaccountlocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

Entrada de ejemplo:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

Lo que genera el siguiente resultado cuando se aprovisiona la nueva cuenta:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).

Después de que el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB**.

## <a id="deploy-documentdb-from-a-template"></a>Tarea: Creación de una cuenta de DocumentDB con una plantilla ARM

Siga las instrucciones de esta sección para crear una cuenta de DocumentDB con una plantilla del Administrador de recursos de Azure (ARM) y un archivo opcional de parámetros, ambos son archivos JSON. El uso de una plantilla le permite describir exactamente lo que desea y repetirlo sin errores.

### Descripción de plantillas ARM y grupos de recursos

La mayoría de las aplicaciones se desarrollan a partir de una combinación de distintos tipos de recursos (por ejemplo, una o varias cuentas de DocumentDB, cuentas de almacenamiento, una red virtual o una red de entrega de contenido). La API de administración de servicios de Azure predeterminada y el portal de Azure clásico representan estos elementos mediante un enfoque de servicio por servicio, que requiere implementar y administrar servicios individuales (o buscar otras herramientas que lo hagan) y no como una unidad lógica de implementación.

*Las plantillas del Administrador de recursos de Azure* permiten implementar y administrar estos recursos diferentes como una unidad lógica de implementación de manera declarativa. En lugar de indicar imperativamente a Azure que debe implementar un comando tras otro, describa la implementación completa en un archivo JSON (todos los recursos y configuración asociada y parámetros de implementación) e indíquele a Azure que implemente esos recursos como un único grupo.

Puede aprender mucho más acerca de los grupos de recursos de Azure y su utilidad en [Información general del Administrador de recursos de Azure](../resource-group-overview.md). Si le interesa la creación de plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

### Paso 1: Creación de un archivo de plantilla y de parámetros

Cree un archivo de plantilla local con el siguiente contenido. Asigne al archivo el nombre azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Esta plantilla solo requiere un parámetro, el nombre de cuenta de base de datos que se va a crear. La ubicación de la nueva cuenta de base de datos se establece en la misma ubicación que el grupo de recursos.

Dado que la plantilla solo toma un parámetro, puede escribir el valor en la línea de comandos o crear un archivo de parámetros para especificar el valor.

Para crear un archivo de parámetros, copie el siguiente contenido en un nuevo archivo y asigne al archivo el nombre azuredeploy.parameters.json. Si va a especificar el nombre de cuenta de base de datos en el símbolo del sistema, puede continuar sin crear este archivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

En el archivo azuredeploy.parameters.json, actualice el valor "samplearmacct" al nombre de base de datos que desee usar y después guarde el archivo. `<databaseAccountName>` solo admite minúsculas, números, el carácter "-", y debe tener entre 3 y 50 caracteres.

### Paso 2: Creación o recuperación del grupo de recursos

Para crear una cuenta de DocumentDB, necesita primero un grupo de recursos. Si ya conoce el nombre del grupo de recursos que desea usar, asegúrese de que la ubicación sea una [región donde DocumentDB esté disponible de forma general](https://azure.microsoft.com/regions/#services) y después vaya al [paso 3](#create-account-from-template). En la plantilla, la ubicación de la cuenta se crea en la misma región que el grupo de recursos, por lo que intentar crear una cuenta en una región donde DocumentDB no esté disponible dará lugar a un error de implementación.

Para revisar una lista de todos los grupos de recursos actuales, ejecute el siguiente comando y tome nota del nombre del grupo de recursos que quiere usar:

    azure group list

Para crear un grupo de recursos, ejecute el siguiente comando, especifique el nombre del nuevo grupo de recursos que se va crear y la región en la que se va a crear:

	azure group create <resourcegroupname> <databaseaccountlocation>

 - `<resourcegroupname>` solo admite caracteres alfanuméricos, puntos, caracteres de subrayado, el carácter "-" y paréntesis, y no puede terminar en punto. 
 - `<databaseaccountlocation>` debe ser una de las regiones en que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

Entrada de ejemplo:

	azure group create new_res_group westus

Lo que genera el siguiente resultado:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).

### <a id="create-account-from-template"></a>Paso 3: Creación de una cuenta de DocumentDB con una plantilla ARM

Para crear una cuenta de DocumentDB en el grupo de recursos, ejecute el comando siguiente e indique la ruta de acceso al archivo de plantilla, la ruta de acceso al archivo de parámetros o el valor del parámetro, el nombre del grupo de recursos en el que se va a implementar y un nombre de implementación (-n es opcional).

Para usar un archivo de parámetros:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` es la ruta de acceso al archivo azuredeploy.json creado en el paso 1.
 - `<PathToParameterFile>` es la ruta de acceso al archivo azuredeploy.parameters.json creado en el paso 1.
 - `<resourcegroupname>` es el nombre del grupo de recursos existente en el que se va a agregar una cuenta de base de datos de DocumentDB. 
 - `<deploymentname>` es el nombre opcional de la implementación.

Entrada de ejemplo:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

O bien, para especificar el parámetro de nombre de cuenta de base de datos sin un archivo de parámetros y que en su lugar se le pida el valor, ejecute el siguiente comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Entrada de ejemplo que muestra el símbolo del sistema y una entrada para una cuenta de base de datos denominada new\_db\_acct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Cuando se haya aprovisionado la cuenta, aparecerá la siguiente información:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    location             String  West US
    info:    group deployment create command OK

Si se producen errores, consulte [Solución de problemas](#troubleshooting).

Después de que el comando responda, la cuenta se encontrará en el estado **Creando** unos minutos antes de pasar al estado **En línea**, en el que está lista para usarse. Puede comprobar el estado de la cuenta en el [Portal de Azure](https://portal.azure.com), en la hoja **Cuentas de DocumentDB**.

## Solución de problemas

Si recibe errores como `Deployment provisioning state was not successful` al crear la cuenta de base de datos o de grupo de recursos, dispone de varias opciones de solución de problemas.

> [AZURE.NOTE] Si se proporcionan caracteres incorrectos en el nombre de la cuenta de base de datos o una ubicación en la que DocumentDB no está disponible, esto causará errores de implementación. Los nombres de cuenta de base de datos solo pueden incluir minúsculas, números y el carácter "-"; y deben tener entre 3 y 50 caracteres. Se enumeran todas las ubicaciones de cuenta de base de datos válidas en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

- Si la salida contiene lo siguiente: `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, revise la información del error en el archivo azure.err.

- Puede encontrar información útil en el archivo de registro para el grupo de recursos. Para ver el archivo de registro, ejecute el siguiente comando:

    	azure group log show <resourcegroupname> --last-deployment

    Entrada de ejemplo:

    	azure group log show new_res_group --last-deployment

    Después, consulte [Solución de problemas de implementaciones de grupo de recursos en Azure](../virtual-machines/resource-group-deploy-debug.md) para más información.

- También hay información del error disponible en el Portal de Azure, como se muestra en la captura de pantalla siguiente. Para navegar a la información de error: haga clic en Grupos de recursos en la barra de salto, seleccione el grupo de recursos que tuvo el error. En el área Essentials de la hoja Grupo de recursos, haga clic en la fecha de la última implementación. En la hoja Historial de implementaciones, seleccione la implementación con errores. En la hoja Implementación, haga clic en los detalles de la operación con el signo de exclamación rojo. El mensaje de estado para la implementación con errores se muestra en la hoja Detalles de la operación.

    ![Captura de pantalla del Portal de Azure que muestra cómo navegar hasta el mensaje de error de implementación](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Pasos siguientes

Ahora que tiene una cuenta de DocumentDB, el siguiente paso es crear una base de datos de DocumentDB. Puede crear una base de datos mediante uno de los siguientes procedimientos:

- El Portal de Azure, tal como se describe en [Cómo crear una base de datos para DocumentDB](documentdb-create-database.md).
- Los ejemplos de C# .NET en el proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repositorio [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) de GitHub.
- Los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. 

Después de crear la base de datos, tendrá que [agregar una o más colecciones](documentdb-create-collection.md) a ella y después [agregar documentos](documentdb-view-json-document-explorer.md) a las colecciones.

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-queries) en los documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) en el Portal de vista previa, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para obtener más información acerca de DocumentDB, explore estos recursos:

-	[Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)

Para obtener más plantillas que puede usar, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0128_2016-->