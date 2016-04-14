<properties
	pageTitle="Integración continua en VS Team Services mediante proyectos del Grupo de recursos de Azure | Microsoft Azure"
	description="Describe cómo configurar la integración continua en Visual Studio Team Services mediante proyectos de implementación del Grupo de recursos de Azure en Visual Studio."
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
	ms.date="01/26/2016"
	ms.author="tarcher" />

# Integración continua en Visual Studio Team Services mediante proyectos de implementación del Grupo de recursos de Azure

Para implementar una plantilla de Azure, tiene que realizar tareas que pasan por las distintas fases: compilación, prueba, copia en Azure (también denominada "almacenamiento provisional") y plantilla de implementación. Hay dos maneras diferentes de hacerlo en Visual Studio Team Services (VS Team Services). Ambos métodos proporcionan los mismos resultados, así que puede elegir el que mejor se adapte a su flujo de trabajo.

-	Agregue un paso único a la definición de compilación que ejecuta el script de PowerShell incluido en el proyecto de implementación del Grupo de recursos de Azure (Deploy-AzureResourceGroup.ps1). El script copia los artefactos y, a continuación, implementa la plantilla.
-	Agregue varios pasos de compilación de VS Team Services, cada uno de los cuales realiza una tarea de fase.

En este artículo se muestra cómo utilizar la primera opción (utilice una definición de compilación para ejecutar el script de PowerShell). Una ventaja de esta opción es que el script que utilizan los desarrolladores de Visual Studio es el mismo que el que utiliza VS Team Services. En este procedimiento se supone que ya tiene un proyecto de implementación de Visual Studio activado en VS Team Services.

## Copia de artefactos en Azure 

Independientemente del escenario, si dispone de los artefactos necesarios para la implementación de plantillas, deberá dar al Administrador de recursos de Azure acceso a ellos. Estos artefactos pueden incluir archivos como:

-	Plantillas anidadas
-	Scripts de configuración y de DSC
-	Archivos binarios de aplicación

### Plantillas anidadas y scripts de configuración
Al utilizar las plantillas proporcionadas por Visual Studio (o compilar con fragmentos de código de Visual Studio), el script de PowerShell no solo almacena provisionalmente los artefactos, sino que parametriza también el URI de los recursos de las distintas implementaciones. El script copia los artefactos en un contenedor seguro de Azure, crea un token de SaS para dicho contenedor y, a continuación, pasa esta información a la implementación de plantilla. Consulte [Crear una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx) para obtener más información acerca de las plantillas anidadas.

## Configuración de la implementación continua en VS Team Services

Para llamar al script de PowerShell en VS Team Services, debe actualizar la definición de compilación. En resumen, los pasos son:

1.	Edición de la definición de compilación.
1.	Configuración de la autorización de Azure en VS Team Services.
1.	Incorporación de un paso de compilación de Azure PowerShell que hace referencia al script de PowerShell en el proyecto de implementación del Grupo de recursos de Azure.
1.	Establecimiento del valor del parámetro *-ArtifactsStagingDirectory* para trabajar con un proyecto compilado en VS Team Services.

### Tutorial detallado

Los pasos siguientes le guiarán por los pasos necesarios para configurar la implementación continua en VS Team Services

1.	Edite la definición de compilación de VS Team Services y agregue un paso de compilación de Azure PowerShell. Elija la definición de compilación en la categoría **Definiciones de compilación** y, a continuación, elija el vínculo **Editar**.

    ![][0]

1.	Agregue un nuevo paso de compilación **Azure PowerShell** a la definición de compilación y elija el botón **Agregar paso de compilación...**

    ![][1]

1.	Elija la categoría de tarea **Implementar**, seleccione la tarea **Azure PowerShell** y, a continuación, elija el botón **Agregar** correspondiente.

    ![][2]

