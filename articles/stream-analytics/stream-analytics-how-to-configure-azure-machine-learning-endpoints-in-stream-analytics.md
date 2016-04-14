<properties 
	pageTitle="Configuración de los puntos de conexión de Aprendizaje automático de Azure en Análisis de transmisiones | Microsoft Azure" 
	description="Funciones definidas por el usuario de Aprendizaje automático de Azure en Análisis de transmisiones"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>

# Integración de Aprendizaje automático en Análisis de transmisiones

Análisis de transmisiones proporciona compatibilidad con las funciones definidas por el usuario que llamen a puntos de conexión de Aprendizaje automático de Azure. La compatibilidad con la API de REST para esta característica se detalla en la [biblioteca API de REST de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artículo proporciona información adicional necesaria para una implementación correcta de esta capacidad en Análisis de transmisiones. También se ha publicado un tutorial, que está disponible [aquí](stream-analytics-machine-learning-integration-tutorial.md).

## Información general: terminología de Aprendizaje automático de Azure

Aprendizaje automático de Microsoft Azure proporciona una herramienta colaborativa de arrastrar y colocar, que le permite crear, probar e implementar soluciones de análisis predictivo en sus datos. Esta herramienta de denomina *Estudio de aprendizaje automático de Azure*. El estudio se usará para interactuar con los recursos de Aprendizaje automático, así como compilar, probar e iterar en su diseño. A continuación, se proporcionan estos recursos y sus definiciones.

- **Área de trabajo**: el *área de trabajo* es un contenedor con todos los demás recursos de Aprendizaje automático en un solo lugar para la administración y el control.
- **Experimento**: los científicos de datos crean *experimentos* para utilizar conjuntos de datos y entrenar un modelo de Aprendizaje automático.
- **Punto de conexión**: los *puntos de conexión* son objetos de Aprendizaje automático de Azure que se usan para tomar características como entradas, aplicar un modelo de Aprendizaje automático especificado y devolver la salida con puntuación.
- **Servicio web de puntuación**: un *servicio web de puntuación* es una colección de puntos de conexión, como se mencionó anteriormente.

Cada punto de conexión tiene varias API para la ejecución de lotes y la ejecución sincrónica. Análisis de transmisiones usa la ejecución sincrónica. El servicio específico se denomina un [servicio de solicitud/respuesta](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) en Estudio de aprendizaje automático de Azure.

## Recursos de Aprendizaje automático necesarios para trabajos de Análisis de transmisiones

Para el procesamiento de trabajos de Análisis de transmisiones, para una ejecución correcta son necesarios un punto de conexión de solicitud/respuesta, una [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key) y una definición de Swagger. Análisis de transmisiones tiene un punto de conexión adicional que construye la dirección URL de Swagger, busca en la interfaz y devuelve una definición de función definida por el usuario predeterminada al usuario.

## Configuración de un Análisis de transmisiones y funciones definidas por el usuario de Aprendizaje automático mediante la API de REST

Mediante las API de REST, puede configurar el trabajo para llamar a funciones de Aprendizaje automático de Azure. Los pasos son los siguientes:

1. Creación de un trabajo de Análisis de transmisiones
2. Definición de una entrada
3. Definición de una salida
4. Creación de una función definida por el usuario
5. Creación de una transformación de Análisis de transmisiones que llame a la función definida por el usuario
6. Inicio del trabajo

## Creación de una función definida por el usuario con las propiedades básicas

Por ejemplo, el siguiente código de ejemplo crea una función definida por el usuario escalar denominada *newudf* que enlaza a un punto de conexión de Aprendizaje automático de Azure. Tenga en cuenta que el *punto de conexión* (URI de servicio) se puede encontrar en la página de ayuda de API para el servicio seleccionado, y la *apiKey* puede encontrarse en la página principal de servicios.

PUT: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>

Ejemplo del cuerpo de solicitud:

	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
						"apiKey": "replacekeyhere"
					}
				}
			}
		}
	}

## Llamada al punto de conexión RetrieveDefaultDefinition para la función definida por el usuario predeterminada

Una vez creado el esqueleto de la función definida por el usuario, es necesaria la definición completa de la función definida por el usuario. El punto de conexión RetreiveDefaultDefinition ayuda a obtener la definición predeterminada para una función escalar enlazada a un punto de conexión de Aprendizaje automático de Azure. La siguiente carga requiere obtener la definición de la función definida por el usuario predeterminada para una función escalar enlazada a un punto de conexión de Aprendizaje automático de Azure. No especifica el punto de conexión real, porque ya se ha proporcionado durante la solicitud PUT. Análisis de transmisiones llamará al punto de conexión proporcionado en la solicitud si se proporciona explícitamente. De lo contrario, usará al que se hace referencia originalmente. Aquí, la función definida por el usuario toma un parámetro de una sola cadena (una frase) y devuelve una única salida de tipo "string" que indica la etiqueta "sentiment" para esa frase.

POST: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>

Ejemplo del cuerpo de solicitud:

	{
		"bindingType": "Microsoft.MachineLearning/WebService",
		"bindingRetrievalProperties": {
			"executeEndpoint": null,
			"udfType": "Scalar"
		}
	}

Un ejemplo de salida tendría el aspecto siguiente.


	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"inputs": [{
					"dataType": "nvarchar(max)",
					"isConfigurationParameter": null
				}],
				"output": {
					"dataType": "nvarchar(max)"
				},
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
						"apiKey": null,
						"inputs": {
							"name": "input1",
							"columnNames": [{
								"name": "tweet",
								"dataType": "string",
								"mapTo": 0
							}]
						},
						"outputs": [{
							"name": "Sentiment",
							"dataType": "string"
						}],
						"batchSize": 10
					}
				}
			}
		}
	}

## Revisión de la función definida por el usuario con la respuesta 

Ahora se debe revisar la función definida por el usuario con la respuesta anterior, tal como se muestra a continuación.

PATCH: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>

Cuerpo de la solicitud: salida de RetrieveDefaultDefinition

## Implementación de una transformación de Análisis de transmisiones que llame a la función definida por el usuario

Ahora consulte la función definida por el usuario (aquí llamada scoreTweet) para cada evento de entrada y escriba una respuesta para ese evento en una salida.

	{
		"name": "transformation",
		"properties": {
			"streamingUnits": null,
			"query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
		}
	}

Para obtener más información, consulte:

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->