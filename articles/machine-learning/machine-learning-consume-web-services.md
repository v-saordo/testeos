<properties
	pageTitle="Consumo de un servicio web de Aprendizaje automático de Microsoft Azure"
	description="Una vez implementado un servicio de aprendizaje automático, se puede consumir el servicio web RESTFul facilitado como servicio de respuesta de solicitud o como servicio de ejecución por lotes."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="02/21/2016"
	ms.author="garye" />


# Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático

## Introducción

Cuando se implementa como servicio web, los experimentos de Aprendizaje automático de Azure proporcionan una API de REST que puede ser consumida por una amplia gama de dispositivos y plataformas. Esto es porque la sencilla API de REST acepta y responde con mensajes de formato JSON. El portal de Aprendizaje automático de Azure proporciona código que se puede utilizar para llamar al servicio web en R, C# y Python. Pero estos servicios se pueden llamar mediante cualquier lenguaje de programación y desde cualquier dispositivo que satisfaga tres criterios:

* Disponer de una conexión de red
* Disponer de capacidades SSL para ejecutar solicitudes HTTPS
* Disponer de capacidad para analizar JSON (a mano o mediante bibliotecas de apoyo)

Esto significa que los servicios se pueden consumir desde aplicaciones web, aplicaciones móviles, aplicaciones de escritorio personalizadas e incluso desde dentro de Excel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Un servicio web de Aprendizaje automático de Azure se puede consumir de dos maneras diferentes, como un servicio de solicitud-respuesta o como un servicio de ejecución por lotes. En cada escenario se proporciona la funcionalidad a través del servicio web RESTFul que se facilita para consumirse una vez que se ha implementado el experimento. Implementando un servicio web de aprendizaje automático en Azure con un punto extremo de servicio web Azure, donde el servicio se escala automáticamente según el uso, puede evitar los costos continuados derivados de los recursos de hardware.

