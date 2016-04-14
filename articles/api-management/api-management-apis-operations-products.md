<properties 
	pageTitle="Creación de API, operaciones y productos en Administración de API de Azure" 
	description="Obtenga información acerca de cómo crear API, operaciones y productos en Administración de API." 
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
	ms.date="02/01/2016" 
	ms.author="sdanie"/>

# Creación de API, operaciones y productos en Administración de API de Azure

En Administración de API de Azure, las API y sus operaciones se agregan a productos, donde las pueden utilizar los desarrolladores que crean aplicaciones que utilizan dichas API. Las guías de esta sección muestran cómo crear una API, agregarle operaciones y, a continuación, asociar dicha API con un producto y publicarla para ponerla a disposición de los desarrolladores.

## <a name="create-apis"> </a>Creación de API

En Administración de API, una API representa un conjunto de operaciones que puede invocarse por las aplicaciones cliente. Se crean nuevas API en el portal del publicador.

En este guía se muestra cómo crear y configurar una nueva API en Administración de API.

-   [Creación de API][]

## <a name="add-operations"> </a>Incorporación de operaciones a una API

Es necesario agregar operaciones para poder utilizar una API en Administración de API. En esta guía se muestra cómo agregar y configurar diferentes tipos de operaciones a una API en Administración de API.

-   [Incorporación de operaciones a una API][]

También es posible importar una API y sus operaciones en un paso, en formato WADL o Swagger.

-	[Importación de la definición de una API con operaciones][]

## <a name="add-product"> </a>Creación y publicación de un producto

En Administración de API, un producto contiene una o varias API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse a él y comenzar a utilizar sus API. Estos temas ofrecen orientaciones acerca de cómo crear un producto, agregarle una API y publicarlo para los desarrolladores.

-   [Incorporación y publicación de un producto][]
-	[Creación y definición de configuraciones de productos avanzadas][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Creación de API]: api-management-howto-create-apis.md
[Incorporación de operaciones a una API]: api-management-howto-add-operations.md
[Incorporación y publicación de un producto]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Importación de la definición de una API con operaciones]: api-management-howto-import-api.md
[Creación y definición de configuraciones de productos avanzadas]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0204_2016-->