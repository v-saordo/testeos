<properties 
	pageTitle="Registro para los servicios web de Aprendizaje automático | Microsoft Azure" 
	description="Aprenda cómo habilitar el registro para los servicios web de Aprendizaje automático. El registro proporciona información adicional para ayudar a solucionar las API." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="02/04/2016"
	ms.author="raymondl;garye"/>

#Habilitar el registro para los servicios web de Aprendizaje automático  

Este documento proporciona información sobre la capacidad de registro de los servicios web de Aprendizaje automático de Azure. Al habilitar el registro en los servicios web, se proporciona información adicional para ayudar a solucionar las API para ir más allá de solo el número de error y el mensaje.

-	Cómo habilitar el registro en los servicios web:   
	-	Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/)
	-	Haga clic en Aprendizaje automático, luego, en el área de trabajo y, finalmente, en la opción de menú Servicio web.
	-	En la lista de servicios web, haga clic en el nombre del servicio web.
	-	En la lista de extremos, haga clic en el nombre del extremo.
	-	Haga clic en la opción de menú Configurar.
	-	Establezca el nivel de seguimiento de diagnóstico en Error o Todos y, luego, haga clic en Guardar en la barra de menús de la parte inferior.
-	Efecto de habilitar el registro:  
	-	Cuando el registro está habilitado, todos los diagnósticos y errores del extremo predeterminado se registran en la cuenta de almacenamiento de Azure vinculada al área de trabajo del usuario. Puede ver esta cuenta de almacenamiento en la vista de panel del Portal de Azure clásico (parte inferior de la sección Vista rápida) de su área de trabajo.  
-	Cómo ver los registros:  
	-	Los registros pueden verse mediante cualquiera de las diversas herramientas disponibles para 'explorar' una cuenta de almacenamiento de Azure. Lo más sencillo puede ser simplemente ir a la cuenta de almacenamiento en el Portal de Azure clásico y hacer clic en la pestaña CONTENEDORES. A continuación, verá un contenedor denominado **ml-diagnostics**. Este contenedor contiene toda la información de diagnóstico de todos los extremos de servicio web para todas las áreas de trabajo asociadas a esta cuenta de almacenamiento.  
-	Cuáles son los detalles de blob de registro:  
	-	Cada blob del contenedor contiene la información de diagnóstico para exactamente uno de los siguientes elementos:
		-	Una ejecución del método Batch-Execution  
		-	Una ejecución del método Request-Response  
		-	Inicialización de un contenedor Request-Response  
	-	El nombre de cada blob tiene un prefijo de la siguiente forma: {id. de área de trabajo}-{id. de servicio web}-{id. de extremo}/{tipo de registro}.  
-	El tipo de registro toma uno de los valores siguientes:  
	- lote  
	- puntuación/solicitudes  
	- puntuación/inic  

 

<!---HONumber=AcomDC_0211_2016-->