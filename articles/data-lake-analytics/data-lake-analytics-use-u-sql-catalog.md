<properties
   pageTitle="Introducción al catálogo de U-SQL de Análisis de Azure Data Lake | Azure"
   description="Introducción al catálogo de U-SQL de Análisis de Azure Data Lake"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/26/2015"
   ms.author="jgao"/>

# Uso del catálogo de U-SQL

El catálogo de U-SQL se usa para estructurar datos y código, para que puedan compartirse mediante scripts de U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake.

Cada cuenta de Análisis de Azure Data Lake tiene exactamente un catálogo de U-SQL asociado. No se puede eliminar el catálogo de U-SQL. Actualmente no se pueden compartir los catálogos de U-SQL entre cuentas de Almacén de Data Lake.

Cada catálogo de U-SQL contiene una base de datos denominada **maestra**. No se puede eliminar la base de datos maestra. Cada catálogo de U-SQL puede contener varias bases de datos adicionales.

La base de datos U-SQL contiene:

- Ensamblados: compartir código .NET entre scripts de U-SQL.
- Funciones de valores de tabla: compartir código de U-SQL entre scripts de U-SQL.
- Tablas: compartir datos entre scripts de U-SQL.
- Esquemas: compartir esquemas de tabla entre scripts de U-SQL.

## Administración de catálogos
Cada cuenta de Análisis de Azure Data Lake depende de una cuenta del Almacén de Azure Data Lake predeterminada asociada a ella. Esta cuenta de Almacén de Data Lake se conoce como la cuenta predeterminada del Almacén de Data Lake. El catálogo de U-SQL se almacena en la cuenta predeterminada del Almacén de Data Lake en la carpeta /catalog. No elimine ningún archivo de la carpeta /catalog.

### Usar el portal de Azure

Consulte [Administración de Análisis de Data Lake mediante el Portal](data-lake-analytics-use-portal.md#view-u-sql-catalog)


### Uso de Data Lake Tools for Visual Studio.

Puede usar Data Lake Tools for Visual Studio para administrar el catálogo. Para obtener más información acerca de las herramientas, consulte [Uso de Data Lake Tools for Visual Studio para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Para administrar el catálogo**

1. Abra Visual Studio y conéctese a Azure. Para obtener instrucciones, consulte [Conectar a Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Presione **CTRL+ALT+S** para abrir el **Explorador de servidores**.
2. Desde el **Explorador de servidores**, expanda **Azure**, expanda **Análisis de Data Lake**, expanda su cuenta de Análisis de Data Lake, expanda **Bases de datos** y, finalmente, expanda **Maestro**.



    - Para agregar una nueva base de datos, haga clic con el botón derecho en **Base de datos** y, después, haga clic en **Crear base de datos**.
    - Para agregar un nuevo ensamblado, haga clic con el botón derecho en **Ensamblados** y, después, haga clic en **Registrar ensamblado**.
    - Para agregar un nuevo esquema, haga clic con el botón derecho en **Esquemas** y, después, haga clic en "Crear esquema**.
    - Para agregar una nueva tabla, haga clic con el botón derecho en **Tablas** y, después, haga clic en ""Crear tabla**.
    - Para agregar una función con valores de tabla, consulte [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Examen de catálogos de U-SQL de Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## Consulte también

- Primeros pasos
    - [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
    - [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Introducción a Análisis de Data Lake mediante el SDK de .NET de Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

- U-SQL y desarrollo
    - [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
    - [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
    - [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Data Lake](data-lake-analtyics-u-sql-user-defined-operators.md)

- Administración
    - [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-use-portal.md)
    - [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-use-powershell.md)
    - [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Tutorial integral
    - [Uso de tutoriales interactivos de Análisis de Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
    - [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_1203_2015-->