<properties 
	pageTitle="Conceptos clave de Administración de API" 
	description="Obtenga información acerca de las API, los productos, los roles, los grupos y otros conceptos clave de Administración de API." 
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

# Importación de la definición de una API con operaciones en Administración de API de Azure

En Administración de API se pueden crear nuevas API y agregar las operaciones manualmente o se puede importar la API junto con las operaciones en un solo paso.

Las API y sus operaciones se pueden importar con los siguientes formatos.

-	WADL
-	Swagger

En esta guía se muestra cómo crear una API e importar sus operaciones en un solo paso. Para obtener información sobre la creación manual de una API y la incorporación de operaciones, consulte [Creación de API][] e [Incorporación de operaciones a una API][].

## <a name="import-api"> </a>Importación de una API

Las API se crean y se configuran en el portal del publicador. Para llegar al portal del publicador, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API. Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

![Portal del publicador][api-management-management-console]

Haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Importar API**.

![Import API][api-management-import-apis]

La ventana **Importar API** tiene tres pestañas que corresponden a tres formas de proporcionar la especificación de API.

-	**Desde el portapapeles** permite pegar la especificación de API en el cuadro de texto designado para ello.
-	**Desde el archivo** permite buscar el archivo que contiene la especificación de API y seleccionarlo.
-	**Desde URL** permite suministrar la dirección URL de la especificación para la API.

![Import API format][api-management-import-api-clipboard]

Después de proporcionar la especificación de API, use los botones de radio de la derecha para indicar el formato de especificación. Se admiten los siguientes formatos.

-	WADL
-	Swagger

A continuación, especifique un **Sufijo de dirección URL API web**. Este se anexa a la dirección URL base del servicio Administración de API. La dirección URL base es común para todas las API hospedadas en cada instancia de un servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de una instancia específica de un servicio de Administración de API.

Una vez especificados todos los valores, haga clic en **Guardar** para crear la API y las operaciones asociadas.

>[AZURE.NOTE]Para ver un tutorial de la importación de una API de calculadora básica en formato Swagger, consulte [Administración de su primera API en Administración de API de Azure](api-management-get-started.md).

## <a name="export-api"> </a> Exportación de una API

Además de importar nuevas API, puede exportar las definiciones de sus API desde el portal del publicador. Para ello, haga clic en **Exportar API** en la **pestaña Resumen** de la **API**.

![Export API][api-management-export-api]

Las API se pueden exportar con WADL o Swagger. Seleccione el formato que desee, haga clic en **Guardar** y elija la ubicación en la que desea guardar el archivo.

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>Pasos siguientes

Una vez creada una API e importadas las operaciones, se pueden revisar y definir las configuraciones adicionales, agregar la API a un producto y publicarlo para ponerlo a disposición de los desarrolladores. Para obtener más información, consulte las siguientes guías.

-	[Definición de la configuración de la API][]
-	[Creación y publicación de un producto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance

[Incorporación de operaciones a una API]: api-management-howto-add-operations.md
[Creación y publicación de un producto]: api-management-howto-add-products.md
[Creación de API]: api-management-howto-create-apis.md
[Definición de la configuración de la API]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=AcomDC_1210_2015-->