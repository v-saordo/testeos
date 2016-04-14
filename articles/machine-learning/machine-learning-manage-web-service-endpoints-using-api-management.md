<properties
	pageTitle="Aprenda a administrar los servicios web de AzureML con Administración de API | Microsoft Azure"
	description="Una guía que muestra cómo administrar los servicios web de AzureML mediante la Administración de API."
	keywords="aprendizaje automático, administración de api"
	services="machine-learning"
	documentationCenter=""
	authors="roalexan"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="roalexan" />


# Aprenda a administrar los servicios de web de AzureML mediante la Administración de API

##Información general

En esta guía se muestra cómo empezar a usar rápidamente la Administración de API para administrar los servicios web de AzureML.

##¿Qué es la Administración de API de Azure?

Administración de API de Azure es un servicio de Azure que le permite administrar los extremos de la API de REST al definir el acceso del usuario, el límite de uso y la supervisión de panel. Haga clic [aquí](https://azure.microsoft.com/services/api-management/) para obtener más información sobre Administración de API de Azure. Haga clic [aquí](api-management/api-management-get-started.md) para obtener una guía sobre cómo empezar a trabajar con Administración de API de Azure. Esta otra guía, en la que está basada esta guía, aborda más temas, incluidos las configuraciones de notificación, el nivel de precios, el control de respuestas, la autenticación de los usuarios, la creación de productos, las suscripciones de desarrollador y los paneles de uso.

##¿Qué es AzureML?

AzureML es un servicio de Azure para el aprendizaje automático que permite crear, implementar y compartir fácilmente las soluciones de análisis avanzado. Haga clic en [aquí](https://azure.microsoft.com/services/machine-learning/) para obtener información detallada sobre AzureML.

##Requisitos previos

Para completar a esta guía, necesita:

* Una cuenta de Azure. Si no tiene una cuenta de Azure, haga clic [aquí](https://azure.microsoft.com/pricing/free-trial/) para obtener más información sobre cómo crear una cuenta de evaluación gratuita.
* Una cuenta de AzureML. Si no dispone de una cuenta de Aprendizaje automático de Azure, haga clic [aquí](https://studio.azureml.net/) para obtener más información sobre cómo crear una cuenta de evaluación gratuita.
* El área de trabajo, el servicio y la api\_key para un experimento de Aprendizaje automático de Azure implementado como un servicio web. Haga clic [aquí](machine-learning/machine-learning-create-experiment.md) para obtener más información sobre cómo crear un experimento de Aprendizaje automático de Azure. Haga clic [aquí](machine-learning/machine-learning-publish-a-machine-learning-web-service.md) para obtener más información sobre cómo implementar un experimento de Aprendizaje automático de Azure como un servicio web. Además, el Apéndice A contiene instrucciones sobre cómo crear y probar un experimento de Aprendizaje automático de Azure sencillo e implementarlo como un servicio web.

##Creación de una instancia de Administración de API

A continuación se muestran los pasos para usar Administración de API para administrar el servicio web de AzureML. En primer lugar, cree una instancia del servicio. Inicie sesión en el [Portal clásico](https://manage.windowsazure.com/) y haga clic en **Nuevo** > **Servicios de aplicaciones** > **Administración de API** > **Crear**.

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Especifique una **dirección URL** única. Esta guía usa **demoazureml**, por lo que deberá elegir algo diferente. Elija la **Suscripción** y la **Región** deseadas para la instancia de servicio. Después de realizar las selecciones pertinentes, haga clic en el botón Siguiente.

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Especifique un valor para **Nombre de la organización**. Esta guía usa **demoazureml**, por lo que deberá elegir algo diferente. Escriba su dirección de correo electrónico en el campo **correo electrónico del administrador**. Esta dirección de correo electrónico se utiliza para notificaciones por parte del sistema Administración de API.

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Haga clic en la casilla para crear su instancia de servicio. *Tarda hasta treinta minutos en crear un nuevo servicio*.

##Creación de la API

Una vez creada la instancia de servicio, el paso siguiente es crear la API. Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones API se realizan con proxy en servicios web existentes. Esta guía crea las API que representan los servicios web de AzureML RRS y BES existentes.

Las API se crean y se configuran desde el portal para editores de API, al que se obtiene acceso a través del Portal de Azure clásico. Para ponerse en contacto con el portal para editores, seleccione la instancia de servicio.

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Haga clic en **Administrar** en el Portal de Azure clásico en el servicio de Administración de la API.

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Agregar API**.

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Escriba **API de demostración de Aprendizaje automático de Azure** como el **nombre de API web**. Escriba ****https://ussouthcentral.services.azureml.net** como la **dirección URL del Servicio web**. Escriba **azureml-demo** como **sufijo de la URL de API web**. Seleccione **HTTPS** como el esquema de **URL de API web**. Seleccione **Starter** en **Productos**. Cuando haya finalizado, haga clic en **Guardar** para crear la API.

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##Adición de operaciones

Haga clic en **Agregar operación** para agregar operaciones a esta API.

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

##Adición de una operación de registro de recursos

En primer lugar cree una operación para el servicio RRS de AzureML. Seleccione **POST** como **verbo HTTP**. Escriba **/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}** como **plantilla URL**. Escriba **Ejecutar RRS** como **Nombre para mostrar**.

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Haga clic en **Respuestas** > **AGREGAR** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##Adición de operaciones BES

No se incluyen capturas de pantalla para las operaciones BES ya que son muy similares a las de agregar la operación RRS.

###Envío (pero no inicio) de un trabajo de ejecución por lotes

Haga clic en **Agregar operación** para agregar la operación BES de AzureML a la API. Seleccione **POST** como **verbo HTTP**. Escriba **/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}** para la **plantilla URL**. Escriba **Envío de BES** en el **Nombre para mostrar**. Haga clic en **Respuestas** > **AGREGAR** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###Inicio de un trabajo de ejecución por lotes

Haga clic en **Agregar operación** para agregar la operación BES de AzureML a la API. Seleccione **POST** como **verbo HTTP**. Escriba **/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}** para la **plantilla URL**. Escriba **Inicio de BES** en el **Nombre para mostrar**. Haga clic en **Respuestas** > **AGREGAR** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###Obtener el estado o el resultado de un trabajo de ejecución por lotes

Haga clic en **Agregar operación** para agregar la operación BES de AzureML a la API. Seleccione **GET** como **verbo HTTP**. Escriba **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** para la **plantilla URL**. Escriba **Estado de BES** en el **Nombre para mostrar**. Haga clic en **Respuestas** > **AGREGAR** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###Eliminación de un trabajo de ejecución por lotes

Haga clic en **Agregar operación** para agregar la operación BES de AzureML a la API. Seleccione **DELETE** como **verbo HTTP**. Escriba **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** para la **plantilla URL**. Escriba **Eliminación de BES** en el **Nombre para mostrar**. Haga clic en **Respuestas** > **AGREGAR** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

##Llamada a una operación desde el portal para desarrolladores

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso de la guía llamará al método **Ejecución de RRS** que se agregó a la **API de demo de AzureML**. Haga clic en **Portal para desarrolladores**, en el menú que se encuentra en la parte superior derecha del Portal clásico.

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Haga clic en **API** en el menú superior y, a continuación, haga clic en **API de demo de AzureML** para ver las operaciones disponibles.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Seleccione **Ejecución de RRS** para la operación. Haga clic en **Pruébelo**.

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Para los parámetros de solicitud, escriba su **área de trabajo**, **servicio**, **2.0** para **apiversion** y **true** para **detalles**. Puede encontrar el **área de trabajo** y el **servicio** en el panel del servicio web de AzureML (consulte **Prueba del servicio web** en el Apéndice A).

Para los encabezados de solicitud, haga clic en **Agregar encabezado** y escriba **Content-Type** y **application/json**, después haga clic en **Agregar encabezado** y escriba **Autorización** y **Portador<YOUR AZUREML SERVICE API-KEY>**. Puede encontrar su **clave de API** en el panel del servicio web AzureML (consulte **Prueba del servicio web** en el apéndice A).

Escriba **{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["Este es un buen día"]]}}, "GlobalParameters": {}}** para el cuerpo de la solicitud.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Haga clic en **Send** (Enviar).

