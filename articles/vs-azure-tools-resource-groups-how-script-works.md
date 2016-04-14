<properties
	pageTitle="Información general sobre el script de implementación del proyecto de grupo de recursos de Azure | Microsoft Azure"
	description="Describe cómo funciona el script de PowerShell en el proyecto de implementación de grupo de recursos de Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Información general sobre el script de implementación del proyecto de grupo de recursos de Azure

Los proyectos de implementación de grupo de recursos de Azure le ayudan a realizar copias intermedias e implementar archivos y otros artefactos en Azure. Al crear un proyecto de implementación del Administrador de recursos de Azure en Visual Studio, se agrega un script de PowerShell denominado **Deploy-AzureResourceGroup.ps1**. Este tema proporciona detalles sobre lo que hace este script y cómo ejecutarlo tanto dentro como fuera de Visual Studio.

## ¿Qué hace el script?

El script Deploy-AzureResourceGroup.ps1 realiza dos acciones que son importantes para el flujo de trabajo de implementación.

- Carga de los archivos o los artefactos necesarios para la implementación de plantilla
- Implementación de la plantilla

La primera parte del script carga los archivos y los artefactos para la implementación y el último cmdlet en el script implementa la plantilla. Por ejemplo, si una máquina virtual tiene que estar configurada con un script, el script de implementación primero carga de forma segura el script de configuración en una cuenta de almacenamiento de Azure. Esto lo pone a disposición del Administrador de recursos de Azure para configurar la máquina virtual durante el aprovisionamiento.

Dado que no todas las implementaciones de plantilla necesitan tener artefactos adicionales que tienen que cargarse, se evalúa un parámetro de modificador denominado *uploadArtifacts*. Si es necesario cargar algún artefacto, establezca el modificador *uploadArtifacts* al llamar al script. Tenga en cuenta que no es necesario cargar el archivo de plantilla principal ni el archivo de parámetros. Solo hay que cargar otros archivos, como los scripts de configuración, las plantillas de implementación anidadas y los archivos de aplicación.

## Descripción detallada del script

A continuación se describe lo que hacen secciones seleccionadas del script de PowerShell de Azure Deploy-AzureResourceGroup.ps1.

>[AZURE.NOTE] La descripción corresponde a la versión 1.0 del script Deploy-AzureResourceGroup.ps1.

1.	Declarar los parámetros necesarios para el proyecto de implementación del Administrador de recursos de Azure. Algunos parámetros tienen valores predeterminados que se establecieron cuando se creó el proyecto. Puede cambiar estos valores predeterminados o agregar valores de parámetro distintos antes de ejecutar el script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |Parámetro|Descripción|
    |---|---|
    |$ResourceGroupLocation|La región o ubicación del centro de datos para el grupo de recursos, como **Oeste de EE. UU.** o **Asia oriental**.|
    |$ResourceGroupName|El nombre del grupo de recursos de Azure.|
    |$UploadArtifacts|Un valor binario que indica si hay o no artefactos que tienen que cargarse en Azure desde el sistema.|
    |$storageAccountName|El nombre de la cuenta de almacenamiento de Azure donde se cargan los artefactos.|
    |$StorageAccountResourceGroupName|El nombre del grupo de recursos de Azure que contiene la cuenta de almacenamiento.|
    |$StorageContainerName|El nombre del contenedor de almacenamiento utilizado para cargar los artefactos.|
    |$TemplateFile|La ruta de acceso al archivo de implementación (`<app name>.json`) en el proyecto del grupo de recursos de Azure.|
    |$TemplateParametersFile|La ruta de acceso al archivo de parámetros (`<app name>.parameters.json`) en el proyecto del grupo de recursos de Azure.|
    |$ArtifactStagingDirectory|La ruta de acceso en el sistema donde se cargan localmente los artefactos, incluida la carpeta raíz de script de PowerShell. Esta ruta de acceso puede ser absoluta o relativa a la ubicación del script.|
    |$AzCopyPath|La ruta de acceso donde la herramienta AzCopy.exe copia sus archivos .zip, incluida la carpeta raíz del script de PowerShell. Esta ruta de acceso puede ser absoluta o relativa a la ubicación del script.|
    |$DSCSourceFolder|La ruta de acceso a la carpeta de origen DSC (configuración de estado deseado), incluida la carpeta raíz del script de PowerShell. Esta ruta de acceso puede ser absoluta o relativa a la ubicación del script. Consulte la entrada de blog [Introducing the Azure PowerShell DSC (Desired State Configuration) extension](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), si procede, para obtener más información.|

1.	Compruebe si los artefactos tienen que cargarse en Azure. Si no es necesario, vaya al paso 11. De lo contrario, realice los siguiente pasos:

