<properties 
	pageTitle="Red CDN - Mejora del rendimiento mediante la compresión de archivos" 
	description="Puede mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de archivos." 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Mejora del rendimiento mediante la compresión de archivos

Este tema trata de cómo mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de archivos.

Hay dos maneras de que la red CDN pueda admitir la compresión:

- Puede habilitar la compresión en el servidor de origen, en cuyo caso la red CDN admitirá la compresión de forma predeterminada y entregará archivos comprimidos a los clientes. 
- Puede habilitar la compresión directamente en los servidores perimetrales de la red CDN, en cuyo caso esta red comprimirá los archivos y los servirá a los usuarios finales.

## Habilitar la compresión

> [AZURE.NOTE]Los niveles estándar y premium de CDN proporcionan la misma funcionalidad de compresión, pero la interfaz de usuario es distinta. Para obtener más información sobre las diferencias entre los niveles estándar y premium de CDN, consulte [Información general sobre la red CDN de Azure](cdn-overview.md).

### Nivel Standard

1. En la hoja de perfil de red CDN, haga clic en el punto de conexión de CDN que desea administrar.
	
	![Puntos de conexión de hoja del perfil de red CDN](./media/cdn-file-compression/cdn-endpoints.png)

	Se abre la hoja del punto de conexión de CDN.

2. Elija el botón **Configurar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-file-compression/cdn-config-btn.png)
	
	Se abre la hoja de configuración de CDN.
	
3. Active la **Compresión**.
	
	![Opciones de compresión de red CDN](./media/cdn-file-compression/cdn-compress-standard.png)
	
4. Use los tipos predeterminados o modifique la lista quitando o agregando los tipos de archivo.

5. Tras efectuar los cambios, haga clic en el botón **Guardar**.

### Nivel Premium

1. En la hoja de perfil de red CDN, haga clic en el botón **Administrar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-file-compression/cdn-manage-btn.png)
	
	Se abre el portal de administración de CDS.
	
2. Desplace el mouse sobre la pestaña **HTTP grande** y, a continuación, mantenga el mouse sobre el control flotante **Configuración de caché**. Haga clic en **Compresión**.
	
	Se muestran las opciones de compresión.
	
	![Compresión de archivos](./media/cdn-file-compression/cdn-compress-files.png)
	
3. Después de modificar la lista de tipos de archivo, haga clic en el botón **Actualizar**.


## Reglas y proceso de compresión

1. El solicitante envía una solicitud de contenido.
2. Un servidor perimetral comprueba si hay un encabezado **Accept-Encoding**.
	1. Si se incluye, este encabezado identifica el método de compresión solicitado.
	1. Si falta, este tipo de solicitud se servirá en un formato sin comprimir.
3.	El POP perimetral más cercano comprueba el estado de la memoria caché, el método de compresión y si aún tiene un período de vida válido.
	1.	Error de caché: si la versión solicitada no está en la caché, la solicitud se reenvía al origen.
	2.	Acierto de caché con el mismo método de compresión: el servidor perimetral entregará inmediatamente el contenido comprimido al cliente.
	3.	Acierto de caché con un método de compresión diferente: el servidor perimetral transcodificará el recurso para el método de compresión solicitado. 
	4.	Acierto de caché y sin comprimir: si la solicitud inicial provocó que el activo se almacenara en un formato sin comprimir, se realizará una comprobación para ver si es válida la solicitud para la compresión de servidor perimetral (según los criterios de la sección anterior sobre definición y requisitos).
		1.	Si es válida, el servidor perimetral comprimirá el archivo y lo servirá al cliente.
		2.	Si no es válida: el servidor perimetral entregará inmediatamente el contenido sin comprimir al cliente. 



## Consideraciones 

1. Para los puntos de conexión de streaming habilitados para la red CDN de Servicios multimedia, la compresión está habilitada de forma predeterminada para los siguientes tipos de contenido: application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. No se puede habilitar o deshabilitar la compresión de los tipos mencionados mediante el Portal de Azure.  
2. Una sola versión del archivo (comprimido o sin comprimir) se almacenará en caché en el servidor perimetral. Una solicitud de una versión diferente provocará que el servidor perimetral transcodifique el contenido.  

<!---HONumber=AcomDC_1203_2015-->