<properties 
	pageTitle="CDN - Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas" 
	description="El motor de reglas le permite personalizar cómo se controlan las solicitudes HTTP, como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP." 
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

# Invalidar el comportamiento HTTP predeterminado mediante el motor de reglas

## Información general

El motor de reglas le permite personalizar cómo se controlan las solicitudes HTTP, como el bloqueo de la entrega de determinados tipos de contenido, la definición de una directiva de almacenamiento en caché y la modificación de encabezados HTTP. En este tutorial se mostrará la creación de una regla que cambiará el comportamiento del almacenamiento en caché de los activos de CDN.

> [AZURE.NOTE]El motor de reglas es una característica del nivel de CDN Premium. Para una comparación de las características de los niveles Standard y Premium de CDN, vea [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md).

## Tutorial

1. En la hoja de perfil de CDN, haga clic en el botón **Administrar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
	
	Se abre el portal de administración de CDN.
	
2. Haga clic en la pestaña **HTTP grandes**, seguida del **Motor de reglas**.
	
	Se muestran las opciones para una nueva regla.
	
	![Opciones de nueva regla de CDN](./media/cdn-rules-engine/cdn-new-rule.png)

3. Escriba un nombre en el cuadro de texto **Nombre/Descripción**.

4. Identifique el tipo de solicitudes al que se aplicará la regla. De forma predeterminada, se selecciona la condición de coincidencia **Siempre**. Usará **Siempre** para este tutorial, así pues, deje esa opción seleccionada.

	![Condición de coincidencia de red CDN](./media/cdn-rules-engine/cdn-request-type.png)
	
	>[AZURE.TIP]Hay muchos tipos de condiciones de coincidencia disponibles en la lista desplegable. Haga clic en el icono de información azul de la izquierda de la condición de coincidencia para ver una explicación de la condición seleccionada actualmente en detalle.
	>
	>Para la lista completa de las condiciones de coincidencia en detalle, vea [Condición de coincidencia del motor de reglas y detalles de la característica](cdn-rules-engine-details.md#match-conditions).
	
5.  Haga clic en el botón **+** situado junto a **características** para agregar una nueva característica. En la lista desplegable de la izquierda, seleccione **Aplicar Max-Age interno**. En el cuadro de texto que aparece, escriba **300**. Deje los valores predeterminados restantes.

	![Característica de CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE]Como con las condiciones de coincidencia, haga clic en el icono informativo azul de la izquierda de la nueva característica para ver la información sobre esta característica. En el caso de **Aplicar Max-Age interno**, estamos reemplazando los encabezados **Cache-Control** y **Expires** del activo para controlar cuándo el nodo de punto de conexión de CDN actualizará el activo desde el origen. Nuestro ejemplo de 300 segundos significa que el nodo perimetral de CDN almacenará en caché el activo durante 5 minutos antes de actualizar el activo desde su origen.
	>
	>Para la lista completa de las características en detalle, vea [Condición de coincidencia del motor de reglas y detalles de la característica](cdn-rules-engine-details.md#features).
	
6.  Haga clic en el botón **Agregar** para guardar la nueva regla. La nueva regla ahora está pendiente de aprobación. Una vez que se haya aprobado, el estado cambiará de **XML pendiente** a **XML activo**.

## Consideraciones

- El orden en que se muestran varias reglas afecta a la manera en que se controlan. Una regla posterior puede invalidar las acciones especificadas por una regla anterior.

## Consulte también
* [Detalles de características y condiciones de coincidencia del motor de reglas de](cdn-rules-engine-details.md)
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Estadísticas en tiempo real en CDN de Microsoft Azure](cdn-real-time-stats.md)
* [Informes de HTTP avanzados](cdn-advanced-http-reports.md)
* [Analizar el rendimiento perimetral](cdn-edge-performance.md)


	

<!---HONumber=AcomDC_1217_2015-->