1.	Elija el paso de compilación **Azure PowerShell** y rellene sus valores.

    1.	Si ya tiene un punto de conexión de servicio de Azure agregado a VS Team Services, elija la suscripción en el cuadro de lista desplegable **Suscripción de Azure** y vaya a la sección siguiente. 

        Si no tiene ningún punto de conexión de servicio de Azure en VS Team Services, debe agregar uno. Este subapartado le guiará por el proceso. Si su cuenta de Azure usa una cuenta de Microsoft (como Hotmail), debe seguir estos pasos para obtener una autenticación de entidad de servicio.

    1.	Elija el vínculo **Administrar** situado junto al cuadro de lista desplegable **Suscripción de Azure**.

        ![][3]

    1. Elija **Azure** en el cuadro de lista desplegable **Nuevo punto de conexión de servicio**.

        ![][4]

    1.	En el cuadro de diálogo **Agregar suscripción de Azure**, seleccione la opción **Entidad de servicio**.

        ![][5]

    1.	Agregue la información de suscripción de Azure en el cuadro de diálogo **Agregar suscripción de Azure**. Tiene que proporcionar información para los siguientes elementos:
        -	Id. de suscripción
        -	Subscription Name
        -	Id. de entidad del servicio
        -	Clave de entidad del servicio
        -	Identificador de inquilino

    1.	Agregue un nombre de su elección en el cuadro de nombre **Suscripción**. Este valor aparecerá más adelante en la lista desplegable **Suscripción de Azure** en VS Team Services.

    1.	Si no conoce el identificador de la suscripción de Azure, puede usar uno de los siguientes comandos para obtenerlo.
        
        Para scripts de PowerShell, use:

        `Get-AzureRmSubscription`

        Para la CLI de Azure, utilice:

        `azure account show`
    

    1.	Para obtener un id. de entidad de servicio, una clave de entidad de servicio y un id. de inquilino, siga el procedimiento de [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](resource-group-create-service-principal-portal.md) o [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

    1.	Agregue los valores de id. de entidad de servicio, clave de entidad de servicio e id. de inquilino en el cuadro de diálogo **Agregar suscripción de Azure** y, a continuación, elija el botón **Aceptar**.

        Ahora dispone de una entidad de servicio válida que puede utilizar para ejecutar el script de Azure PowerShell.

1.	Edite la definición de compilación y elija el paso de compilación **Azure PowerShell**. Seleccione la suscripción en el cuadro de lista desplegable **Suscripción de Azure**. (Si la suscripción no aparece, elija el botón **Actualizar** que se muestra junto al vínculo **Administrar**.)

    ![][8]

1.	Proporcione una ruta de acceso al script de PowerShell Deploy-AzureResourceGroup.ps1. Para ello, elija el botón de puntos suspensivos (...) junto al cuadro **Ruta de acceso del script**, vaya al script de PowerShell Deploy-AzureResourceGroup.ps1 en la carpeta **Scripts** del proyecto, selecciónelo y elija el botón **Aceptar**.

    ![][9]

1. Después de seleccionar el script, actualice la ruta de acceso al script para que se ejecute desde Build.StagingDirectory (el mismo directorio en el que está establecido *ArtifactsLocation*). Puede hacerlo agregando “$(Build.StagingDirectory)/” al principio de la ruta de acceso del script.

    ![][10]

1.	En el cuadro **Argumentos del script** escriba los parámetros siguientes (en una sola línea). Al ejecutar el script en Visual Studio, puede ver cómo usa VS los parámetros en la ventana **Resultados**. Se puede utilizar como punto de partida para configurar los valores de parámetro en el paso de compilación.

    | Parámetro | Descripción|
    |---|---|
    | -ResourceGroupLocation | El valor de la ubicación geográfica donde se encuentra el grupo de recursos, como **eastus** o **'Este de EE. UU.'**. (Agregue comillas simples si hay un espacio en el nombre). Para obtener más información, consulte [Regiones de Azure](https://azure.microsoft.com/es-ES/regions/).| |
    | -ResourceGroupName | El nombre del grupo de recursos que se usa para esta implementación.| |
    | -UploadArtifacts | Este parámetro, cuando está presente, especifica que los artefactos tienen que cargarse en Azure desde el sistema local. Solo debe establecer este modificador si su implementación de plantilla requiere artefactos adicionales que desea almacenar provisionalmente mediante el script de PowerShell (como scripts de configuración o plantillas anidadas). |
    | -StorageAccountName | El nombre de la cuenta de almacenamiento utilizada para almacenar provisionalmente los artefactos en esta implementación. Este parámetro solo es necesario si está copiando artefactos en Azure. La implementación no creará automáticamente esta cuenta de almacenamiento, que debe existir previamente.| |
    | -StorageAccountResourceGroupName | El nombre del grupo de recursos asociado a la cuenta de almacenamiento. Este parámetro solo es necesario si está copiando artefactos en Azure.| |
    | -TemplateFile | La ruta de acceso al archivo de plantilla en el proyecto de implementación del Grupo de recursos de Azure. Para mejorar la flexibilidad, utilice una ruta de acceso para este parámetro que sea relativa a la ubicación del script de PowerShell en lugar de una ruta de acceso absoluta.|
    | -TemplateParametersFile | La ruta de acceso al archivo de parámetros en el proyecto de implementación del Grupo de recursos de Azure. Para mejorar la flexibilidad, utilice una ruta de acceso para este parámetro que sea relativa a la ubicación del script de PowerShell en lugar de una ruta de acceso absoluta.|
    | -ArtifactStagingDirectory | Este parámetro permite que el script de PowerShell conozca la carpeta desde la que se deben copiar los archivos binarios del proyecto. Este valor invalida el valor predeterminado usado por el script de PowerShell. Para el uso de VS Team Services, establezca el valor en: ArtifactStagingDirectory $(Build.StagingDirectory) |

    A continuación se muestra un ejemplo de argumentos de script (línea dividida para mejorar la legibilidad):

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    Cuando haya terminado, el cuadro **Argumentos del script** debe ser similar al siguiente.

    ![][11]

1.	Después de agregar todos los elementos necesarios para el paso de compilación de Azure PowerShell, elija el botón de compilación **Cola** para generar el proyecto de compilación. La pantalla **Compilación** muestra el resultado del script de PowerShell.

## Pasos siguientes

Lea [Información general del Administrador de recursos de Azure](resource-group-overview.md) para obtener más información sobre el Administrador de recursos de Azure y los Grupos de recursos de Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0128_2016-->