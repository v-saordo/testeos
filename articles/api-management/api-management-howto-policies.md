<properties 
	pageTitle="Directivas de Administración de API de Azure" 
	description="Obtenga información acerca de cómo crear, editar y configurar directivas en Administración de API." 
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
	ms.date="12/02/2015" 
	ms.author="sdanie"/>


#Directivas de Administración de API de Azure

En Administración de API de Azure, las directivas constituyen una eficaz funcionalidad del sistema que permite al editor cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Entre las declaraciones más usadas se encuentran la conversión de formato de XML a JSON y la limitación de tasa de llamadas para restringir la cantidad de llamadas entrantes de un desarrollador. Hay muchas más directivas disponibles y listas para usar.

Consulte la [Referencia de directivas][] para obtener una lista de declaraciones de directiva con su configuración.

Las directivas se aplican en la puerta de enlace que se encuentra entre el consumidor de la API y la API administrada. La puerta de enlace recibe todas las solicitudes y normalmente las reenvía sin modificar a la API subyacente. Sin embargo, una directiva puede aplicar cambios a la solicitud de entrada y a la respuesta de salida.

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control][] y [Establecer variable][] se basan en expresiones de directiva. Para obtener más información, consulte [Directivas avanzadas][] y [Expresiones de directiva][].

## <a name="scopes"> </a>Configuración de directivas
Las directivas se pueden configurar globalmente o en el ámbito de un [producto][], una [API][] o una [operación][]. Para configurar una directiva, vaya al editor de directivas del portal de publicadores.

![Policies menu][policies-menu]

El editor de directivas consta de tres secciones principales: el ámbito de la directiva (superior), la definición de la directiva donde se editan las directivas (izquierda) y la lista de instrucciones (derecha):

![Policies editor][policies-editor]

Para comenzar a configurar una directiva, se debe seleccionar en primer lugar el ámbito en el que se debe aplicar. En la captura de pantalla siguiente, se selecciona el producto **Starter**. Tenga en cuenta que el símbolo de recuadro junto al nombre de la directiva indica que ya se aplica una directiva en este nivel.

![Scope][policies-scope]

Puesto que ya se ha aplicado una directiva, la configuración se muestra en la vista de definición.

![Configuración][policies-configure]

La directiva aparece como de solo lectura al principio. Para editar la definición, haga clic en la acción **Configurar directiva**.

![Edit][policies-edit]

La definición de la directiva es un documento XML simple que describe una secuencia de declaraciones de entrada y de salida. El XML se puede editar directamente en la ventana de definición. Se ofrece una lista de instrucciones a la derecha y las instrucciones aplicables al ámbito actual están habilitadas y resaltadas; como demuestra la instrucción **Límite de tasa de llamadas** de la captura de pantalla anterior.

Al hacer clic en una declaración habilitada se agregará el XML correspondiente en la ubicación del cursor en la vista de definición.

Hay una lista completa de instrucciones de directivas y su configuración disponible en la [Referencia de directivas][].

Por ejemplo, para agregar una nueva instrucción a fin de restringir las solicitudes de entrada a las direcciones IP especificadas, sitúe el cursor exactamente dentro del contenido del elemento XLM `inbound` y haga clic en la instrucción **Restringir las IP del autor de llamada**.

![Restriction policies][policies-restrict]

Así, se agregará un fragmento de código XML al elemento `inbound` que ofrece orientación sobre cómo configurar la instrucción.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Para limitar las solicitudes de entrada y aceptar solo las procedentes de una dirección IP de 1.2.3.4, modifique el XML de la manera siguiente:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Save][policies-save]

Cuando complete la configuración de las instrucciones de la directiva, haga clic en **Guardar**; los cambios se propagarán inmediatamente a la puerta de enlace de Administración de API.

##<a name="sections"> </a>Descripción de la configuración de directivas

Una directiva es una serie de declaraciones que se ejecutan en orden para una solicitud y una respuesta. La configuración se divide adecuadamente en las secciones `inbound`, `backend`, `outbound` y `on-error`, como se muestra en la siguiente configuración.

	<policies>
	  <inbound>
	    <!-- statements to be applied to the request go here -->
	  </inbound>
	  <backend>
	    <!-- statements to be applied before the request is forwarded to 
	         the backend service go here -->
	  </backend>
	  <outbound>
	    <!-- statements to be applied to the response go here -->
	  </outbound>
	  <on-error>
	    <!-- statements to be applied if there is an error condition go here -->
	  </on-error>
	</policies> 

Si se produce un error durante el procesamiento de una solicitud, los pasos restantes de las secciones `inbound`, `backend` o `outbound` se omiten y la ejecución salta a las instrucciones de la sección `on-error`. Mediante la colocación de instrucciones de directiva en la sección `on-error` puede revisar el error con la propiedad `context.LastError`, inspeccionar y personalizar la respuesta de error con la directiva `set-body` y configurar lo que ocurre si se produce un error. Existen códigos de error para pasos integrados y errores que pueden producirse durante el procesamiento de las instrucciones de directiva. Para más información, consulte [Control de errores en las directivas de administración de API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Como las directivas se pueden especificar en distintos niveles (global, de producto, de API y de operación), la configuración ofrece una manera de que se especifique el orden en el que se ejecutan las instrucciones de la definición de la directiva con respecto a la directiva principal.

Los ámbitos de la directiva se evalúan en el orden siguiente.

1. Ámbito global
2. Ámbito del producto
3. Ámbito de la API
4. Ámbito de la operación

Las instrucciones dentro de ellos se evalúan según la ubicación del elemento `base`, si existe.

Por ejemplo, si tiene una directiva de nivel global y una directiva configurada para una API, cuando se use esa directiva en concreto, se aplicarán ambas directivas. Administración de API tiene en cuenta el orden determinista de declaraciones de directiva combinadas mediante el elemento base.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

En la definición de directiva del ejemplo anterior, la instrucción `cross-domain` se ejecutaría antes de las directivas superiores que, a su vez, irían seguidas de la directiva `find-and-replace`.

Si la misma directiva aparece dos veces en la instrucción de directiva, se aplica la directiva más recientemente evaluada. Puede utilizar esto para reemplazar las directivas que se definen en un ámbito superior. Para ver las directivas en el ámbito actual en el editor de directivas, haga clic en **Recalcular la directiva en vigor del ámbito seleccionado**.

Tenga en cuenta que una directiva global no tiene una directiva principal y el uso del elemento `<base>` en ella no surte efecto.

## Pasos siguientes

Visualice el siguiente vídeo acerca de expresiones de directivas.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referencia de directivas]: api-management-policy-reference.md
[producto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[operación]: api-management-howto-add-operations.md

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=AcomDC_1203_2015-->