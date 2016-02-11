<properties
    pageTitle="Orquestación de HPC y de datos mediante Lote y Factoría de datos de Azure"
    description="Describe cómo procesar grandes cantidades de datos en una canalización de Factoría de datos de Azure mediante la funcionalidad de procesamiento paralelo de Lote de Azure."
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
    ms.date="01/20/2016"
    ms.author="spelluru"/>
# Orquestación de HPC y de datos mediante Lote y Factoría de datos de Azure

Informática de alto rendimiento (HPC) ha sido el dominio de los centros de datos locales: un superequipo que trabaja en los datos, pero que está limitado por el número de máquinas físicas disponibles. El servicio Lote de Azure revoluciona este concepto, ya que proporciona HPC como servicio. Puede configurar tantas máquinas como sea necesario. Lote también controla la programación y coordinación del trabajo, lo que le permite concentrarse en los algoritmos que se van a ejecutar. Factoría de datos de Azure es un complemento perfecto para Lote, ya que simplifica la orquestación del movimiento de datos. Con Factoría de datos, puede especificar movimientos regulares de datos para ETL, procesar los datos y luego mover los resultados a un almacenamiento permanente. Por ejemplo, los datos que se recopilan de los sensores se mueven (mediante la Factoría de datos) a una ubicación temporal en la que Lote (bajo el control de la Factoría de datos) procesa los datos y genera un nuevo conjunto de resultados. Después, Factoría de datos mueve los resultados a un repositorio final. Con estos dos servicios funcionando en conjunto, puede usar HPC eficazmente para procesar grandes cantidades de datos según una programación regular.

Aquí ofrecemos un ejemplo de solución de un extremo a otro que mueve y procesa conjuntos de datos a gran escala automáticamente. La arquitectura es relevante para muchos escenarios, como el modelado de riesgo por parte de servicios financieros, el procesamiento y representación de imágenes, y el análisis genómico. Los arquitectos y los responsables de la toma de decisiones sobre TI obtendrán una visión general con el diagrama y los pasos básicos. Los desarrolladores pueden usar el código como punto de partida para su propia implementación. Este artículo contiene toda la solución.

Consulte la documentación de [Lote de Azure](../batch/batch-api-basics.md) y [Factoría de datos](data-factory-introduction.md) si no está familiarizado con dichos servicios antes de seguir con la solución de ejemplo.

## Diagrama de la arquitectura

