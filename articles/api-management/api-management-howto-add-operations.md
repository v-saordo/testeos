<properties 
	pageTitle="Incorporación de operaciones a una API en Administración de API de Azure" 
	description="Obtenga información acerca de cómo agregar operaciones a una API en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Incorporación de operaciones a una API en Administración de API de Azure

Es necesario agregar operaciones para poder utilizar una API en Administración de API. En esta guía se muestra cómo agregar y configurar diferentes tipos de operaciones a una API en Administración de API.

## <a name="add-operation"> </a>Agregar una operación

Las operaciones se agregan y se configuran para una API en el portal del publicador. Para llegar al portal del publicador, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API.

![Portal del publicador][api-management-management-console]

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

Seleccione las API que desee en el portal del publicador y luego seleccione la pestaña **Operaciones**.

![Operaciones][api-management-operations]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

![Add operation][api-management-add-operation]

Especifique el **Verbo HTTP** seleccionándolo en la lista desplegable.

![HTTP method][api-management-http-method]

Defina la plantilla de URL escribiendo un fragmento de la dirección URL que conste de uno o más segmentos de ruta de la URL y de cero o más parámetros de la cadena de consulta. La plantilla de URL, que se anexa a la dirección URL base de la API, identifica una sola operación HTTP. Puede contener uno o más elementos de variable con nombre que se identifican mediante llaves. Estos elementos de variable se denominan parámetros de plantilla y son valores asignados automáticamente que se extraen de la dirección URL de la solicitud al procesar la solicitud en la plataforma Administración de API.

![URL template][api-management-url-template]

Si lo desea, especifique la **plantilla de la URL de reescritura**. Esto permite usar la plantilla estándar de URL para procesar solicitudes entrantes en el front-end, al tiempo que se llama al back-end mediante una URL convertida en función de la plantilla de reescritura. Deben usarse los parámetros de plantilla de la plantilla de URL en la plantilla de reescritura. En el siguiente ejemplo se muestra cómo se puede incorporar tipo de contenido codificado en forma de segmento de ruta al servicio web del ejemplo anterior como parámetro de consulta de la API publicada mediante la plataforma Administración de API con los modelos de URL.

![URL template rewrite][api-management-url-template-rewrite]

Los usuarios que llamen a la operación usarán el formato `/customers?customerid=ALFKI`, que se asignará a `/Customers('ALFKI')` al invocar al servicio back-end.


**Nombre para mostrar** y **Descripción** ofrecen una descripción de la operación y se usan para proporcionar documentación a los desarrolladores que usen esta API en el portal para desarrolladores.

![Descripción][api-management-description]

La descripción de la operación se puede especificar como texto sin formato o HTML en el cuadro de texto **Descripción**.

## <a name="operation-caching"> </a>Almacenamiento en caché de operaciones

El almacenamiento en caché de respuestas reduce la latencia que perciben los consumidores de la API, rebaja el consumo de ancho de banda y disminuye la carga en el servicio web HTTP que implementa la API.

Para habilitar fácil y rápidamente el almacenamiento en caché de la operación, seleccione la pestaña **Caching** y active la casilla **Habilitar**.

![Almacenamiento en caché][api-management-caching-tab]

**Duración** especifica el período de tiempo durante el que la respuesta de la operación permanece en la caché. El valor predeterminado es 3.600 segundos o 1 hora.

Se usan claves de caché para diferenciar entre respuestas de forma que la respuesta correspondiente a cada clave de caché distinta obtenga su propio valor almacenado en caché por separado. Opcionalmente, escriba los parámetros específicos de la cadena de consulta o los encabezados HTTP que se usarán para calcular los valores de clave de caché en los cuadros de texto **Variar por parámetros de cadena de consulta** y **Variar por encabezados**, respectivamente. Cuando no se especifica ninguno, se usan la dirección URL de la solicitud completa y los siguientes valores de encabezado HTTP en la generación de claves de caché: **Accept** y **Accept-Charset**.

>Para obtener más información sobre el almacenamiento en caché y las directivas de almacenamiento en caché, consulte [Almacenamiento en caché de resultados de operaciones en Administración de API de Azure][].


## <a name="request-parameters"> </a>Parámetros de solicitud

Los parámetros de la operación se administran en la pestaña Parámetros. Los parámetros especificados en **Modelo de URL** en la pestaña **Firma** se agregan automáticamente y solo pueden cambiarse modificando la plantilla de URL. Se pueden introducir manualmente parámetros adicionales.

Para agregar un nuevo parámetro de consulta, haga clic en **Agregar parámetro de consulta** y especifique la siguiente información:

-	**Nombre**: nombre del parámetro.
-	**Descripción**: breve descripción del parámetro (opcional).
-	**Tipo**: tipo de parámetro, seleccionado en la lista desplegable.
-	**Valores**: valores que se pueden asignar a este parámetro. Uno de los valores se puede marcar como predeterminado (opcional).
-	**Obligatorio**: convierte el parámetro en obligatorio al activar la casilla. 

![Request parameters][api-management-request-parameters]

## <a name="request-body"> </a>Cuerpo de la solicitud

Si la operación lo permite (por ejemplo, PUT, POST) y requiere un cuerpo, puede proporcionar un ejemplo del mismo en todos los formatos de representación compatibles (por ejemplo, json, XML).

>El cuerpo de la solicitud solo se usa a efectos de documentación y no se valida.

Para especificar un cuerpo de la solicitud, cambie a la pestaña **Cuerpo**.

Haga clic en **Agregar representación**, comience a escribir el nombre del tipo de contenido que desee (por ejemplo, aplicación/json), selecciónelo en la lista desplegable y, en el cuadro de texto, pegue el ejemplo de cuerpo de la solicitud que desee en el formato seleccionado.

![Request body][api-management-request-body]

Además de las representaciones, también puede especificar una descripción opcional de texto en el cuadro de texto **Descripción**.

## <a name="responses"> </a>Respuestas

Es recomendable proporcionar ejemplos de respuestas para todos los códigos de estado que puede producir la operación. Cada código de estado puede tener más de un ejemplo de cuerpo de respuesta, uno para cada tipo de contenido admitido.

Para agregar una respuesta, haga clic en **Agregar** y comience a escribir el código de estado que desee. En este ejemplo, el código de estado es **200 OK**. Cuando el código aparezca en la lista desplegable, selecciónelo; el código de respuesta se creará y se agregará a la operación.

![Response code][api-management-response-code]

Haga clic en **Agregar representación**, comience a escribir el nombre del tipo de contenido que desee (por ejemplo, aplicación/json) y selecciónelo en la lista desplegable.

![Body content type][api-management-response-body-content-type]

Pegue el ejemplo de cuerpo de la respuesta en el formato seleccionado en el cuadro de texto.

![Response body][api-management-response-body]

Si lo desea, agregue una descripción opcional en el cuadro de texto **Descripción**.

Una vez configurada la operación, haga clic en **Guardar**.


## <a name="next-steps"> </a>Pasos siguientes

Una vez agregadas las operaciones a una API, el paso siguiente es asociar la API al producto y publicarlo para que los desarrolladores pueden llamar a las operaciones.

-	[Creación y publicación de un producto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Creación y publicación de un producto]: api-management-howto-add-products.md
[Almacenamiento en caché de resultados de operaciones en Administración de API de Azure]: api-management-howto-cache.md

<!---HONumber=AcomDC_1210_2015-->