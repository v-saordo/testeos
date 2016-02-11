<properties 
   pageTitle="Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio | Azure" 
   description="Aprenda a instalar Data Lake Tools for Visual Studio, y a desarrollar y probar scripts U-SQL." 
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
   ms.date="01/07/2015"
   ms.author="jgao"/>

# Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Aprenda a instalar Data Lake Tools for Visual Studio y a usar Data Lake Tools for Visual Studio para escribir y probar scripts U-SQL.

U-SQL es un lenguaje muy escalable y altamente extensible para preparar, transformar y analizar todos los datos que hay en Data Lake y más allá. Para obtener más información, consulte [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).


###Requisitos previos

- **Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C++ instalado** 
- **SDK de Microsoft Azure para .NET versión 2.7.1 o posterior**. Instálelo usando el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**. 

    Una vez que instale Data Lake Tools for Visual Studio, verá el menú Data Lake en Visual Studio:
    
    ![Menú U-SQL en Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Consulte las siguientes dos secciones de [Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)**.

	- [Creación de una cuenta de Análisis de Azure Data Lake](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
	- [Carga de SearchLog.tsv en la cuenta predeterminada de Almacén de Data Lake](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Para su comodidad, puede encontrar un script de ejemplo de PowerShell para crear un servicio de Análisis de Data Lake y cargar un archivo de datos de origen en [Ejemplo de PowerShell Appx-A para preparar el tutorial](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).
    
	Data Lake Tools no permite crear cuentas de Análisis de Data Lake. Por lo tanto, tendrá que crearla mediante el Portal de Azure, Azure PowerShell, SDK para .NET o CLI de Azure. Para ejecutar un trabajo de Análisis de Data Lake, necesitará algunos datos. Aunque Data Lake Tools permite cargar datos, se usará el portal para cargar los datos de ejemplo para que el tutorial sea más fácil de seguir.

## Conexión a Azure

**Para conectarse a Análisis de Data Lake**

1. Abra Visual Studio.
2. En el menú **Ver**, haga clic en **Explorador de servidores** para abrir el Explorador de servidores. O bien, presione **[CTRL] + [ALT] + S**.
3. Haga clic con el botón derecho en **Azure**, haga clic en "Conectar a la suscripción de Microsoft Azure" y luego siga las instrucciones.
4. En el **Explorador de servidores**, expanda **Azure** y después **Análisis de Data Lake**. Verá una lista de las cuentas de Análisis de Data Lake, si las hay. No se pueden crear cuentas de Análisis de Data Lake desde Visual Studio. Para crear una cuenta, consulte [Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md) o [Introducción a Análisis de Azure Data Lake mediante Azure PowerShell](data-lake-analytics--get-started-powershell.md).

## Carga de archivos de datos de origen

Se cargaron algunos datos en la sección **Requisitos previos**, anteriormente en el tutorial.

Si desea usar sus propios datos, estos son los procedimientos para cargar los datos desde Data Lake Tools.

**Para cargar archivos en la cuenta de Azure Data Lake dependiente**

1. En el **Explorador de servidores**, expanda **Azure**, expanda **Análisis de Data Lake**, expanda su cuenta de Análisis de Data Lake y expanda **Cuentas de almacenamiento**. Verá la cuenta de Almacén de Data Lake predeterminada, las cuentas de Almacén de Data Lake vinculadas y las cuentas de almacenamiento de Azure vinculadas. La cuenta de Data Lake predeterminada tiene la etiqueta "Cuenta de almacenamiento predeterminada".
2. Haga clic con el botón derecho en la cuenta de Almacén de Data Lake predeterminada y después haga clic en **Explorador**. Se abre el panel del explorador de Data Lake Tools for Visual Studio. En la izquierda muestra una vista de árbol y la vista de contenido está a la derecha.
3. Vaya a la carpeta donde desea cargar los archivos. 
4. Haga clic con el botón derecho en cualquier espacio en blanco y después haga clic en **Cargar**. 

	![proyecto U-SQL de Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Para cargar archivos en una cuenta de Almacenamiento de blobs de Azure vinculada**

1. En el **Explorador de servidores**, expanda **Azure**, expanda **Análisis de Data Lake**, expanda su cuenta de Análisis de Data Lake y expanda **Cuentas de almacenamiento**. Verá la cuenta de Almacén de Data Lake predeterminada, las cuentas de Almacén de Data Lake vinculadas y las cuentas de almacenamiento de Azure vinculadas. 
2. Expanda la cuenta de almacenamiento de Azure.
3. Haga clic con el botón derecho en el contenedor donde desea cargar los archivos y después haga clic en **Explorador**. Si no tiene un contenedor, primero debe crearlo en el Portal de Azure, con Azure PowerShell o con otras herramientas.
4. Vaya a la carpeta donde desea cargar los archivos. 
5. Haga clic con el botón derecho en cualquier espacio en blanco y después haga clic en **Cargar**. 

## Desarrollar scripts U-SQL 

Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para crear y enviar un trabajo de Análisis de Data Lake**

1. En el menú **Archivo**, haga clic en **Nuevo** y, después, en **Proyecto**.
2. Seleccione el tipo de **proyecto U-SQL**.

	![Nuevo proyecto U-SQL en Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Haga clic en **Aceptar**. Visual Studio crea una solución con un archivo **Script.usql**.
4. Incluya el siguiente script en el archivo **Script.usql**:

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
    
    No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente. Análisis de Data Lake creará la carpeta de salida si no existe.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.

	Tenga en cuenta las siguientes características:

	- **IntelliSense**
	 
		Se mostrarán los nombres completados automáticamente y los miembros de las entidades conjunto de filas, clases, bases de datos, esquemas y objetos definidos por el usuario (UDO).
		 
		IntelliSense para las entidades de catálogo (bases de datos, esquemas, tablas, UDO, etc.) está relacionado con la cuenta de proceso. Puede comprobar la cuenta de proceso activa actual, la base de datos y el esquema en la barra de herramientas superior y cambiarlos en las listas desplegables.
 
    - **Expandir columnas ***

        Haga clic a la derecha de *; aparecerá subrayado azul bajo *. Mantenga el cursor del ratón sobre el subrayado azul y después haga clic en la flecha hacia abajo. ![Data Lake visual studio tools expand *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

        Haga clic en **Expand Columns (Expandir columnas)** y la herramienta reemplazará * por los nombres de columna.
    
	- **Formato automático**
	
		Los usuarios pueden cambiar la sangría del script Scope basándose en la estructura de código que aparece en Edición -> Opciones avanzadas:
  
		- Dar formato al documento (CTRL+E, D): da formato a todo el documento.   
		- Dar formato a la selección (CTRL+K, CTRL+F): da formato a la selección. Si no se ha realizado ninguna selección, este método abreviado da formato a la línea en la que se encuentra el cursor.  
		
		Todas las reglas de formato se pueden configurar en Herramientas -> Opciones -> Editor de texto -> SIP -> Formato.  
	- **Sangría inteligente**
	 
		Data Lake Tools for Visual Studio puede aplicar sangría automáticamente a las expresiones al escribir scripts. Esta característica está deshabilitada de forma predeterminada; los usuarios deben activarla en U-SQL -> Opciones y configuración -> Modificadores -> Habilitar sangría inteligente.

	- **Ir a definición y Buscar todas las referencias**
	
		Hacer clic con el botón derecho en el nombre de un conjunto de filas, parámetro, columna, UDO, etc. y hacer clic en Ir a definición (F12) permite ir a su definición. Al hacer clic en Buscar todas las referencias (Mayús+F12) se mostrarán todas las referencias.

	- **Insertar ruta de acceso de Azure**
		
		En lugar de tener que recordar la ruta de acceso de archivo de Azure y escribirla manualmente al escribir scripts, Data Lake Tools for Visual Studio proporciona una manera fácil: haga clic con el botón derecho en el editor Y haga clic en Insertar ruta de acceso de Azure. Vaya hasta el archivo en el cuadro de diálogo Explorador de blobs de Azure. Haga clic en **Aceptar**. La ruta de acceso del archivo se insertará en el código.

5. Especifique la cuenta de Análisis de Data Lake, la base de datos y el esquema. Puede seleccionar **(local)** para ejecutar el script de forma local para pruebas. Para más información, vea [Ejecución de U-SQL localmente](#run-u-sql-locally).

	![Enviar proyecto U-SQL de Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Para obtener más información, consulte [Uso del catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md).

5. En el **Explorador de soluciones**, haga clic con el botón derecho en **Script.usql** y después haga clic en **Compilar script**. Compruebe el resultado en el panel Salida.
6. En el **Explorador de soluciones**, haga clic con el botón derecho en **Script.usql** y después haga clic en **Enviar script**. Si lo desea, también puede hacer clic **Enviar** en el panel de Script.usql. Consulte la captura de pantalla anterior. Haga clic en la flecha hacia abajo situada junto al botón Enviar para enviar mediante las opciones avanzadas:
7. Especifique el **Nombre del trabajo**, compruebe la **Cuenta de Análisis** y haga clic en **Enviar**. Los resultados del envío y el vínculo del trabajo están disponibles en la ventana de resultados de Data Lake Tools for Visual Studio cuando se completa el envío.

	![Enviar proyecto U-SQL de Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Puede hacer clic en el botón Actualizar para ver el estado más reciente del trabajo y actualizar la pantalla. Cuando el trabajo se completa correctamente, se muestra **Job Graph (Gráfico del trabajo)**, **Meta Data Operations (Operaciones de metadatos)**, **State History (Historial de estado)**, **Diagnostics (Diagnósticos)**:

	![Gráfico de rendimiento del trabajo de Análisis de Data Lake de U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* Resumen del trabajo. Muestra la información de resumen del trabajo actual, por ejemplo: estado, progreso, tiempo de ejecución, nombre en tiempo de ejecución, remitente, etc.   
	* Detalles del trabajo. Se proporciona información detallada acerca de este trabajo, incluidos scripts, recursos y la vista de ejecución de vértices.
	* Gráfico del trabajo. Se proporcionan cuatro gráficos para visualizar la información del trabajo: progreso, datos leídos, datos escritos, tiempo de ejecución, promedio de tiempo de ejecución por nodo, capacidad de proceso de entrada, capacidad de proceso de salida.
	* Operaciones de metadatos. Muestra todas las operaciones de metadatos.
	* Historial de estado. 
	* Diagnóstico. Data Lake Tools for Visual Studio diagnosticará automáticamente la ejecución del trabajo. Recibirá alertas cuando en caso de errores o problemas de rendimiento en sus trabajos. Consulte Diagnóstico de trabajos (vínculo por determinar) para obtener más información. 
	
**Para comprobar el estado del trabajo**

1. En el Explorador de servidores, expanda **Azure**, expanda **Análisis de Data Lake** y expanda el nombre de la cuenta de Análisis de Data Lake.
2. Haga doble clic en **Trabajos** para enumerar los trabajos.
2. Haga clic en un trabajo para ver el estado.

**Para ver la salida del trabajo**

1. En el **Explorador de servidores**, expanda **Azure**, **Análisis de Data Lake**, su cuenta de Análisis de Data Lake y **Cuentas de almacenamiento**; haga clic con el botón derecho en la cuenta de Almacén de Data Lake y después haga clic en **Explorador**. 
2.  Haga doble clic en **salida** para abrir la carpeta.
3.  Haga doble clic en **SearchLog-From-adltools.csv**.


###Reproducción del trabajo 

La reproducción del trabajo permite ver el progreso de la ejecución del trabajo y detectar visualmente los cuellos de botella y anomalías de rendimiento. Esta característica se puede usar antes de que finalice la ejecución del trabajo (es decir, durante el tiempo en que el trabajo se está ejecutando activamente), así como una vez finalizada la ejecución. Realizar la reproducción durante la ejecución del trabajo permitirá al usuario reproducir el progreso hasta el momento actual.

**Para ver el progreso de la ejecución del trabajo**

1. Haga clic en **Load Profile (Cargar perfil)** en la esquina superior derecha. Consulte la captura de pantalla anterior.
2. Haga clic en el botón Reproducir, en la esquina inferior izquierda, para revisar el progreso de la ejecución del trabajo. 
3. Durante la reproducción, haga clic en **Pausa** para detenerla o arrastre directamente la barra de progreso a posiciones específicas. 


###Mapa térmico 

Data Lake Tools for Visual Studio permite realizar superposiciones de colores seleccionables por el usuario en la vista del trabajo para indicar el progreso, la E/S de datos, el tiempo de ejecución, la capacidad de proceso de E/S de cada fase. De este modo, los usuarios pueden determinar posibles problemas y las propiedades de distribución del trabajo de forma directa e intuitiva. Puede elegir un origen de datos para mostrar en la lista desplegable.

## Ejecutar SQL U localmente

Con la experiencia de ejecución local de U-SQL en Visual Studio, puede:

- Ejecutar scripts de U-SQL localmente, junto con los ensamblados de C#. 
- Depurar ensamblados de C# localmente. 
- Cree, elimine y vea bases de datos locales, ensamblados, esquemas y tablas en el Explorador de servidores de la misma manera que puede hacerlo para el servicio de Análisis con Azure Data Lake. 

Verá una cuenta *Local* en Visual Studio y el instalador creará una carpeta *DataRoot* que se encuentra en *C:\\LocalRunRoot*. Se usará la carpeta DataRoot:

- Almacene metadatos incluidos tablas, bases de datos, funciones TVF, etc.
- Para un script determinado: si se hace referencia a una ruta de acceso relativa en rutas de acceso de entrada y salida, buscaremos la DataRoot (así como la ruta de acceso del script si es su entrada).
- NO se hará referencia a la carpeta DataRoot si está intentando registrar un ensamblado y usar una ruta de acceso relativa (vea la parte "Usar ensamblados al realizar la ejecución local" para más detalles).

El vídeo siguiente muestra la característica de ejecución local U-SQL:

>[AZURE.VIDEO usql-localrun]

### Problemas conocidos y limitaciones

- La ejecución Local de U-SQL no admite consultas de conjuntos de archivos localmente. Vea [Conjuntos de archivos de U-SQL](https://msdn.microsoft.com/library/azure/mt621294.aspx). Esto se resolverá en el futuro.
- Rendimiento lento debido al bajo paralelismo, porque los planes de trabajo se ejecutan en serie en un proceso único. 
- La ejecución local no puede mostrar gráficos de trabajos en Visual Studio. Esto se tratará en el futuro. 
- No se puede crear la tabla/DB, etc. en el Explorador de servidores para la cuenta local.
- Cuando se hace referencia a una ruta de acceso relativa:

    - En la entrada del script (EXTRACT * FROM “/path/abc”): se buscará tanto en la ruta de acceso de DataRoot como en la ruta de acceso del script. 
    - En la salida del script (OUTPUT TO “path/abc”): se usará la ruta de acceso de DataRoot como la carpeta de salida. 
    - En el registro de ensamblado (CRREATE ASSEMBLY xyz FROM “/path/abc”): se buscará en la ruta de acceso del script, pero no la DataRoot. 
    - En la vista/TVF registrada u otras entidades de metadatos: se buscará en la ruta de acceso de DataRoot, pero no en la ruta de acceso del script. 
    
    Para los scripts ejecutados en el servicio de Data Lake, la cuenta de almacenamiento predeterminada se usará como carpeta raíz y se buscará en ella en consecuencia.

### Probar scripts de U-SQL localmente
Para obtener instrucciones sobre el desarrollo de scripts de U-SQL, vea [Desarrollar scripts U-SQL](#develop-and-test-u-sql-scripts). Para compilar y ejecutar scripts de U-SQL localmente, seleccione **(Local)** en la lista desplegable del clúster y luego haga clic en **Enviar**. Asegúrese de que se hace referencia a los datos correctos; haga referencia a la ruta de acceso absoluta o coloque los datos en la carpeta DataRoot.

![Enviar proyecto U-SQL de Visual Studio localmente](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

También puede hacer clic con el botón derecho en un script y luego hacer clic en **Ejecutar plan local** en el menú contextual o presionar **CTRL+F5** para desencadenar la ejecución local.

### Usar ensamblados en la ejecución local

Hay dos maneras de ejecutar los archivos de C# personalizados:

- Escriba ensamblados en el archivo de código subyacente y los ensamblados se registrarán automáticamente y se quitarán una vez finalizado el script. 
- Cree un proyecto de ensamblado de C# y registrar la dll de salida a la cuenta local a través de un script como a continuación. Tenga en cuenta que la ruta de acceso es en relación con el script en lugar de con la carpeta DataRoot.

![Usar ensamblados en ejecución local de u-sql](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)
 
### Depurar scripts y ensamblados de C# localmente

Puede depurar ensamblados de C# sin enviarlos y registrarlos en el servicio de análisis de Azure Data Lake. Puede establecer puntos de interrupción en el código subyacente del archivo y en un proyecto de C# al que se hace referencia.

**Para depurar código local en el archivo de código subyacente** 1. Establezca puntos de interrupción en el archivo de código subyacente. 2. Presione **F5** para depurar el script localmente.

El siguiente procedimiento solo funciona en Visual Studio 2015. En versiones anteriores de Visual Studio puede que necesite agregar manualmente los archivos pdb.

**Para depurar el código local en un proyecto de C# al que se hace referencia** 1. Cree un proyecto de ensamblado de C# y compílelo para generar la dll de salida. 2. Registre la dll mediante una instrucción de U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
3.	Establezca puntos de interrupción en el código de C#.
4.	Presione **F5** para depurar el script con referencia a la dll de C# localmente.  

##Consulte también

Para empezar a trabajar con Análisis de Data Lake usando diferentes herramientas, consulte:

- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a Análisis de Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introducción a Análisis de Data Lake mediante .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Para ver más temas de desarrollo:

- [Analizar weblogs mediante Análisis de Data Lake](data-lake-analytics-analyze-weblogs.md)
- [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
- [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Data Lake](data-lake-analytics-u-sql-user-defined-operators.md)

##Ejemplo de PowerShell Appx-A para preparar el tutorial

El siguiente script de PowerShell prepara una cuenta de Análisis de Azure Data Lake y los datos de origen para usted, por lo que puede ir directamente a [Desarrollar scripts U-SQL](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location 

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

<!---HONumber=AcomDC_0114_2016-->