En el diagrama, se muestra 1) cómo Factoría de datos organiza el movimiento y el procesamiento de datos y 2) cómo Lote de Azure procesa los datos en paralelo. Descargue e imprima el diagrama para facilitar su consulta (11 x 17 pulgadas o tamaño A3): [Orquestación de HPC y de datos mediante Lote y Factoría de datos de Azure](http://go.microsoft.com/fwlink/?LinkId=717686).

![HPC como diagrama de servicio](./media/data-factory-data-processing-using-batch/image1.png)

Estos son los pasos básicos del proceso. La solución incluye código y explicaciones para compilar la solución completa.

1.  Configure Lote de Azure con un grupo de nodos de proceso (máquinas virtuales). Puede especificar el número de nodos y el tamaño de cada nodo.

2.  Cree una instancia de Factoría de datos de Azure que esté configurada con entidades que representen Almacenamiento de blobs de Azure, el servicio de proceso de Lote de Azure, los datos de entrada y salida y un flujo de trabajo o una canalización con las actividades que mueven y transforman datos.

3.  La canalización de Factoría de datos tiene una actividad .NET personalizada, que está configurada para ejecutarse en el grupo de nodos de Lote de Azure.

4.  Almacene grandes cantidades de datos de entrada como blobs en Almacenamiento de Azure. Los datos se dividen en segmentos lógicos (normalmente, en función de la fecha y la hora).

5.  Factoría de datos copia los datos que se procesarán en paralelo a la ubicación secundaria.

6.  Factoría de datos ejecuta la actividad personalizada con el grupo asignado por Lote. Además, puede ejecutar actividades al mismo tiempo. Cada actividad procesa un segmento de datos. Los resultados se almacenan en Almacenamiento de Azure.

7.  Una vez obtenidos todos los resultados, Factoría de datos los mueve a una tercera ubicación para que se distribuyan a través de una aplicación o para que se sigan procesando con otras herramientas.

## Solución de arquitectura

La solución cuenta el número de apariciones de un término de búsqueda ("Microsoft") en los archivos de entrada organizados en una serie temporal y genera el recuento en archivos de salida.

**Tiempo**: si está familiarizado con Azure, Factoría de datos y Lote, y satisface los requisitos previos, se calcula que esta solución tardará entre 1 y 2 horas en completarse.

### Requisitos previos

1.  **Suscripción de Azure**. Si carece de suscripción de Azure, puede crear una cuenta de prueba gratuita en tan solo unos minutos. Consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

2.  **Cuenta de almacenamiento de Azure**. En este tutorial, usará una cuenta de almacenamiento de Azure para almacenar los datos. Si no dispone de una, consulte [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account). En la solución de ejemplo, se usa el almacenamiento de blobs.

3.  Cree una **cuenta de Lote de Azure** en el [Portal de Azure](http://manage.windowsazure.com/). Consulte [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure](../batch/batch-account-create-portal.md). Anote el nombre y la clave de la cuenta de Lote de Azure. También puede usar el cmdlet [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) para crear una cuenta de Lote de Azure. Consulte [Introducción a los cmdlets de PowerShell de Lote de Azure](../batch/batch-powershell-cmdlets-get-started.md) para obtener instrucciones detalladas para usar este cmdlet.

    La solución de ejemplo usa Lote de Azure (de forma indirecta mediante una canalización de Factoría de datos de Azure) para procesar datos en paralelo en un grupo de nodos de proceso, que es una colección administrada de máquinas virtuales.

4.  Cree un **grupo de Lote de Azure** con al menos 2 nodos de proceso.

	 Puede descargar el código fuente para la [herramienta Explorador de Lote de Azure](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer), compilarla y usarla para crear el grupo (**muy recomendado para esta solución de ejemplo**) o puede usar [biblioteca de Lote de Azure para .NET](../batch/batch-dotnet-get-started.md) para crear el grupo. Consulte el [tutorial de ejemplo del Explorador de Lote de Azure](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx) para obtener instrucciones paso a paso para usar el Explorador de Lote de Azure. También puede usar el cmdlet [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) para crear un grupo de Lote de Azure.

	 Use el Explorador de Lote para crear el grupo con la siguiente configuración:

	-   Especifique un identificador para el grupo (**Identificador del grupo**). Anote el **identificador del grupo**; lo necesitará al crear la solución de Factoría de datos.

	-   Especifique **Windows Server 2012 R2** en la configuración **Familia del sistema operativo**.

	-   Especifique **2** como valor en la configuración **Máximo de tareas por nodo de proceso**.

	-   Especifique **2** como valor en la configuración **Número de destino dedicado**.

	 ![](./media/data-factory-data-processing-using-batch/image2.png)

5.  [Explorador de almacenamiento de Azure 6 (herramienta)](https://azurestorageexplorer.codeplex.com/) o [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (de ClumsyLeaf Software). Estas herramientas de GUI sirven para inspeccionar y modificar los datos en sus proyectos de Almacenamiento de Azure, incluidos los registros de las aplicaciones hospedadas en la nube.

    1.  Cree un contenedor denominado **mycontainer** con acceso privado (sin acceso anónimo).

    2.  Si usa **CloudXplorer**, cree carpetas y subcarpetas con la estructura siguiente:

 		![](./media/data-factory-data-processing-using-batch/image3.png)

		 **Inputfolder** y **outputfolder** son carpetas de nivel superior en **mycontainer**, e **inputfolder** tiene subcarpetas con marcas de fecha y hora (AAAA-MM-DD-HH).

		 Si usa el **Explorador de almacenamiento de Azure**, en el paso siguiente, tendrá que cargar archivos con los nombres: inputfolder/2015-11-16-00/file.txt, inputfolder/2015-11-16-01/file.txt y así sucesivamente. Se crearán automáticamente las carpetas.

	3.  Cree un archivo de texto **file.txt** en el equipo con contenido que incluya la palabra clave **Microsoft**. Por ejemplo: "test custom activity Microsoft test custom activity Microsoft".

	4.  Cargue el archivo a las siguientes carpetas de entrada en Almacenamiento de blobs de Azure.

		![](./media/data-factory-data-processing-using-batch/image4.png)

	 	Si usa el **Explorador de almacenamiento de Azure**, cargue el archivo **file.txt** a **mycontainer**. Haga clic en **Copiar** en la barra de herramientas para crear una copia del blob. En el cuadro de diálogo **Copiar blob**, cambie el **nombre de blob de destino** a **inputfolder/2015-11-16-00/file.txt.** Repita este paso para crear inputfolder/2015-11-16-01/file.txt, inputfolder/2015-11-16-02/file.txt, inputfolder/2015-11-16-03/file.txt, inputfolder/2015-11-16-04/file.txt y así sucesivamente. Se crearán automáticamente las carpetas.

	3.  Cree otro contenedor denominado **customactivitycontainer**. Va a cargar el archivo ZIP de actividad personalizada a este contenedor.

6.  **Microsoft Visual Studio 2012 o posterior** (para crear la actividad personalizada de Lote que se usará en la solución de Factoría de datos).

### Pasos de alto nivel para crear la solución

1.  Cree una actividad personalizada para usarla en la solución de Factoría de datos. La actividad personalizada contiene la lógica de procesamiento de datos.

    1.  En Visual Studio (o en el editor de código preferido), cree un proyecto de la biblioteca de clases .NET, agregue el código para procesar los datos de entrada y compile el proyecto.

    2.  Comprima todos los archivos binarios y el archivo PDB (opcional) en la carpeta de salida.

    3.  Cargue el archivo ZIP en Almacenamiento de blobs de Azure.

	Encontrará pasos detallados en la sección [Creación de la actividad personalizada](#_Coding_the_custom).

2.  Cree una factoría de datos de Azure que use la actividad personalizada:

    1.  Crear una factoría de datos de Azure.

    2.  Cree servicios vinculados.

        1.  StorageLinkedService: proporciona credenciales de almacenamiento para tener acceso a los blobs.

        2.  AzureBatchLinkedService: especifica Lote de Azure como proceso.

    3.  Crear conjuntos de datos.

        1.  InputDataset: especifica el contenedor de almacenamiento y la carpeta para los blobs de entrada.

        2.  OuputDataset: especifica el contenedor de almacenamiento y la carpeta para los blobs de salida.

    4.  Crea una canalización que usa la actividad personalizada.

    5.  Ejecutar y probar la canalización.

    6.  Depurar la canalización.

 	Encontrará pasos detallados en la sección [Creación de la factoría de datos](#create-the-data-factory).

## Creación de la actividad personalizada

La actividad personalizada de Factoría de datos es el núcleo de esta solución de ejemplo. La solución de ejemplo usa Lote de Azure para ejecutar la actividad personalizada. Consulte [Uso de actividades personalizadas en una canalización de Factoría de datos de Azure](data-factory-use-custom-activities.md) para ver información básica para desarrollar actividades personalizadas y usarlas en las canalizaciones de Factoría de datos de Azure.

Para crear una actividad personalizada de .NET que se pueda usar en una canalización de Factoría de datos de Azure, deberá crear un proyecto de **biblioteca de clases .NET** con una clase que implemente la interfaz **IDotNetActivity**. Esta interfaz tiene un solo método, **Execute**. Esta es la firma de dicho método:

	public IDictionary<string, string> Execute(
	            IEnumerable<LinkedService> linkedServices,
	            IEnumerable<Dataset> datasets,
	            Activity activity,
	            IActivityLogger logger)

El método tiene algunos componentes clave que debe conocer.

-   El método toma cuatro parámetros:

    1.  **linkedServices**. Se trata de una lista enumerable de servicios vinculados que vincula los orígenes de datos de entrada y salida (por ejemplo: Almacenamiento de blobs de Azure) a la factoría de datos. En este ejemplo, hay solo un servicio vinculado del tipo Almacenamiento de Azure que se utilice como entrada y salida.

    2.  **datasets**. Se trata de una lista enumerable de conjuntos de datos. Este parámetro se puede usar para obtener las ubicaciones y esquemas que definen los conjuntos de datos de entrada y salida.

    3.  **activity**. Este parámetro representa la entidad de proceso actual, en este caso, un servicio Lote de Azure.

    4.  **logger**. El parámetro logger le permite escribir comentarios de depuración que se mostrarán como el registro de "User" en la canalización.

-   El método devuelve un diccionario que se puede usar para encadenar actividades personalizadas. Esta característica no se usará en esta solución de ejemplo.

### Procedimiento: Creación de la actividad personalizada

1.  Cree un proyecto de biblioteca de clases .NET en Visual Studio.

    1.  Inicie **Visual Studio 2012**/**2013/2015**.

    2.  Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.

    3.  Expanda **Plantillas** y seleccione **Visual C#**. En este tutorial se usa C#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.

    4.  Seleccione **Biblioteca de clases** en la lista de tipos de proyecto de la derecha.

    5.  Escriba **MyDotNetActivity** para **Nombre**.

    6.  Seleccione **C:\\ADF** para **Ubicación**. Cree la carpeta **ADF** si no existe.

    7.  Haga clic en **Aceptar** para crear el proyecto.

2.  Haga clic en **Herramientas**, seleccione **Administrador de paquetes NuGet** y haga clic en **Consola del Administrador de paquetes**.

3.  En la **Consola del Administrador de paquetes**, ejecute el siguiente comando para importar **Microsoft.Azure.Management.DataFactories**.

			Install-Package Microsoft.Azure.Management.DataFactories

4.  Importe el paquete NuGet de **Almacenamiento de Azure** en el proyecto. Lo necesitará porque va a usar la API de Almacenamiento de blobs.

		Install-Package Azure.Storage

5.  Agregue las siguientes directivas **using** al archivo de origen en el proyecto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6.  Cambie el nombre del **espacio de nombres** a **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7.  Cambie el nombre de la clase a **MyDotNetActivity** y derívela desde la interfaz **IDotNetActivity** tal como se muestra a continuación.

		public class MyDotNetActivity : IDotNetActivity

8.  Implemente (agregue) el método **Execute** de la interfaz **IDotNetActivity** en la clase **MyDotNetActivity** y copie el siguiente código de ejemplo en el método. Consulte la sección [Método Execute](#execute-method) para obtener una explicación de la lógica usada en este método.

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement.
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>
        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);

                // Calculate method returns the number of occurrences of
                // the search term (“Microsoft”) in each blob associated
        		// with the data slice.
        		//
        	    // definition of the method is shown in the next step.
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file.
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }


9.  Agregue los siguientes métodos auxiliares a la clase. Estos métodos se invocan desde el método **Execute**. Y lo que es más importante, el método **Calculate** aísla el código que procesa una iteración de cada blob.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
		/// </summary>
		private static string GetFolderPath(Dataset dataArtifact)
		{
		    if (dataArtifact == null || dataArtifact.Properties == null)
		    {
		        return null;
		    }

		    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
		    if (blobDataset == null)
		    {
		        return null;
		    }

		    return blobDataset.FolderPath;
		}

		/// <summary>
		/// Gets the fileName value from the input/output dataset.
		/// </summary>

		private static string GetFileName(Dataset dataArtifact)
		{
		    if (dataArtifact == null || dataArtifact.Properties == null)
		    {
		        return null;
		    }

		    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
		    if (blobDataset == null)
		    {
		        return null;
		    }

		    return blobDataset.FileName;
		}

		/// <summary>
		/// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
		/// and prepares the output text that will be written to the output blob.
		/// </summary>

		public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
		{
		    string output = string.Empty;
		    logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
		    foreach (IListBlobItem listBlobItem in Bresult.Results)
		    {
		        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
		        if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
		        {
		            string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
		            logger.Write("input blob text: {0}", blobText);
		            string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
		            var matchQuery = from word in source
		                             where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
		                             select word;
		            int wordCount = matchQuery.Count();
		            output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
		        }
		    }
		    return output;
		}


	El método **GetFolderPath** devuelve la ruta de acceso a la carpeta a la que apunta el conjunto de datos y el método **GetFileName** devuelve el nombre del blob o archivo al que apunta el conjunto de datos.

	    "name": "InputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "file.txt",
	            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",


	El método **Calculate** calcula el número de instancias de la palabra clave **Microsoft** en los archivos de entrada (los blobs de la carpeta). El término de búsqueda ("Microsoft") está codificado de forma rígida en el código.

10.  Compile el proyecto. Haga clic en **Compilar** en el menú y haga clic en **Compilar solución**.

11.  Inicie el **Explorador de Windows** y vaya a la carpeta **bin\\debug** o **bin\\release**, según el tipo de compilación.

12.  Cree un archivo ZIP **MyDotNetActivity.zip** que contenga todos los archivos binarios en la carpeta **\\bin\\Debug**. Podría incluir el archivo MyDotNetActivity.**pdb** para obtener detalles adicionales, como el número de línea en el código fuente que causó el problema, en caso de error.

	![](./media/data-factory-data-processing-using-batch/image5.png)

13.  Cargue **MyDotNetActivity.zip** como un blob en el contenedor de blobs: **customactvitycontainer** en el Almacenamiento de blobs de Azure que usa el servicio vinculado **StorageLinkedService** en **ADFTutorialDataFactory**. Cree el contenedor de blobs **customactivitycontainer** si no existe.

### Método Execute

En esta sección se proporcionan más detalles y notas sobre el código del método Execute.

1.	Los miembros para procesar una iteración en la colección de entrada se encuentran en el espacio de nombres [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). El procesamiento de una iteración en la colección de blobs requiere el uso de la clase **BlobContinuationToken**. En esencia, es preciso usar un bucle do-while con el token como mecanismo para salir del bucle. Para más información, consulte [Uso del almacenamiento de blobs de .NET](../storage/storage-dotnet-how-to-use-blobs.md). Aquí se muestra un bucle básico:

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{
		// Get the list of input blobs from the input storage client object.
		BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
		    					true,
		                                  BlobListingDetails.Metadata,
		                                  null,
		                                  continuationToken,
		                                  null,
		                                  null);
		// Return a string derived from parsing each blob.
		    output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

		} while (continuationToken != null);

	Para más información, consulte la documentación del método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).

2.	Lógicamente, el código para trabajar en el conjunto de blobs lógicamente está dentro del bucle do-while. En el método **Execute**, el bucle do-while pasa la lista de blobs a un método denominado **Calculate**. El método devuelve una variable de cadena denominada **output** que es el resultado de haber procesado una iteración en todos los blobs del segmento.

	Devuelve el número de apariciones del término de búsqueda (**Microsoft**) en el blob que se pasa al método **Calculate**.

		output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	Una vez que el método **Calculate** ha terminado el trabajo, debe escribirse en un blob nuevo. Por consiguiente, por cada conjunto de blobs que se procesa, se puede escribir un blob nuevo con los resultados. Para escribir en un blob nuevo, primero es preciso buscar el conjunto de datos de salida.

		// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
		Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

		// Convert to blob location object.
		outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	El código también llama a un método auxiliar, **GetFolderPath**, para recuperar la ruta de acceso de la carpeta (el nombre del contenedor de almacenamiento).

		folderPath = GetFolderPath(outputDataset);

	El método **GetFolderPath** convierte el objeto DataSet en una clase AzureBlobDataSet, que tiene una propiedad denominada FolderPath.

		AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

		return blobDataset.FolderPath;

5.	El código llama al método **GetFileName** para recuperar el nombre de archivo (nombre del blob). El código es similar al código anterior para obtener la ruta de acceso de la carpeta.

		AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

		return blobDataset.FileName;

6.	El nombre del archivo se escribe mediante la creación de un nuevo objeto URI. El constructor URI usa la propiedad **BlobEndpoint** para devolver el nombre del contenedor. Tanto la ruta de la carpeta como el nombre de archivo se agregan al construir el identificador URI del blob de salida.

		// Write the name of the file.
		Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	Se ha escrito el nombre del archivo y ahora puede escribir la cadena de salida desde el método **Calculate** en un blob nuevo:

		// Create a new blob and upload the output text.
		CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
		logger.Write("Writing {0} to the output blob", output);
		outputBlob.UploadText(output);


## Creación de la factoría de datos

En la sección [Creación de la actividad personalizada](#create-the-custom-activity), se creó una actividad personalizada y se cargó el archivo ZIP con archivos binarios y el archivo PDB en un contenedor de blobs de Azure. En esta sección, creará una **factoría de datos** de Azure con una **canalización** que usa la **actividad personalizada**.

El conjunto de datos de entrada de la actividad personalizada representa los blobs (archivos) de la carpeta de entrada (mycontainer\\inputfolder) del almacenamiento de blobs. El conjunto de datos de salida de la actividad representa los blobs de salida de la carpeta de salida (mycontainer\\outputfolder) del almacenamiento de blobs.

Colocará uno o varios archivos en las carpetas de entrada:

	mycontainer -> inputfolder
		2015-11-16-00
		2015-11-16-01
		2015-11-16-02
		2015-11-16-03
		2015-11-16-04

Por ejemplo, coloque un archivo (file.txt) con el siguiente contenido en cada una de las carpetas.

	test custom activity Microsoft test custom activity Microsoft

Cada carpeta de entrada corresponde a un segmento de Factoría de datos de Azure, incluso si la carpeta contiene 2 o más archivos. Cuando la canalización procesa cada segmento, la actividad personalizada procesa una iteración en todos los blobs de la carpeta de entrada de dicho segmento.

Verá cinco archivos de salida con el mismo contenido. Por ejemplo, el archivo de salida resultante de procesar el archivo en la carpeta 2015-11-16-00 tendrá el siguiente contenido:

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

Si coloca varios archivos (file.txt, file2.txt, file3.txt) con el mismo contenido en la carpeta de entrada, verá el siguiente contenido en el archivo de salida. Cada carpeta (2015-11-16-00, etc.) corresponde a un segmento en este ejemplo, incluso si la carpeta tiene varios archivos de entrada.

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.


Observe que el archivo de salida tiene ahora tres líneas, una por cada archivo de entrada (blob) en la carpeta asociada con el segmento (2015-11-16-00).

Se crea una tarea para cada ejecución de actividad. En este ejemplo, solamente hay una actividad en la canalización. Cuando se procesa un segmento en la canalización, la actividad personalizada se ejecuta en Lote de Azure para procesar el segmento. Puesto que hay 5 segmentos (cada segmento puede tener varios blobs o archivos), se crearán 5 tareas en Lote de Azure. Cuando se ejecuta una tarea en Lote, lo que realmente se está ejecutando es la actividad personalizada.

En el siguiente tutorial, se proporcionan más detalles.

### Paso 1: Creación de la factoría de datos

1.  Tras iniciar sesión en el [Portal de Azure](https://portal.azure.com/), siga estos pasos:

    1.  Haga clic en **NUEVO** en el menú de la izquierda.

    2.  Haga clic en **Datos y análisis** en la hoja **Nuevo**.

    3.  Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

2.  En la hoja **Nueva factoría de datos**, escriba **CustomActivityFactory** en el campo Nombre. El nombre del generador de datos de Azure debe ser único global. Si recibe el error **El nombre "CustomActivityFactory" de factoría de datos no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, **suNombreCustomActivityFactory**) e intente crearla de nuevo.

3.  Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione un grupo de recursos existente o cree uno nuevo.

4.  Compruebe que usa la suscripción y la región correctas en las que desea que se cree la factoría de datos.

5.  Haga clic en **Crear** en la hoja **Nueva factoría de datos**.

6.  Verá que la factoría de datos se crea en el **Panel** del Portal de Azure.

7.  Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma.

 ![](./media/data-factory-data-processing-using-batch/image6.png)

### Paso 2: Creación de servicios vinculados

Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. En este paso, vinculará su cuenta de **Almacenamiento de Azure** y la cuenta de **Lote de Azure** con su factoría de datos.

#### Creación de un servicio vinculado de Almacenamiento de Azure

1.  Haga clic en el icono **Crear e implementar** de la hoja **FACTORÍA DE DATOS** de **CustomActivityFactory**. Esto inicia el Editor de la Factoría de datos.

2.  Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **Almacenamiento de Azure**. Debería ver el script JSON para crear un servicio vinculado de Almacenamiento de Azure en el editor.

    ![](./media/data-factory-data-processing-using-batch/image7.png)

3.  Reemplace **account name** por el nombre de la cuenta de Almacenamiento de Azure y **account key** por la clave de acceso de la cuenta de Almacenamiento de Azure. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

4.  Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

    ![](./media/data-factory-data-processing-using-batch/image8.png)

#### Creación del servicio vinculado de Lote de Azure

En este paso, creará un servicio vinculado para su cuenta de **Lote de Azure** que se usará para ejecutar la actividad personalizada de Factoría de datos.

1.  Haga clic en **Nuevo proceso** en la barra de comandos y elija **Lote de Azure**. Debería ver el script JSON para crear un servicio vinculado de Lote de Azure en el editor.

2.  En el script JSON:

    1.  Reemplace **account name** por el nombre de la cuenta de Lote de Azure.

    2.  Reemplace **access key** por la clave de acceso de la cuenta de Lote de Azure.

    3.  Escriba el identificador del grupo para la propiedad **poolName****. ** Para esta propiedad, puede especificar el nombre o el identificador de grupo.

    4.  Escriba el identificador URI de lote para la propiedad **batchUri** de JSON. La **dirección URL** de la **hoja de la cuenta de Lote de Azure** tiene el formato siguiente: <nombreDeCuenta>.<región>.batch.azure.com. Para la propiedad **batchUri** en el script JSON, necesitará **quitar "nombreDeCuenta."** de la dirección URL. Por ejemplo: "batchUri": "https://eastus.batch.azure.com".

        ![](./media/data-factory-data-processing-using-batch/image9.png)

    5.  Especifique **StorageLinkedService** para la propiedad **linkedServiceName**. Ha creado este servicio vinculado en el paso anterior. Este almacenamiento se usa como área de almacenamiento provisional para archivos y registros.

3.  Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

### Paso 3: Creación de conjuntos de datos

En este paso, creará conjuntos de datos que representen los datos de entrada y salida.

#### Creación de un conjunto de datos de entrada

1.  En el **Editor** de Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Almacenamiento de blobs de Azure** en el menú desplegable.

2.  Reemplace el script JSON del panel derecho por el siguiente fragmento de código JSON:

		{
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
		            "format": {
		                "type": "TextFormat"
		            },
		            "partitionedBy": [
		                {
		                    "name": "Year",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy"
		                    }
		                },
		                {
		                    "name": "Month",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "MM"
		                    }
		                },
		                {
		                    "name": "Day",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "dd"
		                    }
		                },
		                {
		                    "name": "Hour",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		}


	 En este tutorial, creará posteriormente una canalización cuya hora de inicio es: 2015-11-hora 16T00:00:00Z y cuya hora de finalización es: 2015-11-16T05:00:00Z. Está programada para generar datos **cada hora**, por lo que habrá 5 segmentos de entrada y salida (entre **00**:00:00 -> **05**:00:00).

	 Los valores de **frequency** e **interval** del conjunto de datos de entrada se establecen en **Hour** y **1** respectivamente, lo que significa que el segmento de entrada está disponible cada hora.

	 Estas son las horas de inicio de cada segmento, que se representa mediante la variable de sistema **SliceStart** en el fragmento de código JSON anterior.

	| **Segmento** | **Hora de inicio** |
	|-----------|-------------------------|
	| 1 | 2015-11-16T**00**:00:00 |
	| 2 | 2015-11-16T**01**:00:00 |
	| 3 | 2015-11-16T**02**:00:00 |
	| 4 | 2015-11-16T**03**:00:00 |
	| 5 | 2015-11-16T**04**:00:00 |

	 La propiedad **folderPath** se calcula usando la parte de año, mes, día y hora de la hora de inicio del segmento (**SliceStart**). Por lo tanto, aquí se muestra cómo se asigna una carpeta de entrada a un segmento.

	| **Segmento** | **Hora de inicio** | **Carpeta de entrada** |
	|-----------|-------------------------|-------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3.  Haga clic en **Implementar** en la barra de herramientas para crear e implementar la tabla **InputDataset**. Confirme que aparece el mensaje **TABLA CREADA CORRECTAMENTE** en la barra de título del Editor.

#### Creación del conjunto de datos de salida

En este paso, creará otro conjunto de datos de tipo AzureBlob para representar los datos de salida.

1.  En el **Editor** de Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Almacenamiento de blobs de Azure** en el menú desplegable.

2.  Reemplace el script JSON del panel derecho por el siguiente fragmento de código JSON:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "mycontainer/outputfolder",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        }
		    }
		}


 	Se genera un blob o archivo de salida para cada segmento de entrada. Así es cómo se asigna el nombre al archivo de salida de cada segmento. Todos los archivos de salida se generan en una carpeta de salida, **mycontainer\\outputfolder**.


	| **Segmento** | **Hora de inicio** | **Archivo de salida** |
	|-----------|-------------------------|-----------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

	 Recuerde que todos los archivos de una carpeta de entrada (por ejemplo, 2015-11-16-00) forman parte de un segmento con la hora de inicio 2015-11-16-00. Cuando se procesa este segmento, la actividad personalizada examinan todos los archivos y produce una línea en el archivo de salida con el número de apariciones del término de búsqueda ("Microsoft"). Si hay tres archivos en la carpeta 2015-11-16-00, habrá tres líneas en el archivo de salida 2015-11-16-00.txt.

3.  Haga clic en **Implementar** en la barra de herramientas para crear e implementar **OutputDataset**.

### Paso 4: Creación y ejecución de la canalización con una actividad personalizada

En este paso, creará una canalización con la actividad personalizada que creó antes.

> [AZURE.IMPORTANT] Si no ha cargado **file.txt** a las carpetas de entrada en el contenedor de blobs, hágalo antes de crear la canalización. La propiedad **isPaused** está establecida en false en el código JSON de la canalización, por lo que esta se ejecutará de inmediato, ya que la fecha de inicio (**start**) ya ha pasado.

1.  En el Editor de Factoría de datos, haga clic en **Nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (puntos suspensivos)** para verlo.

2.  Reemplace el script JSON del panel derecho con el siguiente script JSON.

		{
			"name": "PipelineCustom",
			"properties": {
				"description": "Use custom activity",
				"activities": [
					{
						"type": "DotNetActivity",
						"typeProperties": {
							"assemblyName": "MyDotNetActivity.dll",
							"entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
							"packageLinkedService": "StorageLinkedService",
							"packageFile": "customactivitycontainer/MyDotNetActivity.zip"
						},
						"inputs": [
							{
								"name": "InputDataset"
							}
						],
						"outputs": [
							{
								"name": "OutputDataset"
							}
						],
						"policy": {
							"timeout": "00:30:00",
							"concurrency": 5,
							"retry": 3
						},
						"scheduler": {
							"frequency": "Hour",
							"interval": 1
						},
						"name": "MyDotNetActivity",
						"linkedServiceName": "AzureBatchLinkedService"
					}
				],
				"start": "2015-11-16T00:00:00Z",
				"end": "2015-11-16T05:00:00Z",
				"isPaused": false
		   }
		}

	Tenga en cuenta lo siguiente:

	-   Solo hay una actividad en la canalización y es del tipo **DotNetActivity**.

	-   **AssemblyName** se establece en el nombre del archivo DLL, **MyDotNetActivity.dll**.

	-   **EntryPoint** se establece en establecido en **MyDotNetActivityNS.MyDotNetActivity**. Es básicamente <espacioDeNombres>.<nombreDeClase> en el código.

	-   **PackageLinkedService** está establecido en **StorageLinkedService**, que apunta al almacenamiento de blobs que contiene el archivo ZIP de la actividad personalizada. Si usa diferentes cuentas de Almacenamiento de Azure para los archivos de entrada y salida y el archivo ZIP de actividad personalizada, tendrá que crear otro servicio vinculado de Almacenamiento de Azure. En este artículo, se da por supuesto que usa la misma cuenta de Almacenamiento de Azure.

	-   **PackageFile** se establece en **customactivitycontainer/MyDotNetActivity.zip**. Está en el formato <contenedorDelZIP>/<nombreDelZIP.zip>.

	-   La actividad personalizada toma **InputDataset** como entrada y **OutputDataset** como salida.

	-   La propiedad **linkedServiceName** de la actividad personalizada apunta a **AzureBatchLinkedService**, que indica a Factoría de datos de Azure que la actividad personalizada debe ejecutarse en Lote de Azure.

	-   La configuración **concurrency** es importante. Si usa el valor predeterminado, que es 1, aunque tenga 2 o más nodos de proceso en el grupo de Lote de Azure, los segmentos se procesarán uno tras otro. Por lo tanto, no se aprovecha la ventaja de la funcionalidad de procesamiento paralelo de Lote de Azure. Si establece **concurrency** en un valor mayor, digamos 2, significa que se pueden procesar 2 segmentos (que se corresponden con 2 tareas en Lote de Azure) al mismo tiempo, en cuyo caso,se usan ambas máquinas virtuales en el grupo de Lote de Azure. Por tanto, establezca concurrency correctamente.

	-   De manera predeterminada, solo se ejecuta una tarea (segmento) en una máquina virtual en un momento dado. Esto es porque, de forma predeterminada, **Máximo de tareas por máquina virtual** se establece en 1 para un grupo de Lote de Azure. Como parte de los requisitos previos, ha creado un grupo con esta propiedad establecida en 2, por lo que los dos segmentos de Factoría de datos se pueden ejecutar en una máquina virtual al mismo tiempo.


	-   La propiedad **isPaused** se establece en false de forma predeterminada. La canalización se ejecuta inmediatamente en este ejemplo, ya que los segmentos se inician en el pasado. Esta propiedad se puede establecer en true para pausar la canalización y se puede volver a establecer en false para reiniciarla.

	-   Hay una diferencia de 5 horas entre la hora de inicio (**start**) y las horas de finalización (**end**) y los segmentos se producen cada hora, por lo que la canalización produce 5 segmentos.

3.  Haga clic en **Implementar** en la barra de comandos para implementar la canalización.

### Paso 5: Prueba de la canalización

En este paso, probará la canalización colocando archivos en las carpetas de entrada. Comencemos a probar la canalización con un archivo por carpeta de entrada.

1.  En la hoja Factoría de datos del Portal de Azure, haga clic en **Diagrama**.

    ![](./media/data-factory-data-processing-using-batch/image10.png)

2.  En la vista de diagrama, haga doble clic en el conjunto de datos de entrada, **InputDataset**.

    ![](./media/data-factory-data-processing-using-batch/image11.png)

3.  Debería ver la hoja **InputDataset** con los 5 segmentos listos. Observe los valores de **HORA DE INICIO DE SEGMENTO** y **HORA DE FINALIZACIÓN DE SEGMENTO** para cada segmento.

    ![](./media/data-factory-data-processing-using-batch/image12.png)

4.  En la **vista de diagrama**, haga clic ahora en **OutputDataset**.

5.  Si ya se han producido, debería ver que los cinco segmentos están en estado Listo.

    ![](./media/data-factory-data-processing-using-batch/image13.png)

6.  Use el [Explorador de Lote de Azure](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx) para ver las **tareas** asociadas con los **segmentos** y en qué máquina virtual se ejecutó cada segmento. Verá que se crea un trabajo con el nombre **adf-<nombreDeGrupo>**. Este trabajo tendrá una tarea para cada segmento. En este ejemplo, habrá 5 segmentos y, por tanto, 5 tareas en Lote de Azure. Con el valor **concurrency** establecido en **5** en el código JSON de la canalización en Factoría de datos de Azure y **Máximo de tareas por máquina virtual** establecido en **2** en el grupo de Lote de Azure con **2** máquinas virtuales, las tareas se ejecutaban muy rápido (consulte la información en **Creada**).

    ![](./media/data-factory-data-processing-using-batch/image14.png)

7.  Debería ver los archivos de salida en la carpeta **outputfolder** de **mycontainer** en su Almacenamiento de blobs de Azure.

    ![](./media/data-factory-data-processing-using-batch/image15.png)

    Debería ver cinco archivos de salida, uno para cada segmento de entrada. Cada archivo de salida debe tener contenido similar al siguiente:

    	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

    En el siguiente diagrama, se ilustra cómo los segmentos de Factoría de datos se asignan a tareas de Lote de Azure. En este ejemplo, un segmento tiene solo una ejecución.

    ![](./media/data-factory-data-processing-using-batch/image16.png)

8.  Ahora, probemos con varios archivos en una carpeta. Cree los archivos **file2.txt**, **file3.txt**, **file4.txt** y **file5.txt** con el mismo contenido que file.txt en la carpeta **2015-11-06-01**.

9.  En la carpeta de salida, **elimine** el archivo de salida **2015-11-16-01.txt**.

10. Ahora, en la hoja **OutputDataset**, haga clic con el botón derecho en el segmento con el valor de **HORA DE INICIO DE SEGMENTO** establecido en **11/16/2015 01:00:00 a.m.** y haga clic en **Ejecutar** para volver a ejecutar o procesar el segmento. Ahora, el segmento tiene 5 archivos en lugar de uno.

    ![](./media/data-factory-data-processing-using-batch/image17.png)

11. Una vez que se ejecuta el segmento y su estado es **Listo**, compruebe el contenido del archivo de salida para este segmento (**2015-11-16-01.txt**) en la carpeta **outputfolder** de **mycontainer** en el almacenamiento de blobs. Debería aparecer una línea para cada archivo del segmento.

		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.


    **Nota:** Si no ha eliminado el archivo de salida 2015-11-16-01.txt antes de probar con 5 archivos de entrada, verá una línea de la anterior ejecución de segmento y cinco líneas para la actual. De forma predeterminada, se anexa el contenido al archivo de salida si ya existe.

### Depuración de la canalización

La depuración se compone de varias técnicas básicas:

1.  Si el segmento de entrada no está establecido en **Listo**, confirme que la estructura de la carpeta de entrada es correcta y que file.txt se encuentra en las carpetas de entrada.

    ![](./media/data-factory-data-processing-using-batch/image3.png)

2.  En el método **Execute** de la actividad personalizada, use el objeto **IActivityLogger** para registrar información que lo ayudará a solucionar problemas. Los mensajes registrados se mostrarán en el archivo user\_0.log.

    En la hoja **OutputDataset**, haga clic en el segmento para ver la hoja **SEGMENTO DE DATOS** de dicho segmento. Verá **ejecuciones de actividad** para ese segmento. Debería ver una ejecución de actividad del segmento. Si hace clic en **Ejecutar** en la barra de comandos, podrá iniciar otra ejecución de actividad para el mismo segmento.

    Cuando haga clic en la ejecución de actividad, verá la hoja **DETALLES DE LA EJECUCIÓN DE ACTIVIDAD** con una lista de archivos de registro. Verá mensajes registrados en el archivo **user\_0.log**. Si se produce un error, verá tres ejecuciones de actividad, ya que el número de reintentos está establecido en 3 en la canalización o actividad JSON. Al hacer clic en la ejecución de actividad, verá los archivos de registro que puede revisar para solucionar el error.

    ![](./media/data-factory-data-processing-using-batch/image18.png)

    En la lista de archivos de registro, haga clic en **user-0.log**. En el panel derecho, se encuentran los resultados del uso del método **IActivityLogger.Write**.

    ![](./media/data-factory-data-processing-using-batch/image19.png)

    También debería ver en system-0.log si aparecen excepciones o mensajes de error del sistema.

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

		Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully

3.  Incluya el archivo **PDB** en el archivo ZIP para que se ofrezca información como la **pila de llamadas** cuando se proporcionen detalles sobre un error que se haya producido.

4.  Todos los archivos incluidos en el archivo ZIP de la actividad personalizada deben estar en el **nivel superior**; no debe haber subcarpetas.

    ![](./media/data-factory-data-processing-using-batch/image20.png)

5.  Asegúrese de que **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) y **packageLinkedService** (debe apuntar al Almacenamiento de blobs de Azure que contiene el archivo ZIP) estén establecidos en los valores correctos.

6.  Si corrigió algún error y desea volver a procesar el segmento, haga clic con el botón derecho en el segmento, en la hoja **OutputDataset**, y haga clic en **Ejecutar**.

    ![](./media/data-factory-data-processing-using-batch/image21.png)

    **Nota:** Verá un **contenedor** en el Almacenamiento de blobs de Azure denominado **adfjobs**. Este contenedor no se elimina automáticamente, pero puede eliminarlo de forma segura cuando termine de probar la solución. Del mismo modo, la solución de Factoría de datos crea un **trabajo** de Lote de Azure denominado **adf-<identificadorONombreDeGrupo>:job-0000000001**. Puede eliminarlo después de probar la solución, si lo desea.

### Extensión del ejemplo

Puede extender este ejemplo para obtener más información acerca de las características Factoría de datos de Azure y Lote de Azure. Por ejemplo, para procesar los segmentos de un intervalo de tiempo diferente, haga lo siguiente:

1.  Agregue las siguientes subcarpetas en **inputfolder**: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09 y coloque archivos de entrada en ellas. Cambie la hora de finalización de la canalización de 2015-11-16T05:00:00Z a 2015-11-16T10:00:00Z. En el **vista de diagrama**, haga doble clic en **InputDataset** y confirme que los segmentos de entrada están listos. Haga doble clic en **OuptutDataset** para ver el estado de los segmentos de salida. Si se encuentran en el estado Listo, busque los archivos de salida en outputfolder.

2.  Aumente o disminuya la configuración de **concurrency** para saber cómo afecta al rendimiento de la solución, especialmente el procesamiento que se produce en Lote de Azure. (Consulte Paso 4: Creación y ejecución de la canalización con una actividad personalizada para más información sobre la configuración **concurrency**).

3.  Cree un grupo con un valor mayor o menor en **Máximo de tareas por máquina virtual**. Actualice el servicio vinculado de Lote de Azure en la solución de Factoría de datos para que use el nuevo grupo que creó. (Consulte Paso 4: Creación y ejecución de la canalización con una actividad personalizada para más información sobre la configuración **Máximo de tareas por máquina virtual**).

4.  Cree un grupo de Lote de Azure con la característica **Autoescala**. El escalado automático de los nodos de ejecución de un grupo de Lote de Azure es el ajuste dinámico de la potencia de procesamiento que usa su aplicación. Consulte [Escalado automático de los nodos de proceso en un grupo de Lote de Azure](../batch/batch-automatic-scaling.md).

    En la solución de ejemplo, el método **Execute** invoca al método **Calculate** que procesa un segmento de datos de entrada para generar un segmento de datos de salida. Puede escribir su propio método para procesar los datos de entrada y reemplazar la llamada al método Calculate en el método Execute por una llamada a su método.

## Pasos siguientes: Consumo de los datos

Después de procesar datos, puede consumirlos con herramientas en línea como **Microsoft Power BI**. Estos vínculos lo ayudarán a comprender Power BI y aprender a usarlo en Azure:

-   [Exploración de un conjunto de datos en Power BI](https://support.powerbi.com/knowledgebase/articles/475159)

-   [Introducción a Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/471664)

-   [Actualizar datos en Power BI](https://support.powerbi.com/knowledgebase/articles/474669)

-   [Azure y Power BI](https://support.powerbi.com/knowledgebase/articles/568614)

## Referencias

-   [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/)

    -   [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md)

    -   [Introducción a la Factoría de datos de Azure](data-factory-build-your-first-pipeline.md)

    -   [Uso de actividades personalizadas en una canalización de Factoría de datos de Azure](data-factory-use-custom-activities.md)

-   [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

    -   [Datos básicos de Lote de Batch](../batch/batch-technical-overview.md)

    -   [Información general de las características de Lote de Azure](../batch/batch-api-basics.md)

    -   [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure](../batch/batch-account-create-portal.md)

    -   [Introducción a la biblioteca de Lote de Azure para .NET](../batch/batch-dotnet-get-started.md)

<!---HONumber=AcomDC_0128_2016-->