1.	Convierta las variables con rutas de acceso relativas a rutas de acceso absolutas. Por ejemplo, cambe una ruta de acceso como `..\Tools\AzCopy.exe` a `C:\YourFolder\Tools\AzCopy.exe`. Igualmente, inicialice las variables *ArtifactsLocationName* y *ArtifactsLocationSasTokenName* en null. *ArtifactsLocation* y *SaSToken* pueden ser parámetros de la plantilla. Si sus valores son nulos después de leer el archivo de parámetros, el script genera valores para ellos.

    Las herramientas de Azure usan los valores de parámetro *\_artifactsLocation* y *\_artifactsLocationSasToken* en la plantilla para administrar los artefactos. Si el script de PowerShell busca los parámetros con esos nombres, pero no se proporcionan los valores de parámetro, el script carga los artefactos y devuelve los valores adecuados para esos parámetros. A continuación los pasa al cmdlet a través de `@OptionsParameters`.

	|Variable|Descripción|
    |---|---|
    |ArtifactsLocationName|La ruta de acceso a donde se encuentran los artefactos de Azure.|
    |ArtifactsLocationSasTokenName|El nombre de token de la SAS (firma de acceso compartido) que utiliza el script para autenticarse en el Bus de servicio. Para obtener más información, consulte [Autenticación con firma de acceso compartida en Bus de servicio](service-bus-shared-access-signature-authentication.md).|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	Esta sección comprueba si el archivo <app name>.parameters.json (el llamado "archivo de parámetros") tiene un nodo primario denominado **parámetros** (en el bloque `else`). De lo contrario, no tiene nodo primario. Cualquier formato es aceptable.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	Itere a través de la colección de parámetros JSON. Si se ha asignado un valor de parámetro a *\_artifactsLocation* o *\_artifactsLocationSasToken*, a continuación, establezca la variable *$OptionalParameters* con esos valores. Esto impide que el script sobrescriba accidentalmente los valores de parámetro que usted proporcione.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	Obtenga la clave de la cuenta de almacenamiento y el contexto para el recurso de la cuenta de almacenamiento que se ha utilizado para contener los artefactos para la implementación.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	Si está usando DSC de PowerShell para configurar una máquina virtual, la extensión de DSC requiere que los artefactos estén en un único archivo zip. Por lo tanto, cree un archivo .zip para la configuración de DSC. Para ello, compruebe si existe $DSCSourceFolder. Si existe una configuración de DSC, quítela y, a continuación, cree un nuevo archivo comprimido denominado dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	Si no se proporciona ninguna ruta de acceso para los artefactos de Azure en el archivo de parámetros, establezca una para el script de PowerShell para usarla al cargar los artefactos. Para ello, cree una ruta de acceso mediante una combinación de la ruta de acceso del punto de conexión de la cuenta de almacenamiento, más el nombre del contenedor de almacenamiento. A continuación, actualice el archivo de parámetros con esta nueva ruta de acceso.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	Utilice la utilidad **AzCopy** (incluida en la carpeta **Herramientas** de su proyecto de implementación de grupo de recursos de Azure) para copiar los archivos de la ruta de colocación del almacenamiento local en su cuenta de almacenamiento de Azure en línea. Si se produce un error en este paso, salga del script, ya que es probable que la implementación no se realice correctamente sin los artefactos necesarios.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	Si no se proporciona un token de SAS para la ubicación de artefactos en el archivo de parámetros, cree uno para proporcionar acceso temporal de solo lectura al contenedor de almacenamiento en línea. A continuación, pase ese token SAS a línea de comandos como "optionalParameter". Tenga en cuenta que todos los parámetros pasados en línea de comandos tienen prioridad sobre los valores que se proporcionan en el archivo de parámetros.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Cree el grupo de recursos si todavía no existe y compruebe el archivo de plantilla y parámetros para asegurarse de que no hay errores de validación que impidan que la implementación se realice con éxito.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Finalmente, implemente la plantilla. Este código crea un nombre único para la implementación mediante una marca de tiempo.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## Implementación del grupo de recursos

### Para implementar el grupo de recursos en Visual Studio

1. En el menú contextual del proyecto de grupo de recursos de Azure, elija **Implementar**, **Nueva implementación**.

    ![][0]

1. En el cuadro de diálogo **Implementar en grupo de recursos**, seleccione un grupo ya existente en el cuadro de lista desplegable o elija **&lt;Crear nuevo...&gt;** para crear un nuevo grupo de recursos para la implementación.

    ![][1]

1. Si se le solicita, escriba un nombre y una ubicación para el grupo de recursos en el cuadro de diálogo **Crear grupo de recursos** y, a continuación, elija el botón **Crear**.

    ![][2]

1. Elija el botón **Editar parámetros** para ver el cuadro de diálogo **Editar parámetros** y luego escriba los valores de parámetro que faltan.

    ![][3]

	>[AZURE.NOTE] Si alguno de los parámetros requeridos necesita valores, este cuadro de diálogo aparece automáticamente al implementar.

    ![][4]

1. Cuando haya terminado de especificar valores de parámetro, seleccione el botón **Guardar** y, a continuación, elija el botón **Implementar**.

    Se ejecuta el script de implementación (Deploy-AzureResourceGroup.ps1) y la plantilla, junto con los artefactos, se implementa en Azure.

### Implementación del grupo de recursos con PowerShell

Si desea ejecutar el script sin usar la interfaz de usuario y el comando de implementación de Visual Studio, en el menú contextual del script, elija **Abrir con PowerShell ISE**.

![][5]


## Ejemplos de comandos de implementación

### Implementación con los valores predeterminados

Este ejemplo muestra cómo ejecutar el script con los valores de parámetro predeterminados. (Dado que el parámetro de ubicación no tiene un valor predeterminado, tendrá que proporcionar uno.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### Implementación reemplazando los valores predeterminados

Este ejemplo muestra cómo ejecutar el script para implementar archivos de plantilla y parámetros que difieren de los valores predeterminados.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### Implementación mediante UploadArtifacts para ensayo

Este ejemplo muestra cómo ejecutar el script para cargar los artefactos de la carpeta de versión e implementar plantillas no predeterminadas.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Este ejemplo muestra cómo ejecutar el script en una tarea de PowerShell de Azure en Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## Pasos siguientes
Para obtener más información sobre el Administrador de recursos de Azure, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_0211_2016-->