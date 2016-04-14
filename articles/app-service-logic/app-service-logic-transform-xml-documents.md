<properties 
	pageTitle="Uso de la transformación de BizTalk en las aplicaciones lógicas del Servicio de aplicaciones de Azure | Microsoft Azure" 
	description="Aprenda a transformar documentos XML de un esquema a otro." 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="anuragdalmia"/>

# Transformación de BizTalk

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

## Información general
La aplicación de API Transformación de BizTalk convierte los datos de un formato a otro. Por ejemplo, puede tomar las direcciones de envío y facturación de un pedido de compra e insertarlas en un documento de factura. También puede darse el caso de que tenga un mensaje entrante en el que la fecha actual tenga el formato *YearMonthDay*. Si lo desea, puede cambiar el formato de la fecha a *MonthDayYear*.

Puede hacerlo mediante la aplicación de API de transformación incluida en el servicio de aplicaciones de Microsoft Azure. Una Transformación, que se conoce también como asignación, está formada por un esquema XML de origen (la entrada) y un esquema XML de destino (la salida). Puede utilizar las diferentes funciones integradas para administrar o controlar los datos, incluidos aspectos como las manipulaciones de cadenas, las asignaciones condicionales, las expresiones aritméticas, los formateadores de tiempo y fecha e, incluso, las construcciones en bucle.

Las asignaciones se crean en Visual Studio mediante el [SDK de los servicios de BizTalk de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087). Cuando haya terminado de crear y probar la asignación, cárguela (.trfm) en la aplicación de API de transformación de BizTalk.

Otras características:

- La transformación creada en una asignación puede ser simple, como copiar un nombre y una dirección de un documento a otro. O bien puede crear transformaciones más complejas mediante las operaciones de asignación integradas.
- Hay varias operaciones de asignación o funciones a las que se puede acceder fácilmente, por ejemplo, cadenas, funciones de fecha hora, etc.
- Se puede hacer una copia de datos directa entre los esquemas. En el Asignador de BizTalk, es tan sencillo como dibujar una línea que conecte los elementos del esquema de origen con sus equivalentes en el esquema de destino.
- Al crear una asignación, se muestra una representación gráfica de la asignación, incluidas todas las relaciones y los vínculos que cree.
- Utilice la característica **Comprobar asignación** para agregar un mensaje XML de ejemplo. Con un solo clic, puede probar la asignación creada y ver el resultado generado.
- Cargue las asignaciones de servicios de BizTalk de Azure existentes (.trfm) y use todas las ventajas de la aplicación de API de transformación.
- Cuando crea la asignación, no es necesario agregar un esquema. Cuando la asignación esté lista, agréguela a la aplicación de API de transformación y ya puede comenzar. 
- Es compatible con el formato XML.


## Descarga de esquemas desde aplicaciones de API de conectores
Puede descargar los esquemas XML para conectores como SQL, SAP y SharePoint desde la página de resumen de la aplicación de API. Por ejemplo, si desea descargar esquemas XML para una aplicación de API específica de conector de SAP, busque la aplicación de API y abra la página de resumen. Elija **Descargar esquemas** y se descargará en el equipo un archivo zip con todos los esquemas correspondientes a las acciones de SAP. Puede utilizar los esquemas para crear una asignación (.trfm) en Visual Studio.


## Creación e incorporación de la asignación
Las transformaciones o asignaciones se crean en Visual Studio mediante el [SDK de servicios de BizTalk de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087), que se descarga de forma gratuita.

Para obtener ayuda acerca de la creación de una asignación, consulte [Creación de una asignación en Visual Studio](http://aka.ms/createamapinvs). Una vez que se ha creado la asignación y está lista para usarla, puede agregar la asignación (archivo .trfm) a la aplicación de API de transformación de BizTalk que creó en el Portal de Azure.

Si la asignación cambia o se modifica después de cargarla, puede cargar la asignación actualizada y reemplazar la existente en la aplicación de API de transformación.

1.	Seleccione **Examinar** en el Portal de Azure (a la izquierda de la pantalla) y elija **Aplicaciones de API**. Si no se muestra **Aplicaciones de API**, elija **Todo** y, a continuación, elija **Aplicaciones de API** en la lista disponible:

	![][7]

2.	Se muestra la lista de todas las **aplicaciones de API** creadas en la suscripción de Azure:

	![][8]

3.	Elija la aplicación de API de transformación de BizTalk que creó en la sección anterior.

4.	Se abre la hoja de configuración para la aplicación de API. Puede ver **Asignaciones** en la sección Componentes:

	![][9]

5.	Elija **Asignaciones** para abrir la hoja nueva con la lista de asignaciones.

6.	Elija el icono **Agregar asignación** en la parte superior para abrir la hoja **Agregar asignación**:

	![][10]

7.	Elija el icono de archivo y busque un archivo de asignación (.trfm) en el equipo local.

8.  Elija **Aceptar**. Se crea una nueva asignación. Se muestra en la lista de asignaciones.


## Uso de una aplicación de API de transformación de BizTalk en una aplicación lógica
Una vez que se haya creado y probado la asignación, estará lista para su uso.

1. Dentro de la aplicación lógica, la transformación de BizTalk está disponible en la galería, en la parte derecha. Elija **Servicio de transformación de BizTalk** en la galería. La transformación se agrega al flujo de:

	![][11]

2. Elija la acción **Transformar**. Se muestran los parámetros de entrada:

	![][12]

3. Especifique los siguientes parámetros para completar la configuración de la acción **Transformar**:
		 
	- XML de entrada
		- Escriba el contenido XML válido que se ajuste al esquema de origen de una asignación en la aplicación de API de transformación. Esto puede ser un resultado de una acción anterior en la aplicación lógica, por ejemplo, ‘Llamada RFC – SAP’ o ‘Insertar en tabla – SQL’.
		
	- Asignar nombre (opcional)
		- Escriba un nombre válido para la asignación que ya está cargada en la aplicación de API de transformación. Si no se especifica ninguna asignación, se elige una automáticamente basada en el esquema de origen adecuado para el XML de entrada.

	![][13]

4. El resultado de la acción 'XML de salida' puede usarse en las acciones posteriores en las aplicaciones lógicas.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=AcomDC_0224_2016-->