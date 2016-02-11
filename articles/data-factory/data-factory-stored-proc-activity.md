<properties 
	pageTitle="Actividad de procedimiento almacenado de SQL Server" 
	description="Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure o en un Almacenamiento de datos SQL de Azure desde una canalización de Factoría de datos." 
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
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Actividad de procedimiento almacenado de SQL Server

Puede usar la actividad de procedimiento almacenado de SQL Server en una [canalización](data-factory-create-pipelines.md) de Factoría de datos para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos.


- Base de datos SQL de Azure 
- Almacenamiento de datos SQL de Azure  
- Base de datos de SQL Server en la empresa o en una máquina virtual de Azure. Data Management Gateway se debe instalar en la misma máquina que hospeda la base de datos o en una máquina independiente, con el fin de evitar la competencia por los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales u orígenes de datos hospedados en máquinas virtuales de Azure con servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway. 

Este artículo se basa en el artículo [actividades de transformación de datos](data-factory-data-transformation-activities.md), que presenta una descripción general de la transformación de datos y las actividades de transformación admitidas.

## Sintaxis
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Detalles de la sintaxis

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
name | Nombre de la actividad | Sí
description | Texto que describe para qué se usa la actividad. | No
type | SqlServerStoredProcedure | Sí
inputs | Los conjuntos de datos de entrada que deben estar disponibles (en estado "Listo") para que se ejecute la actividad de procedimiento almacenado. Las entradas a la actividad de procedimiento almacenado solo se usan para la administración de dependencias al encadenar esta actividad con otras. Los conjuntos de datos de entrada no se pueden usar en el procedimiento almacenado como parámetro. | No
outputs | Conjuntos de datos de salida generados por la actividad de procedimiento almacenado. Asegúrese de que la tabla de salida usa un servicio vinculado que vincule una Base de datos SQL de Azure o un Almacenamiento de datos SQL de Azure con la Factoría de datos. Las salidas de la actividad de procedimiento almacenado pueden servir como una forma de pasar el resultado de la actividad de procedimiento almacenado para el posterior procesamiento o como administración de dependencias cuando se encadena esta actividad con otras. | Sí
storedProcedureName | Especifique el nombre del procedimiento almacenado en la Base de datos SQL de Azure o en el Almacenamiento de datos SQL de Azure que se representa mediante el servicio vinculado que usa la tabla de salida. | Sí
storedProcedureParameters | Especifique valores para los parámetros de procedimiento almacenado. | No

## Tutorial de ejemplo

### Procedimiento almacenado y tabla de ejemplo
> [AZURE.NOTE] En este ejemplo se usa Base de datos SQL de Azure, pero funciona de la misma manera con Almacenamiento de datos SQL de Azure y Base de datos de SQL Server.

1. Cree la siguiente **tabla** en la Base de datos SQL de Azure con SQL Server Management Studio o cualquier otra herramienta que le resulte cómoda. La columna datetimestamp indica la fecha y la hora en que se generó el identificador correspondiente. 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	Id es el identificador único y la columna datetimestamp indica la fecha y la hora en que se generó el identificador correspondiente. ![Datos de ejemplo](./media/data-factory-stored-proc-activity/sample-data.png)

2. Cree el siguiente **procedimiento almacenado** que inserta datos en **sampletable**.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT] El **nombre** y el **uso de mayúsculas y minúsculas** en el parámetro (DateTime en este ejemplo) deben coincidir con los del parámetro especificado en el código JSON de la canalización o actividad. En la definición del procedimiento almacenado, asegúrese de que se usa **@** como prefijo del parámetro.
	
