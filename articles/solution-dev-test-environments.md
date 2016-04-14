<properties
   pageTitle="Entornos de desarrollo y pruebas | Microsoft Azure"
   description="Obtenga información acerca de cómo usar las plantillas del Administrador de recursos de Azure para crear y eliminar de manera rápida y coherente entornos de desarrollo y pruebas."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="jdial"/>

# Entornos de desarrollo y pruebas en Microsoft Azure

Las aplicaciones personalizadas se implementan normalmente en varios entornos de desarrollo y pruebas antes de hacerlo en producción. Cuando se crean entornos en local, se adquieren o se asignan recursos informáticos para cada entorno para cada aplicación. Los entornos a menudo incluyen varias máquinas físicas o virtuales con configuraciones específicas que se implementan manualmente o con scripts de automatización complejos. Las implementaciones suelen tardar horas y generar configuraciones incoherentes entre entornos.

## Escenario ##

Al aprovisionar los entornos de desarrollo y pruebas en Microsoft Azure, solo paga por los recursos que usa. En este artículo se explica cómo puede crear, mantener y eliminar de forma rápida y coherente entornos de desarrollo y pruebas mediante el uso de plantillas y archivos de parámetros del Administrador de recursos de Azure, tal como se muestra a continuación.

![Escenario](./media/solution-dev-test-environments/scenario.png)

Arriba se muestran tres entornos de desarrollo y pruebas. Cada uno tiene recursos de aplicación web y de base de datos SQL, que se especifican en un archivo de plantilla. Los nombres de la aplicación y la base de datos en cada entorno son diferentes y se especifican en archivos de parámetros únicos para cada entorno.

Si no está familiarizado con los conceptos del Administrador de recursos de Azure, se recomienda que lea el artículo [Información general sobre el Administrador de recursos de Azure](resource-group-overview.md) antes de leer este artículo.

Quizás prefiera seguir primero los pasos de este artículo sin leer ninguno de los artículos a los que se hace referencia para obtener rápidamente algo de experiencia con las plantillas del Administrador de recursos de Azure. Pero después de haberlos realizado una vez, haber experimentado con ellos y haber leído los artículos de referencia, obtendrá respuestas a las preguntas que le surgieron por primera vez.

## Planeación del uso de recursos de Azure
Una vez que disponga de un diseño de alto nivel de la aplicación, puede definir:

- Qué recursos de Azure incluirá su aplicación. Puede compilar la aplicación e implementarla como una aplicación web de Azure con una base de datos SQL de Azure. Puede compilar la aplicación en máquinas virtuales con PHP y MySQL o IIS y SQL Server, u otros componentes. El artículo [Comparación entre el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales](app-service-web/choose-web-site-cloud-service-vm.md) le ayuda a decidir qué recursos de Azure podría usar para su aplicación.
- Qué requisitos de nivel de servicio, como disponibilidad, seguridad y escalabilidad, cumplirá la aplicación.

## Descarga de una plantilla existente
Una plantilla del Administrador de recursos de Azure define todos los recursos de Azure que usa la aplicación. Existen varias plantillas que puede implementar directamente en el Portal de Azure, o bien descargar, modificar y guardar en un sistema de control de código fuente con el código de aplicación. Complete los pasos siguientes para descargar una plantilla existente.

1. Busque plantillas existentes en el repositorio de GitHub de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/). En la lista, verá una carpeta "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Puesto que muchas aplicaciones personalizadas incluyen una aplicación web y una base de datos SQL, esta plantilla se usa como ejemplo en el resto de este artículo para ayudarle a entender cómo usar las plantillas. La explicación completa de todo lo que esta plantilla crea y configura queda fuera del ámbito de este artículo, pero si tiene la intención de usarla para crear entornos reales en su organización, querrá saber todo sobre ella, lo que puede conseguir leyendo el artículo [Aprovisionamiento de una aplicación web con una base de datos SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).
2. Haga clic en el archivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) en la carpeta 201-web-app-sql-database para ver su contenido. Este es el archivo de plantilla del Administrador de recursos de Azure. 
3. En el modo de vista, haga clic en el botón "[Sin formato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)". 
4. Con el mouse, seleccione todo el contenido de este archivo y guárdelo en el equipo como un archivo llamado "TestApp1-Template.json". 
5. Examine el contenido de la plantilla y observe lo siguiente:
 - Sección **Recursos**: esta sección define los tipos de recursos de Azure creados por esta plantilla. Entre otros tipos de recursos, esta plantilla crea recursos de [aplicación web de Azure](app-service-web/app-service-web-overview.md) y de [base de datos SQL de Azure](sql-database/sql-database-technical-overview.md). Si prefiere ejecutar y administrar servidores web y SQL en máquinas virtuales, puede utilizar las plantillas "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" o "[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)" pero las instrucciones de este artículo se basan en la plantilla [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database).
 - Sección **Parámetros**: esta sección define los parámetros con los que se puede configurar cada recurso. Algunos de los parámetros especificados en la plantilla tienen propiedades "defaultValue", mientras que otros no. Al implementar recursos de Azure con una plantilla, debe proporcionar valores para todos los parámetros que no tengan propiedades defaultValue especificadas en la plantilla. Si no proporciona valores para los parámetros con propiedades defaultValue, se usará el valor especificado para el parámetro defaultValue en la plantilla.

