<properties
	pageTitle="Volver a entrenar modelos de aprendizaje automático mediante programación | Microsoft Azure"
	description="Obtenga información acerca de cómo volver a entrenar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en el Aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="raymondl;garye"/>


#Volver a entrenar modelos de aprendizaje automático mediante programación  

Como parte del proceso de operacionalización de modelos de aprendizaje automático de Azure, un modelo se debe entrenar y guardar y, después, usarse para crear un servicio web predictivo. A continuación, el servicio web se puede consumir en sitios web, paneles y aplicaciones móviles.

Con frecuencia, deberá volver a entrenar el modelo creado en el primer paso con nuevos datos. Anteriormente, esto solo era posible a través de la interfaz de usuario de Aprendizaje automático de Azure, pero con la introducción de la característica de API de reentrenamiento mediante programación, ahora puede volver a entrenar el modelo y actualizar el servicio web, con el modelo recién entrenado, mediante programación con las API de reentrenamiento.

Este documento describe el proceso anterior y muestra cómo usar las API de reciclaje.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Por qué volver a entrenar: definición del problema  
Como parte del proceso de entrenamiento de aprendizaje automático, un modelo se entrena usando un conjunto de datos. Los modelos debe volver a entrenarse en escenarios en los que los nuevos datos dejan de estar disponibles, o cuando el consumidor de la API tenga sus propios datos para entrenar el modelo, o si los datos deben filtrarse y el modelo debe entrenarse con el subconjunto de datos, etc.

En estos casos, una API de programación proporciona una manera cómoda de permitirle a usted o al consumidor de sus API crear un cliente que pueda, en un momento determinado o de forma habitual, volver a entrenar un modelo usando sus propios datos. A continuación, puede evaluar los resultados del reentrenamiento y actualizar la API del servicio web para utilizar el modelo nuevamente entrenado.

##Cómo volver a entrenar: el proceso completo  
Para empezar, el proceso implica a los siguientes componentes: un experimento de entrenamiento y un experimento predictivo publicados como un servicio web. Para habilitar el reentrenamiento de un modelo entrenado, el experimento de entrenamiento también debe publicarse como servicio web con el resultado de un modelo entrenado. Esto permite a la API obtener acceso al modelo para el nuevo entrenamiento. El proceso de configuración de reentrenamiento implica los pasos siguientes:

![][1]

Diagrama 1: Descripción del proceso de reentrenamiento

1. *Crear un entrenamiento de entrenamiento* Para este ejemplo, usaremos el experimento "Ejemplo 5 (entrenamiento, prueba, evaluación para la clasificación binaria: conjunto de datos de adultos)" de los experimentos de ejemplo de aprendizaje automático de Azure. Como verá a continuación, he quitado algunos módulos para simplificar el ejemplo. También he denominado el experimento "Modelo de censo".

 	![][2]

	Con estas piezas en su lugar, podemos ahora hacer clic en Ejecutar en la parte inferior de la pantalla para ejecutar este experimento.  
2. *Crear un experimento de puntuación y publicarlo como servicio web*  

	![][3]

	Una vez completado el experimento, hacemos clic en Crear experimento predictivo. Esto crea un experimento predictivo, guarda el modelo como un modelo entrenado y agrega los módulos de entrada y salida de servicio web como se muestra a continuación. A continuación, hacemos clic en Ejecutar.

	Una vez ejecutado el experimento, al hacer clic en "Publicar servicio web", se publicará el experimento predictivo como un servicio web y se creará un punto de conexión predeterminado. El modelo de aprendizaje en este servicio web se puede actualizar, como se muestra a continuación. Los detalles de este extremo se mostrarán en la pantalla.  
3. *Publicar el experimento de entrenamiento como un servicio web* Para volver a entrenar el modelo entrenado, necesitamos publicar el experimento de formación que hemos creado en el paso 1 anterior como un servicio web. Este servicio web necesitará un módulo de salida del servicio web conectado al módulo [Entrenar modelo][train-model] para poder generar nuevos modelos entrenados. Haga clic en el icono Experimentos en el panel izquierdo, y luego haga clic en el experimento denominado Modelo de censo para volver al experimento de entrenamiento.  

	Ahora, agregamos una entrada de servicio web y dos módulos de salida de servicio web al flujo de trabajo. La salida del servicio web para Entrenar modelo nos ofrecerá el nuevo modelo entrenado. La salida vinculada a Evaluar modelo devolverá el resultado de la evaluación del modelo de dicho módulo.

	Ahora podemos hacer clic en Ejecutar. Una vez que el experimento ha finalizado su ejecución, el flujo de trabajo resultante debe ser el siguiente:

	![][4]

	Ahora hacemos clic en el botón Implementar servicio web y luego en Sí. Esto implementará el experimento de entrenamiento como un servicio web que genera un modelo entrenado y resultados de evaluación del modelo. Aparecerá el panel del servicio web con la clave de API y la página de Ayuda de API para la ejecución por lotes. Tenga en cuenta que solo se puede usar el método de ejecución por lotes para crear modelos entrenados.  
