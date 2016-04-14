<properties
	pageTitle="Plantillas"
	description="En este tema se explican las plantillas para los Centros de notificaciones de Azure."
	services="notification-hubs"
	documentationCenter=".net"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Plantillas

##Información general

Las plantillas permiten que una aplicación cliente especifique el formato exacto de la notificación que desea recibir. Con las plantillas, una aplicación puede obtener muchos beneficios distintos, incluidos los siguientes:

* Un back-end independiente de la plataforma

* Notificaciones personalizadas

* Independencia de la versión del cliente

* Localización sencilla

Esta sección proporciona ejemplos detallados sobre cómo usar las plantillas para enviar notificaciones independientes de la plataforma orientadas a todos los dispositivos en todas las plataformas y para personalizar la notificación de difusión a cada dispositivo.

##Uso de plantillas multiplataforma

La forma estándar de enviar notificaciones push es enviar una carga específica a los servicios de notificación de plataforma (WNS, APNS) para cada notificación que se debe enviar. Por ejemplo, para enviar una alerta a APNS, la carga es un objeto JSON con el formato siguiente:

	{"aps": {"alert" : "Hello!" }}

Para enviar un mensaje de notificación del sistema similar en una aplicación de la Tienda Windows, la carga XML es la siguiente:

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">Hello!</text>
	    </binding>
	  </visual>
	</toast>

Puede crear cargas similares para plataformas MPNS (Windows Phone) y GCM (Android).

Este requisito obliga al back-end de la aplicación a generar cargas distintas para cada plataforma y, de manera efectiva, hace que el back-end sea responsable de parte del nivel de presentación de la aplicación. Algunos problemas incluye diseños gráficos y de localización (especialmente para las aplicaciones de la Tienda Windows que incluyen notificaciones para varios tipos de iconos).

La característica de plantilla de Centros de notificaciones permite que una aplicación cliente cree registros especiales, llamados registros de plantilla, que, además del conjunto de etiquetas, incluye una plantilla. La característica de plantilla de Centros de notificaciones permite que una aplicación cliente asocie los dispositivos con plantillas, ya sea que trabaje con Instalaciones (la opción de preferencia) o con Registros. Dados los ejemplos de carga anteriores, la única información independiente de la plataforma es el mensaje de alerta mismo (Hello!). Una plantilla es un conjunto de instrucciones para el Centro de notificaciones sobre cómo dar formato a un mensaje independiente de la plataforma para el registro de esa aplicación cliente específica. En el ejemplo anterior, el mensaje independiente de la plataforma es una propiedad única: **message = Hello!**.

La siguiente ilustración muestra el proceso anterior:

![](./media/notification-hubs-templates/notification-hubs-hello.png)


La plantilla para el registro de una aplicación cliente de iOS es la siguiente:

	{"aps": {"alert": "$(message)"}}

La plantilla correspondiente a una aplicación cliente de la Tienda Windows es:

	<toast>
		<visual>
			<binding template="ToastText01">
				<text id="1">$(message)</text>
			</binding>
		</visual>
	</toast>

Observe que la expresión $(message) sustituye al mensaje mismo. Esta expresión indica al Centro de notificaciones, cada vez que envía un mensaje a este registro en especial, que cree un mensaje que lo siga y cambia el valor común.

Si trabaja con el modelo de Instalación, la clave "plantillas" de la instalación contiene el código JSON de varias plantillas. Si trabaja con el modelo de Registro, la aplicación cliente puede crear varios registros para usar varias plantillas; por ejemplo, una plantilla para los mensajes de alerta y una plantilla para las actualizaciones de icono. Las aplicaciones cliente también pueden combinar los registros nativos (registros sin plantilla) y los registros de plantilla.

El Centro de notificaciones envía una notificación para cada plantilla sin considerar si pertenecen a la misma aplicación cliente. Este comportamiento se puede usar para traducir las notificaciones independientes de la plataforma en más notificaciones. Por ejemplo, el mismo mensaje independiente de la plataforma al Centro de notificaciones se puede traducir sin problemas en una alerta de notificación del sistema y una actualización de icono, sin requerir que el back-end lo sepa. Tenga en cuenta que algunas plataformas (por ejemplo, iOS) puede contraer las diversas notificaciones en el mismo dispositivo si se envían en un período breve.

##Uso de plantillas para personalización

Otra ventaja de usar plantillas es la capacidad de usar Centros de notificaciones para ejecutar la personalización por registro de las notificaciones. Por ejemplo, considere una aplicación para el clima que muestra un icono con las condiciones climáticas de una ubicación específica. Un usuario puede elegir entre grados Celsius o Fahrenheit, además de un pronóstico de un día o de cinco días. Con las plantillas, cada instalación de aplicación cliente puede registrar el formato requerido (pronóstico de 1 día en grados Celsius, pronóstico de 1 día en grados Fahrenheit, pronóstico de 5 días en grados Celsius, pronóstico de 5 días en grados Fahrenheit) y hacer que el back-end envíe un mensaje único con toda la información necesaria para rellenar esas plantillas (por ejemplo, un pronóstico de 5 días con grados Celsius y Fahrenheit).

