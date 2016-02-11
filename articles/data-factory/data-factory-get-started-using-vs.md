<properties 
	pageTitle="Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio" 
	description="En este tutorial, creará una canalización de la factoría de datos de Azure con una actividad de copia mediante Visual Studio." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="11/02/2015" 
	ms.author="spelluru"/>

# Tutorial: Creación y supervisión de una factoría de datos mediante Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Apartados de este tutorial
En este tutorial hará lo siguiente mediante Visual Studio 2013:

1. Cree dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlinkedService1**. AzureStorageLinkedService1 vincula un almacenamiento de Azure y AzureSqlLinkedService1 vincula una Base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactoryVS**. Los datos de entrada para la canalización se encuentran en un contenedor de blob en el almacenamiento de blobs de Azure y los datos de salida se almacenarán en una tabla en la base de datos SQL de Azure. Por lo tanto, agregue estos almacenes de datos como servicios vinculados en la factoría de datos.
2. Creará dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida. También especificará otras propiedades como la estructura de los datos, la disponibilidad de los datos, etc.
3. Cree una canalización denominada **ADFTutorialPipeline** en ADFTutorialDataFactoryVS. La canalización dispondrá de una opción para la **actividad de copia** que copia datos de entrada del blob de Azure a la tabla de salida de Azure SQL.
4. Cree una factoría de datos e implemente servicios vinculados, tablas y la canalización.    

## Creación e implementación de las entidades de Factoría de datos con Visual Studio 

### Requisitos previos
Debe tener instalado en el equipo lo siguiente: - Visual Studio 2013 - Descargue el SDK de Azure para Visual Studio 2013. Vaya a la [Página de descargas de Azure](https://azure.microsoft.com/downloads/) y haga clic en **Instalación de VS 2013**en la sección **.NET**.

### Tutorial

#### Creación del proyecto de Visual Studio 
1. Inicie **Visual Studio 2013**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debería ver el cuadro de diálogo **Nuevo proyecto**.  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de factoría de datos**. Si no ve la plantilla DataFactory, cierre Visual Studio, instale el SDK de Azure para Visual Studio 2013 y vuelva a abrir Visual Studio.  

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; a continuación, haga clic en **Aceptar**.

	![Explorador de soluciones](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, una base de datos SQL de Azure o una base de datos SQL Server local.

En este paso, creará dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlLinkedService1**. El servicio vinculado AzureStorageLinkedService1 vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a la factoría de datos: **ADFTutorialDataFactory**.

##### Creación del servicio vinculado de Almacenamiento de Azure

4. Haga clic con el botón derecho en **Servicios vinculados**en el Explorador de soluciones, seleccione **Agregar** y haga clic en **Nuevo elemento**.      
5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Servicio vinculado de Almacenamiento de Azure** en la lista y haga clic en **Agregar**. 

	![Nuevo servicio vinculado](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Reemplace **accountname** y **accountkey** por el nombre de la cuenta de almacenamiento de Azure y su clave.

	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Guarde el archivo **AzureStorageLinkedService1.json**.

#### Cree el servicio vinculado SQL de Azure.

5. Haga doble clic con el botón derecho en el nodo **Servicios vinculados ** en el **Explorador de soluciones** de nuevo, apunte a **Agregar** y haga clic en **Nuevo elemento**. 
6. Esta vez, seleccione **Servicios vinculados de SQL Azure** y haga clic en **Agregar**. 
7. En el archivo **AzureSqlLinkedService1.json** reemplace **servername**, **databasename**, **username@servername** y **password** por los nombres del servidor de SQL Azure, la base de datos, la cuenta de usuario y la contraseña.
8.  Guarde el archivo **AzureSqlLinkedService1.json**. 


### Crear tablas de entrada y salida
En el paso anterior, creó los servicios vinculados **AzureStorageLinkedService1** y **AzureSqlLinkedService1** para vincular una cuenta de Almacenamiento de Azure y una base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactory**. En este paso, definirá dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos a los que hacen referencia AzureStorageLinkedService1 y AzureSqlLinkedService1 respectivamente. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida.

#### Creación de la tabla de entrada

9. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.
10. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Blob de Azure** y haga clic en **Agregar**.   
10. Reemplace el texto JSON con el texto siguiente y guarde el archivo **AzureBlobLocation1.json**. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

#### Creación de una tabla de salida

11. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones** de nuevo, seleccione **Agregar** y haga clic en **Nuevo elemento**.
12. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **SQL de Azure** y haga clic en **Agregar**. 
13. Reemplace el texto JSON con el JSON siguiente y guarde el archivo **AzureSqlTableLocation1.json**.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

#### Creación de la canalización 
Ya ha creado las tablas y los servicios vinculados de entrada/salida. Ahora, va a crear una canalización con una **actividad de copia** para copiar datos del blob de Azure a Base de datos SQL de Azure.


1. Haga clic con el botón derecho en **Canalizaciones** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.  
15. Seleccione **Copiar canalización de datos** en el cuadro de diálogo **Agregar nuevo elemento** y haga clic en **Agregar**. 
16. Reemplace el JSON por el siguiente JSON y guarde el archivo **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

#### Publicación e implementación de las entidades de Factoría de datos
  
18. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
20. Debería ver el siguiente cuadro de diálogo:

	![Cuadro de diálogo Publicar](./media/data-factory-get-started-using-vs/publish.png)

21. En la página Configurar la factoría de datos, haga lo siguiente:
	1. Seleccione la opción **Crear la nueva factoría de datos**.
	2. Escriba **VSTutorialFactory** para **Nombre**.  
	
		> [AZURE.NOTE]  
		El nombre del generador de datos de Azure debe ser único global. Si recibe un error sobre el nombre de la factoría de datos cuando se publica, cámbielo (por ejemplo, sunombreVSTutorialFactory) e intente publicar de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
		> 
		> El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.
	3. Seleccione la suscripción correcta en el campo **Suscripción**. 
	4. Seleccione el **grupo de recursos** para la factoría de datos que se va a crear. 
	5. Seleccione la **Región** de la factoría de datos. 
	6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**. 
23. En la página **Publicar elementos**, asegúrese de que todas las factorías de datos están seleccionadas y haga clic en **Siguiente** para cambiar a la página **Resumen**.     
24. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página **Estado de implementación**, debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar. 


## Uso del Explorador de servidores para revisar las entidades de Factoría de datos

1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y **Factoría de datos**. Si aparece **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure en su suscripción. Verá el estado de esta operación en la ventana **Lista de tareas de Factoría de datos**.
	![Explorador de servidores](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar Exportar Factoría de datos al nuevo proyecto para crear un proyecto de Visual Studio basado en una factoría de datos existente. 
	![Exportar factoría de datos a un proyecto de VS](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Actualización de herramientas de Factoría de datos para Visual Studio
Para actualizar las herramientas de Factoría de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en el menú **Herramientas** y seleccione **Extensiones y actualizaciones**. 
2. Seleccione **Actualizaciones** en el panel izquierdo y, luego, seleccione **Galería de Visual Studio**.
4. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Consulte [Supervisión de los conjuntos de datos y la canalización](data-factory-get-started-using-editor.md#MonitorDataSetsAndPipeline) para obtener instrucciones sobre cómo usar el Portal de Azure para supervisar la canalización y los conjuntos de datos creados en este tutorial.

<!---HONumber=AcomDC_0128_2016-->