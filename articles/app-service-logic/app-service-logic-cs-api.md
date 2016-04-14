<properties
   pageTitle="Ejecución de expresiones de C# en una aplicación de API de C# en una aplicación lógica | Microsoft Azure"
   description="Aplicación de API de C# o conector"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/22/2016"
   ms.author="jehollan"/>

#Aplicación de API de C#

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

La aplicación de API de C# proporciona una manera fácil de ejecutar expresiones de C# simples *mientras se ejecuta la aplicación lógica*.

##¿Cuándo debe usar esta aplicación de API?
El escenario clave para el uso de esta aplicación de API es cuando quiere que el ciclo de vida del código que escribe sea el mismo que el de la aplicación lógica, y *no* quiere que se llame al código en otros escenarios.

Por otro lado, si desea un fragmento de código reutilizable que tenga un ciclo de vida independiente de la aplicación lógica, debe usar la aplicación de API de WebJobs para crear expresiones de código simples y llamarlas desde la aplicación lógica.

Por último, si desea incluir paquetes adicionales, necesitará pasar el ensamblado (.dll) al conector como cadena binaria con cifrado Base64 (como la salida del almacenamiento de blobs). Si desea más flexibilidad en los paquetes y ensamblajes, es posible que la mejor opción sea WebJob.

Utilice la [aplicación de API de JavaScript](app-service-logic-javascript-api.md) si prefiere escribir sus expresiones en JS.

##Creación de una aplicación de API de C#
Para usar la aplicación de API de C# deberá crear primero una instancia de ella. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API de C# en Azure Marketplace.

##Uso de la aplicación de API de C# en la superficie del diseñador de aplicaciones lógicas
###Desencadenador
Puede crear un desencadenador que el Servicio de aplicaciones lógicas sondee (en el intervalo que defina) y, si devuelve algo distinto de `false`, la aplicación lógica se ejecuta. De lo contrario, espera hasta el siguiente intervalo de sondeo para volver a comprobarlo.

Las entradas para el desencadenador son: - **Expresión de C#**: una expresión que se evalúa. Se invoca dentro de una función y debe devolver `false` cuando no desee que se ejecute la aplicación lógica, y puede devolver algo diferente cuando sí desee que se ejecute. Puede usar el contenido de la respuesta en las acciones de la aplicación lógica.

Por ejemplo, podría disponer de un desencadenador simple que solo ejecute la aplicación lógica entre los 15 y los 30 minutos de cada hora:

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###Acción

Del mismo modo, puede proporcionar la acción que desea ejecutar.

Las entradas para la acción son: - **Expresión de C#**: una expresión que se evalúa. Debe incluir la instrucción de `return` para obtener cualquier contenido. -**Objetos de contexto**: un objeto de contexto opcional que se puede pasar al desencadenador. Puede definir todas las propiedades que desee, pero la base debe ser un objeto JObject `{ ... }`, y puede hacerse referencia a los objetos en el script a través del nombre clave (el valor se pasa como a JToken correspondiente al nombre). - **Bibliotecas**: una matriz opcional de archivos .dll que incluir en la compilación del script. La matriz usa la siguiente estructura y funciona mejor junto a un conector de almacenamiento de blobs con el .dll como resultado:

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

Por ejemplo, imagine que utiliza el desencadenador de Office 365 **Nuevo correo electrónico**. Que devuelve el siguiente objeto:

```javascript
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

Sin embargo, desea cargar estos archivos adjuntos en una publicación de Yammer. Desafortunadamente, el esquema para los datos adjuntos de Yammer es ligeramente diferente. Ahora, puede analizar esto dentro de la aplicación lógica. Para el objeto de contexto, solo pase: `@triggerBody()`. Para la expresión, pase:

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

La acción devuelve el objeto que usted devolvió a partir de la función en un objeto de resultados. Por lo tanto, en la aplicación de API de Yammer, puede hacer referencia a `@body('csapi').results` para la propiedad **Datos adjuntos**.

## Aplicaciones adicionales del conector
Después de crear el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->