Una plantilla define qué recursos de Azure se crean y los parámetros con los que se puede configurar cada recurso. Puede obtener más información sobre las plantillas y cómo diseñar las suyas propias en el artículo [Procedimientos recomendados para diseñar plantillas del Administrador de recursos de Azure](best-practices-resource-manager-design-templates.md).

## Descarga y personalización de un archivo de parámetros existente

Aunque lo más probable es que prefiera crear los *mismos* recursos de Azure en cada entorno, quizá desee que la configuración de los recursos sea *distinta* en cada uno. Y aquí es donde entran en juego los archivos de parámetros. Cree archivos de parámetros que contengan valores únicos en cada entorno mediante los pasos siguientes.

1. Vea el contenido del archivo [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.parameters.json) de la carpeta 201-web-app-sql-database. Este es el archivo de parámetros del archivo de plantilla que guardó en la sección anterior. 
2. En el modo de vista, haga clic en el botón "[Sin formato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Con el mouse, seleccione el contenido de este archivo y guárdelo en tres archivos distintos en el equipo con los siguientes nombres:
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. Con cualquier editor de texto o JSON, edite el archivo de parámetros del entorno de desarrollo que creó en el paso 3 y reemplace los valores enumerados a la derecha de los valores de parámetros en el archivo por los *valores* enumerados a la derecha de los **parámetros** indicados a continuación: 
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *replace with your password*
 - **databaseName**: *testapp1devdb*

4. Con cualquier editor de texto o JSON, edite el archivo de parámetros del entorno de prueba que creó en el paso 3 y reemplace los valores enumerados a la derecha de los valores de parámetros en el archivo por los *valores* enumerados a la derecha de los **parámetros** indicados a continuación:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPla*n
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *replace with your password*
 - **databaseName**: *testapp1testdb*

5. Con cualquier editor de texto o JSON, edite el archivo de parámetros de preproducción que creó en el paso 3. Reemplace todo el contenido del archivo por lo siguiente:

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
    	},
    	"sku" : {
    	  "value" : "Standard"
    	},
    		"requestedServiceObjectiveName" : {
    		  "value" : "S1"
    	}
    	  }
    	}

En el archivo de parámetros de preproducción anterior, se *agregaron* los parámetros **sku** y **requestedServiceObjectiveName**. Estos parámetros no se agregaron a los archivos de parámetros de desarrollo y pruebas. Esto se debe a que hay valores predeterminados especificados para estos parámetros en la plantilla y, en los entornos de desarrollo y pruebas, se usan los valores predeterminados, pero en el entorno de preproducción se usan valores no predeterminados para estos parámetros.

