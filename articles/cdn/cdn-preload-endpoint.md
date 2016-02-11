<properties 
	pageTitle="Carga previa de activos en un punto de conexión de CDN de Azure" 
	description="Obtenga información sobre cómo precargar el contenido almacenado en caché en un punto de conexión de CDN." 
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
	
# Carga previa de activos en un punto de conexión de CDN de Azure

De forma predeterminada, los recursos primero se almacenan en caché conforme se solicitan. Esto significa que la primera solicitud de cada región puede tardar más tiempo, ya que los servidores perimetrales no tendrán el contenido almacenado en caché y deberán reenviar la solicitud al servidor de origen. La precarga del contenido evita esta latencia de primera visita.

Además de proporcionar una mejor experiencia de cliente, la precarga de los recursos almacenados en caché puede reducir el tráfico de red en el servidor de origen.

> [AZURE.NOTE] La precarga de recursos es útil para grandes eventos o contenido que se pone a disposición de un gran número de usuarios simultáneamente, como el lanzamiento de una nueva película o una actualización de software.

Este tutorial le guiará a través de la precarga de contenido almacenado en la caché en todos los nodos perimetrales de CDN de Azure.

## Tutorial

1. En el [Portal de Azure](https://portal.azure.com), examine el perfil de CDN que contiene el punto de conexión que quiere precargar. Se abre la hoja del perfil.

2. Haga clic en el punto de conexión de la lista. Se abre la hoja del punto de conexión.

3. En la hoja del punto de conexión de CDN, haga clic en el botón Cargar.
	
	![Hoja Punto de conexión de CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
	
	Se abre la hoja Cargar.
	
	![Hoja Carga de CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
	
4. Escriba la ruta de acceso completa de cada activo que quiera cargar (por ejemplo, */pictures/kitten.png*) en el cuadro de texto **Ruta de acceso**.

	> [AZURE.TIP] Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos. Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
	>
	> Las rutas de acceso deben ser una dirección URL relativa. Se puede usar el asterisco (*) como carácter comodín.
	
    ![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-paths.png)
    
5. Haga clic en el botón **Cargar**.

	![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-button.png)
	

## Consulte también
- [Purgar un punto de conexión de red CDN de Azure](cdn-purge-endpoint.md)
- [Referencia de la API de REST de red de CDN de Azure - purgar o cargar previamente un punto de conexión](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0128_2016-->