<properties 
	pageTitle="Purgar un punto de conexión de red CDN de Azure" 
	description="Obtenga información sobre cómo purgar todo el contenido almacenado en caché desde un punto de conexión de red de CDN." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# Purgar un punto de conexión de red CDN de Azure

## Información general 

Los nodos perimetrales de la red CDN almacenarán recursos en caché hasta que el período de vida de dichos recursos (TTL) expire. Tras la expiración del TTL del activo, cuando un cliente solicite el activo desde el nodo perimetral, este nodo recuperará una nueva copia actualizada del activo para atender la solicitud de cliente y almacenar actualizada la memoria caché.

A veces puede que quiera purgar contenido almacenado en caché de todos los nodos perimetrales y forzarlos todos para recuperar nuevos activos actualizados. Esto puede deberse a actualizaciones de la aplicación web o a actualizaciones rápidas de los activos de actualización que contienen información incorrecta.

Este tutorial le guiará a través de purga de los recursos de todos los nodos perimetrales de un punto de conexión.

## Tutorial

1. En el [Portal de Azure](https://portal.azure.com), examine el perfil de red CDN que contiene el punto de conexión que quiera purgar.

2. En la hoja Perfil de CDN, haga clic en el botón para purgar.
	
	![Hoja del perfil de red de CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	Abre la hoja para purgar.
	
	![Hoja de purga de red de CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. En la hoja para purgar, seleccione la dirección del servicio que quiera purgar en la lista desplegable de la dirección URL.

	![Formulario para purgar](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE] También puede obtener acceso a la hoja para purgar haciendo clic en el botón **Purgar** de la hoja del punto de conexión de red de CDN. En ese caso, el campo **URL** se rellenará previamente con la dirección del servicio de ese punto de conexión concreto.
	
4. Seleccione los activos que quiera purgar de los nodos perimetrales. Si quiere borrar todos los recursos, haga clic en la casilla **Purgar todo**. De lo contrario, escriba la ruta de acceso completa de cada activo que quiera purgar (por ejemplo, */pictures/kitten.png*) en el cuadro de texto **Ruta de acceso**.

	> [AZURE.TIP] Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos. Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
	>
	> Las rutas de acceso deben ser una dirección URL relativa. Se puede usar el asterisco (*) como carácter comodín.
	
5. Haga clic en el botón **Purgar**.

	![Botón Purgar](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## Consulte también
- [Carga previa de activos en un punto de conexión de CDN de Azure](cdn-preload-endpoint.md)
- [Referencia de la API de REST de red de CDN de Azure - purgar o cargar previamente un punto de conexión](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0128_2016-->