![send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Después de invocar una operación, el portal para desarrolladores mostrará el campo **Dirección URL solicitada** en el servicio de back-end, así como los campos **Estado de respuesta**, **Encabezados de respuesta** y **Contenido de respuesta**.

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##Apéndice A: Creación y prueba de un servicio web sencillo de AzureML

###Creación del experimento

A continuación se muestran los pasos para crear un experimento de Aprendizaje automático de Azure sencillo e implementarlo como un servicio web. El servicio web toma como entrada una columna de texto arbitrario de entrada y devuelve un conjunto de características representadas como números enteros. Por ejemplo:

Texto | Texto con hash
--- | ---
Este es un buen día | 1 1 2 2 0 2 0 1

En primer lugar, mediante el explorador que prefiera, vaya a: [https://studio.azureml.net/](https://studio.azureml.net/) y escriba sus credenciales para iniciar sesión. Después, cree un nuevo experimento en blanco.

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Cambie su nombre a **SimpleFeatureHashingExperiment**. Expanda **Conjuntos de datos guardados** y arrastre **Book Reviews from Amazon** al experimento.

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda **Transformación de datos** y **Manipulación** y arrastre **Columnas del proyecto** al experimento. Conecte **Book Reviews from Amazon** a **Columnas del proyecto**.

![project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Haga clic en **Columnas del proyecto** y después haga clic en **Iniciar selector de columnas** y seleccione **Col2**. Haga clic en la marca de verificación para aplicar estos cambios.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **Análisis de texto** y arrastre **Hash de características** al experimento. Conecte **Columnas del proyecto** a **Hash de características**.

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Escriba **3** en **Tamaño de bits de hash**. Se crearán 8 (23) columnas.

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

En este punto, quizá quiera hacer clic en **Ejecutar** para probar el experimento.

![run](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###Creación de un servicio web

Ahora va a crear un servicio web. Expanda **Servicio web** y arrastre **Entrada** al experimento. Conecte **Entrada** a **Hash de características**. Arrastre también **Resultado** al experimento. Conecte **Resultado** a **Hash de características**.

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Haga clic en **Publicar servicio web**.

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Haga clic en **Sí** para publicar el experimento.

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###Prueba del servicio web

Un servicio web de AzureML consta de los extremos RRS (servicio de solicitud/respuesta) y BES (servicio de ejecución por lotes). RRS sirve para la ejecución sincrónica. BES sirve para la ejecución por lotes asincrónica. Para probar el servicio web con el origen de Python del ejemplo siguiente, puede que necesite descargar e instalar el SDK de Azure para Python (consulte: [Cómo instalar Python](python-how-to-install.md)).

También necesitará el **área de trabajo**, el **servicio** y la **api\_key** del experimento para el origen del ejemplo siguiente. Puede encontrar el área de trabajo y el servicio haciendo clic en **Solicitud/respuesta** o en **Ejecución por lotes** del experimento en el panel del servicio web.

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Puede encontrar la **api\_key** haciendo clic en el experimento del panel del servicio web.

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####Prueba del extremo RRS

#####Botón Probar

Una manera fácil de probar el extremo RRS es hacer clic en **Probar** en el panel del servicio web.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Escriba **Este es un buen día** para **col2**. Haga clic en la marca de verificación.

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Verá algo parecido a lo siguiente:

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####Código de ejemplo

Otra forma de probar el RRS es desde el código de cliente. Si hace clic en **Solicitud/respuesta** en el panel y se desplaza hasta la parte inferior, verá el código de ejemplo de C#, Python y R. También verá la sintaxis de la solicitud de RRS, incluidos los URI de solicitud, los encabezados y el cuerpo.

Esta guía muestra un ejemplo de Python en funcionamiento. Necesitará modificarlo con el **área de trabajo**, el **servicio** y la **api\_key** del experimento.

	import urllib2
	import json
	workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
	service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
	api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
	data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
	}
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
	headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	body = str.encode(json.dumps(data))
	try:
    	req = urllib2.Request(url, body, headers)
    	response = urllib2.urlopen(req)
    	result = response.read()
    	print "result:" + result
			except urllib2.HTTPError, error:
    	print("The request failed with status code: " + str(error.code))
    	print(error.info())
    	print(json.loads(error.read()))

####Prueba de extremo BES
Haga clic en **Ejecución por lotes** en el panel y desplácese hasta la parte inferior. Verá el código de ejemplo de C#, Python y R. También verá la sintaxis de las solicitudes BES para enviar un trabajo, iniciar un trabajo, obtener el estado o los resultados de un trabajo y eliminar un trabajo.

Esta guía muestra un ejemplo de Python en funcionamiento. Debe modificarlo con el **área de trabajo**, el **servicio** y la **api\_key** del experimento. Además, deberá modificar el **nombre de la cuenta de almacenamiento**, la **clave de la cuenta de almacenamiento** y el **nombre de contenedor de almacenamiento**. Por último, deberá modificar la ubicación del **archivo de entrada** y la ubicación del **archivo de salida**.

	import urllib2
	import json
	import time
	from azure.storage import *
	workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
	service = "<REPLACE WITH YOUR SERVICE ID>"
	api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
	storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
	storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
	storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
	input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
	output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
	input_blob_name = "mydatablob.csv"
	output_blob_name = "myresultsblob.csv"
	def printHttpError(httpError):
	print("The request failed with status code: " + str(httpError.code))
	print(httpError.info())
	print(json.loads(httpError.read()))
	return
	def saveBlobToFile(blobUrl, resultsLabel):
	print("Reading the result from " + blobUrl)
	try:
		response = urllib2.urlopen(blobUrl)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	with open(output_file, "w+") as f:
		f.write(response.read())
	print(resultsLabel + " have been written to the file " + output_file)
	return
	def processResults(result):
	first = True
	results = result["Results"]
	for outputName in results:
		result_blob_location = results[outputName]
		sas_token = result_blob_location["SasBlobToken"]
		base_url = result_blob_location["BaseLocation"]
		relative_url = result_blob_location["RelativeLocation"]
		print("The results for " + outputName + " are available at the following Azure Storage location:")
		print("BaseLocation: " + base_url)
		print("RelativeLocation: " + relative_url)
		print("SasBlobToken: " + sas_token)
		if (first):
			first = False
			url3 = base_url + relative_url + sas_token
			saveBlobToFile(url3, "The results for " + outputName)
	return

	def invokeBatchExecutionService():
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
	blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
	print("Uploading the input to blob storage...")
	data_to_upload = open(input_file, "r").read()
	blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
	print "Uploaded the input to blob storage"
	input_blob_path = "/" + storage_container_name + "/" + input_blob_name
	connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
	payload =  {
		"Input": {
			"ConnectionString": connection_string,
			"RelativeLocation": input_blob_path
		},
		"Outputs": {
			"output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
		},
		"GlobalParameters": {
		}
	}
		body = str.encode(json.dumps(payload))
	headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
	print("Submitting the job...")
	# submit the job
	req = urllib2.Request(url + "?api-version=2.0", body, headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	result = response.read()
	job_id = result[1:-1] # remove the enclosing double-quotes
	print("Job ID: " + job_id)
	# start the job
	print("Starting the job...")
	req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	url2 = url + "/" + job_id + "?api-version=2.0"

	while True:
		print("Checking the job status...")
		# If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
		req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
		try:
			response = urllib2.urlopen(req)
		except urllib2.HTTPError, error:
			printHttpError(error)
			return
		result = json.loads(response.read())
		status = result["StatusCode"]
		print "status:" + status
		if (status == 0 or status == "NotStarted"):
			print("Job " + job_id + " not yet started...")
		elif (status == 1 or status == "Running"):
			print("Job " + job_id + " running...")
		elif (status == 2 or status == "Failed"):
			print("Job " + job_id + " failed!")
			print("Error details: " + result["Details"])
			break
		elif (status == 3 or status == "Cancelled"):
			print("Job " + job_id + " cancelled!")
			break
		elif (status == 4 or status == "Finished"):
			print("Job " + job_id + " finished!")
			processResults(result)
			break
		time.sleep(1) # wait one second
	return
	invokeBatchExecutionService()

<!---HONumber=AcomDC_0128_2016-->