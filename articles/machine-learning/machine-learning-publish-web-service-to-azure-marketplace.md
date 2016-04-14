<properties 
	pageTitle="Publicación de los servicios web de Aprendizaje automático en Azure Marketplace | Microsoft Azure" 
	description="Publicación de un servicio web de Aprendizaje automático de Azure en Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2016" 
	ms.author="bharaths"/>

# Publicación de los servicios web de Aprendizaje automático de Azure en Azure Marketplace 

Azure Marketplace ofrece la posibilidad de publicar servicios web de Aprendizaje automático de Azure como servicios gratuitos o de pago para su consumo por clientes externos. Este artículo proporciona una introducción a ese proceso, junto con vínculos a directrices para comenzar. Mediante este proceso, puede poner a disposición de otros desarrolladores sus servicios web para que los consuman en sus aplicaciones.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Introducción al proceso de publicación 

A continuación, se muestran los pasos para publicar un servicio web de Aprendizaje automático de Azure en Azure Marketplace:

1. Crear y publicar un servicio de solicitud-respuesta (RRS) de Aprendizaje automático
2. Implementar el servicio en producción y obtener la información de extremo de OData y la clave de API
3. Usar la URL del servicio web publicado para publicar en [Azure Marketplace (DataMarket)](https://publish.windowsazure.com/workspace/). 
4. Una vez enviada la oferta, se revisa y es necesario aprobarla antes de que los clientes puedan comenzar a comprarla. El proceso de publicación puede tardar varios días laborables. 

## Tutorial
###Paso 1: crear y publicar un servicio de solicitud-respuesta (RRS) de Aprendizaje automático###
 Si no lo ha hecho todavía, consulte este [tutorial](machine-learning-walkthrough-5-publish-web-service.md).

###Paso 2: implementar el servicio en producción y obtener la información de extremo de OData y la clave de API###
1. En el [Portal de Azure clásico](http://manage.windowsazure.com), elija la opción **Aprendizaje automático** en la barra de navegación de la izquierda y, a continuación, elija el área de trabajo. 

2. Haga clic en la pestaña **Servicios web** y, a continuación, seleccione el servicio web que desee publicar en Marketplace.

	![Azure Marketplace][workspace]

3. Elija el extremo que desea ubicar en Marketplace para su consumo. Si no ha creado ningún extremo adicional, puede elegir el extremo **predeterminado**.

4. Una vez que haya hecho clic en el extremo, podrá ver la **clave de API**. Necesitará esta información más adelante en el paso 3, por lo que se aconseja que la copie.

	![Azure Marketplace][apikey]

5. Haga clic en el método **Solicitud/respuesta**. En este momento, no se admite la publicación de servicios de ejecución de lotes en Marketplace. Esto le llevará a la página de Ayuda de la API para el método de solicitud/respuesta.

6. Copie la **dirección de extremo de OData**, ya que necesitará esta información más adelante en el paso 3.

	![Azure Marketplace][odata]




Implementación del servicio en la producción.



###Paso 3: usar la URL del servicio web publicado para realizar la publicación en Azure Marketplace (DataMarket)###

1.  Acceda a [Azure Marketplace (DataMarket)](http://datamarket.azure.com/home) 
2.  Haga clic en el vínculo **Publicar** en la parte superior de la página. Esto le llevará a [Portal de publicación de Microsoft Azure](https://publish.windowsazure.com).
3.  Haga clic en la sección **Publicadores** para registrarse como publicador.
4.	Cuando cree una nueva oferta, elija **Servicios de datos** y, a continuación, haga clic en **Crear un nuevo servicio de datos**. 
 
	![Azure Marketplace][image1]

	<br />


5.	En **Planes**, deberá proporcionar información sobre su oferta, junto con un plan de precios. Decida si ofrecerá un servicio gratuito o de pago. Para que le paguen, deberá proporcionar información de pago, por ejemplo, la cuenta bancaria y los datos fiscales.

6.	En **Marketing**, proporcione información acerca de la oferta, como el título y la descripción.

7.	En **Precios**, puede establecer el precio de los planes para países específicos, o dejar que el sistema aplique automáticamente el precio de la oferta.

8. En la pestaña **Servicio de datos**, elija **Servicio web** como **Origen de datos**.

	![Azure Marketplace][image2]

9.	Obtenga la clave de API y la dirección URL del servicio web en el Portal de Azure clásico, tal y como se explica en el paso 2 anterior.

10.	En el cuadro de diálogo de configuración del servicio de datos de Marketplace, pegue la dirección de extremo de OData en el cuadro de texto **Dirección URL del servicio**.

11. En **Autenticación**, elija **Encabezado** como **Esquema de autenticación**.

	- Escriba "Autorización" en **Nombre de encabezado**.
	- En **Valor de encabezado**, escriba "Portador" (sin las comillas), haga clic en la barra **Espacio** y, a continuación, pegue la clave de API.
	- Marque la casilla **Este servicio es OData**.
	- Haga clic en **Prueba de conexión** para probar la conexión.

12.	En **Categorías**, asegúrese de que **Aprendizaje automático** esté seleccionado.

13. Cuando haya terminado de escribir todos los metadatos acerca de la oferta, haga clic en **Publicar**, y, a continuación, haga clic en **Pasar a ensayo**. En este momento, se le notificarán los problemas restantes que deba corregir.

14. Una vez que se haya asegurado de que no quedan problemas pendientes, haga clic en **Solicitar aprobación para pasar a producción**. El proceso de publicación puede tardar varios días laborables.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=AcomDC_0218_2016-->