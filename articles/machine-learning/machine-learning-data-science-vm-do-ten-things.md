<properties
	pageTitle="Diez cosas que puede hacer en Data Science Virtual Machine | Microsoft Azure"
	description="Realice diversas tareas de exploración y modelado de datos en Data Science Virtual Machine."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/13/2016"
	ms.author="gokuma;weig;bradsev" />

# Diez cosas que puede hacer en Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) es un eficaz entorno de desarrollo de ciencia de datos que le permite realizar diversas tareas de exploración y modelado de datos. El entorno incluye ya en su compilación varias herramientas populares de análisis de datos que le permitirán comenzar rápidamente su análisis con toda facilidad de implementaciones locales, en la nube o híbridas. DSVM trabaja estrechamente con muchos servicios de Azure y puede leer y procesar los datos que ya están almacenados en Azure, en Almacenamiento de datos SQL de Azure, Azure Data Lake, Almacenamiento de Azure o DocumentDB. También puede sacar provecho de otras herramientas de análisis como Aprendizaje automático de Azure y Data Factory de Azure.


En este artículo le mostraremos cómo utilizar DSVM para realizar diferentes tareas relacionadas con la ciencia de datos e interactuar con otros servicios de Azure. Estas son algunas de las cosas que puede hacer en DSVM:

1. Explorar datos y desarrollar modelos localmente en DSVM con el Servidor R de Microsoft o Python
2. Utilizar un cuaderno de Jupyter Notebook para experimentar con los datos en un explorador mediante Python 2, Python 3 o Microsoft R, una versión de R para uso empresarial diseñada para ofrecer escalabilidad y rendimiento
3. Poner en operación los modelos creados con R y Python en Aprendizaje automático de Azure para que aplicaciones cliente puedan tener acceso a los modelos mediante una sencilla interfaz de servicios web
4. Administrar los recursos de Azure mediante el Portal de Azure o Powershell 
5. Ampliar el espacio de almacenamiento y compartir código o conjuntos de datos de gran escala entre todo el equipo mediante la creación de Almacenamiento de archivos de Azure como una unidad que se puede montar en su DSVM 
6. Compartir código con su equipo mediante GitHub y tener acceso a su repositorio utilizando los clientes de Git preinstalados: Git Bash, Git GUI
7. Obtener acceso a diversos servicios de análisis y datos de Azure, como Almacenamiento de blobs de Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure DocumentDB, Almacenamiento de datos SQL de Azure y bases de datos
8. Crear informes y paneles con la aplicación Power BI Desktop preinstalada en DSVM e implementarlos en la nube
9. Escalar dinámicamente DSVM para satisfacer las necesidades del proyecto 
10. Instalar herramientas adicionales en la máquina virtual

