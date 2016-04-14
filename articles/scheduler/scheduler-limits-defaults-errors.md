<properties
 pageTitle="Límites, valores predeterminados y códigos de error de Programador"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Límites, valores predeterminados y códigos de error de Programador

## Aceleradores, valores predeterminados, límites y cuotas de Programador

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## Encabezado x-ms-request-id

Cada solicitud realizada en el servicio Programador devuelve un encabezado de respuesta denominado **x-ms-request-id**. Este encabezado contiene un valor opaco que identifica de forma única la solicitud.

Si una solicitud genera error sistemáticamente y se ha comprobado que la solicitud está formulada correctamente, se puede usar este valor para notificar el error a Microsoft. En el informe, incluya el valor ofx-ms-request-id, la hora aproximada en que se realizó la solicitud, el identificador de la suscripción, el servicio en la nube, la colección de trabajos o el trabajo, así como el tipo de operación que intenta realizar la solicitud.

## Códigos de error y de estado de Programador

Además de los códigos de estado HTTP estándar, la API de REST de Programador de Azure devuelve códigos de error extendido y mensajes de error. Los códigos extendidos no reemplazan los códigos de estado HTTP estándar, pero proporcionan información adicional que permite realizar acciones y que puede usarse junto con los códigos de estado HTTP estándar.

Por ejemplo, puede producirse un error HTTP 404 por diversos motivos, por lo que tener la información adicional en el mensaje extendido puede ayudarle con la resolución del problema. Para obtener más información sobre los códigos HTTP estándar que devuelve la API de REST, vea [Códigos de error y de estado de administración del servicio](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). Las operaciones de API de REST para la API de administración de servicios devuelven códigos de estado HTTP estándar, tal como se establece en [Definiciones de código de estado HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). En la tabla siguiente se describen los errores comunes que es posible que el servicio devuelva.

|Código de error|Código de estado HTTP|Mensaje de usuario|
|----|----|----|
|MissingOrIncorrectVersionHeader|Solicitud incorrecta (400)|El encabezado de control de versiones no se especificó o se especificó incorrectamente.|
|InvalidXmlRequest|Solicitud incorrecta (400)|El XML del cuerpo de la solicitud no es válido o no se especificó correctamente.|
|MissingOrInvalidRequiredQueryParameter|Solicitud incorrecta (400)|Un parámetro de consulta necesario no se especificó para esta solicitud o se especificó incorrectamente.|
|InvalidHttpVerb|Solicitud incorrecta (400)|El verbo HTTP especificado no es reconocible para el servidor o no es válido para este recurso.|
|AuthenticationFailed|Prohibido (403)|El servidor no pudo autenticar la solicitud. Compruebe que el certificado es válido y está asociado a esta suscripción.|
|ResourceNotFound|No encontrado (404)|El recurso especificado no existe.|
|InternalError|Error interno del servidor (500)|Se produjo un error interno en el servidor. Vuelva a intentar realizar la solicitud.|
|OperationTimedOut|Error interno del servidor (500)|La operación no se pudo completar en el tiempo permitido.|
|ServerBusy|Servicio no disponible 503|El servidor (o un componente interno) no está disponible actualmente para recibir solicitudes. Vuelva a intentar realizar la solicitud.|
|SubscriptionDisabled|Prohibido (403)|La suscripción se encuentra en estado deshabilitado.|
|BadRequest|Solicitud incorrecta (400)|Un parámetro era incorrecto.|
|ConflictError|Conflicto (409)|Se produjo un conflicto que impide completar la operación.|
|TemporaryRedirect|Redirección temporal (307)|El objeto solicitado no está disponible. Se puede obtener un URI temporal para la nueva ubicación del objeto del campo de ubicación de la respuesta. Puede repetir la solicitud original en el nuevo URI.|

Las operaciones de API pueden devolver también información adicional de error definida por el servicio de administración. Esta información de error adicional se devuelve en el cuerpo de la respuesta.

## Otras referencias


 [¿Qué es Programador?](scheduler-intro.md)
 
 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)
 
  

<!---HONumber=AcomDC_0128_2016-->