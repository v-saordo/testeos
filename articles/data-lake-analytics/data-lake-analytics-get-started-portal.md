<properties 
   pageTitle="Introducción a Análisis de Azure Data Lake mediante el Portal de Azure | Azure" 
   description="Aprenda a usar el Portal de Azure para crear una cuenta de Análisis de Data Lake, crear un trabajo de Análisis de Data Lake mediante U-SQL y enviar el trabajo." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="jgao"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante el Portal de Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar el Portal de Azure para crear cuentas de Análisis de Azure Data Lake, definir trabajos de Análisis de Data Lake en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos a cuentas de Análisis de Data Lake. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para realizar el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas de la parte superior de esta sección. Una vez que el primer trabajo se lleve a cabo correctamente, puede empezar a escribir transformaciones de datos más complejas con U-SQL.

**El proceso básico de Análisis de Data Lake:**

![Diagrama de flujo de proceso de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Cree una cuenta de Análisis de Data Lake.
2. Prepare los datos de origen. Los trabajos de Análisis de Data Lake pueden leer datos desde cuentas de Almacén de Azure Data Lake o desde cuentas de almacenamiento de blobs de Azure. En este ejemplo, se leerán desde el Almacén de Azure Data Lake.  
3. Desarrolle un script U-SQL.
4. Envíe un trabajo (script U-SQL) a la cuenta de Análisis de Data Lake. El trabajo lee desde el origen de datos, procesa los datos como se indica en el script U-SQL y después guarda la salida en una cuenta de Almacén de Data Lake o de almacenamiento de blobs.

###Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

##Creación de una cuenta de Análisis de Data Lake

Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos.

Cada cuenta de Análisis de Data Lake depende de una cuenta del [Almacén de Azure Data Lake](). Esta cuenta se conoce como la cuenta predeterminada de Almacén de Data Lake. Puede crear la cuenta de Almacén de Data Lake previamente o cuando cree la cuenta de Análisis de Data Lake. En este tutorial, creará la cuenta de Almacén de Data Lake con la cuenta de Análisis de Data Lake.

**Para crear una cuenta de Análisis de Data Lake**

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, en **Datos y análisis** y después en **Análisis de Data Lake**.
6. Escriba o seleccione los valores siguientes:

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Nombre**: el nombre de la cuenta de Análisis.
	- **Almacén de Data Lake**: cada cuenta de Análisis de Data Lake depende de una cuenta del Almacén de Data Lake. La cuenta de Análisis de Data Lake y la cuenta de Almacén de Data Lake dependiente deben ubicarse en el mismo centro de datos de Azure. Siga las instrucciones para crear una nueva cuenta de Almacén de Data Lake o seleccione una existente.
	- **Suscripción**: seleccione la suscripción de Azure usada para la cuenta de Análisis.
	- **Grupo de recursos**: seleccione un grupo de recursos de Azure existente o cree uno nuevo. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md). 
	- **Ubicación**: seleccione un centro de datos de Azure para la cuenta de Análisis de Data Lake. 