> [AZURE.TIP] Para conocer una forma sencilla de crear una aplicación web para obtener acceso al servicio web predictivo, vea [Consumo de un servicio web de Aprendizaje automático de Azure con una plantilla de aplicación web](machine-learning-consume-web-service-with-web-app-template.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

Para obtener información acerca de cómo crear e implementar un servicio web de Aprendizaje automático de Azure, consulte [Implementar un servicio web de Aprendizaje automático de Azure][publish]. Para obtener un tutorial paso a paso acerca de la creación de un experimento de Aprendizaje automático e implementarlo, consulte [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Servicio de solicitud-respuesta (RRS)

Un Servicio de solicitud-respuesta (RRS) es un servicio web altamente escalable y de baja latencia utilizado para proporcionar una interfaz para los modelos sin estado que se han creado e implementado desde un experimento de Estudio de aprendizaje automático de Microsoft Azure. Habilita escenarios donde la aplicación consumidora espera una respuesta en tiempo real.

RRS acepta una sola fila, o varias filas, de parámetros de entrada y puede generar una fila única o varias filas, como salida. Las filas de resultados pueden contener varias columnas.

Un ejemplo de RRS es validar la autenticidad de una aplicación. En este caso, pueden esperarse cientos de millones de instalaciones de una aplicación. Cuando se inicia la aplicación, realiza una llamada al servicio RRS con la entrada correspondiente. A continuación, la aplicación recibe una respuesta de validación desde el servicio que permite o bloquea el funcionamiento de la aplicación.


## Servicio de ejecución de lotes (BES)

Un Servicio de ejecución de lotes (BES) es un servicio que maneja una puntuación de gran volumen asincrónica de un lote de registros de datos. La entrada de BES contiene un lote de registros de una variedad de orígenes, como blobs, tablas de Azure, SQL Azure, HDInsight (resultados de una consulta de Hive, por ejemplo) y orígenes de HTTP. La salida de BES contiene los resultados de la puntuación. Los resultados se transfieren a un archivo del almacenamiento de blobs de Azure y los datos del extremo de almacenamiento se devuelven en la respuesta.

Un BES resultaría útil cuando las respuestas no se necesiten inmediatamente, como para una puntuación programada regularmente de individuos o para dispositivo de Internet de las cosas (IOT).

## Ejemplos
Para mostrar cómo funcionan RRS y BES, usamos un ejemplo de servicio web de Azure. Este servicio se usaría en un escenario IOT (Internet de las cosas). Para facilitarlo, nuestro dispositivo solo envía un valor, `cog_speed`, y recibe una sola respuesta.

Hay cuatro elementos de información que son necesarios para llamar al servicio RRS o BES. Esta información está disponible en las páginas de servicios de [Estudio de aprendizaje automático de Azure](https://studio.azureml.net) una vez que se ha implementado el experimento. Haga clic en la pestaña SERVICIOS WEB situada a la izquierda de la pantalla y verá los servicios implementados. Haga clic en un servicio para encontrar los vínculos y la información siguientes para RRS y BES:

1.	La **clave de API** de servicio, disponible en el Panel de servicios
2.	El **identificador URI de la solicitud** de servicio, disponible en la página de ayuda de la API del servicio seleccionado
3.	Los **encabezados** y el **cuerpo de la solicitud** de la API, disponible en la página de ayuda de la API del servicio seleccionado
4.	Los **encabezados** y el **cuerpo de la respuesta** de la API, disponible en la página de ayuda de la API del servicio seleccionado

En los dos ejemplos siguientes, se utiliza el lenguaje C# para ilustrar el código necesario y la plataforma de destino es un escritorio de Windows 8.

### Ejemplo de RRS
Haga clic en **SOLICITUD/RESPUESTA** en la **PÁGINA DE AYUDA DE LA API** en el Panel de servicio para ver la página de ayuda de la API. En esta página, además del identificador URI, verá ejemplos de código y definiciones de entrada y salida. La entrada de API, para este servicio en particular, se muestra a continuación y es la carga de la llamada a la API.

**Solicitud de ejemplo**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


De forma similar, la respuesta de la API para este servicio también se muestra a continuación.

**Respuesta de ejemplo**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

Hacia la parte inferior de la página de ayuda, encontrará los ejemplos de código. A continuación se muestra el código de ejemplo para la implementación de C#.

**Código de ejemplo en C#**

	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;

	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }

	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }

	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () {
	                        {
	                            "input1",
	                            new StringTable()
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };

	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);

	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");

	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

**Código de ejemplo en Java**

El código de ejemplo siguiente muestra cómo crear una solicitud de API de REST en Java. Supone que las variables (apikey y apiurl) tienen los detalles de la API necesarios y la variable jsonBody tiene un objeto JSON correcto según lo esperado por la API de REST para realizar una predicción correcta. Puede descargar el código completo desde github: [https://github.com/nk773/AzureML\_RRSApp](https://github.com/nk773/AzureML_RRSApp). Este ejemplo de Java requiere una [biblioteca de cliente http de Apache](https://hc.apache.org/downloads.cgi).

	/**
	 * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
 	 */
    	/**
     	  * Call REST API for retrieving prediction from Azure ML 
     	  * @return response from the REST API
     	  */	
    	public static String rrsHttpPost() {
        
        	HttpPost post;
        	HttpClient client;
        	StringEntity entity;
        
        	try {
            		// create HttpPost and HttpClient object
            		post = new HttpPost(apiurl);
            		client = HttpClientBuilder.create().build();
            
            		// setup output message by copying JSON body into 
            		// apache StringEntity object along with content type
            		entity = new StringEntity(jsonBody, HTTP.UTF_8);
            		entity.setContentEncoding(HTTP.UTF_8);
            		entity.setContentType("text/json");

            		// add HTTP headers
            		post.setHeader("Accept", "text/json");
            		post.setHeader("Accept-Charset", "UTF-8");
        
            		// set Authorization header based on the API key
            		post.setHeader("Authorization", ("Bearer "+apikey));
            		post.setEntity(entity);

            		// Call REST API and retrieve response content
            		HttpResponse authResponse = client.execute(post);
            
            		return EntityUtils.toString(authResponse.getEntity());
            
        	}
        	catch (Exception e) {
            
            		return e.toString();
        	}
    
    	}
    
    	
 

### Ejemplo de BES
A diferencia del servicio RRS, el servicio BES es asincrónico. Esto significa que la API de BES simplemente pone en cola un trabajo que se va a ejecutar y el llamador sondea el estado del trabajo para cuándo se ha completado. Estas son las operaciones admitidas actualmente para los trabajos por lotes:

1. Crear (enviar) un trabajo por lotes.
1. Iniciar un trabajo por lotes.
1. Obtener el estado o el resultado de un trabajo por lotes
1. Cancelar un trabajo por lotes en ejecución.

**1. Crear un trabajo de ejecución por lotes**

Al crear un trabajo por lotes para el punto de conexión de servicio de Aprendizaje automático de Azure, se pueden especificar varios parámetros que definan la ejecución de lotes:

* **Input**: representa una referencia del blob al lugar en que se almacena la entrada del trabajo por lotes.
* **GlobalParameters**: representa el conjunto de parámetros globales que se pueden definir para el experimento. Los experimentos de Aprendizaje automático de Azure pueden tener parámetros opcionales y obligatorios que personalicen la ejecución del servicio, y se espera que el llamador proporcione todos los parámetros obligatorios, si procede. Estos parámetros se especifican como una colección de pares clave-valor.
* **Outputs**: si el servicio tiene definidas una o varias salidas, permitimos al llamador redirigir cualquiera de ellas a una ubicación del blob de Azure. Esto permite guardar las salidas del servicio en la ubicación preferida y con un nombre de predicción, ya que, de lo contrario, el nombre de blob de salida se genera aleatoriamente. 

    Tenga en cuenta que el servicio espera que el contenido de la salida, según su tipo, se guarde en cualquiera de los formatos admitidos:
  - salidas de conjuntos de datos: se pueden guardar como **.csv, .tsv o .arff**
  - salidas de modelos entrenados: se pueden guardar como **.ilearner**

  Las invalidaciones de la ubicación de salida se especifican como una colección de *<output name  blob reference>* pares, donde el *nombre de salida* es el nombre definido por el usuario para un nodo de salida específico (que también se muestra en la página de ayuda de la API del servicio) y la *referencia del blob* es una referencia a una ubicación de blobs de Azure a la que se redirigirá la salida.

Todos estos parámetros de creación de trabajos pueden ser opcionales, en función de la naturaleza del servicio. Por ejemplo, los servicios sin nodos de entrada definidos no requieren que se use un parámetro *Input*. Del mismo modo, la característica de invalidación de la ubicación de salida es totalmente opcional. De lo contrario, las salidas se guardarán en la cuenta de almacenamiento predeterminada configurada para el área de trabajo de Aprendizaje automático de Azure. A continuación encontrará una carga de solicitudes de ejemplo, tal como se pasan a la API de REST, para un servicio en el que solo se proporciona la información de entrada:

**Solicitud de ejemplo**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

La respuesta a la API de creación de trabajos por lotes es el identificador de trabajo único que estaba asociado al trabajo. Este identificador es muy importante porque proporciona el único medio para hacer referencia a este trabajo en el sistema para otras operaciones.

**Respuesta de ejemplo**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Iniciar un trabajo de ejecución por lotes**

La creación de un trabajo por lotes lo registra en el sistema y lo coloca en el estado *No iniciado*. Para programar realmente el trabajo para su ejecución, llame a la API de **inicio** que se describe en la página de ayuda de la API del punto de conexión de servicio y especifique el identificador de trabajo que se obtuvo al crear el trabajo.

**3. Obtener el estado de un trabajo de ejecución por lotes**

El estado de un trabajo por lotes asincrónico se puede sondear en cualquier momento pasando el identificador de trabajo a la API GetJobStatus. La respuesta de la API contendrá un indicador del estado actual del trabajo, así como los resultados reales del trabajo por lotes si se ha completado correctamente. En caso de error, en la propiedad *Detalles* se puede encontrar más información sobre las razones reales del error, como se muestra a continuación:

**Carga de respuesta**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

*StatusCode* puede ser cualquiera de los siguientes:

* No iniciado
* Ejecución
* Con error
* Cancelado
* Terminado

La propiedad *Results* solo se rellena si el trabajo se ha completado correctamente (de lo contrario, su valor es **null**). Tras la finalización del trabajo y si el servicio tiene al menos un nodo de salida definido, los resultados se devolverán en forma de colección de pares *[nombre de salida, referencia de blob]*, donde la referencia de blob es una referencia SAS de solo lectura al blob que contiene el resultado real.

**Respuesta de ejemplo**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4. Cancelar un trabajo por lotes en ejecución**

Los trabajos por lotes en ejecución se pueden cancelar en cualquier momento llamando a la API CancelJob designada y pasando el identificador del trabajo. Esto se llevaría a cabo por diversas razones, como que el trabajo está tardando demasiado en completarse.



#### Uso del SDK de BES

El [paquete de NuGet SDK de BES](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) proporciona funciones que simplifican la llamada a BES para su puntuación en el modo por lotes. Para instalar el paquete NuGet, en Visual Studio, en el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** y haga clic en **Consola del Administrador de paquetes**.

Los experimentos de Aprendizaje automático de Azure implementados como servicios web pueden incluir módulos de entrada de servicio web. Esto significa que esperan que la entrada se realice a través de la llamada de servicio web en forma de referencia a una ubicación de blob. También existe la opción de no usar un módulo de entrada del servicio web y usar en su lugar un módulo **lector**. En ese caso, el módulo **lector** normalmente leería de una base de datos SQL mediante una consulta en tiempo de ejecución para obtener los datos. Los parámetros de servicio web pueden utilizarse para apuntar dinámicamente a otros servidores o tablas, etc. El SDK es compatible con ambas plataformas.

El código de ejemplo siguiente muestra cómo se puede enviar y supervisar un trabajo por lotes con un extremo de servicio de Aprendizaje automático de Azure mediante el SDK de BES. En los comentarios encontrará más información sobre la configuración y las llamadas.

#### **Código de ejemplo**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning

	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;

	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;

	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }

	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);

	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results

	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.

	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };

	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };

	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();

	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

