<properties
   pageTitle="Creación e implementación de proyectos de Visual Studio del Grupo de recursos de Azure | Microsoft Azure"
   description="Use Visual Studio para crear un proyecto del grupo de recursos de Azure e implementar los recursos en Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="tomfitz" />

# Creación e implementación de grupos de recursos de Azure mediante Visual Studio

Con Visual Studio y [Azure SDK](https://azure.microsoft.com/downloads/), puede crear un proyecto que implementa su infraestructura y código en Azure. Por ejemplo, puede definir el host de web, el sitio web y la base de datos para una aplicación, e implementar esa infraestructura junto con el código. O puede definir una máquina virtual, una red virtual y una cuenta de almacenamiento e implementar esa infraestructura junto con un script que se ejecuta en la máquina virtual. El proyecto de implementación del **grupo de recursos de Azure** permite implementar todos los recursos necesarios en una sola operación que se puede repetir. Para más información sobre la implementación y administración de grupos de recursos, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md).

Los proyectos del grupo de recursos de Azure contienen plantillas JSON del Administrador de recursos de Azure que definen los recursos que se implementan en Azure. Para más información sobre los elementos de la plantilla del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md). Visual Studio permite modificar estas plantillas y proporciona herramientas que simplifican el trabajo con plantillas.

En este tema, vamos a implementar una aplicación web y Base de datos SQL, pero, de todas formas, los pasos son prácticamente los mismos para cualquier tipo de recurso. Puede implementar igual de fácilmente una máquina virtual y sus recursos relacionados. Visual Studio proporciona muchas plantillas de inicio diferentes para la implementación de escenarios comunes.

## Creación de un proyecto de grupo de recursos de Azure

En este procedimiento, creará un proyecto de grupo de recursos de Azure con una plantilla **Aplicación web + SQL**.

