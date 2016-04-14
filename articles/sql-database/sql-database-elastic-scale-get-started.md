<properties 
	pageTitle="Introducción a las herramientas de base de datos elástica" 
	description="Explicación básica de la característica de herramientas de base de datos elástica de Base de datos SQL de Azure, incluida una aplicación de ejemplo de ejecución sencilla." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove" 
	editor="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016" 
	ms.author="ddove;sidneyh"/>

# Introducción a las herramientas de base de datos elástica

Este documento le presentaba la experiencia del desarrollador al ejecutar la aplicación de ejemplo. El ejemplo crea una aplicación particionada sencilla y explora las capacidades clave de las herramientas de base de datos elástica. El ejemplo muestra las funciones de la [biblioteca de cliente de Base de datos elástica](sql-database-elastic-database-client-library.md)

## Requisitos previos

1. Se necesita Visual Studio 2012 o superior con C#. Descargue una versión gratuita desde [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 o superior. Para obtener la versión más reciente, consulte [Instalación de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## Descarga y ejecución de la aplicación de ejemplo

La aplicación de ejemplo **Base de datos elástica con SQL de Azure - Introducción** ilustra los aspectos más importantes de la experiencia de desarrollo para aplicaciones particionadas usando herramientas de Base de datos elástica de SQL de Azure. Se centra en los casos de uso claves para la [administración de asignación de particiones](sql-database-elastic-scale-shard-map-management.md), el [enrutamiento dependiente de datos](sql-database-elastic-scale-data-dependent-routing.md) y las [consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md). Para descargar y ejecutar el ejemplo, siga estos pasos:

1. Abra Visual Studio y seleccione **Archivo -> Nuevo -> Proyecto**.
2. En el cuadro de diálogo, haga clic en **En línea**.

    ![New Project>Online][2]
3. A continuación, en **Ejemplos**, haga clic en **Visual C#**.

    ![Click Visual C#][3]
4. En el cuadro de búsqueda, escriba **elastic db** para buscar el ejemplo. Aparece el título **Elastic DB Tools for Azure SQL - Getting Started**.

    ![Search Box][1]
 
5. Seleccione el ejemplo, elija un nombre y una ubicación para el nuevo proyecto y presione **Aceptar** para crear el proyecto.
6. Abra el archivo **app.config** en la solución para el proyecto de ejemplo y siga las instrucciones del archivo para agregar el nombre de servidor de base de datos SQL y su información de inicio de sesión (nombre de usuario y contraseña).
7. Compile y ejecute la aplicación. Cuando se le pregunte, permita que Visual Studio restaure los paquetes de NuGet de la solución. Esto descargará la versión más reciente de la biblioteca de clientes de base de datos elástica desde NuGet.
8. Juegue con las diferentes opciones para obtener información sobre las capacidades de la biblioteca de clientes. Anote los pasos que la aplicación lleva a cabo en la salida de la consola y explore el código que hay detrás a su antojo.

    ![progreso][4]

Enhorabuena; ha creado y ejecutado su primera aplicación particionada utilizando herramientas de base de datos elástica en Base de datos SQL de Azure. Eche un vistazo rápido a las particiones que creó el ejemplo conectando Visual Studio o SQL Server Management Studio a su servidor de Base de datos de Azure. Observará nuevas bases de datos de particiones de ejemplo y una base de datos de administrador de mapas de particiones que ha creado el ejemplo.

**Nota**: si no tiene SQL Server Management Studio, consulte [Administración de Base de datos SQL de Azure usando SQL Server Management Studio](sql-database-manage-azure-ssms.md), que incluye instrucciones para obtener la herramienta.

### Piezas clave del ejemplo de código

1. **Administración de particiones y mapas de particiones**: el código ilustra cómo trabajar con particiones, intervalos y asignaciones en el archivo **ShardMapManagerSample.cs**. Puede encontrar más información acerca de este tema aquí: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).  
2. **Enrutamiento dependiente de los datos**: el enrutamiento de transacciones a la partición correcta se muestra en el archivo **DataDependentRoutingSample.cs**. Para obtener más detalles, consulte [Enrutamiento dependiente de datos](http://go.microsoft.com/?linkid=9862596). 
3. **Consultas a través de particiones múltiples**: las consultas a través de particiones se ilustran en el archivo **MultiShardQuerySample.cs**. Para obtener más detalles, consulte [Consultas a través de particiones múltiples](http://go.microsoft.com/?linkid=9862597).
4. **Incorporación de particiones vacías**: la incorporación interactiva de nuevas particiones vacías se realiza mediante el código del archivo **AddNewShardsSample.cs**. Aquí encontrará detalles de este tema: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).

### Otras operaciones de escalado elástico

1. **División de una partición existente**: la capacidad de dividir particiones se proporciona a través de la **herramienta de división y combinación**. Puede encontrar más información sobre esta herramienta aquí: [Información general sobre la herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Combinación de particiones existentes**: las combinaciones de particiones también se realizan mediante la **herramienta de división y combinación**. Para obtener más información, consulte: [Información general sobre la herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md).   


## Coste

Las herramientas de base de datos elástica son gratuitas. No imponen cargos adicionales al coste del uso de Azure.

Por ejemplo, la aplicación de ejemplo crea nuevas bases de datos. El coste depende de la edición de Base de datos SQL de Azure que elija y del uso de Azure de la aplicación.

Para obtener información sobre los precios, consulte [Detalles de precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Pasos siguientes
Para obtener más información sobre las herramientas de base de datos elástica, consulte:

* [Mapa de documentación de herramientas de base de datos elástica](../learning-paths/sql-database-elastic-scale.md) 
-    Ejemplos de código: 
    -    [Elastic DB with Azure SQL - Getting Started (Bases de datos elásticas con SQL de Azure - Introducción)](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastic DB with Azure SQL - Integrating with Entity Framework (Bases de datos elásticas con SQL de Azure - Integración con Entity Framework)](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard Elasticity on Script Center (Elasticidad de particiones en el Centro de scripts)](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [Elastic Scale Announcement](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/) (Anuncio de Escalado elástico)
-    Canal 9: [vídeo Elastic Scale Overview](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale) (Información general de Escalado elástico)
-    Foro de discusión: [Foro de Base de datos SQL de Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=AcomDC_0204_2016-->