#### Código de ejemplo en Java para BES
La API de REST del servicio de ejecución de lotes toma JSON, que consta de una referencia a un csv de ejemplo de entrada y un csv de ejemplo de salida, como se muestra a continuación y crea un trabajo en Aprendizaje automático de Azure para hacer predicciones de lotes. Puede ver el código completo en [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp). Este ejemplo de Java requiere una [biblioteca de cliente http de Apache](https://hc.apache.org/downloads.cgi).


	{ "GlobalParameters": {}, 
    	"Inputs": { "input1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=Q8kkieg==", 
        	"RelativeLocation": "myContainer/sampleinput.csv" } }, 
    	"Outputs": { "output1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
        	"RelativeLocation": "myContainer/sampleoutput.csv" } } 
	} 


#####Creación de un trabajo de BES	
	    
	    /**
	     * Call REST API to create a job to Azure ML 
	     * for batch predictions
	     * @return response from the REST API
	     */	
	    public static String besCreateJob() {
	        
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(apiurl);
	            client = HttpClientBuilder.create().build();
	            
	            // setup output message by copying JSON body into 
	            // apache StringEntity object along with content type
	            entity = new StringEntity(jsonBody, HTTP.UTF_8);
	            entity.setContentEncoding(HTTP.UTF_8);
	            entity.setContentType("text/json");
	
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
				// note a space after the word "Bearer " - don't miss that
	            post.setHeader("Authorization", ("Bearer "+apikey));
	            post.setEntity(entity);
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll(""", "");
	            
	            
	            return jobId;
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    
	    }
	    
#####Inicio de un trabajo de BES creado anteriormente	        
	    /**
	     * Call REST API for starting prediction job previously submitted 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besStartJob(String job){
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
#####Cancelación de un trabajo de BES creado anteriormente
	    
	    /**
	     * Call REST API for canceling the batch job 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besCancelJob(String job) {
	        HttpDelete post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpDelete(startJobUrl+job);
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	         
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
	    
###Otros entornos de programación
También puede generar el código en muchos otros idiomas mediante un documento swagger desde la página de ayuda de la API y siguiendo las instrucciones proporcionadas en el sitio [swagger.io](http://swagger.io/). Vaya a [swagger.io](http://swagger.io/swagger-codegen/) y siga las instrucciones para descargar el código swagger, java y apache mvn. Este es el resumen de instrucciones sobre cómo configurar swagger para otros entornos de programación.

* Asegúrese de instalar Java 7 o una versión posterior
* Instale apache mvn (en Ubuntu, puede usar *apt-get install mvn*)
* Vaya a github para swagger y descargue el proyecto swagger como archivo zip
* Descomprima swagger
* Cree herramientas swagger ejecutando el *paquete mvn* desde el directorio de origen de swagger

Ahora puede usar cualquiera de las herramientas swagger. Aquí encontrará las instrucciones para generar el código de cliente de Java.

* Vaya a la página de ayuda de la API de Aprendizaje automático de Azure (ejemplo [aquí](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs))
* Busque la dirección URL para swagger.json para las API de REST de Aprendizaje automático de Azure (penúltima viñeta en la parte superior de la página de ayuda de la API)
* Haga clic en el vínculo de documento swagger (ejemplo [aquí](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument))
* Utilice el siguiente comando, como se muestra en el [archivo Léame de swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) para generar el código de cliente

**Línea de comandos de ejemplo para generar código de cliente**

	java -jar swagger-codegen-cli.jar generate\
	 -i https://ussouthcentral.services.azureml.net:443/workspaces/\
	fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
	 -l java -o /home/username/sample

* Combine valores en el host de campos, basePath y "/ swagger.json" en el ejemplo de una [página de ayuda de la API](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument) swagger que se muestra a continuación para crear la dirección URL swagger usada en la línea de comandos anterior

**Página de ayuda de la API de ejemplo**


	{
	  "swagger": "2.0",
	  "info": {
	    "version": "2.0",
	    "title": "Sample 5: Binary Classification with Web Service: Adult Dataset [Predictive Exp.]",
	    "description": "No description provided for this web service.",
	    "x-endpoint-name": "default"
	  },
	  "host": "ussouthcentral.services.azureml.net:443",
	  "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
	  "schemes": [
	    "https"
	  ],
	  "consumes": [
	    "application/json"
	  ],
	  "produces": [
	    "application/json"
	  ],
	  "paths": {
	    "/swagger.json": {
	      "get": {
	        "summary": "Get swagger API document for the web service",
	        "operationId": "getSwaggerDocument",
	        

<!---HONumber=AcomDC_0224_2016-->