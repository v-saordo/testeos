<properties
	pageTitle="Consumo de un servicio web de Aprendizaje automático con una plantilla de aplicación web | Microsoft Azure"
	description="Use una plantilla de aplicación web en Azure Marketplace para consumir un servicio web predictivo en Aprendizaje automático de Azure."
	keywords="servicio web,operacionalización,API de REST,aprendizaje automático"
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
	ms.author="garye;raymondl"/>

# Consumo de un servicio web de Aprendizaje automático de Azure con una plantilla de aplicación web

Una vez que desarrolla su modelo predictivo y lo implementa como un servicio web de Azure con Estudio de aprendizaje automático o con herramientas como R o Python, puede tener acceso al modelo de operaciones con una API de REST.

Hay varias maneras de usar la API de REST y tener acceso al servicio web. Por ejemplo, puede escribir una aplicación en C#, R o Python con el código de ejemplo que se genera cuando se implementa el servicio web (disponible en la página de ayuda de la API en el panel del servicio web en Estudio de aprendizaje automático). O bien, puede usar el libro de Microsoft Excel de ejemplo creado automáticamente (también disponible en el panel del servicio web en Studio).

Pero la manera más rápida y fácil para tener acceso al servicio web es a través de las plantillas de aplicación web disponibles en [Marketplace de aplicaciones web de Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Plantillas de aplicación web de Aprendizaje automático de Azure

Las plantillas de aplicación web disponibles en Azure Marketplace pueden crear una aplicación web personalizada que conoce los datos de entrada del servicio web y los resultados esperados. Todo lo que necesita hacer es conceder el acceso de la aplicación web al servicio web y los datos, y la plantilla se encarga del resto.

Existen dos plantillas:

- [Plantilla de aplicación web Request-Response Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Plantilla de aplicación web Batch Execution Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada plantilla crea una aplicación de ASP.NET de ejemplo mediante el URI de la API y la clave para el servicio web y la implementa como un sitio web en Azure. La plantilla Request-Response Service (RRS) crea una aplicación web que permite enviar una sola fila de datos al servicio web para obtener un resultado único. La plantilla Batch Execution Service (BES) crea una aplicación web que permite enviar muchas filas de datos para obtener varios resultados.

No es necesaria ninguna codificación para usar estas plantillas. Simplemente, proporcione el URI de la API y la clave y la plantilla creará la aplicación.

## Uso de la plantilla Request-Response Service (RRS)

Una vez que haya implementado el servicio web, puede seguir estos pasos para usar la plantilla de aplicación web RRS, como se muestra en el diagrama siguiente.

![Proceso para usar la plantilla web RRS][image1]

1. En Estudio de aprendizaje automático, abra la pestaña **Servicios web** y, luego, abra el servicio web al que desea tener acceso. Copie la clave bajo **Clave de API** y guárdela.

	![Clave de API][image3]

2. Abra la página de ayuda de la API **REQUEST/RESPONSE**. En la parte superior de la página de ayuda, en **Solicitud**, copie el valor de **URI de la solicitud** y guárdelo. Este valor tendrá este aspecto:

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![URI de solicitud][image4]

3. Vaya al [Portal de Azure](https://portal.azure.com), **Inicio de sesión**, haga clic en **Nuevo**, busque y seleccione **Aplicación web Request-Response Service de Aprendizaje automático de Azure** y luego haga clic en **Crear**.

	- Asigne un nombre único a la aplicación web. La dirección URL de la aplicación web será este nombre seguido de `.azurewebsites.net.`; por ejemplo, `http://carprediction.azurewebsites.net.`

	- Seleccione los servicios y la suscripción de Azure en los que se ejecuta el servicio web.

	- Haga clic en **Crear**.

	![Crear una aplicación web][image5]

4. Cuando Azure termine de implementar la aplicación web, haga clic en la **dirección URL** en la página de configuración de la aplicación web en Azure o escriba la dirección URL en un explorador web. Por ejemplo, `http://carprediction.azurewebsites.net.`

5. Cuando se ejecute la aplicación web por primera vez, le pedirá la **dirección URL del mensaje de API** y **clave de API**. Escriba los valores que guardó anteriormente:
	- **URI de solicitud** de la página de ayuda de la API para **URL del mensaje de API**
	- **Clave de API** desde el panel del servicio web para obtener la **clave de API**.

	Haga clic en **Enviar**.

	![Escriba el URI del mensaje y la clave de API][image6]

6. La aplicación web muestra su página **Configuración de la aplicación web** con la configuración actual del servicio web. Aquí puede realizar cambios en la configuración usada por la aplicación web.

	> [AZURE.NOTE] El cambio de estas opciones solo las cambia para esta aplicación web. No cambia la configuración predeterminada del servicio web. Por ejemplo, si cambia la **Descripción** aquí no cambia la descripción mostrada en el panel del servicio web en Estudio de aprendizaje automático.

	Cuando termine, haga clic en **Guardar cambios** y, luego, haga clic en **Ir a la página principal**.

7. En la página principal, puede especificar valores para enviarlos a su servicio web; haga clic en **Enviar** y se devolverá el resultado.

Si desea volver a la página **Configuración**, vaya a la página `setting.aspx` de la aplicación web. Por ejemplo: `http://carprediction.azurewebsites.net/setting.aspx.` le pedirá que vuelva a escribir la clave de API, lo que es necesario para tener acceso a la página y actualizar la configuración.

Puede detener, reiniciar o eliminar la aplicación web en el Portal de Azure como cualquier otra aplicación web. Mientras se esté ejecutando, puede ir a la dirección web de inicio y escribir los nuevos valores.

## Uso de la plantilla Batch Execution Service (BES)

Puede usar la plantilla de aplicación web BES de la misma manera que la plantilla RRS, excepto que la aplicación web que se crea permite enviar varias filas de datos y recibir varios resultados.

Los resultados de un servicio web de ejecución de lotes se almacenan en un contenedor de almacenamiento de Azure; los valores de entrada pueden proceder de un archivo local o del almacenamiento de Azure. Por lo tanto, necesitará un contenedor de almacenamiento de Azure para guardar los resultados devueltos por la aplicación web, y deberá preparar los datos de entrada.

![Proceso para usar la plantilla web BES][image2]

1. Siga el mismo procedimiento para crear la aplicación web BES que para RRS, excepto:
	- Obtenga el **URI de solicitud** desde la página de ayuda de la API **ejecución por lotes** para el servicio web.
	- Vaya a [Plantilla de aplicación web Batch Execution Service de Aprendizaje automático de Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir la plantilla BES en Azure Marketplace y haga clic en **Crear aplicación web**.

2. Para especificar dónde desea que se almacenen los resultados, escriba la información del contenedor de destino en la página principal de la aplicación web. Especifique también dónde puede obtener los valores de entrada la aplicación web, en un archivo local o en un contenedor de almacenamiento de Azure. Haga clic en **Enviar**.

	![Información de almacenamiento][image7]

La aplicación web mostrará una página con el estado del trabajo. Cuando se haya completado el trabajo, se le proporcionará la ubicación de los resultados en el almacenamiento de blobs de Azure. También tiene la opción de descargar los resultados en un archivo local.

## Para obtener más información

Para obtener más información sobre...

- la creación de un experimento de aprendizaje automático con Estudio de aprendizaje automático, consulte [Creación del primer experimento en Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md).

- cómo implementar el experimento de aprendizaje automático como un servicio web, consulte [Implementación de un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)

- otras formas de tener acceso al servicio web, consulte [Consumo de servicios web de Aprendizaje automático de Azure](machine-learning-consume-web-services.md).


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_0204_2016-->