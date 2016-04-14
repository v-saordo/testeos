<properties
	pageTitle="Carga de datos para trabajos de Hadoop en HDInsight | Microsoft Azure"
	description="Aprenda a cargar datos en HDInsight y a obtener acceso a ellos para trabajos de Hadoop con la CLI de Azure, el Explorador de almacenamiento de Azure, Azure PowerShell, la línea de comandos de Hadoop o Sqoop."
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="jgao"/>



#Carga de datos para trabajos de Hadoop en HDInsight

HDInsight de Azure ofrece un sistema de archivos distribuido de Hadoop (HDFS) completo a través del servicio de almacenamiento de blobs de Azure. Está diseñado como una extensión de HDFS para ofrecer una experiencia continua para los clientes. Habilita que el conjunto completo de componentes en el ecosistema de Hadoop opere directamente en los datos que administra. El almacenamiento de blobs de Azure y HDFS son sistemas de archivos diferentes que se han optimizado para el almacenamiento de datos y el cálculo en ellos. Para obtener más información sobre las ventajas del uso del almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

**Requisitos previos**

Tenga en cuenta el requisito siguiente antes de empezar:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

##Motivos para almacenar blobs

Los clústeres de HDInsight de Azure se implementan normalmente para ejecutar trabajos de MapReduce y dichos clústeres se anulan cuando los trabajos se completan. El mantenimiento de datos en los clústeres de HDFS después de que se hayan completado los cálculos supondría un alto coste para el almacenamiento de estos datos. El almacenamiento de blobs de Azure tiene una excelente disponibilidad, es altamente escalable, cuenta con una gran capacidad, un bajo coste y la opción de almacenamiento que se puede compartir para los datos que se van a procesar usando HDInsight. El almacenamiento de los datos en un blob permite que los clústeres de HDInsight que se usan para los cálculos se lancen de forma segura y sin perder los datos.

###Directorios

Los contenedores del almacenamiento de blobs de Azure almacenan los datos como pares de clave/valor y no hay jerarquía de directorios. No obstante, el carácter "/" se puede usar en el nombre de la clave para que parezca que el archivo está almacenado dentro de una estructura de directorios. HDInsight los ve como si fueran directorios reales.

Por ejemplo, la clave de un blob puede ser *input/log1.txt*. No hay directorios "input", pero dada la presencia del carácter "/" en el nombre de la clave, parece la ruta de un archivo.

Por eso, si usa las herramientas de Azure Explorer, puede que vea algunos archivos de 0 bytes. Estos archivos tienen dos propósitos:

- Si hay carpetas vacías, son una marca de la existencia de la carpeta. El almacenamiento de blobs de Azure es suficientemente inteligente para saber que si existe un blob que se llama foo/bar, es porque hay una carpeta llamada **foo**. Pero la única forma de indicar una carpeta vacía llamada **foo** es disponer este archivo especial de 0 bytes en su sitio.

- Contienen metadatos especiales que necesita el sistema de archivos de Hadoop, concretamente los permisos y los propietarios de las carpetas.

##Utilidades de la ea de comandos

Microsoft proporciona las utilidades siguientes para trabajar con el almacenamiento de blobs de Azure:

| Herramienta | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interfaz de la línea de comandos de Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Línea de comandos de Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Mientras que la CLI de Azure y Azure PowerShell AzCopy se pueden utilizar desde fuera de Azure, la línea de comandos de Hadoop sólo está disponible en el clúster de HDInsight y sólo permite cargar datos del sistema de archivos local en el almacenamiento de blobs de Azure.

###<a id="xplatcli"></a>Azure CLI

La CLI de Azure es una herramienta multiplataforma que le permite administrar los servicios de Azure. Para cargar datos en el almacenamiento de blobs de Azure, siga estos pasos:

1. [Instalación y configuración de la CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md).

2. Abra un símbolo del sistema, bash u otro shell y use lo siguiente para autenticarse en su suscripción de Azure.

		azure login

	Cuando se le solicite, escriba el nombre de usuario y la contraseña de su suscripción.

3. Escriba el comando siguiente para enumerar las cuentas de almacenamiento de su suscripción:

		azure storage account list

4. Seleccione la cuenta de almacenamiento que contiene el blob con en el que quiere trabajar y use el comando siguiente para recuperar la clave de esta cuenta:

		azure storage account keys list <storage-account-name>

	Esto debería devolver las claves **Principal** y **Secundaria**. Copie el valor de la clave **Principal** ya que se usará en los pasos siguientes.

5. Use el comando siguiente para recuperar una lista de contenedores de blobs dentro de la cuenta de almacenamiento:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Use los comandos siguientes para cargar y descargar archivos en el blob:

	* Para cargar un archivo:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Para descargar un archivo:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Si siempre trabajará con la misma cuenta de almacenamiento, puede establecer las siguientes variables de entorno en lugar de especificar la cuenta y la clave para cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: el nombre de la cuenta de almacenamiento.
>
> * **AZURE\_STORAGE\_ACCESS\_KEY**: la clave de la cuenta de almacenamiento.

###<a id="powershell"></a>Azure PowerShell.

Azure PowerShell es un eficaz entorno de scripting que se puede usar para controlar y automatizar la implementación y la administración de cargas de trabajo en Azure. Para obtener información sobre cómo configurar su estación de trabajo para que ejecute Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

**Para cargar un archivo local en el almacenamiento de blobs de Azure**

