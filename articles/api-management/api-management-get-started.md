<properties
	pageTitle="Administración de su primera API en Administración de API de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo crear API, agregar operaciones y comenzar a usar Administración de API."
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
	ms.topic="hero-article"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Administración de su primera API en Administración de API de Azure

## <a name="overview"> </a>Información general

En esta guía se muestra cómo empezar a usar rápidamente Administración de API de Azure y hacer la primera llamada API.

## <a name="concepts"> </a>¿Qué es la Administración de API de Azure?

Puede usar Administración de API de Azure para tomar cualquier back-end e iniciar un programa de API completo basado en él.

Entre los escenarios habituales se incluyen los siguientes:

* **Protección de la infraestructura móvil** mediante el control de acceso con claves de API, la prevención de ataques de denegación de servicio mediante la limitación o el uso de directivas de seguridad avanzadas como la validación de tokens JWT.
* **Habilitación de los ecosistemas de asociados de fabricantes de software independiente** mediante la oferta de una incorporación rápida de asociados a través del portal para desarrolladores y la creación de una fachada de API para desacoplar desde implementaciones internas que no son adecuada para el consumo de los asociados.
* **Ejecución de un programa de API interno** mediante la oferta de una ubicación centralizada para la organización a fin de comunicar acerca de la disponibilidad y los últimos cambios en las API y el control de acceso en función de cuentas de organizaciones, todo ello basado en un canal seguro entre la puerta de enlace de la API y el back-end.


El sistema consta de los siguientes componentes:

* La **puerta de enlace de la API** es el extremo que:
  * Acepta llamadas de API y las enruta a los back-end.
  * Comprueba las claves de API, los tokens JWT, los certificados y otras credenciales.
  * Aplica cuotas de uso y límites de frecuencia.
  * Transforma la API sobre la marcha sin modificaciones de código.
  * Almacena en caché las respuestas de back-end donde se instalaron.
  * Registra los metadatos de llamada para fines de análisis.

* El **portal para editores** es la interfaz administrativa donde se configura el programa de API, con acciones como las siguientes: Utilícelo para:
	* definir o importar el esquema de API
	* empaquetar las API en productos
	* establecer directivas, como cuotas o transformaciones, en las API
	* obtener información del análisis
	* administrar usuarios

* El **portal para desarrolladores** actúa como la presencia web principal para desarrolladores, donde estos pueden:
	* leer documentación de la API
	* probar una API a través de la consola interactiva
	* crear una cuenta y suscribirse para obtener claves de API
	* obtener acceso a análisis sobre su propio uso


## <a name="create-service-instance"> </a>Creación de una instancia de Administración de API

>[AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

El primer paso para trabajar con Administración de API es crear una instancia de servicio. Inicie sesión en el [Portal de Azure clásico][] y haga clic en **Nuevo**, **Servicios de aplicaciones**, **Administración de API**, **Crear**.

![API Management new instance][api-management-create-instance-menu]

En **URL**, especifique el nombre de subdominio único que desee usar para la dirección URL de servicio.

Elija la **Suscripción** y la **Región** deseadas para la instancia de servicio. Después de realizar las selecciones pertinentes, haga clic en el botón **Siguiente**.

![New API Management service][api-management-create-instance-step1]

Escriba **Contoso Ltd.** en **Nombre de la organización** y especifique su dirección de correo electrónico en el campo **Correo electrónico del administrador**.

>[AZURE.NOTE] Esta dirección de correo electrónico se utiliza para notificaciones por parte del sistema Administración de API. Para más información, consulte [Configuración de notificaciones y plantillas de correo electrónico en Administración de API de Azure][].

![New API Management service][api-management-create-instance-step2]

Las instancias de servicio de Administración de API están disponibles en tres niveles: Desarrollador, Estándar y Premium. De forma predeterminada, las nuevas instancias de servicio Administración de API se crean en el nivel de desarrollador. Para seleccionar el nivel Estándar o Premiun, active la casilla **Configuración avanzada** y seleccione el nivel deseado en la pantalla siguiente.

>[AZURE.NOTE] El nivel de desarrollador está orientado al desarrollo, las pruebas y los programas de API piloto para los que la disponibilidad no es un problema. En el nivel Estándar y Premium, puede escalar el número de unidades reservadas para administrar más tráfico. Los niveles Estándar y Premium proporcionan al servicio Administración de API la mayor potencia de procesamiento y rendimiento. Puede completar este tutorial mediante cualquier nivel. Para obtener más información acerca de los niveles de Administración de API, consulte [Precios de Administración de API][].

Haga clic en la casilla para crear su instancia de servicio.

![New API Management service][api-management-instance-created]

Una vez creada la instancia de servicio, el paso siguiente es crear o importar una API.

## <a name="create-api"> </a>Importación de una API

Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones API se realizan con proxy en servicios web existentes.

Las API se pueden crear (y las operaciones se pueden agregar) manualmente o se pueden importar. En este tutorial se importará una API para un servicio web de calculadora de ejemplo proporcionado por Microsoft y hospedado en Azure.

>[AZURE.NOTE] Para obtener instrucciones sobre la creación de una API y la incorporación manual de operaciones, consulte [Creación de API](api-management-howto-create-apis.md) e [Incorporación de operaciones a una API](api-management-howto-add-operations.md).

Las API se configuran desde el portal para editores, al que se accede mediante el Portal de Azure clásico. Para llegar al portal para editores, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API.

![Portal del publicador][api-management-management-console]

Para importar la API de la calculadora, haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Siga los pasos siguientes para configurar la API de la calculadora:

1. Haga clic en **Desde dirección URL**, escriba ****http://calcapi.cloudapp.net/calcapi.json** en el cuadro de texto la **Dirección URL del documento de especificación** y haga clic en el botón de opción **Swagger**.
2. Escriba **calc** en el cuadro de texto **Sufijo de la dirección URL de la API web**.
3. Haga clic en el cuadro **Productos (opcional)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Add new API][api-management-import-new-api]

