<properties
	pageTitle="Incorporación del almacenamiento en caché para mejorar el rendimiento en Administración de API de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo mejorar la latencia, el consumo de ancho de banda y la carga de servicios web para las llamadas de servicio de Administración de API."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Incorporación del almacenamiento en caché para mejorar el rendimiento en Administración de API de Azure

En Administración de API, las operaciones se pueden configurar para el almacenamiento en caché de respuestas. El almacenamiento en caché de respuestas puede reducir significativamente la latencia de la API, el consumo de ancho de banda y la carga del servicio web en cuanto a datos que no cambian con frecuencia.

En esta guía se muestra cómo agregar el almacenamiento en caché de respuestas de la API y cómo configurar directivas para las operaciones de la API Eco de ejemplo. Después, puede llamar a la operación desde el portal para desarrolladores para comprobar el almacenamiento en caché en acción.

>[AZURE.NOTE] Para obtener información sobre el almacenamiento en caché de los elementos por parte de la clave mediante expresiones de directiva, consulte [Custom caching in Azure API Management](api-management-sample-cache-by-key.md).

## Requisitos previos

Antes de seguir los pasos de esta guía, debe tener una instancia del servicio Administración de API con una API y un producto configurados. Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

## <a name="configure-caching"> </a>Configuración de una operación para almacenamiento en caché

En este paso, se revisará la configuración del almacenamiento en caché de la operación **Recurso GET (en caché)** de la API Eco de ejemplo.

>[AZURE.NOTE] Cada instancia del servicio Administración de API viene previamente configurada con una API Eco que se puede usar para experimentar con Administración de API y aprender a usarlo. Para obtener más información, consulte [Introducción a Administración de API de Azure][].

Para comenzar, haga clic en **Administrar** en el Portal de Azure clásico del servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API.

![Portal del publicador][api-management-management-console]

Haga clic en **API** en el menú **Administración de API** de la izquierda y, a continuación, en **API Eco**.

![API Eco][api-management-echo-api]

Seleccione la pestaña **Operaciones** y haga clic en la operación **Recurso GET (en caché)** en la lista **Operaciones**.

![Echo API operations][api-management-echo-api-operations]

Haga clic en la pestaña **Almacenamiento en caché** para ver la configuración del almacenamiento en caché de esta operación.

![Caching tab][api-management-caching-tab]

Para habilitar el almacenamiento en caché en una operación, active la casilla **Habilitar**. En este ejemplo, el almacenamiento en caché está habilitado.

Todas las respuestas de la operación se codifican según los valores de los campos **Variar por parámetros de cadena de consulta** y **Variar por encabezados**. Si desea almacenar en caché varias respuestas basadas en parámetros de la cadena de consulta o encabezados, puede configurarlas en estos dos campos.

**Duración** especifica el intervalo de expiración de las respuestas almacenadas en caché. En este ejemplo, el intervalo es **3600** segundos, lo que equivale a una hora.

Con la configuración de almacenamiento en caché de este ejemplo, la primera solicitud de la operación **Recurso GET (en caché)** devolverá una respuesta del servicio back-end. Esta respuesta se almacenará en caché, con clave especificada mediante encabezados y parámetros de la cadena de consulta. Las siguientes llamadas a la operación, con parámetros coincidentes, devolverán la respuesta almacenada en caché hasta que el intervalo de duración en la caché haya expirado.

## <a name="caching-policies"> </a>Revisión de las directivas de almacenamiento en caché

En este paso, se revisa la configuración del almacenamiento en caché de la operación **Recurso GET (en caché)** de la API Eco de ejemplo.

Cuando se haya definido la configuración de almacenamiento en caché para una operación en la pestaña **Caching**, se agregarán las directivas de almacenamiento en caché para la operación. Estas directivas se pueden ver y editar en el editor de directivas.

Haga clic en **Directivas** en el menú **Administración de API** de la izquierda y seleccione **API eco/Recurso GET (en caché)** en la lista desplegable **Operación**.

![Policy scope operation][api-management-operation-dropdown]

Se muestran las directivas para esta operación en el editor de directivas.

![API Management policy editor][api-management-policy-editor]

La definición de directiva para esta operación incluye las directivas que definen la configuración de almacenamiento en caché que se revisaron con la pestaña **Caching** en el paso anterior.

	<policies>
		<inbound>
			<base />
			<cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
				<vary-by-header>Accept</vary-by-header>
				<vary-by-header>Accept-Charset</vary-by-header>
			</cache-lookup>
			<rewrite-uri template="/resource" />
		</inbound>
		<outbound>
			<base />
			<cache-store caching-mode="cache-on" duration="3600" />
		</outbound>
	</policies>

>[AZURE.NOTE] Los cambios efectuados en las directivas de almacenamiento en caché en el Editor de directivas se reflejarán en la pestaña **Almacenamiento en caché** de una operación, y viceversa.

## <a name="test-operation"> </a>Llamada a una operación y prueba del almacenamiento en caché

Para ver cómo funciona el almacenamiento en caché, podemos llamar a la operación desde el portal para desarrolladores. Haga clic en **Portal para desarrolladores** en el menú superior derecho.

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y seleccione **API Eco**.

![API Eco][api-management-apis-echo-api]

>Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.

Seleccione la operación **Recurso GET (en caché)** y, a continuación, haga clic en **Abrir consola**.

![Open console][api-management-open-console]

La consola permite invocar operaciones directamente desde el portal para desarrolladores.

![Consola][api-management-console]

Mantenga los valores predeterminados de **param1** y **param2**.

Seleccione la clave que desee en la lista desplegable **subscription-key**. Si su cuenta tiene solo una suscripción, ya estará seleccionada.

Escriba **sampleheader:value1** en el cuadro de texto **Encabezados de solicitud**.

Haga clic en **HTTP Get** y tome nota de los encabezados de respuesta.

Escriba **sampleheader:value2** en el cuadro de texto **Encabezados de solicitud** y haga clic en **HTTP Get**.

Observe que el valor de **sampleheader** sigue siendo **value1** en la respuesta. Pruebe algunos valores distintos y observe que se devuelve la respuesta almacenada en caché.

Escriba **25** en el campo **param2** y, a continuación, haga clic en **HTTP Get**.

Observe que el valor de **sampleheader** de la respuesta es ahora **value2**. Debido a que los resultados de la operación incluyen una clave especificada mediante la cadena de consulta, no se devolvió la anterior respuesta almacenada en caché.

## <a name="next-steps"> </a>Pasos siguientes

-	Consulte el resto de temas del tutorial [Introducción a la configuración de API avanzada][].
-	Para más información sobre las directivas de almacenamiento en caché, consulte [Directivas de almacenamiento en caché][] en la [Referencia de la directiva de Administración de API][].
-	Para obtener información sobre el almacenamiento en caché de los elementos por parte de la clave mediante expresiones de directiva, consulte [Custom caching in Azure API Management](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a Administración de API de Azure]: api-management-get-started.md
[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Introducción a la configuración de API avanzada]: api-management-get-started-advanced.md

[Referencia de la directiva de Administración de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Directivas de almacenamiento en caché]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps

<!---HONumber=AcomDC_0309_2016-->