7. Seleccione **Anclar a Panel de inicio**. Esto es necesario para seguir este tutorial.
8. Haga clic en **Crear**. Se abre el Panel de inicio del portal. Se agrega un nuevo icono al Panel de inicio con la etiqueta "Implementación de Análisis de Azure Data Lake". Se tarda unos momentos en crear una cuenta de Análisis de Data Lake. Cuando la cuenta está creada, se abre la cuenta en una hoja nueva en el portal.

	![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


Una vez creada la cuenta de Análisis de Data Lake, puede agregar más cuentas de Almacén de Data Lake y cuentas de Almacenamiento de Azure. Para obtener instrucciones, consulte [Administración de orígenes de datos de la cuenta de Análisis de Data Lake](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##Preparación de los datos de origen

En este tutorial, va a procesar algunos registros de búsqueda. El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

El Portal de Azure proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo a la cuenta predeterminada de Data Lake, entre los que se incluye un archivo de registro de búsqueda.

**Para copiar los archivos de datos de ejemplo**

1. En el Portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el icono con el nombre de la cuenta de Análisis de Data Lake. Se ancló aquí cuando se creó la cuenta. Si la cuenta no está anclada ahí, consulte [Apertura de una cuenta de Análisis de Data Lake desde el portal](data-lake-analytics-manage-use-portal.md#access-adla-account) para abrirla.
3. Expanda el panel **Essentials** y después haga clic en **Explorar trabajos de ejemplo**. Se abre otra hoja llamada **Trabajos de ejemplo**.
4. Haga clic en **Copiar datos de ejemplo** y después haga clic en **Aceptar** para confirmar.
5. Haga clic en **Notificación**, un icono en forma de campana. Verá un registro que indica: **Actualización de datos de ejemplo completada**. Haga clic en cualquier lugar fuera del panel de notificación para cerrarlo.
7. En la hoja de la cuenta de Análisis de Data Lake, haga clic en **Explorador de datos** en la parte superior. 

	![Botón Explorador de datos de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Se abren dos hojas. Una es **Explorador de datos** y la otra, la cuenta predeterminada de Almacén de Data Lake.
8. En la hoja de la cuenta predeterminada de Almacén de Data Lake, haga clic en **Ejemplos** para expandir la carpeta y después en **Datos** para expandirla también. Verá los siguientes archivos y carpetas:

    - AmbulanceData/
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    En este tutorial, usará SearchLog.tsv.

En la práctica, programará sus aplicaciones para que escriban datos en una cuenta de almacenamiento vinculada o para cargar datos. Para cargar archivos, consulte [Carga de datos en el Almacén de Data Lake](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) o [Carga de datos en el almacenamiento de blobs](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

##Creación y envío de trabajos de Análisis de Data Lake

Después de preparar el origen de datos, puede comenzar a desarrollar un script U-SQL.

**Para enviar el trabajo**

1. En la hoja de la cuenta de Análisis de Data Lake en el portal, haga clic en **Nuevo trabajo**. 

	![Botón Nuevo trabajo de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Si no ve la hoja, consulte [Apertura de una cuenta de Análisis de Data Lake desde el portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
4. Escriba el **Nombre del trabajo** y el siguiente script U-SQL:

	![Crear trabajos de U-SQL de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y después crea un archivo csv mediante **Outputters.Csv()**.
    
    No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente. Análisis de Data Lake creará la carpeta de salida si no existe. En este caso, usamos rutas de acceso relativas sencillas.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Para obtener más información acerca de U-SQL, consulte [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md) y la página de [referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
     
5. Haga clic en **Enviar trabajo** en la parte superior. Se abre un nuevo panel llamado Detalles del trabajo. En la barra de título, se muestra el estado del trabajo.
6. Espere a que el estado del trabajo cambie a **Correcto**. Cuando se completa el trabajo, se abre una nueva hoja en el portal con los detalles del trabajo:

    ![Detalles del trabajo de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    En la captura de pantalla anterior, puede ver que el trabajo tardó aproximadamente 1,5 minutos en completarse desde que se envío hasta que se finalizó.
    
    Si se produce un error en el trabajo, consulte la página sobre la [supervisión y la solución de problemas con trabajos de Análisis de Data Lake](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md).

7. Al final de la hoja **Detalles del trabajo**, haga clic en el nombre del trabajo en **SearchLog-from-Data-Lake.csv**. Puede obtener una vista previa, descargar, cambiar el nombre y eliminar el archivo de salida.

    ![Propiedades del archivo de salida del trabajo de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. Haga clic en **Vista previa** para ver el archivo de salida.

    ![Vista previa del archivo de salida del trabajo de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##Consulte también

- Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).
- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.

<!---HONumber=AcomDC_0114_2016-->