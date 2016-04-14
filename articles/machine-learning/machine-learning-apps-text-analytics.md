<properties
	pageTitle="API de aprendizaje automático: análisis de texto | Microsoft Azure"
	description="La API de análisis de texto de aprendizaje automático de Microsoft puede usarse para analizar el texto no estructurado para el análisis de opiniones, la extracción de frases clave, la detección de idiomas y la detección de temas."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="onewth"/>


# API de aprendizaje automático: análisis de texto de opinión, extracción de frases clave, detección de idiomas y detección de temas

## Información general

La API de análisis de texto es un conjunto de [servicios web](https://datamarket.azure.com/dataset/amla/text-analytics) de análisis de texto creado con Aprendizaje automático de Azure. La API puede usarse para analizar el texto no estructurado para tareas como el análisis de opiniones, la extracción de frases clave, la detección de idiomas y la detección de temas. No se necesitan datos de formación para usar esta API, simplemente pase los datos del texto. Esta API usa técnicas de procesamiento de lenguaje natural avanzadas para proporcionar las mejores predicciones.

Puede ver el análisis de texto en acción en nuestro [sitio de demostración](https://text-analytics-demo.azurewebsites.net/), donde también encontrará [ejemplos](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sobre cómo implementar el análisis de texto en C# y Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Análisis de opiniones

La API devuelve una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una opinión positiva, y las puntuaciones cercanas a 0 indican una opinión negativa. La puntuación de las opiniones se genera mediante técnicas de clasificación. Las funciones de entrada en el clasificador incluyen n-gramas, funciones generadas a partir de etiquetas de categorías gramaticales e incrustaciones de palabras. Actualmente, el inglés es el único idioma que se admite.
 
## Extracción de la frase clave

La API devuelve una lista de cadenas que representan los puntos clave de la conversación en el texto de entrada. Empleamos las técnicas del kit de herramientas de procesamiento de lenguaje natural sofisticadas de Microsoft Office. Actualmente, el inglés es el único idioma que se admite.

## Detección de idiomas

La API devuelve el idioma detectado y una puntuación numérica comprendida entre 0 y 1. Las puntuaciones cercanas a 1 indican una certeza del 100 % de que el idioma identificado es verdadero. Se admiten un total de 120 idiomas.

## Detección de temas

Se trata de una API recién publicada que devuelve los temas detectados más importantes para obtener los registros de texto enviados. Se identifica un tema con una frase clave, que puede ser una o más palabras relacionadas. Esta API requiere un mínimo de 100 registros de texto que enviar, pero se ha diseñado para detectar temas en cientos o miles de registros. Tenga en cuenta que esta API cobra una transacción por registro de texto enviado. La API se ha diseñado para funcionar bien con texto escrito humano y corto, como revisiones y comentarios del usuario.

---

## Definición de la API

### Encabezados

Asegúrese de incluir los encabezados correctos en la solicitud, que debería ser como sigue:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Puede encontrar la clave de su cuenta en [Microsoft Azure Marketplace ](https://datamarket.azure.com/account/keys).

---

## API de respuesta única

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitud de ejemplo**

En la siguiente llamada, vamos a solicitar el análisis de opinión para la frase "Hello World":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Esto devolverá una respuesta como sigue:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitud de ejemplo**

En la siguiente llamada, vamos a solicitar las frases clave encontradas en el texto "It was a wonderful hotel to stay at, with unique decor and friendly staff":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Esto devolverá una respuesta como sigue:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Solicitud de ejemplo**

En la siguiente llamada GET, se precisa la opinión de las frases clave en el texto *Hello World*.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Esto devolverá una respuesta como sigue:

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Parámetros opcionales**

`NumberOfLanguagesToDetect` es un parámetro opcional. El valor predeterminado es 1.

---

## API de lote

El servicio de análisis de texto permite realizar extracciones de opiniones y frases clave en el modo por lotes. Tenga en cuenta que cada uno de los registros puntuó recuentos como una transacción. A modo de ejemplo, si solicita una opinión para 1000 registros en una sola llamada, se deducirán 1000 transacciones.

Tenga en cuenta que los identificadores especificados en el sistema son los identificadores que devuelve el sistema. El servicio web no comprueba que estos identificadores sean únicos. Es responsabilidad del autor de la llamada hacerlo.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitud de ejemplo**

En la siguiente llamada POST, vamos a solicitar las opiniones sobre las frases "Hello World", "Hello Foo World" y "Hello My World" en el cuerpo de la solicitud:

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Cuerpo de la solicitud:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
	    {"Id":"2","Text":"hello foo world"},
	    {"Id":"3","Text":"hello my world"},
	]}

En la respuesta siguiente, obtendrá la lista de puntuaciones asociadas a sus identificadores de texto:

	{
	  "odata.metadata":"<url>", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitud de ejemplo**

En este ejemplo, vamos a solicitar la lista de opiniones para las frases clave de los siguientes textos:

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

Esta solicitud se realiza como una llamada POST al punto de conexión :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Cuerpo de la solicitud:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

En la respuesta siguiente, obtendrá la lista de las frases clave asociadas a sus identificadores de texto:

	{ "odata.metadata":"<url>",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

En la siguiente llamada POST, vamos a solicitar la detección de idioma para las dos entradas de texto:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Cuerpo de la solicitud:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Esto devuelve la respuesta siguiente, donde se detecta inglés en la primera entrada y francés en la segunda:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## API de detección de temas

Se trata de una API recién publicada que devuelve los temas detectados más importantes para obtener los registros de texto enviados. Se identifica un tema con una frase clave, que puede ser una o más palabras relacionadas. Tenga en cuenta que esta API cobra una transacción por registro de texto enviado.

Esta API requiere un mínimo de 100 registros de texto que enviar, pero se ha diseñado para detectar temas en cientos o miles de registros.


### Temas: Envío de trabajo

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Solicitud de ejemplo**


En la siguiente llamada POST, vamos a solicitar temas para un conjunto de 100 artículos, donde se muestran los primeros y últimos artículos de entrada, y se incluyen dos StopPhrases.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Cuerpo de la solicitud:

	{"Inputs":[
		{"Id":"1","Text":"I loved the food at this restaurant"},
		...,
		{"Id":"100","Text":"I hated the decor"}
	],
	"StopPhrases":[
		"restaurant", “visitor"
	]}

En la respuesta siguiente, obtendrá el JobId para el trabajo enviado:

	{
		"odata.metadata":"<url>",
		"JobId":"<JobId>"
	}

Una lista de palabras únicas o frases con varias palabras que no se deben devolver como temas. Puede usarse para filtrar temas muy genéricos. Por ejemplo, en un conjunto de datos acerca de reseñas de hoteles, "hotel" y "hostal" pueden ser frases de detección razonables.

### Temas: Sondeo de resultados del trabajo

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Solicitud de ejemplo**

Apruebe el JobId devuelto para el paso "Envío de trabajo" para capturar los resultados. Se recomienda que llame a este punto de conexión cada minuto hasta que aparezca Status=’Complete’ en la respuesta. Un trabajo tardará aproximadamente 10 minutos en completarse o más si los trabajos tienen miles de registros.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Mientras se está procesando, la respuesta será de la siguiente forma:

	{
		"odata.metadata":"<url>",
		"Status":"Running",
 		"TopicInfo":[],
		"TopicAssignment":[],
		"Errors":[]
	}


La API devuelve una salida con formato JSON en el formato siguiente:

	{
		"odata.metadata":"<url>",
		"Status":"Finished",
		"TopicInfo":[
		{
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Score":8.0,
			"KeyPhrase":"food"
		},
		...
		{
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Score":6.0,
			"KeyPhrase":"decor"
    		}
  		],
		"TopicAssignment":[
		{
			"Id":"1",
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Distance":0.7809
		},
		...
		{
			"Id":"100",
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Distance":0.8034
		}
		],
		"Errors":[]


Las propiedades de cada parte de la respuesta son las siguientes:

**Propiedades de TopicInfo**

| Clave | Descripción |
|:-----|:----|
| TopicId | Un identificador único para cada tema. |
| Score | Recuento de registros asignados al tema. |
| KeyPhrase | Una palabra o frase de resumen para el tema. Puede ser una palabra o varias. |

**Propiedades de TopicAssignment**

| Clave | Descripción |
|:-----|:----|
| Id | Identificador del registro. Equivale al identificador incluido en la entrada. |
| TopicId | El identificador de tema que se ha asignado al registro. |
| Distancia | Confianza de que el registro pertenece al tema. La distancia más cercana a cero indica mayor confianza. |

<!---HONumber=AcomDC_0224_2016-->