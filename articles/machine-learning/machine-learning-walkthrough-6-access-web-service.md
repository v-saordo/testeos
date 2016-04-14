<properties
	pageTitle="Paso 6: Acceso al servicio web de Aprendizaje automático | Microsoft Azure"
	description="Paso 6 del tutorial Desarrollo de una solución predictiva: acceso a un servicio web activo de Estudio de aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye"/>


# Paso 6 del tutorial: Acceso al servicio web de Aprendizaje automático de Azure

Este es el último paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Acceso al servicio web**

----------

En el paso anterior de este tutorial hemos implementado un servicio web que usa nuestro modelo de predicción del riesgo de crédito. Ahora es necesario que los usuarios puedan enviar datos al servicio y recibir resultados.

El servicio web es un servicio web de Azure que puede recibir y devolver datos con las API de REST de una de estas dos maneras:

-	**Solicitud/respuesta**: el usuario envía una o varias filas de datos de crédito al servicio mediante un protocolo HTTP, y el servicio responde con uno o más conjuntos de resultados.
-	**Ejecución de lotes**: el usuario almacena una o varias filas de datos de crédito en un blob de Azure y luego envía la ubicación del blob al servicio. El servicio puntúa todas las filas de datos en el blob de entrada, almacena los resultados en otro blog y devuelve la dirección URL del contenedor.  

La manera más rápida y fácil de acceder al servicio web es a través de las plantillas de aplicación web disponibles en [Marketplace de aplicaciones web de Azure](https://azure.microsoft.com/marketplace/web-applications/all/). Estas plantillas de aplicación web pueden compilar una aplicación web personalizada que reconoce los datos de entrada del servicio web y lo que devolverá. Todo lo que necesita hacer es conceder acceso al servicio web y a los datos, y la plantilla se encarga del resto.

Para obtener más información sobre el uso de plantillas de aplicación web, vea [Consumo de un servicio web de Aprendizaje automático de Azure con una plantilla de aplicación web](machine-learning-consume-web-service-with-web-app-template.md).

También puede desarrollar una aplicación personalizada para acceder al servicio web con código de inicio proporcionado en los lenguajes de programación R, C# y Python. Puede encontrar todos los detalles en [Cómo consumir un servicio web de Aprendizaje automático de Azure publicado en un experimento de Aprendizaje automático](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0211_2016-->