4. *Agregar un nuevo punto de conexión* El servicio web predictivo publicado en el paso 2 anterior se creó con un punto de conexión predeterminado. Los extremos predeterminados se mantienen sincronizados con el experimento de formación y puntuación original y, por tanto, el modelo entrenado de un extremo predeterminado no se puede reemplazar. Para crear un punto de conexión actualizable, visite el Portal de Azure clásico y haga clic en Agregar punto de conexión (más detalles [aquí](machine-learning-create-endpoint.md)).

5. *Volver a entrenar el modelo con nuevos datos y BES* Para llamar a las API de reentrenamiento, crearemos una nueva aplicación de consola C# en Visual Studio (Nuevo -> Proyecto -> Windows Desktop -> Aplicación de consola).

	A continuación, copiamos el código C# de ejemplo de la página de Ayuda de API del servicio web de entrenamiento para la ejecución por lotes (que se creó en el paso 3 anterior) y lo pegamos en el archivo Program.cs, asegurándonos de que el espacio de nombres permanece intacto.

	Tenga en cuenta que el código de ejemplo tiene comentarios que indican las partes del código que necesitan actualizaciones. Además, al especificar la ubicación "output1" en la carga de solicitud, la extensión de archivo de "RelativeLocation" se debe cambiar a ".ilearner" como en:

	```c#
	Outputs = new Dictionary<string, AzureBlobDataReference>()
	{
		{
			"output1",
			new AzureBlobDataReference()
			{
				ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
				RelativeLocation = "mycontainer/output1results.ilearner"
			}
		},
	},
	```
	1. Proporcionar información de almacenamiento de Azure El código de ejemplo para BES cargará un archivo desde una unidad local (por ejemplo, “C:\\temp\\CensusIpnput.csv”) para que el Almacenamiento de Azure lo procese y escriba los resultados de nuevo en Almacenamiento de Azure.  

		Para ello, deberá recuperar el nombre, la clave y la información de contenedor de su cuenta de Almacenamiento desde el Portal de Azure clásico para, después, actualizar el código aquí. También deberá asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código.

		Hemos configurado este experimento de entrenamiento con dos salidas, por lo que los resultados incluirán información de ubicación de almacenamiento para ambos, tal como se muestra a continuación. "output1" es la salida del modelo entrenado, y "output2" es la salida del modelo evaluado. Tenga en cuenta también que la extensión de archivo de la salida para el modelo entrenado (Output1) es ".ileaner", no ".csv".

		![][6]

6. *Evaluar los resultados de reentrenamiento* Mediante la combinación de BaseLocation, RelativeLocation y SasBlobToken de los resultados de salida anteriores para "output2", podemos ver los resultados de rendimiento del modelo reentrenado copiado y pegando la dirección URL completa en la barra de direcciones del explorador.

	Esto nos indicará si el modelo recientemente entrenado funciona lo suficientemente bien como para reemplazar el existente.

7. *Actualizar modelo entrenado del punto de conexión agregado* Para completar el proceso, es preciso actualizar el modelo entrenado del punto de conexión predictivo creado en el paso 4 anterior.

	(Si ha agregado el nuevo punto de conexión mediante el Portal de Azure, puede hacer clic en el nombre del nuevo punto de conexión y luego en el vínculo UpdateResource para obtener la dirección URL que necesitará para actualizar el modelo del punto de conexión).

	La salida de BES anterior muestra la información del resultado de reentrenamiento de "output1", que contiene la información de ubicación del modelo reentrenado. Ahora necesitamos tomar este modelo entrenado y actualizar el extremo de puntuación (creado en el paso 4). El código de ejemplo es el siguiente:

	```C#
	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}
	```

	"apiKey" y "endpointUrl" para esta llamada están visibles en el panel del extremo. El parámetro "Name" de los recursos debe coincidir con el nombre del modelo entrenado guardado en el experimento predictivo.

	Con esta llamada realizada correctamente, se iniciará el nuevo extremo mediante un modelo reentrenado aproximadamente en 15 segundos.

##Resumen  
Al usar las API de reentrenamiento, podemos actualizar el modelo entrenado de un servicio web predictivo, habilitando escenarios como el reentrenamiento periódico de modelos con nuevos datos o la distribución de modelos a clientes con el objetivo de permitirles volver a entrenar el modelo con sus propios datos.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0224_2016-->