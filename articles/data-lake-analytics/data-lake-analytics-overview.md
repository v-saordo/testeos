<properties 
   pageTitle="Información general de Análisis de Microsoft Azure Data Lake | Azure" 
   description="Análisis de Data Lake es un servicio de cálculo de macrodatos de Azure que le permite usar datos para impulsar el negocio con los conocimientos adquiridos de los datos en la nube, independientemente de dónde se encuentren y de su tamaño. Análisis de Data Lake lo permite de la forma más sencilla, escalable y económica posible." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Información general de Análisis de Microsoft Azure Data Lake

## ¿Qué es Análisis de Azure Data Lake?

Análisis de Azure Data Lake es un nuevo servicio, creado para facilitar el análisis de macrodatos. Este servicio le permite centrarse en escribir, ejecutar y administrar los trabajos, en lugar de en la infraestructura distribuida. En lugar de implementar, configurar y ajustar el hardware, escribirá consultas para transformar los datos y extraer ideas valiosas. El servicio de análisis puede tratar trabajos de cualquier escala al instante, simplemente estableciendo el ajuste adecuado. Solo tiene que pagar por su trabajo cuando se está ejecutando, lo que da lugar a una solución económica. El servicio de análisis admite Azure Active Directory, lo que le permite administrar de forma sencilla el acceso y los roles, que se integran con su sistema de identidad local. También incluye U-SQL, un lenguaje que unifica las ventajas de SQL con el poder expresivo del código del usuario. El tiempo de ejecución distribuido escalable de U-SQL permite analizar de forma eficiente los datos del almacén y entre servidores SQL Server en Azure, Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure.


## Principales capacidades

- **Escalado dinámico** 

    Análisis de Data Lake se ideó desde cero para su escala y rendimiento en la nube. Aprovisiona recursos de forma dinámica y permite analizar terabytes o incluso exabytes de datos. Cuando el trabajo finaliza, reduce los recursos automáticamente y usted solo paga por la capacidad de procesamiento que ha utilizado. Conforme aumenta o disminuye el tamaño de los datos almacenados o la cantidad de proceso utilizado, no tiene que reescribir código. Esto le permite centrarse únicamente en su lógica de negocios y no en cómo procesar y almacenar grandes conjuntos de datos.

- **Desarrollo más rápido, depuración y optimización más inteligentes con herramientas que ya conoce**

    Análisis de Data Lake está profundamente integrado con Visual Studio, de forma que puede usar herramientas familiares para ejecutar, depurar y ajustar su código. Las visualizaciones de sus trabajos de U-SQL le permiten ver cómo se ejecuta el código a escala. De este modo, puede identificar fácilmente cuellos de botella en el rendimiento y optimizar los costos.

- **U-SQL: sencillo y familiar, potente y extensible**

    Análisis de Data Lake incluye U-SQL, un lenguaje de consulta que amplia la sencilla y familiar naturaleza declarativa de SQL con la capacidad expresiva de C#. El lenguaje U-SQL está basado en el mismo motor de tiempo de ejecución distribuido que sustenta los sistemas de macrodatos en Microsoft. Ahora, millones de desarrolladores de SQL y .NET pueden procesar y analizar todos sus datos con los conocimientos que ya tienen.

- **Se integra sin problemas con su inversión en TI**

    Análisis de Data Lake puede usar su inversión actual en TI para identidades, administración, seguridad y almacenamiento de datos. Esto simplifica el control de los datos y facilita la ampliación de sus aplicaciones de datos actuales. Análisis de Data Lake se integra con Active Directory para la administración de usuarios y permisos, y viene con supervisión y auditoría integradas.

- **Asequible y rentable**

    Análisis de Data Lake es una solución muy rentable para ejecutar cargas de trabajo de macrodatos. Usted paga por trabajo cuando se procesan los datos. No se requieren licencias, hardware ni contratos de soporte específicos para el servicio. El sistema se escala o reduce verticalmente de forma automática cuando el trabajo comienza y finaliza, es decir, nunca paga por más recursos de los que necesita.

- **Funciona con todos los datos de Azure**

    Análisis de Data Lake puede funcionar con diversos orígenes de datos de Azure, como almacenamiento de blobs de Azure y Base de datos SQL de Azure, y está especialmente optimizado para trabajar con el almacén de Azure Data Lake, proporcionando el máximo nivel de rendimiento, capacidad de proceso y ejecución en paralelo para las cargas de trabajo de macrodatos.

## Consulte también

- Primeros pasos
    - [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
    - [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Introducción a Análisis de Data Lake mediante el SDK de .NET de Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake.](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL y desarrollo
    - [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake.](data-lake-analytics-u-sql-get-started.md)
    - [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
    - [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Administración
    - [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md)
    - [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
    - [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Tutorial integral
    - [Uso de tutoriales interactivos de Análisis de Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
    - [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

- Díganos su opinión
    - [Deje sus comentarios en nuestro trabajo pendiente de documentación](data-lake-analytics-documentation-backlog.md)
    - [Envíe una solicitud de característica](http://aka.ms/adlafeedback)
    - [Obtenga ayuda en los foros](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0302_2016-->