<properties
	pageTitle="CDN - Control del comportamiento del almacenamiento en caché de las solicitudes con cadenas de consulta"
	description="El almacenamiento en caché de las cadenas de consultas de CDN controla la manera en que se almacenarán en caché los archivos cuando contienen cadenas de consulta."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

#Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##Información general

El almacenamiento en caché de las cadenas de consultas controla la manera en que se almacenarán en caché los archivos cuando contienen cadenas de consulta.

> [AZURE.NOTE] Los niveles estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento de caché de cadenas de consultas, pero la interfaz de usuario es distinta. En este documento se describe la interfaz de usuario de nivel **Estándar**. Para el nivel Premium, vea [Control del comportamiento de almacenamiento en caché de solicitudes de CDN con cadenas de consulta - Premium](cdn-query-string-premium.md).

Hay tres modos disponibles:

- **Ignorar cadenas de consultas**: este es el modo predeterminado. El nodo perimetral de CDN pasará la cadena de consulta del solicitante al origen en la primera solicitud y almacenará en la memoria caché el activo. Todas las solicitudes posteriores de ese activo que se ofrecen desde el nodo perimetral ignorarán la cadena de consulta hasta que expira el activo en caché.
- **Omitir el almacenamiento en caché para dirección URL con cadenas de consultas**: en este modo, las solicitudes con cadenas de consultas no se almacenan en caché en el nodo perimetral de CDN. El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
- **Almacenar en caché cada URL única**: este modo trata cada solicitud con una cadena de consulta como un activo único con su propia memoria caché. Por ejemplo, la respuesta desde el origen para una solicitud de *foo.ashx?q=bar* se almacenaría en la memoria caché en el nodo perimetral y se devolvería para cachés posteriores con esa misma cadena de consulta. Se almacenaría en caché una solicitud de *foo.ashx?q=somethingelse* como un activo independiente con su propio período de vida.

	>[AZURE.WARNING] No se debe usar este modo cuando la cadena de consultas contiene parámetros que cambiarán con cada solicitud, como un id. de sesión o un nombre de usuario, ya que esto produciría una proporción de aciertos de memoria caché muy baja.

##Cambiar la configuración del almacenamiento en caché de cadenas de consulta

1. En la hoja de perfil de red CDN, haga clic en el punto de conexión de CDN que quiera administrar.

	![Puntos de conexión de hoja del perfil de red CDN](./media/cdn-query-string/cdn-endpoints.png)

	Se abre la hoja del punto de conexión de CDN.

2. Elija el botón **Configurar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-query-string/cdn-config-btn.png)

	Se abre la hoja de configuración de CDN.

3. Seleccione una opción en la lista desplegable **Comportamiento del almacenamiento en caché de cadenas de consultas**.

	![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string/cdn-query-string.png)

4. Tras efectuar su selección, haga clic en el botón **Guardar**.

<!---HONumber=AcomDC_0302_2016-->