### Crear una factoría de datos  
4. Tras iniciar sesión en el [Portal de Azure](https://portal.azure.com/), haga lo siguiente:
	1.	Haga clic en **NUEVO** en el menú de la izquierda. 
	2.	Haga clic en **Análisis de datos** en la hoja **Creación**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.
4.	En la hoja **Nueva factoría de datos**, escriba **SProcDF** para el nombre. Los nombres de la Factoría de datos de Azure son únicos globalmente. Deberá agregar su nombre como prefijo al nombre de la factoría de datos para permitir la creación correcta de la factoría. 
3.	Si no creó ningún grupo de recursos, tendrá que crearlo. Para ello, siga estos pasos:
	1.	Haga clic en **NOMBRE DEL GRUPO DE RECURSOS**.
	2.	Seleccione **Crear un nuevo grupo de recursos** en la hoja **Grupo de recursos**.
	3.	Escriba **ADFTutorialResourceGroup** para el **Nombre** en la hoja **Crear grupo de recursos**.
	4.	Haga clic en **Aceptar**.
4.	Una vez seleccionado el grupo de recursos, compruebe que usa la suscripción correcta en la que quiere crear la factoría de datos.
5.	Haga clic en **Crear** en la hoja **Nueva factoría de datos**.
6.	Verá que la factoría de datos se crea en el **Panel de inicio** del Portal de Azure. Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma.

### Crear un servicio vinculado SQL de Azure.  
Después de crear la factoría de datos, cree un servicio vinculado SQL de Azure que vincule la Base de datos SQL de Azure a la factoría de datos. Esta es la base de datos que contiene la tabla sampletable y el procedimiento almacenado sp\_sample.

7.	Haga clic en **Crear e implementar** en la hoja **FACTORÍA DE DATOS** para **SProcDF**. Esto inicia el Editor de la Factoría de datos. 
2.	Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **SQL de Azure**. Debería ver el script JSON para crear un servicio vinculado SQL de Azure en el editor. 
4. Reemplace **servername** por el nombre del servidor de la Base de datos SQL de Azure, **databasename** por el nombre de la base de datos donde creó la tabla y el procedimiento almacenado, ****username@servername** por la cuenta de usuario con acceso a la base de datos y **password** por la contraseña de la cuenta de usuario.
5. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

### Crear un conjunto de datos de salida
6. Haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **SQL de Azure**.
7. Copie y pegue el siguiente script JSON en el editor de JSON.

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos. 

### Crear una canalización con una actividad SqlServerStoredProcedure
Ahora, vamos a crear una canalización con una actividad SqlServerStoredProcedure.
 
9. Haga clic en **... (puntos suspensivos)** en la barra de comandos y después en **Nueva canalización**. 
9. Copie y pegue el siguiente fragmento de código JSON. El valor de **storedProcedureName** se establece en **sp\_sample**. El nombre y el uso de mayúsculas y minúsculas en el parámetro **DateTime** deben coincidir con los del parámetro de la definición del procedimiento almacenado.  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. Haga clic en **Implementar** en la barra de herramientas para implementar la canalización.  

### Supervisar la canalización

6. Haga clic en **X** para cerrar las hojas del Editor de la Factoría de datos y volver a la hoja Factoría de datos y, después, haga clic en **Diagrama**.
7. En la Vista de diagrama, verá información general de las canalizaciones y conjuntos de datos empleados en este tutorial. 
8. En la Vista de diagrama, haga doble clic en el conjunto de datos **sprocsampleout**. Se verán los segmentos con estado Listo. Debe haber 24 segmentos, porque se genera un segmento para cada hora entre el 02/01/2015 y el 03/01/2015. 
10. Cuando un segmento tiene el estado **Listo**, ejecute una consulta **select * from sampledata** en la base de datos SQL de Azure para comprobar que el procedimiento almacenado ha insertado los datos en la tabla.

	![Datos de salida](./media/data-factory-stored-proc-activity/output.png)

	Vea [Supervisar la canalización](data-factory-monitor-manage-pipelines.md) para obtener información detallada sobre cómo supervisar las canalizaciones de Factoría de datos de Azure.

> [AZURE.NOTE] En el ejemplo anterior, SprocActivitySample no tiene entradas. Si desea vincular esta cadena con una actividad de nivel superior (por ejemplo, antes del procesamiento), las salidas de la actividad de nivel superior pueden usarse como entradas en esta actividad. En este caso, esta actividad no se ejecutará hasta que se completa la actividad de nivel superior y las salidas de dichas actividades están disponibles (en estado Listo). Las entradas no se pueden usar directamente como un parámetro para la actividad de procedimiento almacenado.

## Pasar un valor estático 
Ahora, pensemos en agregar otra columna denominada 'Escenario' en la tabla que contiene un valor estático denominado 'Ejemplo de documento'.

![Datos de ejemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Para ello, pase el parámetro Escenario y el valor de la actividad de procedimiento almacenado. La sección typeProperties del ejemplo anterior es como sigue:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0128_2016-->