La plantilla para un pronóstico de 1 día con temperaturas expresadas en Celsius es la siguiente:

	<tile>
	  <visual>
	    <binding template="TileWideSmallImageAndText04">
	      <image id="1" src="$(day1_image)" alt="alt text"/>
	      <text id="1">Seattle, WA</text>
	      <text id="2">$(day1_tempC)</text>
	    </binding>  
	  </visual>
	</tile>

El mensaje enviado al Centro de notificaciones contiene todas las propiedades siguientes:


<table border="1">
<tr><td>day1\_image</td><td>day2\_image</td><td>day3\_image</td><td>day4\_image</td><td>day5\_image</td></tr>
<tr><td>day1\_tempC</td><td>day2\_tempC</td><td>day3\_tempC</td><td>day4\_tempC</td><td>day5\_tempC</td></tr>
<tr><td>day1\_tempF</td><td>day2\_tempF</td><td>day3\_tempF</td><td>day4\_tempF</td><td>day5\_tempF</td></tr>
</table><br/>


Con este patrón, el back-end solo envía un mensaje único sin tener que almacenar opciones de personalización específicas para los usuarios de la aplicación. La siguiente ilustración muestra este escenario:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

##Registro de las plantillas

Para registrar las plantillas con el modelo de Instalación (la opción de preferencia) o el modelo de Registro, consulte [Administración de registros](notification-hubs-registration-management.md).

##Lenguaje de expresión de plantilla

Las plantillas se limitan a los formatos de documento XML o JSON. Además, solo puede ubicar expresiones en lugares específicos; por ejemplo, valores o atributos de nodo para XML, valores de propiedad de cadena para JSON.



La tabla siguiente muestra el lenguaje que se permite en las plantillas:

| Expresión | Descripción |
|------------|-------------|
| $(prop) | Referencia a una propiedad de evento con el nombre especificado. Los nombres de propiedad no distinguen mayúsculas de minúsculas. Esta expresión se resuelve en el valor de texto de la propiedad o en una cadena vacía, si la propiedad no está presente. |
| $(prop, n) | Igual que el caso anterior, pero el texto se recorta explícitamente en n caracteres, por ejemplo, $(title, 20) recorta el contenido de la propiedad title en 20 caracteres. |
| .(prop, n) | Igual que el caso anterior, pero se agregan tres puntos como sufijo al texto debido a que se recorta. El tamaño total de la cadena recortada y el sufijo no exceden los n caracteres. .(title, 20) con una propiedad de entrada de "Esta es la línea del título", con lo que queda como **Esta es la línea ...** |
| %(prop) | Similar a $(name), salvo en que la salida está codificada en URI. |
| #(prop) | Se usa en las plantillas JSON (por ejemplo, para plantillas de iOS y Android).<br><br>Esta función se comporta igual que $(prop) especificada anteriormente, salvo cuando se usa en plantillas de JSON (por ejemplo, plantillas de Apple). En este caso, si esta función no está entre “{‘,’}” (por ejemplo, ‘myJsonProperty’ : ‘#(name)’) y se evalúa en un número en formato JavaScript, por ejemplo, regexp: (0|(&#91;1-9&#93;&#91;0-9&#93;*))(.&#91;0-9&#93;+)?((e|E)(+|-)?&#91;0-9&#93;+)?, el JSON de salida es un número.<br><br>Por ejemplo, ‘badge : ‘#(name)’ se convierte en ‘badge’ : 40 (y no ‘40‘). |
| ‘text’ o “text” | Un literal. Los literales contienen texto arbitrario entre comillas simples o dobles. |
| expr1 + expr2 | El operador de concatenación que une ambas expresiones en una sola cadena.

Las expresiones pueden estar en cualquiera de los formatos anteriores.

Cuando se usa la concatenación, toda la expresión debe estar entre {}. Por ejemplo, {$(prop) + ‘ - ’ + $(prop2)}. |


El siguiente ejemplo no es una plantilla XML válida:

	<tile>
	  <visual>
	    <binding $(property)>
	      <text id="1">Seattle, WA</text>
	    </binding>  
	  </visual>
	</tile>


Como se explicó anteriormente, cuando se usa la concatenación, las expresiones deben ir entre llaves. Por ejemplo:

	<tile>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">{'Hi, ' + $(name)}</text>
	    </binding>  
	  </visual>
	</tile>

<!----HONumber=AcomDC_1210_2015-->