1. En Visual Studio, elija **Archivo**, **Nuevo proyecto** y **C#** o **Visual Basic**. A continuación, elija **Nube** y después el proyecto **Grupo de recursos de Azure**.

    ![Proyecto de implementación de la nube](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Elija la plantilla que desea implementar en el Administrador de recursos de Azure. Observe que hay muchas opciones diferentes basados en el tipo de proyecto que desee implementar. En este ejemplo, hemos elegido la plantilla **Aplicación web + SQL**.

    ![Seleccionar una plantilla](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    La plantilla que elija es simplemente un punto de partida, puede agregar y quitar recursos para adaptarse a su escenario específico.

    >[AZURE.NOTE] La lista de plantillas disponibles se recupera en línea y puede cambiar.

    Visual Studio crea un proyecto de implementación de grupo de recursos de Azure para la aplicación web y Base de datos SQL.

1. Expanda los nodos del proyecto de implementación para ver lo que se ha creado.

    ![mostrar nodos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Dado que elegimos la plantilla de aplicación web + SQL para este ejemplo, verá los archivos a continuación.

    |Nombre de archivo|Descripción|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Un script de PowerShell que invoca los comandos de PowerShell que se implementarán en el Administrador de recursos de Azure.<br />** Nota** Este script de PowerShell se usa por Visual Studio para implementar su plantilla. Los cambios que realice a este script también afectarán a la implementación en Visual Studio, por tanto, tenga cuidado.|
    |WebSiteSQLDatabase.json|La plantilla del Administrador de recursos que define la infraestructura que desea implementar en Azure, y los parámetros que puede proporcionar durante la implementación. También define las dependencias entre los recursos, de modo que se implementen en el orden correcto.|
    |WebSiteSQLDatabase.parameters.json|Un archivo de parámetros que contiene valores necesarios para la plantilla. Estos son los valores que se transfieren para personalizar cada implementación.|
    |AzCopy.exe|Herramienta usada por el script de PowerShell para copiar archivos desde la ruta de colocación del almacenamiento local al contenedor de cuentas de almacenamiento. Esta herramienta se usa solamente si configura el proyecto de implementación para implementar el código junto con la plantilla.|

    Todos los proyectos de implementación de grupo de recursos contienen estos cuatro archivos básicos. Otros proyectos pueden contener archivos adicionales para admitir otras funcionalidades.

## Personalización de la plantilla del Administrador de recursos

Puede personalizar un proyecto de implementación mediante la modificación de las plantillas JSON que describen los recursos que desea implementar. JSON significa JavaScript Object Notation y es un formato de datos serializados con el que resulta sencillo trabajar. Los archivos JSON usan un esquema al que se hace referencia en la parte superior de cada archivo. Puede descargar el esquema y analizarlo para así comprenderlo mejor. El esquema define qué elementos se permiten, los tipos y los formatos de los campos, los valores posibles de los valores enumerados, etc. Para más información sobre los elementos de la plantilla del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

Para trabajar en la plantilla, abra **WebSiteSQLDatabase.json**.

El editor de Visual Studio proporciona herramientas para ayudarle con la edición de la plantilla del Administrador de recursos. La ventana **Esquema JSON** facilita la visualización de los elementos definidos en la plantilla.

![mostrar el esquema JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

La selección de cualquiera de los elementos del esquema le llevará a la parte de la plantilla y resaltará el JSON correspondiente.

![navegar a JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Puede agregar un recurso nuevo a la plantilla bien seleccionando el botón **Agregar recurso** situado en la parte superior de la ventana Esquema JSON, o haciendo clic con el botón derecho en **recursos** y seleccionando **Agregar nuevo recurso**.

![agregar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, seleccione **Cuenta de almacenamiento** y asígnele un nombre. Para el nombre de una cuenta de almacenamiento solo se pueden usar números y letras minúsculas, y menos de 24 caracteres. El proyecto agregará una cadena única de 13 caracteres en el nombre que proporcione, así que asegúrese de que su nombre no tiene más de 11 caracteres.

![agregar almacenamiento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Tenga en cuenta que no solo se agregó el recurso, sino que también se agregó un parámetro para la cuenta de almacenamiento de tipo y una variable para el nombre de la cuenta de almacenamiento.

![mostrar el esquema](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

El parámetro **WebSitePicturesType** está predefinido con tipos permitidos y un tipo predeterminado. Puede dejar estos valores o modificarlos para su escenario específico. Si no desea permitir que cualquiera pueda implementar una cuenta de almacenamiento **Premium\_LRS** a través de esta plantilla, no tiene más que quitarla de los tipos permitidos, como se muestra a continuación.

    "WebSitePicturesType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio también proporciona IntelliSense para ayudarle a entender qué propiedades están disponibles al editar la plantilla. Por ejemplo, para editar las propiedades de su plan de servicio de aplicaciones, vaya a los recursos **HostingPlan** y agregue un nuevo valor para las **propiedades**. Observe que IntelliSense muestra los valores disponibles y proporciona una descripción de cada valor.

![mostrar IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Puede establecer **numberOfWorkers** en 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Implementación del proyecto de grupo de recursos en Azure

Ahora está preparado para implementar el proyecto. Al implementar un proyecto de grupo de recursos de Azure, lo implementa en un grupo de recursos de Azure, que es simplemente una agrupación lógica de los recursos de Azure, como aplicaciones web, bases de datos, etc.

1. En el menú contextual del nodo de proyecto de implementación, elija **Implementar**, **Nueva implementación**.

    ![Implementar, elemento de menú Nueva implementación](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    Aparece el cuadro de diálogo **Implementar en grupo de recursos**.

    ![Cuadro de diálogo Implementar en grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. En el cuadro de lista desplegable **Grupo de recursos**, elija un grupo de recursos existente o cree uno nuevo. Para crear un grupo de recursos, abra la lista desplegable **Grupo de recursos** y elija **<Create New...>**.

    ![Cuadro de diálogo Implementar en grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    Aparece el cuadro de diálogo **Crear grupo de recursos**. Asigne al grupo un nombre y una ubicación y seleccione el botón **Crear**.

    ![Cuadro de diálogo Crear grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Puede editar los parámetros para la implementación seleccionando el botón **Editar parámetros**. Proporcione valores para los parámetros y seleccione el botón **Guardar**.

    ![Cuadro de diálogo Editar parámetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    La opción **Guardar contraseñas** significa que las contraseñas se guardarán como texto sin formato en el archivo JSON. Esta opción no es segura.

1. Elija el botón **Implementar** para implementar el proyecto en Azure. Puede ver el progreso de la implementación en la ventana **Resultados**. La implementación puede tardar varios minutos en completarse, dependiendo de la configuración. Si se le solicita, escriba la contraseña de administrador de base de datos.

    >[AZURE.NOTE] Puede que se le pida que instale los cmdlets de Azure PowerShell. Como estos cmdlets son necesarios para implementar grupos de recursos de Azure, deberá instalarlos.
    
1. Cuando haya terminado la implementación, debería ver un mensaje en la ventana de **salida**, algo como:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. En un explorador, abra el [Portal de Azure](https://portal.azure.com/) e inicie sesión en su cuenta. Para ver el grupo de recursos, seleccione **Grupos de recursos** y el grupo de recursos que implementó.

    ![seleccionar grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Verá todos los recursos implementados.

    ![mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Si realiza cambios y desea volver a implementar el proyecto, puede elegir el grupo de recursos existente directamente desde el menú contextual del proyecto de grupo de recursos de Azure. En el menú contextual, elija **Implementar** y después el grupo de recursos en el que acaba de implementar.

    ![Grupo de recursos de Azure implementado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Implementación de código con la infraestructura

A estas alturas ha implementado la infraestructura de la aplicación, pero no hay ningún código real que se haya implementado con el proyecto. Este tema muestra cómo implementar una aplicación web y las tablas de Base de datos SQL durante la implementación. Si está implementando una máquina virtual en lugar de una aplicación web, tiene que ejecutar algún código en el equipo como parte de la implementación. El proceso para implementar el código para una aplicación web o para configurar una máquina virtual es prácticamente el mismo.

1. En la solución de Visual Studio, agregue una **aplicación web ASP.NET**. 

    ![agregar aplicación web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Seleccione **MVC** y desactive el campo de **Host en la nube** porque el proyecto del grupo de recursos llevará a cabo esa tarea.

    ![seleccionar MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Una vez creada la aplicación web, debe agregar una referencia al proyecto de aplicación web en el proyecto del grupo de recursos.

    ![agregar referencia](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Al agregar una referencia, se vincula el proyecto de aplicación web con el proyecto del grupo de recursos y se establecen tres propiedades clave. **Propiedades adicionales** contiene la ubicación de ensayo del paquete de implementación web que se insertará en Almacenamiento de Azure. **Incluir ruta del archivo** contiene la ruta de acceso donde se creará el paquete. **Incluir destinos** contiene el comando que ejecutará la implementación. El valor predeterminado **Build;Package** permite a la implementación generar y crear un paquete de implementación web (package.zip). No se necesita un perfil de publicación ya que la implementación obtiene la información necesaria de las propiedades para crear el paquete.
    
      ![ver referencia](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Agregue un nuevo recurso a la plantilla y esta vez, seleccione **Web Deploy para aplicaciones Web**.

    ![agregar implementación web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Vuelva a implementar el proyecto del grupo de recursos en el grupo de recursos. Esta vez, hay algunos parámetros nuevos. No es necesario proporcionar valores para ** \_artifactsLocation ** o ** \_artifactsLocationSasToken ** ya que se generan automáticamente. Establezca la carpeta y el nombre de archivo en la ruta de acceso que contiene el paquete de implementación.

    ![agregar implementación web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Para la **Cuenta de almacenamiento de artefactos** puede utilizar una implementada con este grupo de recursos.
    
Una vez finalizada la implementación, puede ir al sitio y podrá observar que se ha implementado la aplicación ASP.NET predeterminada.

![mostrar la aplicación implementada](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Pasos siguientes

- Para más información sobre cómo administrar sus recursos a través del portal, consulte [Uso del Portal de Azure para administrar los recursos de Azure](./azure-portal/resource-group-portal.md).
- Para más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0218_2016-->