>[AZURE.NOTE] A muchos de los servicios de almacenamiento y análisis de datos adicionales que se enumeran en este artículo se les aplican gastos de uso adicionales. Para más detalles, consulte la página [Precios de Azure](https://azure.microsoft.com/pricing/).

**Requisitos previos**

- Necesitará una suscripción de Azure. [Aquí](https://azure.microsoft.com/free/) puede registrarse para obtener una evaluación gratuita. 

- Las instrucciones para el aprovisionamiento de Data Science Virtual Machine en el Portal de Azure están disponibles en [Creación de una máquina virtual de Windows en el Portal de Azure](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## 1\. Explorar datos y desarrollar modelos con el Servidor R de Microsoft o Python

Puede utilizar lenguajes como R y Python para realizar el análisis de datos directamente en DSVM.

En el caso de R, puede utilizar un IDE llamado "Revolution R Enterprise 8.0" que se encuentra en el menú de inicio o el escritorio. Microsoft ha aportado bibliotecas adicionales además de la de CRAN-R y de código abierto para habilitar el análisis escalable y la capacidad de analizar datos mayores que el tamaño de memoria permitido mediante el análisis de fragmentos en paralelo. También puede instalar el IDE de R que prefiera, como [RStudio](https://www.rstudio.com/products/rstudio-desktop/).

En el caso de Python, puede usar un IDE como Visual Studio Community Edition, que ya tiene preinstalada la extensión Herramientas de Python para Visual Studio (PTVS). De forma predeterminada, en PTVS solo está configurada una versión básica de Python 2.7 (sin ninguna biblioteca de análisis como SciKit o Pandas). Para habilitar Anaconda Python 2.7 y 3.5, es necesario hacer lo siguiente:

* Cree entornos personalizados para cada versión; para hacerlo, navegue a **Herramientas** -> **Herramientas de Python** -> **Entornos de Python** y haga clic en "**+ Personalizar**" en Visual Studio 2015 Community Edition.
* Incluya una descripción y establezca las rutas de acceso del prefijo del entorno, como *c:\\anaconda* para Anaconda Python 2.7 O *c:\\anaconda\\envs\\py35* para Anaconda Python 3.5. 
* Haga clic en **Detección automática** y luego en **Aplicar** para guardar el entorno. 

Este es el aspecto que tendrá la configuración personalizada del entorno en Visual Studio.

![Programa de instalación de PTVS](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

Para más detalles acerca de cómo crear entornos de Python, consulte la [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).
  
Ya está configurado el entorno para crear un nuevo proyecto de Python. Navegue a **Archivo** -> **Nuevo** -> **Proyecto** -> **Python** y seleccione el tipo de aplicación de Python que va a compilar. Puede establecer el entorno de Python del proyecto actual en la versión deseada (Anaconda 2.7 o 3.5): haga clic con el botón derecho en **Python environment** (Entorno de Python), seleccione **Add/Remove Python Environments** (Agregar o quitar entornos de Python) y seleccione el entorno que desea asociar con el proyecto. Puede encontrar más información acerca de cómo trabajar con PTVS en la página de [documentación](https://github.com/Microsoft/PTVS/wiki) del producto.

## 2\. Utilizar un cuaderno de Jupyter Notebook para explorar y modelar los datos con Python o R

Jupyter Notebook es un eficaz entorno que proporciona un IDE basado en explorador para la exploración y el modelado de datos. En un cuaderno de Jupyter Notebook se pueden utilizar Python 2, Python 3 o R (tanto de código abierto como de Microsoft R Server).
 
Para iniciar el cuaderno de Jupyter Notebook, haga clic en el icono del menú Inicio o en el inicio del Escritorio **Jupyter Notebook**. En DSVM, también puede ir a "https://localhost:9999/" para tener acceso al cuaderno de Jupyter Notebook. Si se le pide una contraseña, utilice las instrucciones que se proporcionan en la [página de documentación de DSVM](machine-learning-data-science-provision-vm.md/#how-to-create-a-strong-password-on-the-jupyter-notebook-server) para crear una contraseña para acceder al cuaderno de Jupyter Notebook.

Una vez que lo haya abierto, verá un directorio con varios cuadernos de ejemplo que se incluyen en DSVM. Ahora puede:

- Hacer clic en el cuaderno para ver el código. 
- Ejecutar cada celda presionando **MAYÚS+ENTRAR**. 
- Ejecutar todo el cuaderno haciendo clic en **Cell** (Celda) -> **Run** (Ejecutar).
- Crear un nuevo cuaderno, para lo que debe hacer clic en el icono de Jupyter (esquina superior izquierda), luego en el botón **New** (Nuevo) de la derecha y, finalmente, elegir el idioma del cuaderno (también conocido como kernel). 

>[AZURE.NOTE] Actualmente se admite Python 2.7, Python 3.5 y R. El kernel de R admite la programación tanto en R de código abierto como en el Servidor R de Microsoft escalable para empresas.

Cuando se encuentre en el cuaderno, podrá explorar los datos, compilar el modelo y probar el modelo con una selección de bibliotecas.


## 3\. Compilación de modelos mediante R y Python, y hacer que estén operativos mediante Aprendizaje automático de Azure

Una vez compilado y validado el modelo, normalmente el paso siguiente es implementarlo en producción. Esto permite a las aplicaciones cliente invocar las predicciones del modelo en tiempo real o en el modo por lotes. Aprendizaje automático de Azure proporciona un mecanismo para hacer que un modelo creado en R o Python esté operativo.

Al poner en operación su modelo en Aprendizaje automático de Azure, se expone un servicio web que permite a los clientes realizar llamadas de REST que pasan parámetros de entrada y reciben predicciones del modelo como salidas.

>[AZURE.NOTE] Si aún no se ha registrado en Aprendizaje automático de Azure y desea obtener un área de trabajo gratuita o un área de trabajo estándar, visite la página principal de [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/) y haga clic en "Get Started" (Comenzar).

### Compilación y puesta en operación de modelos de Python

Este es un fragmento de código desarrollado en un cuaderno de Jupyter Notebook de Python que crea un modelo simple mediante la biblioteca SciKit-learn.
  
	#IRIS classification
	from sklearn import datasets
	from sklearn import svm
	clf = svm.SVC()
	iris = datasets.load_iris()
	X, y = iris.data, iris.target
	clf.fit(X, y) 
 
El método que se usa para implementar los modelos de Python en Aprendizaje automático de Azure encapsula la predicción del modelo en una función y la decora con los atributos que proporciona la biblioteca de Python de Aprendizaje automático de Azure preinstalada que denotan el identificador de área de trabajo de Aprendizaje automático de Azure, la clave de API y los parámetros de entrada y retorno.

	from azureml import services
	@services.publish(workspaceid, auth_token)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(int) #0, or 1, or 2
	def predictIris(sep_l, sep_w, pet_l, pet_w):
 	inputArray = [sep_l, sep_w, pet_l, pet_w]
	return clf.predict(inputArray)

Un cliente ahora puede realizar llamadas al servicio web. Para su comodidad, hay contenedores que construyen las solicitudes de la API de REST. Aquí puede ver un código de ejemplo para consumir el servicio web.

	# Consume through web service URL and keys
	from azureml import services
	@services.service(url, api_key)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(float)
	def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

	IrisPredictor(3,2,3,4)


>[AZURE.NOTE] En la actualidad, la biblioteca de Aprendizaje automático de Azure solo se admite en Python 2.7.

### Compilación y puesta en operación de modelos de R

Los modelos de R generados en Data Science Virtual Machine, o en cualquier otro lugar, se pueden implementar en Aprendizaje automático de Azure de forma similar a como se hace para Python. Estos son los pasos:

- Crea un archivo settings.json como el que aparece más abajo para proporcionar el identificador del área de trabajo y el token de autenticación.
- Escriba un contenedor para la función de predicción del modelo. 
- Llame a ```publishWebService``` en la biblioteca de Aprendizaje automático de Azure para pasar el contenedor de funciones.  

Este es el procedimiento y los fragmentos de código que se pueden utilizar para configurar, compilar, publicar y consumir un modelo como un servicio web en Aprendizaje automático de Azure.

#### Configuración

1.  Instale el paquete de R de AzureML, para lo que debe escribir escribiendo ```install.packages("AzureML")``` en el IDE de Revolution R Enterprise 8.0 o en su IDE de R. 
2.  Descargue la RTools de [aquí](https://cran.r-project.org/bin/windows/Rtools/). Para que R esté operativo en AzureMl, necesita la utilidad de compresión de la ruta de acceso (y el archivo zip.exe con nombre). 
3.  Crear un archivo de settings.json en un directorio llamado ```.azureml``` de su directorio particular y especifique los parámetros desde el área de trabajo de AzureML:

Estructura del archivo settings.json:

	{"workspace":{
	"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
	"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
	}}


#### Creación de un modelo en R y posterior publicación en AzureML

	library(AzureML)
	ws <- workspace(config="~/.azureml/settings.json")

	if(!require("lme4")) install.packages("lme4")
	library(lme4)
	set.seed(1)
	train <- sleepstudy[sample(nrow(sleepstudy), 120),]
	m <- lm(Reaction ~ Days + Subject, data = train)
 
	# Define a prediction function to publish based on the model:
	sleepyPredict <- function(newdata){
  		predict(m, newdata=newdata)
	}
 
	ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### Consumo del modelo implementado en Aprendizaje automático de Azure

Para consumir el modelo desde una aplicación cliente, usaremos la biblioteca de Aprendizaje automático de Azure para buscar el servicio web publicado por su nombre mediante la llamada de API `services`, con el fin de determinar el punto de conexión. Luego basta con llamar a la función `consume` y pasar la trama de datos que se va a predecir. El código siguiente se utiliza para consumir el modelo publicado como servicio web de Aprendizaje automático de Azure.


	library(AzureML)
	library(lme4)
	ws <- workspace(config="~/.azureml/settings.json")

	s <-  services(ws, name = "sleepy lm")
	s <- tail(s, 1) # use the last published function, in case of duplicate function names

	ep <- endpoints(ws, s)

	# OK, try this out, and compare with raw data
	ans = consume(ep, sleepstudy)$ans

[Aquí](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf) se puede encontrar más información acerca de la biblioteca de R de Aprendizaje automático de Azure.

## 4\. Administrar los recursos de Azure mediante el Portal de Azure o Powershell

DSVM no solo le permite crear la solución de análisis localmente en la máquina virtual, sino que también posibilita el acceso a servicios en la nube de Microsoft Azure. Azure proporciona varios servicios de proceso, almacenamiento, análisis de datos y de otra índole que puede administrar desde su DSVM y a los que puede tener acceso desde dicho entorno.

Para administrar una suscripción y los recursos en la nube de Azure, puede usar el explorador y apuntar al [Portal de Azure](portal.azure.com). También puede usar Azure Powershell para administrar la suscripción y los recursos de Azure mediante un script. Puede ejecutar Azure Powershell desde un acceso directo en el escritorio o desde el menú de inicio denominado "Microsoft Azure Powershell". Para más información acerca de cómo administrar su suscripción y los recursos de Azure mediante los scripts de Windows Powershell, consulte la [documentación de Microsoft Azure Powershell](../powershell-azure-resource-manager.md).


## 5\. Ampliar el espacio de almacenamiento con un sistema de archivos compartidos

Los científicos de datos pueden compartir grandes conjuntos de datos, código u otros recursos dentro del equipo. La propia máquina DSVM dispone de 70 GB de espacio disponible. Para ampliar el almacenamiento, puede utilizar el servicio de archivos de Azure y montarlo en DSVM o acceder a él través de la API de REST.

>[AZURE.NOTE] El espacio máximo del recurso compartido del servicio de archivos de Azure es de 5TB y el límite de tamaño para un archivo individual es de 1TB.

Puede usar Azure Powershell para crear un recurso compartido del servicio de archivos de Azure. Aquí se muestra el script que debe ejecutar en Azure PowerShell para crear un recurso compartido del servicio de archivos de Azure.

	# Authenticate to Azure. 
	Login-AzureRmAccount
	# Select your subscription
	Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
	# Create a new resource group. 
	New-AzureRmResourceGroup -Name <dsvmdatarg>
	# Create a new storage account. You can reuse existing storage account if you wish. 
	New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
	# Set your current working storage account
	Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>
	
	# Create a Azure File Service Share
	$s = New-AzureStorageShare <<teamsharename>>
	# Create a directory under the FIle share. You can give it any name
	New-AzureStorageDirectory -Share $s -Path <directory name>
	# List the share to confirm that everything worked
	Get-AzureStorageFile -Share $s


Ahora que ha creado un recurso compartido de archivos de Azure, puede montarlo en cualquier máquina virtual de Azure. Es muy recomendable que la máquina virtual se encuentre en el mismo centro de datos que la cuenta de almacenamiento para evitar la latencia y los cobros por la transferencia de datos. Estos son los comandos para montar la unidad en DSVM que se pueden ejecutar en Azure Powershell.


	# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
	cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>
	
	# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
	net use z:  \<mydatadisk>.file.core.windows.net<<teamsharename>>


Ya puede acceder a esta unidad del mismo modo que a cualquier otra unidad normal de la máquina virtual.

## 6\. Compartir código con su equipo mediante GitHub 

GitHub es un repositorio de código donde puede encontrar una gran cantidad de código de ejemplo y orígenes para diferentes herramientas usando las diversas tecnologías que comparte la comunidad de desarrolladores. Utiliza Git como tecnología para realizar un seguimiento de las versiones de los archivos de código y almacenarlas. GitHub es también una plataforma donde podrá crear su propio repositorio para almacenar el código compartido y la documentación de su equipo, implementar el control de versiones y también controlar quién tiene acceso para ver el código y contribuir al mismo. Para más información acerca del uso de Git, visite las [páginas de ayuda de Github](https://help.github.com/). Puede usar Github como uno de los métodos para colaborar con su equipo, usar el código desarrollado por la comunidad y aportar código de vuelta a la comunidad.

DSVM ya incluye herramientas de cliente tanto en la línea de comandos como en la GUI para tener acceso al repositorio de GitHub. La herramienta de línea de comandos que se usa para trabajar con Git y GitHub se denomina Git Bash. La versión de Visual Studio instalada en DSVM cuenta con las extensiones de Git. Puede encontrar iconos de inicio para estas herramientas en el menú de inicio y en el escritorio.

Para descargar código de un repositorio de GitHub, debe usar el comando ```git clone```. Por ejemplo. para descargar en el directorio actual el repositorio de ciencia de datos que publica Microsoft, puede ejecutar el comando siguiente una vez que se encuentre en ```git-bash```.

	git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

En Visual Studio, puede realizar la misma operación de clonación. La captura de pantalla siguiente muestra cómo acceder a las herramientas de Git y Github en Visual Studio.

![GIT en Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


En los distintos recursos disponibles en github.com puede encontrar más información sobre el uso de Git para trabajar con un repositorio de GitHub. La [hoja de referencia rápida](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) puede resultar un recurso útil.


## 7\. Obtener acceso a diversos servicios de análisis y datos de Azure

### Blob de Azure

Un blob de Azure es un almacenamiento confiable y económico para muchos y pocos datos. Examinemos cómo mover los datos a un blob de Azure y cómo acceder a los datos almacenados en un blob de Azure.

**Requisito previo**

- **Cree una cuenta de Almacenamiento de blobs de Azure desde el [Portal de Azure](http://portal.azure.com).**

![Create\_Azure\_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- Confirme que la herramienta de línea de comandos preinstalada AzCopy se encuentra en ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Para no tener que escribir la ruta de acceso completa cada vez que ejecute esta herramienta, puede agregar el directorio que contiene el archivo azcopy.exe a la variable de entorno PATH. Para más información sobre la herramienta AzCopy, consulte la [documentación de AzCopy](../storage/storage-use-azcopy.md).

- Inicie el Explorador de almacenamiento de Azure desde uno de los iconos del Escritorio de la máquina virtual. También puede encontrarlo en el directorio ```C:\Program Files (x86)\Neudesic\Azure Storage Explorer 6```.

![AzureStorageExplorer\_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**Traslado de datos desde la máquina virtual a un Blob de Azure: AzCopy**

Para mover datos entre los archivos locales y el almacenamiento de blobs, puede usar AzCopy en la línea de comandos o PowerShell: `AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt`

Reemplace **C:\\myfolder** por la ruta de acceso al lugar donde está almacenado su archivo, **mystorageaccount** por el nombre de su cuenta de Almacenamiento de blobs, **mycontainer** por el nombre del contenedor y **storage account key** por la clave de acceso a Almacenamiento de blobs. Las credenciales de su cuenta de almacenamiento pueden encontrarlas en el [Portal de Azure](http://portal.azure.com).

![StorageAccountCredential\_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Ejecute el comando de AzCopy en PowerShell o desde el símbolo del sistema. Aquí puede ver algunos ejemplos del uso de comandos de AzCopy:


	# Copy *.sql from local machine to a Azure Blob
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql
	
	# Copy back all files from Azure Blob container to Local machine
	
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
	


Una vez que ejecute el comando AzCopy para copiar a un blob de Azure, verá que el archivo se mostrará en seguida en el Explorador de almacenamiento de Azure.

![AzCopy\_run\_finshed\_Storage\_Explorer\_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**Traslado de datos desde la máquina virtual a un Blob de Azure: Explorador de almacenamiento de Azure**

También puede cargar datos desde el archivo local en la máquina virtual mediante el Explorador de almacenamiento de Azure:

![](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_upload_v2.png)


**Lectura de datos de Blob de Azure: módulo lector de Aprendizaje automático de Azure**

En Estudio de aprendizaje automático de Azure puede usar un **módulo lector** para leer datos del blob.

![AML\_ReaderBlob\_Module\_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**Lectura de datos de Blob de Azure: ODBC de Python**

Puede usar la biblioteca **BlobService** para leer datos directamente del blob en un programa de cuaderno de Jupyter Notebook o de Python.

En primer lugar, importe los paquetes necesarios:

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage import BlobService
	import tables
	import time
	import zipfile
	import random

A continuación, especifique sus credenciales de la cuenta de Blob de Azure y lea los datos del blob:

    CONTAINERNAME = 'xxx'
	STORAGEACCOUNTNAME = 'xxxx'
	STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
	BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
	localfilename = 'trip_data_1.csv'
	LOCALDIRECTORY = os.getcwd()
	LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

	#download from blob
	t1 = time.time()
	blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
	t2 = time.time()
	print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

	#unzipping downloaded files if needed
	#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
	#    z.extractall(LOCALDIRECTORY)

	df1 = pd.read_csv(LOCALFILE, header=0)
	df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
	print 'the size of the data is: %d rows and  %d columns' % df1.shape
	
Los datos se leen como una trama de datos:

![IPNB\_data\_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### Azure Data Lake

El Almacén de Azure Data Lake es un repositorio a gran escala para cargas de trabajo de análisis de macrodatos compatible con el sistema de archivos distribuido Hadoop (HDFS) que funciona tanto con el ecosistema de Hadoop como con Análisis de Azure Data Lake. Aquí se muestra cómo mover datos al Almacén de Azure Lake y ejecutar análisis con Análisis de Azure Data Lake.

**Requisito previo**

- Cree un Análisis de Azure Data Lake en el [Portal de Azure](http://portal.azure.com).

![Azure\_Data\_Lake\_Create\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- Las **herramientas de Azure Data Lake** para **Visual Studio** que se encuentran en este [vínculo](https://www.microsoft.com/download/details.aspx?id=49504) ya están instaladas en la versión Visual Studio Community Edition de la máquina virtual. Después de iniciar Visual Studio e iniciar sesión en su suscripción de Azure, verá su cuenta de Análisis de datos de Azure y el almacenamiento en el panel izquierdo de Visual Studio. 

![Azure\_Data\_Lake\_PlugIn\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


**Traslado de datos desde la máquina virtual a Data Lake: Explorador de Azure Data Lake**

Puede usar el **Explorador de Azure Data Lake** para cargar datos desde los archivos locales de la máquina virtual al almacenamiento de Data Lake.

![Azure\_Data\_Lake\_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

También puede crear una canalización de datos para producir el movimiento de datos hacia o desde Azure Data Lake. Para ello, debe usar [Data Factory de Azure (ADF)](https://azure.microsoft.com/services/data-factory/). Este [artículo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) le guiará a través de los pasos necesarios para generar las canalizaciones de datos.

**Lectura de datos desde Blob de Azure a Data Lake: U-SQL**

Si los datos residen en el Almacenamiento de blobs de Azure, puede leer directamente los datos desde el blob de almacenamiento de Azure en una consulta U-SQL. Antes de crear la consulta U-SQL, asegúrese de que la cuenta de Almacenamiento de blobs esté vinculada a su instancia de Azure Data Lake. Vaya al **Portal de Azure**, busque el panel de Análisis de Azure Data Lake, haga clic en **Agregar origen de datos**, en Tipo de almacenamiento seleccione **Almacenamiento de Azure** y escriba el nombre y la clave de la cuenta de Almacenamiento de Azure. A continuación, podrá hacer referencia a los datos almacenados en la cuenta de almacenamiento.

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


En Visual Studio, puede leer datos de un Almacenamiento de blobs, manipular los datos, realizar ingeniería de características y presentar los datos resultantes en Azure Data Lake o Almacenamiento de blobs de Azure. Cuando haga referencia a los datos en el Almacenamiento de blobs, use ****wasb://**; sin embargo, cuando haga referencia a los datos de Azure Data Lake, use ****swbhdfs://**

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Puede utilizar las siguientes consultas U-SQL en Visual Studio:

	@a =
	    EXTRACT medallion string,
	            hack_license string,
	            vendor_id string,
	            rate_code string,
	            store_and_fwd_flag string,
	            pickup_datetime string,
	            dropoff_datetime string,
	            passenger_count int,
	            trip_time_in_secs double,
	            trip_distance double,
	            pickup_longitude string,
	            pickup_latitude string,
	            dropoff_longitude string,
	            dropoff_latitude string
	
	    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
	    USING Extractors.Csv();
	
	@b = 
	    SELECT vendor_id, 
	    COUNT(medallion) AS cnt_medallion,
	    SUM(passenger_count) AS cnt_passenger,
	    AVG(trip_distance) AS avg_trip_dist,
	    MIN(trip_distance) AS min_trip_dist,
	    MAX(trip_distance) AS max_trip_dist,
	    AVG(trip_time_in_secs) AS avg_trip_time
	    FROM @a
	    GROUP BY vendor_id;
	
	OUTPUT @b   
	TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
	USING Outputters.Csv();
	
	OUTPUT @b   
	TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
	USING Outputters.Csv();
	


Después de enviar la consulta al servidor, aparecerá un diagrama con el estado de su trabajo.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**Consulta de datos en Data Lake: U-SQL**

Después de que el conjunto de datos se haya introducido en Azure Data Lake, se puede usar el [lenguaje U-SQL](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para explorar los datos y realizar consultas en los mismos. El lenguaje U-SQL es parecido a T-SQL, pero combina algunas características de C# para que los usuarios puedan escribir módulos personalizados, funciones definidas por el usuario, etc. Puede utilizar los scripts del paso anterior.

Una vez que la consulta se envía al servidor, el archivo tripdata\_summary.CSV aparecerá rápidamente en el **Explorador de Azure Data Lake**. Para obtener una vista previa de los datos, haga clic con el botón derecho en el archivo.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

Para ver la información del archivo:

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### Clústeres de Hadoop de HDInsight

HDInsight de Azure es un servicio administrado de Apache Hadoop, Spark, HBase y Storm en la nube. Puede trabajar fácilmente con los clústeres de HDInsight de Azure desde la máquina virtual de ciencia de datos.

**Requisito previo**

- Cree una cuenta de Almacenamiento de blobs de Azure desde el [Portal de Azure](http://portal.azure.com). Esta cuenta de almacenamiento se utiliza para almacenar datos para clústeres de HDInsight.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- Personalice los clústeres de Hadoop de HDInsight de Azure desde el [Portal de Azure](machine-learning-data-science-customize-hadoop-cluster.md).

  - Es necesario vincular la cuenta de almacenamiento creada con el clúster de HDInsight en el momento de su creación. Esta cuenta de almacenamiento se utiliza para tener acceso a datos que se pueden procesar en el clúster.
	
![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

  - Después de crear el clúster debe habilitar el **acceso remoto** al nodo principal del mismo. Recuerde las credenciales de acceso remoto que especifique aquí (distintas de las especificadas para el clúster en su creación), ya que las necesitará más adelante.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

  - Cree un área de trabajo de Aprendizaje automático de Azure. Los experimentos de Aprendizaje automático se almacenarán en esta área de trabajo. Seleccione las opciones resaltadas en el portal, tal como se muestra en la captura de pantalla siguiente. 

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


  - Luego especifique los parámetros del área de trabajo de Aprendizaje automático de Azure

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

  - Cargue los datos mediante el cuaderno de IPython. En primer lugar, importe los paquetes requeridos, escriba las credenciales, cree una base de datos en su cuenta de almacenamiento y cargue los datos en los clústeres de HDI. 


		#Import required Packages
		import pyodbc
		import time as time
		import json
		import os
		import urllib
		import urllib2
		import warnings
		import re
		import pandas as pd
		import matplotlib.pyplot as plt
		from azure.storage.blob import BlobService
		warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


		#Create the connection to Hive using ODBC
		SERVER_NAME='xxx.azurehdinsight.net'
		DATABASE_NAME='nyctaxidb'
		USERID='xxx'
		PASSWORD='xxxx'
		DB_DRIVER='Microsoft Hive ODBC Driver'
		driver = 'DRIVER={' + DB_DRIVER + '}'
		server = 'Host=' + SERVER_NAME + ';Port=443'
		database = 'Schema=' + DATABASE_NAME
		hiveserv = 'HiveServerType=2'
		auth = 'AuthMech=6'
		uid = 'UID=' + USERID 
		pwd = 'PWD=' + PASSWORD
		CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
		connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
		cursor=connection.cursor()


		#Create Hive database and tables
		queryString = "create database if not exists nyctaxidb;"
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.trip
		                ( 
		                    medallion string, 
		                    hack_license string,
		                    vendor_id string, 
		                    rate_code string, 
		                    store_and_fwd_flag string, 
		                    pickup_datetime string, 
		                    dropoff_datetime string, 
		                    passenger_count int, 
		                    trip_time_in_secs double, 
		                    trip_distance double, 
		                    pickup_longitude double, 
		                    pickup_latitude double, 
		                    dropoff_longitude double, 
		                    dropoff_latitude double)  
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.fare 
		                ( 
		                    medallion string, 
		                    hack_license string, 
		                    vendor_id string, 
		                    pickup_datetime string, 
		                    payment_type string, 
		                    fare_amount double, 
		                    surcharge double,
		                    mta_tax double,
		                    tip_amount double,
		                    tolls_amount double,
		                    total_amount double)
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
	
	
		#Upload data from blob storage to HDI cluster
		for i in range(1,13):
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
		    cursor.execute(queryString)
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
		    cursor.execute(queryString)


- Como alternativa, puede seguir este [tutorial](machine-learning-data-science-process-hive-walkthrough.md) para cargar los datos de los taxis de Nueva York en el clúster de HDI. Entre los principales pasos se encuentran los siguientes:

	- AzCopy: descargar los CSV comprimidos desde el blob público a la carpeta local
	- AzCopy: cargar los CSV descomprimidos desde la carpeta local al clúster HDI
	- Inicio de sesión en el nodo principal del clúster de Hadoop y preparación para el análisis de exploración de datos

Después de cargar los datos en el clúster de HDI, puede consultarlos en el Explorador de almacenamiento de Azure. Y tendrá un archivo nyctaxidb de base de datos creado en el clúster HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/Upload_Data_to_HDI_cluster_Azure_Explorer.PNG)


**Exploración de datos: consultas de Hive en Python**

Como los datos están en el clúster de Hadoop, puede utilizar el paquete pyodbc para conectarse a los clústeres de Hadoop y consultar la base de datos mediante Hive para realizar tareas de exploración e ingeniería de características. Puede ver las tablas existentes que hemos creado en el paso de requisitos previos.

	queryString = """
	    show tables in nyctaxidb2;
	    """
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Ahora fijémonos en el número de registros de cada mes y las frecuencia con que se repiten los viajes con o sin propina en la tabla correspondiente:

	queryString = """
	    select month, count(*) from nyctaxidb.trip group by month;
	    """
	results = pd.read_sql(queryString,connection)
	
	%matplotlib inline
	
	results.columns = ['month', 'trip_count']
	df = results.copy()
	df.index = df['month']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


	queryString = """
	    SELECT tipped, COUNT(*) AS tip_freq 
	    FROM 
	    (
	        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
	        FROM nyctaxidb.fare
	    )tc
	    GROUP BY tipped;
	    """
	results = pd.read_sql(queryString,connection)
	
	results.columns = ['tipped', 'trip_count']
	df = results.copy()
	df.index = df['tipped']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Podemos calcular también la distancia entre la ubicación de recogida y depósito y, a continuación, compararla con la distancia del viaje.

	queryString = """
	                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
	                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
	                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance 
	                    from nyctaxidb.trip 
	                    where month=1 
	                        and pickup_longitude between -90 and -30
	                        and pickup_latitude between 30 and 90
	                        and dropoff_longitude between -90 and -30
	                        and dropoff_latitude between 30 and 90;
	            """
	results = pd.read_sql(queryString,connection)
	results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

	results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude', 
	                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
	df = results.loc[results['trip_distance']<=100] #remove outliers
	df = df.loc[df['direct_distance']<=100] #remove outliers
	plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


Ahora vamos a preparar una muestra reducida (1%) de un conjunto de datos para el modelado. Podemos usar estos datos en el módulo lector de Aprendizaje automático de Azure.


		queryString = """
	    create  table if not exists nyctaxi_downsampled_dataset_testNEW (
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    pickup_hour string,
	    pickup_week string,
	    weekday string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double,
	    direct_distance double,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double,
	    tipped string,
	    tip_class string
	    )
	    row format delimited fields terminated by ','
	    lines terminated by '\\n'
	    stored as textfile;
		"""
		cursor.execute(queryString)

		--- now insert contents of the join into the above internal table

		queryString = """
	    insert overwrite table nyctaxi_downsampled_dataset_testNEW
	    select
	    t.medallion,
	    t.hack_license,
	    t.vendor_id,
	    t.rate_code,
	    t.store_and_fwd_flag,
	    t.pickup_datetime,
	    t.dropoff_datetime,
	    hour(t.pickup_datetime) as pickup_hour,
	    weekofyear(t.pickup_datetime) as pickup_week,
	    from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
	    t.passenger_count,
	    t.trip_time_in_secs,
	    t.trip_distance,
	    t.pickup_longitude,
	    t.pickup_latitude,
	    t.dropoff_longitude,
	    t.dropoff_latitude,
	    t.direct_distance,
	    f.payment_type,
	    f.fare_amount,
	    f.surcharge,
	    f.mta_tax,
	    f.tip_amount,
	    f.tolls_amount,
	    f.total_amount,
	    if(tip_amount>0,1,0) as tipped,
	    if(tip_amount=0,0,
	    if(tip_amount>0 and tip_amount<=5,1,
	    if(tip_amount>5 and tip_amount<=10,2,
	    if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
	    from
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    pickup_datetime,
	    dropoff_datetime,
	    passenger_count,
	    trip_time_in_secs,
	    trip_distance,
	    pickup_longitude,
	    pickup_latitude,
	    dropoff_longitude,
	    dropoff_latitude,
	    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	    radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
	    rand() as sample_key
	
	    from trip
	    where pickup_latitude between 30 and 90
	        and pickup_longitude between -90 and -30
	        and dropoff_latitude between 30 and 90
	        and dropoff_longitude between -90 and -30
	    )t
	    join
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    pickup_datetime,
	    payment_type,
	    fare_amount,
	    surcharge,
	    mta_tax,
	    tip_amount,
	    tolls_amount,
	    total_amount
	    from fare
	    )f
	    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
	    where t.sample_key<=0.01
		"""
		cursor.execute(queryString)

Después de un tiempo, verá que los datos se han cargado en clústeres de Hadoop:
		
	queryString = """
	    select * from nyctaxi_downsampled_dataset limit 10;
	    """
	cursor.execute(queryString)
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**Lectura de datos de HDI con Aprendizaje automático de Azure: módulo lector**

También puede usar el módulo **lector** en Estudio de aprendizaje automático de Azure para acceder a la base de datos del clúster de Hadoop. Indique las credenciales de los clústeres HDI y la cuenta de Almacenamiento de Azure y podrá crear modelos de aprendizaje automático con la base de datos en clústeres HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

Puede ver a continuación el conjunto de datos con puntuación:

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### Almacenamiento de datos SQL de Azure y bases de datos

Almacenamiento de datos SQL Azure es un almacén de datos elástico que funciona como un servicio con experiencia SQL Server empresarial.

Para aprovisionar un Almacenamiento de datos SQL Azure, siga las instrucciones que se proporcionan en este [artículo](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Una vez que aprovisione el Almacenamiento de datos de SQL Azure, puede usar este [tutorial](machine-learning-data-science-process-sqldw-walkthrough.md) para realizar la carga, exploración y modelado de datos con los del Almacenamiento de datos SQL.

#### DocumentDB de Azure

Azure DocumentDB es una base de datos NoSQL en la nube. Con él no solo puede trabajar con documentos como JSON, sino que también almacenar y consultar dichos documentos.

Debe realizar los siguientes pasos como requisitos previos para tener acceso a DocumentDB desde DSVM.

1. Instale el SDK de Python de DocumentDB (ejecute ```pip install pydocumentdb``` desde el símbolo del sistema).
1. Cree una cuenta y una base de datos de DocumentDB desde el [Portal de Azure](https://portal.azure.com).
1. Descargue la "herramienta de migración de DocumentDB" desde [aquí](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) y extráigala en el directorio que prefiera.
1. Importe los datos de JSON (datos de volcanes) almacenados en un [blob público](https://cahandson.blob.core.windows.net/samples/volcano.json) en DocumentDB con los siguientes parámetros de comandos para la herramienta de migración (dtui.exe desde el directorio en que instaló la herramienta de migración de DocumentDB) Especifique los parámetros de ubicación de origen y destino de los que se muestran a continuación. 

	/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Después de importar los datos, puede ir a Jupyter y abrir el cuaderno *DocumentDBSample*, que contiene el código de Python necesario para acceder a DocumentDB y realizar algunas consultas básicas. Para más información acerca de DocumentDB, visite la [página de documentación](https://azure.microsoft.com/documentation/learning-paths/documentdb/) del servicio.


## 8\. Crear informes y paneles con la aplicación Power BI Desktop 

Vamos a visualizar el archivo JSON sobre volcanes que vimos en el ejemplo de DocumentDB anterior en Power BI para obtener información visual sobre los datos. En este [artículo de Power BI](../documentdb/documentdb-powerbi-visualize.md) encontrará una explicación detallada de los pasos que se deben seguir. Los pasos de alto nivel son los siguientes:

1. Abra Power BI Desktop y presione "Obtenga datos". Especifique la dirección URL de la siguiente forma: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Debería ver los registros JSON importados en forma una lista.
3. Convierta la lista en una tabla para que Power BI pueda trabajar con ella.
4. Expanda las columnas, para lo que debe hacer clic en el icono de expansión (el que tiene una "flecha izquierda y una flecha derecha" a la derecha de la columna).
5. Observe que la ubicación es un campo de "Registro". Expanda el registro y seleccione solo las coordenadas. Las coordenadas es una columna de la lista.
6. Agregue una nueva columna para convertir la columna de coordenadas de la lista en una columna LatLong separada por comas que une los dos elementos del campo de la lista de coordenadas mediante la fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```. 
7. Por último, convierta la columna ```Elevation``` en Decimal y seleccione **Cerrar** y **Aplicar**.

En lugar de seguir los pasos anteriores, puede pegar el código siguiente, que convierte en scripts estos pasos, en el Editor avanzado de Power BI. De este modo, podrá escribir las transformaciones de datos en un lenguaje de consultas.


	let
	    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
	    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
	    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
	    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
	    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
	    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
	in
	    #"Changed Type"
		


Ya tiene los datos en el modelo de datos de Power BI. El escritorio de Power BI debería tener un aspecto similar al que se muestra a continuación.

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

Puede empezar a crear informes y visualizaciones con el modelo de datos. Para generar un informe, puede seguir los pasos de este [artículo de Power BI](../documentdb/documentdb-powerbi-visualize.md#build-the-reports). El resultado final será un informe similar al siguiente.

![Vista de informes de Power BI Desktop: conector de Power BI](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## 9\. Escalar dinámicamente DSVM para satisfacer las necesidades del proyecto

Puede escalar y reducir verticalmente la máquina DSVM para satisfacer las necesidades del proyecto. Si no necesita utilizar la máquina virtual por la noche o los fines de semana, basta con que la apague desde el [Portal de Azure](https://portal.azure.com).

>[AZURE.NOTE]  Incurrirá en gastos de proceso si solo utiliza el botón de apagado del sistema operativo de la máquina virtual.

Si necesita trabajar con análisis de gran escala y requiere más capacidad de CPU, memoria o disco, puede encontrar una gran variedad de tamaños de máquina virtual en términos de núcleos de CPU, capacidad de memoria y tipos de disco (incluidas las unidades de estado sólido) que satisfarán sus necesidades presupuestarias y de proceso. En la página [Precios de Máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) encontrará la lista completa de máquinas virtuales, junto con sus precios por hora de proceso.

Del mismo modo, si necesita una menor capacidad de proceso de máquina virtual (si, por ejemplo, ha movido la mayor carga de trabajo a un clúster de Hadoop o Spark), puede reducir verticalmente el clúster desde el [Portal de Azure](https://portal.azure.com), en la configuración de su instancia de máquina virtual. Aquí puede ver una captura de pantalla.


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## 10\. Instalar herramientas adicionales en la máquina virtual

Hemos incluido varias herramientas que creemos que pueden cubrir muchas de las necesidades de análisis de datos más habituales y ahorrarle tiempo, ya que evitan que tenga que instalar y configurar los entornos uno por uno, y dinero, ya que solo paga los recursos que utilice.

Para mejorar su entorno de análisis, puede sacar provecho de otros servicios tanto de datos como de análisis de Azure, como se ha perfilado en este artículo. Somos conscientes de que en algunos casos sus necesidades pueden requerir herramientas adicionales, incluidas algunas herramientas propiedad de terceros. Tiene acceso administrativo completo en la máquina virtual para instalar las nuevas herramientas que necesite. También puede instalar paquetes adicionales de Python y R que no estén instalados previamente. En el caso de Python, puede utilizar ```conda``` o ```pip```. En cuanto a R, puede utilizar ```install.packages()``` en la consola de R o recurrir al IDE y elegir "**Packages** (Paquetes) -> **Install Packages** (Instalar paquetes)...".

## Resumen
Estas son solo algunas de las cosas que puede hacer en Microsoft Data Science Virtual Machine. Hay muchas más cosas que puede hacer para que sea un entorno de análisis efectivo.

<!---HONumber=AcomDC_0302_2016-->