1. Abra la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
2. Configure los valores de las cinco primeras variables del script siguiente:

		$subscriptionName = "<AzureSubscriptionName>"
		$resourceGroupName = "<AzureResourceGroupName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		Switch-AzureMode -Name AzureResourceManager

		Add-AzureAccount
		Select-AzureSubscription $subscriptionName

		# Get the storage account key
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Pegue el script en la consola de Azure PowerShell para ejecutarlo para copiar el archivo.

Por ejemplo los scripts de PowerShell creados para trabajar con HDInsight, consulte [HDInsight tools (Herramientas de HDInsight)](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy es una herramienta de la línea de comandos diseñada para simplificar la tarea de transferir datos dentro y fuera de una cuenta de almacenamiento de Azure. Puede usarla como una herramienta independiente o incorporarla a una aplicación existente. [Descarga de AzCopy.][azure-azcopy-download].

La sintaxis de AzCopy es la siguiente:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obtener más información, consulte [AzCopy: carga y descarga de archivos para blobs de Azure][azure-azcopy].


###<a id="commandline"></a>Línea de comandos de Hadoop

La línea de comandos de Hadoop sólo es útil para almacenar los datos en el almacenamiento de blobs cuando los datos ya están presentes en el nodo principal del clúster.

Para usar la línea de comando de Hadoop, primero es preciso conectarse al nodo principal a través de uno de los métodos siguientes:

* **HDInsight para Windows**: [conectar mediante Escritorio remoto](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight para Linux**: conectar mediante SSH ([el comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) o [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Una vez conectado, puede utilizar la siguiente sintaxis para cargar un archivo al almacenamiento.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por ejemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como el sistema de archivos predeterminado de HDInsight está en el almacenamiento de blobs de Azure, /example/datadavinci.txt está en realidad en el almacenamiento de blobs de Azure. También puede referirse al archivo como:

	wasb:///example/data/data.txt

o

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obtener una lista de otros comandos de Hadoop que funcionan con archivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##Clientes gráficos

También hay varias aplicaciones que proporcionan una interfaz gráfica para trabajar con el almacenamiento de Azure. Esta es una lista de algunas de estas aplicaciones:

| Cliente | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Explorador de almacenamiento de Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###<a id="storageexplorer"></a>Explorador de almacenamiento de Azure

El *Explorador de almacenamiento de Azure* es una práctica herramienta para inspeccionar y modificar los datos de blobs. Se trata de una herramienta gratuita que se puede descargar de [http://storageexplorer.com/](http://storageexplorer.com/). El código fuente también está disponible en este vínculo.

Antes de usar la herramienta, debe saber el nombre y la clave de la cuenta de almacenamiento de Azure. Para ver instrucciones sobre cómo obtener esta información, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storage-account].

1. Ejecute el explorador de almacenamiento de Azure. Si es la primera vez que ejecuta el Explorador de almacenamiento, se le pedirá el ___Nombre de la cuenta de almacenamiento__ y la __Clave de cuenta de almacenamiento__. Si ya lo ejecutó antes, use el botón __Agregar__ para agregar un nombre y una clave de cuenta de almacenamiento nuevos.

    Escriba el nombre y la clave para la cuenta de almacenamiento que usa el clúster de HDinsight y seleccione __GUARDAR Y ABRIR__.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. En la lista de contenedores de la izquierda de la interfaz, haga clic en el nombre del contenedor que esté asociado al clúster de HDInsight. De forma predeterminada, es el nombre del clúster de HDInsight, pero puede ser diferente si especificó un nombre concreto al crear el clúster.

6. En la barra de herramientas, seleccione el icono de carga.

    ![Barra de herramientas con el icono de carga resaltado](./media/hdinsight-upload-data/toolbar.png)

7. Especifique el archivo que quiere cargar y haga clic en **Abrir**. Cuando se le pida, seleccione __Cargar__ para cargar el archivo en la raíz del contenedor de almacenamiento. Si quiere cargar el archivo en una ruta de acceso específica, escriba la ruta de acceso en el campo __Destino__ y seleccione __Cargar__.

    ![Cuadro de diálogo de carga de archivos](./media/hdinsight-upload-data/fileupload.png)
    
    Cuando termine de cargar el archivo, puede usarlo desde trabajos en el clúster de HDInsight.

##Montar el almacenamiento de blobs de Azure como unidad local

Vea [Montaje del almacenamiento de blobs de Azure como unidad local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##Servicios

###Factoría de datos de Azure

El servicio Factoría de datos de Azure es un servicio completamente administrado para crear servicios de almacenamiento de datos, procesamiento de datos y movimiento en canalizaciones de producción de datos confiable, escalable y simplificado.

Factoría de datos de Azure puede utilizarse para introducir datos en el almacenamiento de blobs de Azure o para crear canalizaciones de datos que usan directamente las características de HDInsight, como Hive y Pig.

Para obtener más información, consulte [Documentación de Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop es una herramienta diseñada para transferir datos entre Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos de Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS.

Para obtener más información, consulte [Use Sqoop with Hadoop in HDInsight (Uso de Sqoop con HDInsight)][hdinsight-use-sqoop].

##SDK de desarrollo

También es posible obtener acceso al almacenamiento de blobs de Azure mediante un SDK de Azure desde los siguientes lenguajes de programación:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obtener más información acerca de cómo instalar los SDK de Azure, consulte [Descargas de Azure](https://azure.microsoft.com/downloads/)


## Pasos siguientes
Ahora que ya sabe cómo enviar datos a HDInsight, consulte los artículos siguientes para aprender a realizar el análisis:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0218_2016-->