<properties
	pageTitle="Implementación de una aplicación web con MSDeploy con un nombre de host personalizado y un certificado SSL"
	description="Use una plantilla del Administrador de recursos de Azure para implementar una aplicación web mediante MSDeploy y configurar un nombre de host personalizado y un certificado SSL"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="john.dehavilland"/>

# Implementación de una aplicación web con MSDeploy, un nombre de host personalizado y un certificado SSL

En esta guía, se recorre el proceso de crear una implementación integral para una aplicación web de Azure, sacar provecho de MSDeploy, así como agregar un nombre de host personalizado y un certificado SSL a la plantilla de ARM.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

###Creación de la aplicación de ejemplo

Va a implementar una aplicación web ASP.NET. El primer paso consiste en crear una aplicación web sencilla (o puede elegir usar una existente, en cuyo caso puede omitir este paso).

Abra Visual Studio 2015 y seleccione Archivo > Nuevo proyecto. En el cuadro de diálogo que aparece, elija Web > Aplicación web ASP.NET. En Plantillas, elija Web y después la plantilla MVC. En _Cambiar tipo de autenticación_, seleccione _Sin autenticación_. Esto es solo para hacer la aplicación de ejemplo tan sencilla como sea posible.

En este punto, tendrá una aplicación web ASP.NET básica lista para usarla como parte del proceso de implementación.

###Creación del paquete MSDeploy

El siguiente paso consiste en crear el paquete para implementar la aplicación web en Azure. Para ello, guarde el proyecto y después ejecute lo siguiente en la línea de comandos:

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Esto creará un paquete comprimido en la carpeta PackageLocation. La aplicación está lista para implementarse y ahora puede crear una plantilla del Administrador de recursos de Azure para hacerlo.

###Creación de la plantilla de ARM
En primer lugar, vamos a comenzar con una plantilla de ARM básica que creará una aplicación web y un plan de hospedaje (tenga en cuenta que no se muestran los parámetros ni las variables por brevedad).

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

A continuación, debe modificar el recurso de la aplicación web para que admita un recurso de MSDeploy anidado. Esto le permitirá hacer referencia al paquete creado antes e indicar al Administrador de recursos de Azure que use MSDeploy para implementar el paquete en la aplicación web de Azure. A continuación, se muestra el recurso Microsoft.Web/sites con el recurso de MSDeploy anidado:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Ahora verá que el recurso de MSDeploy admite una propiedad **packageUri** que se define como sigue:

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Esta propiedad **packageUri** admite el identificador URI de la cuenta de almacenamiento que apunta a la cuenta de almacenamiento donde se cargará el archivo comprimido del paquete. El Administrador de recursos de Azure aprovechará las [firmas de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) para descargar el paquete localmente desde la cuenta de almacenamiento cuando se implemente la plantilla. Este proceso se automatizará mediante un script de PowerShell que cargará el paquete y llamará a la API de administración de Azure para crear las claves requeridas y pasarlas a la plantilla como parámetros (*\_artifactsLocation* y *\_artifactsLocationSasToken*). Debe definir los parámetros de la carpeta y el nombre de archivo donde se carga el paquete en el contenedor de almacenamiento.

Después, debe agregar otro recurso anidado para configurar los enlaces de nombre de host para sacar provecho de un dominio personalizado. En primer lugar, deberá asegurarse de que posee el nombre de host y configurarlo para que Azure compruebe que en efecto lo posee; consulte [Configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md). Una vez hecho esto, puede agregar lo siguiente a la plantilla en la sección de recursos Microsoft.Web/sites:

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

Por último, debe agregar otro recurso de nivel superior, Microsoft.Web/certificates. Este recurso contendrá su certificado SSL y se encontrará en el mismo nivel que la aplicación web y el plan de hospedaje.

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

Debe tener un certificado SSL válido para configurar este recurso. Una vez que tenga ese certificado válido, debe extraer los bytes pfx como una cadena de base64. Una opción para extraerlos es usar el siguiente comando de PowerShell:

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Después podría pasarlos como parámetro a la plantilla de implementación de ARM.

En este punto, la plantilla de ARM está lista.

###Implementar plantilla

Los pasos finales sirven para juntar todo en una implementación integral completa. Para facilitar la implementación, puede aprovechar el script de PowerShell **Deploy-AzureResourceGroup.ps1** que se agrega al crear un proyecto de grupo de recursos de Azure en Visual Studio para ayudar con la carga de los artefactos necesarios en la plantilla. Es necesario crear la cuenta de almacenamiento que desee usar con antelación. En este ejemplo, he creado una cuenta de almacenamiento compartido en la que cargar el archivo package.zip. El script hará uso de AzCopy para cargar el paquete en la cuenta de almacenamiento. Se pasa la ubicación de la carpeta de artefactos y el script cargará automáticamente todos los archivos de ese directorio en el contenedor de almacenamiento con nombre. Después de llamar a Deploy-AzureResourceGroup.ps1, tendrá que actualizar los enlaces SSL para asignar el nombre de host personalizado a su certificado SSL.

El siguiente script de PowerShell muestra la implementación completa con una llamada a Deploy-AzureResourceGroup.ps1:

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts "container-name" `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

En este momento, se debería haber implementado la aplicación y debería poder ir a ella mediante https://www.yourcustomdomain.com

<!---HONumber=AcomDC_0107_2016-->