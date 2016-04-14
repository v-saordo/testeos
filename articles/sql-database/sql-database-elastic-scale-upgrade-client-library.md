<properties
	
	pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
	description="Upgrade apps and libraries using Nuget" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/01/2016" 
	ms.author="ddove;sidneyh" />

# Actualización a la última biblioteca de clientes de base de datos elástica

Hay nuevas versiones disponibles de la [biblioteca de clientes de Base de datos elástica](sql-database-elastic-database-client-library.md) a través de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) y de la interfaz del Administrador de paquetes de NuGet en Visual Studio. Las actualizaciones contienen correcciones de errores y compatibilidad para nuevas capacidades de la biblioteca de clientes.

Vuelve a generar la aplicación con la nueva biblioteca, así como cambiar los metadatos de administrador de asignación de particiones existentes almacenados en sus bases de datos SQL de Azure para admitir nuevas características.

Al realizar estos pasos de actualización en orden se asegura de que las versiones anteriores de la biblioteca de cliente ya no está presentes en el entorno cuando se actualizan los objetos de metadatos, lo que significa que no se crearán objetos de metadatos de la antigua versión después de la actualización.

## Pasos de actualización

**1. Actualice sus aplicaciones.** En Visual Studio, descargue y haga referencia a la versión más reciente de la biblioteca de clientes en todos los proyectos de desarrollo que usan la biblioteca; a continuación, vuelva a compilar e implementar.

 * En la solución de Visual Studio, seleccione **Herramientas** --> **Administrador de paquetes de NuGet** --> **Administrar paquetes de NuGet para solución**. 
 * (Visual Studio 2013) En el panel izquierdo, seleccione **Actualizaciones** y, a continuación, seleccione el botón **Actualizar** en el paquete **Biblioteca de clientes de escala elástica de Base de datos SQL de Azure** que aparece en la ventana.
 * (Visual Studio 2015) Establezca el cuadro de filtro en **Actualización disponible**. Seleccione el paquete que quiera actualizar y haga clic en el botón **Actualizar**.
	
 
 * Genere e implemente.

**2. Actualice los scripts.** Si está utilizando scripts de **PowerShell** para administrar particiones, [descargue la nueva versión de la biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) y cópiela en el directorio desde el que ejecuta los scripts.

**3. Actualice el servicio de división y combinación.** Si usa la herramienta de división y combinación de base de datos elástica para reorganizar datos particionados, [descargue e implemente la versión más reciente de la herramienta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Puede encontrar pasos detallados para la actualización del servicio [aquí](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Actualice sus bases de datos de administrador de asignación de partición**. Actualice los metadatos compatibles con sus asignaciones de partición en la base de datos SQL de Azure. Hay dos maneras de hacerlo, mediante PowerShell o C#. Ambas opciones se muestran a continuación.

***Opción 1: actualizar los metadatos mediante PowerShell***

1. Descargue la utilidad de línea de comandos más reciente de NuGet desde [aquí](http://nuget.org/nuget.exe) y guárdela en una carpeta. 

2. Abra un símbolo del sistema, navegue a la misma carpeta y emita el comando: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Vaya a la subcarpeta que contiene la nueva versión DLL de cliente que acaba de descargar, por ejemplo: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Descargue el scriptlet de actualización del cliente de base de datos elástica del [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9) y guárdelo en la misma carpeta que contiene el archivo DLL.

5. Desde esa carpeta, ejecute “PowerShell .\\upgrade.ps1” desde el símbolo del sistema y siga las indicaciones.
 
***Opción 2: actualizar los metadatos mediante C#***

De forma alternativa, puede crear una aplicación de Visual Studio que abra su ShardMapManager, recorra en iteración todas las particiones y realice la actualización de metadatos mediante una llamada a los métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) y [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx), como en este ejemplo:

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

Estas técnicas para las actualizaciones de metadatos se pueden aplicar varias veces sin problema. Por ejemplo, si una versión anterior del cliente crea sin darse cuenta una partición después de haber actualizado, puede ejecutar la actualización de nuevo en todas las particiones para asegurarse de que la última versión de metadatos está presente en toda su infraestructura.

**Nota:** las nuevas versiones de la biblioteca de clientes publicadas hasta la fecha seguirán funcionando con versiones anteriores de los metadatos del Administrador de mapa de particiones en la Base de datos SQL de Azure, y viceversa. Sin embargo, para aprovechar algunas de las nuevas características en el cliente más reciente, es necesario actualizar los metadatos. Tenga en cuenta que las actualizaciones de metadatos no afectarán a los datos de usuario ni los datos específicos de la aplicación, solo a los objetos creados y utilizados por el Administrador de mapa de particiones. Las aplicaciones seguirán funcionando a través de la secuencia de actualización que se ha descrito anteriormente.

## Historial de versiones de cliente de base de datos elástica 

**Versión 1.0: abril de 2015**

* Versión de disponibilidad general
* Se ha agregado compatibilidad para los tipos de fecha y hora como claves de particionamiento

**Versión 0.8: marzo de 2015**

* Compatibilidad de Async agregada para el enrutamiento según los datos con los nuevos métodos ShardMap.OpenConnectionForKeyAsync. 
* Propiedad KeyType pública agregada a ShardMap 
* Mejoras agregadas de compatibilidad con escenarios de recuperación ante desastres y restauración de base de datos para particiones 

**Versión 0.7: octubre de 2014**

Versión inicial de vista previa


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 

<!---HONumber=AcomDC_0204_2016-->