La razón de usar valores no predeterminados para estos parámetros en el entorno de preproducción es probar valores para estos parámetros que podrían ser preferibles para su entorno de producción a fin de poderlos probar igualmente. Estos parámetros se relacionan con los [planes de hospedaje de aplicaciones web](https://azure.microsoft.com/pricing/details/app-service/) de Azure, o **sku**, y con la [base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) de Azure, o **requestedServiceObjectiveName** que usa la aplicación. Diferentes SKU y nombres de objetivo de servicio tiene distintos costos y características y admiten diferentes métricas de nivel de servicio.

En la siguiente tabla se muestran los valores predeterminados de estos parámetros especificados en la plantilla y los valores que se usan en lugar de los valores predeterminados en el archivo de parámetros de preproducción.

| Parámetro | Valor predeterminado | Valor de archivo de parámetros |
|---|---|---|
| **sku** | Gratuito | Estándar |
| **requestedServiceObjectiveName** | S0 | S1 |

## Creación de entornos
Todos los recursos de Azure deben crearse dentro de un [grupo de recursos de Azure](azure-portal/resource-group-portal#create-resource-group-and-resources.md). Los grupos de recursos le permiten agrupar recursos de Azure y así administrarlos de forma colectiva. Se pueden asignar [permisos](./active-directory/role-based-access-built-in-roles.md) a grupos de recursos para que personas específicas dentro de su organización puedan crearlos, modificarlos, eliminarlos o verlos, junto con los recursos que contienen. Se pueden ver alertas e información de facturación de los recursos del grupo de recursos en el [Portal de Azure](https://portal.azure.com). Los grupos de recursos se crean en una [región](https://azure.microsoft.com/regions/) de Azure. En este artículo, todos los recursos se crean en la región Centro de EE. UU. Cuando empiece a crear entornos reales, elegirá la región que mejor cumpla sus requisitos.

Cree grupos de recursos para cada entorno mediante cualquiera de los métodos siguientes. Con todos los métodos se obtiene exactamente el mismo resultado.

###Interfaz de la línea de comandos (CLI) de Azure

Asegúrese de que tiene [instalado](xplat-cli-install.md) la CLI en un equipo Windows, OS X o Linux y de que ha [conectado](xplat-cli-connect.md) su [cuenta de Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también denominada profesional o educativa) a la suscripción de Azure. En la línea de comandos CLI, escriba el comando siguiente para crear el grupo de recursos del entorno de desarrollo.

	azure group create "TestApp1-Development" "Central US"

El comando devolverá el siguiente resultado si se ejecuta correctamente:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

Para crear el grupo de recursos del entorno de prueba, escriba el comando siguiente:

	azure group create "TestApp1-Test" "Central US"

Para crear el grupo de recursos del entorno de preproducción, escriba el comando siguiente:

	azure group create "TestApp1-Pre-Production" "Central US"

###PowerShell

Asegúrese de que tiene Azure PowerShell 1.01 o posterior instalado en un equipo Windows y de que ha conectado su [cuenta de Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también denominada profesional o educativa) a la suscripción como se detalla en el artículo [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md). En el símbolo del sistema de PowerShell, escriba el comando siguiente para crear el grupo de recursos del entorno de desarrollo.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

El comando devolverá el siguiente resultado si se ejecuta correctamente:

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Para crear el grupo de recursos del entorno de prueba, escriba el comando siguiente:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Para crear el grupo de recursos del entorno de preproducción, escriba el comando siguiente:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) con una cuenta de [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también denominada profesional o educativa). Haga clic en Nuevo-->Administración-->Grupo de recursos y escriba "TestApp1-Development" en el cuadro Nombre del grupo de recursos, y seleccione la suscripción y luego "Centro de EE. UU." en el cuadro Ubicación del grupo de recursos como se muestra en la siguiente imagen. ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Haga clic en el botón Crear para crear el grupo de recursos.
3. Haga clic en Examinar, desplácese hacia abajo en la lista Grupos de recursos y haga clic en Grupos de recursos como se muestra a continuación. ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Después de hacer clic en Grupos de recursos, verá la hoja Grupos de recursos con el nuevo grupo de recursos. ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Cree los grupos de recursos TestApp1-Test y TestApp1-Pre-Production del mismo modo que creó el grupo de recursos TestApp1-Development anterior.

##Implementación de recursos en entornos

Implemente recursos de Azure en grupos de recursos para cada entorno mediante el archivo de plantilla de la solución y los archivos de parámetros de cada entorno con uno de estos métodos. Con ambos métodos se obtiene exactamente el mismo resultado.

###Interfaz de la línea de comandos (CLI) de Azure

En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de desarrollo, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Después de ver durante unos minutos un mensaje en el que indica que es preciso esperar hasta que se complete la implementación, el comando devolverá el siguiente resultado si se ejecuta correctamente:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

Si el comando no funciona, resuelva los posibles mensajes de error y vuelva a intentarlo. Algunos de los problemas más comunes se deben al uso de valores de parámetros que no cumplen las restricciones de nomenclatura de los recursos de Azure. Se pueden encontrar otras sugerencias de solución de problemas en el artículo [Solución de problemas de implementaciones de grupos de recursos en Azure](virtual-machines/resource-group-deploy-debug.md).

En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de pruebas, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

En la línea de comandos CLI, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de preproducción, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

En el símbolo del sistema de Azure PowerShell (versión 1.01 o posterior), escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de desarrollo y reemplace [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Después de ver durante unos minutos un cursor intermitente, el comando devolverá el siguiente resultado si se ejecuta correctamente:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Si el comando no funciona, resuelva los posibles mensajes de error y vuelva a intentarlo. Algunos de los problemas más comunes se deben al uso de valores de parámetros que no cumplen las restricciones de nomenclatura de los recursos de Azure. Se pueden encontrar otras sugerencias de solución de problemas en el artículo [Solución de problemas de implementaciones de grupos de recursos en Azure](virtual-machines/resource-group-deploy-debug.md).

  En el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de pruebas, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  En el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos en el grupo de recursos que creó para el entorno de preproducción, y sustituya [path] por la ruta de acceso a los archivos guardados en los pasos anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Se puede controlar la versión de los archivos de plantilla y de parámetros y se pueden mantener estos archivos con el código de aplicación en el sistema de control de código fuente. También puede guardar los comandos anteriores en archivos de script y guardarlos con el código.

> [AZURE.NOTE] Puede implementar la plantilla en Azure directamente haciendo clic en el botón "Implementar en Azure" en el artículo [Aprovisionamiento de una aplicación web con una base de datos SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Sin embargo, esto solo sirve de ayuda para obtener información sobre las plantillas, pero no para editar los valores de plantilla y parámetro, ni controlar su versión o guardarlos con el código de aplicación, por lo que no se ofrecerá más información sobre este método en este artículo.

## Mantenimiento de los entornos
Durante el desarrollo, la configuración de los recursos de Azure en los distintos entornos se puede modificar de forma incoherente bien a propósito o por accidente. Esto puede ocasionar que se tengan que resolver problemas innecesariamente durante el ciclo de desarrollo de aplicaciones.

1. Cambie los entornos abriendo el [Portal de Azure](https://portal.azure.com). 
2. Inicie sesión en él con la misma cuenta que usó para realizar los pasos anteriores. 
3. Como se muestra en la siguiente imagen, haga clic en Examinar-->Grupos de recursos (deberá desplazarse hacia abajo para ver Grupos de recursos). ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Después de hacer clic en Grupos de recursos en la imagen anterior, verá la hoja Grupos de recursos y los tres grupos de recursos creados en un paso anterior, tal como se muestra en la imagen siguiente. Al hacer clic en el grupo de recursos TestApp1-Development, verá la hoja que muestra los recursos creados por la plantilla en la implementación del grupo de recursos TestApp1-Development realizada en un paso anterior. Elimine el recurso de aplicación web TestApp1DevApp haciendo clic en TestApp1DevApp en la hoja Grupo de recursos TestApp1-Development y, a continuación, haga clic en Eliminar en la hoja Aplicación web TestApp1DevApp. ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Cuando el portal le pregunte si está seguro de que desea eliminar el recurso, haga clic en "Sí". Cierre la hoja Grupo de recursos TestApp1-Development y, al abrirla de nuevo, se mostrará sin la aplicación web que acaba de eliminar. El contenido del grupo de recursos ahora es diferente de lo que debería ser. Puede seguir experimentando y eliminar varios recursos de varios grupos de recursos o incluso cambiar la configuración de algunos de los recursos. En lugar de usar el Portal de Azure para eliminar un recurso de un grupo de recursos, puede usar el comando [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) de PowerShell o el comando "azure resource delete" desde la CLI para realizar la misma tarea.
6. Para que todos los recursos y la configuración, que se supone que están en los grupos de recursos, vuelvan al estado en el que deberían estar, implemente de nuevo los entornos en los grupos de recursos con los mismos comandos que utilizó en la sección [Implementación de recursos en entornos](#deploy-resources-to-environments) pero reemplazando "Deployment1" por "Deployment2".
7.  Tal como se muestra en la sección Resumen de la hoja TestApp1-Development en la imagen que aparece en el paso 4, verá que la aplicación web que se ha eliminado en el portal en el paso anterior existe de nuevo, igual que cualquier otro recurso que haya podido eliminar. Si ha cambiado la configuración de cualquiera de los recursos, también puede comprobar que han recuperado los valores en los archivos de parámetros igualmente. Una de las ventajas de implementar los entornos con plantillas del Administrador de recursos de Azure es que puede volver a implementarlos fácilmente y devolverlos a un estado conocido en cualquier momento.
8. Si, en la imagen siguiente, hace clic en el texto debajo de "Última implementación", verá una hoja que muestra el historial de implementaciones del grupo de recursos. Puesto que usó el nombre "Deployment1" para la primera implementación y "Deployment2" para la segunda, tendrá dos entradas. Al hacer clic en una implementación, se mostrará una hoja con los resultados de cada implementación. ![Portal](./media/solution-dev-test-environments/portal3.png)



## Eliminación de entornos
Una vez que haya terminado de utilizar un entorno, querrá eliminarlo para así no incurrir en gastos de uso por recursos de Azure que ya no usa. Eliminar entornos es incluso más sencillo que crearlos. En los pasos anteriores, se crearon grupos de recursos individuales de Azure para cada entorno y se implementaron los recursos en los grupos de recursos.

Eliminación de los entornos con cualquiera de los métodos siguientes. Con todos los métodos se obtiene exactamente el mismo resultado.

### Azure CLI

En el símbolo del sistema de CLI, escriba lo siguiente:

	azure group delete "TestApp1-Development"

Cuando se le solicite, escriba y; a continuación, presione Entrar para quitar el entorno de desarrollo y todos sus recursos. Después de unos minutos, el comando devolverá el siguiente resultado:

	info:    group delete command OK

En un símbolo del sistema de CLI, escriba lo siguiente para eliminar el resto de entornos:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

En el símbolo del sistema de Azure PowerShell (versión 1.01 o posterior), escriba el siguiente comando para eliminar el grupo de recursos y todo su contenido.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Cuando se le solicite, si está seguro de que desea quitar el grupo de recursos, escriba y presione la tecla Entrar.

Escriba lo siguiente para eliminar el resto de entornos:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Portal de Azure

1. En el Portal de Azure, vaya a Grupos de recursos como hizo en los pasos anteriores. 
2. Seleccione el grupo de recursos TestApp1-Development y, a continuación, haga clic en Eliminar en la hoja Grupo de recursos TestApp1-Development. Aparecerá una nueva hoja. Escriba el nombre del grupo de recursos y haga clic en el botón Eliminar. ![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Elimine los grupos de recursos TestApp1-Test y TestApp1-Pre-Production del mismo modo que eliminó el grupo de recursos TestApp1-Development.

Con independencia del método que use, los grupos de recursos y todos los recursos que contienen ya no existirán y se le dejará de cobrar por estos recursos.

Para minimizar los gastos de utilización de recursos de Azure que se producen durante el desarrollo de aplicaciones, puede usar [Automatización de Azure](automation/automation-intro.md) para programar trabajos que:

- Detengan las máquinas virtuales al final del día y las reinician al inicio del día siguiente.
- Detengan los entornos enteros al final del día y los vuelvan a crear al inicio del día siguiente.
 
Ahora que ha experimentado lo fácil que es crear, mantener y eliminar entornos de desarrollo y pruebas, puede aprender más sobre lo que acaba de hacer si sigue experimentando con los pasos anteriores y lee las referencias contenidas en este artículo.

## Pasos siguientes

- [Delegue el control administrativo](role-based-access-control-configure.md) en diferentes recursos de cada entorno y asigne grupos o usuarios de Microsoft Azure AD a roles específicos que tengan la capacidad para realizar un subconjunto de operaciones en recursos de Azure.
- [Asigne etiquetas](resource-group-using-tags.md) a los grupos de recursos para cada entorno o a los recursos individuales. Puede agregar una etiqueta "Entorno" a los grupos de recursos y establecer su valor para que coincida con los nombres de su entorno. Las etiquetas pueden resultar especialmente útiles si necesita organizar recursos para facturación o administración.
- Supervise las alertas y la facturación de los recursos del grupo de recursos en el [Portal de Azure](https://portal.azure.com).

<!---HONumber=AcomDC_0128_2016-->