>[AZURE.NOTE] El servicio **Administración de API** admite actualmente las versiones 1.2 y 2.0 del documento Swagger para la importación. Aunque la [especificación Swagger 2.0](http://swagger.io/specification) declara que las propiedades `host`, `basePath` y `schemes` son opcionales, el documento Swagger 2.0 **debe** contener esas propiedades; en caso contrario, no se importará.

Una vez importada la API, se muestra la página de resumen de la API en el portal para editores.

![API summary][api-management-imported-api-summary]

La sección de API tiene varias pestañas. La pestaña **Resumen** muestra información y métricas básicas acerca de la API. La pestaña [Configuración](api-management-howto-create-apis.md#configure-api-settings) se utiliza para ver y editar la configuración de una API. La pestaña [Operaciones](api-management-howto-add-operations.md) se utiliza para administrar las operaciones de la API. La pestaña **Seguridad** puede usarse para configurar la autenticación de puerta de enlace para el servidor back-end mediante la autenticación básica o la [autenticación mutua de certificados](api-management-howto-mutual-certificates.md) y para configurar la [autorización de usuario mediante OAuth 2.0](api-management-howto-oauth2.md). La pestaña **Problemas** se usa para ver los problemas notificados por los desarrolladores que usan las API. La pestaña **Productos** se usa para configurar los productos que contienen esta API.

De forma predeterminada, cada instancia de Administración de API incluye dos productos de ejemplo:

-	**Starter**
-	**Sin límite**

En este tutorial, se agregó la API de Basic Calculator al producto Starter cuando se importó la API.

Para realizar llamadas a una API, los desarrolladores deben suscribirse primero a un producto que les proporcione acceso a ella. Los desarrolladores pueden suscribirse a productos en el portal para desarrolladores; asimismo, los administradores pueden suscribir a los desarrolladores a productos en el portal para editores. Usted es un administrador, ya que creó la instancia de Administración de API en los pasos anteriores del tutorial, por lo que ya está suscrito a todos los productos de manera predeterminada.

## <a name="call-operation"> </a>Llamada a una operación desde el portal para desarrolladores

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial llamará a la operación **Agregar dos enteros** de la API de Basic Calculator. Haga clic en el **portal para desarrolladores** en el menú que se encuentra en la parte superior derecha del portal para editores.

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y, después, haga clic en **Basic Calculator** para ver las operaciones disponibles.

![Portal para desarrolladores][api-management-developer-portal-calc-api]

Observe las descripciones y los parámetros de ejemplo que se importaron junto con la API y las operaciones, que proporcionan documentación para los desarrolladores que usarán esta operación. Estas descripciones también pueden agregarse cuando las operaciones se agregan manualmente.

Para llamar a la operación **Agregar dos enteros**, haga clic en **Pruébelo**.

![Pruébelo][api-management-developer-portal-calc-api-console]

Puede escribir algunos valores para los parámetros o conservar los valores predeterminados y, después, haga clic en **Enviar**.

![HTTP Get][api-management-invoke-get]

Después de invocar una operación, el portal para desarrolladores mostrará el **Estado de respuesta**, los **Encabezados de respuesta** y el **Contenido de respuesta**.

![Response][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Visualización de análisis

Para ver un análisis de Basic Calculator, vuelva al portal para editores seleccionando **Administrar** en el menú situado en la parte superior derecha del portal para desarrolladores.

![Manage][api-management-manage-menu]

La vista predeterminada del portal para editores es el **Panel**, que proporciona información general de su instancia de Administración de API.

![Panel][api-management-dashboard]

Mantenga el puntero encima del gráfico de **Basic Calculator** para ver las métricas específicas de uso de la API durante un período de tiempo determinado.

>[AZURE.NOTE] Si no ve ninguna línea en el gráfico, vuelva al portal para desarrolladores y haga algunas llamadas a la API, espere unos instantes y vuelva al panel

Haga clic en **Ver detalles** para ver la página de resumen de la API, incluida una versión más amplia de las métricas mostradas.

![Análisis][api-management-mouse-over]

![Resumen][api-management-api-summary-metrics]

Para obtener información detallada sobre métricas e informes, haga clic en **Análisis** en el menú **Administración de API** a la izquierda.

![Información general][api-management-analytics-overview]

La sección **Análisis** consta de las cuatro pestañas siguientes:

-	**De un vistazo** proporciona métricas de uso global y de mantenimiento, así como los principales desarrolladores, productos, API y operaciones.
-	**Uso** proporciona información detallada de las llamadas de API y del ancho de banda, incluida una representación geográfica.
-	**Mantenimiento** se centra en los códigos de estado, las tasas de éxito de caché, los tiempos de respuesta y los tiempos de respuesta de la API y del servicio.
-	**Actividad** proporciona informes que detallan la actividad específica por desarrollador, producto, API y operación.

## <a name="next-steps"> </a>Pasos siguientes

-	Consulte el resto de temas del tutorial [Introducción a la configuración de API avanzada][].

[Evaluación gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Configuración de notificaciones y plantillas de correo electrónico en Administración de API de Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Introducción a la configuración de API avanzada]: api-management-get-started-advanced.md
[Precios de Administración de API]: http://azure.microsoft.com/pricing/details/api-management/

[Portal de Azure clásico]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png

<!---HONumber=AcomDC_0309_2016-->