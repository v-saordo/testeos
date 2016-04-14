<properties
	pageTitle="Compatibilidad con Uso compartido de recursos entre orígenes (CORS) | Microsoft Azure"
	description="Aprenda a habilitar la compatibilidad con CORS para los servicios de almacenamiento de Microsoft Azure."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="tamram"/>

# Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure

A partir de la versión 2013-08-15, los servicios de almacenamiento de Azure admiten Uso compartido de recursos entre orígenes (CORS) para los servicios de blob, tablas, colas y archivos. CORS es una característica de HTTP que permite que una aplicación web que se ejecuta en un dominio tenga acceso a recursos de otro dominio. Los exploradores web implementan una restricción de seguridad denominada [directiva del mismo origen](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impide que una página web llame a las API de otro dominio diferente; CORS proporciona una forma segura de permitir que un dominio (el dominio de origen) llame a las API de otro dominio. Consulte la [especificación CORS](http://www.w3.org/TR/cors/) para obtener más detalles sobre CORS.

Puede establecer reglas de CORS individualmente para cada uno de los servicios de almacenamiento mediante una llamada a [Definición de las propiedades del servicio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definición de las propiedades del servicio Cola](https://msdn.microsoft.com/library/hh452232.aspx) y [Definición de las propiedades del servicio Tabla](https://msdn.microsoft.com/library/hh452240.aspx). Una vez establecidas las reglas de CORS para el servicio, se evaluará una solicitud autenticada correctamente realizada en el servicio desde un dominio diferente para determinar si se permite según las reglas que ha especificado.

>[AZURE.NOTE] Tenga en cuenta que CORS no es ningún mecanismo de autenticación. Cualquier solicitud realizada en un recurso de almacenamiento cuando se ha habilitado CORS debe tener una firma de autenticación adecuada o bien se debe realizar en un recurso público.

## Descripción de las solicitudes de CORS

Una solicitud de CORS de un dominio de origen puede estar formada por dos solicitudes diferentes:

- Una solicitud preparatoria, que consulta las restricciones de CORS impuestas por el servicio. La solicitud preparatoria es necesaria, a menos que el método de solicitud sea un [método simple](http://www.w3.org/TR/cors/); es decir, GET, HEAD o POST.

- La solicitud real, realizada en el recurso deseado.

### Solicitud preparatoria

La solicitud preparatoria consulta las restricciones de CORS que el propietario de la cuenta ha establecido para el servicio de almacenamiento. El explorador web (u otro agente de usuario) envía una solicitud OPTIONS que incluye los encabezados, el método y el dominio de origen de la solicitud. El servicio de almacenamiento evalúa la operación deseada según un conjunto preconfigurado de reglas de CORS que especifican qué dominios de origen, métodos de solicitud y encabezados de solicitud se pueden especificar en una solicitud real para un recurso de almacenamiento.

Si se ha habilitado CORS para el servicio y hay una regla de CORS que coincide con la solicitud preparatoria, el servicio responde con el código de estado 200 (CORRECTO) e incluye los encabezados Access-Control necesarios en la respuesta.

Si CORS no se ha habilitado para el servicio o no hay ninguna regla de CORS que coincida con la solicitud preparatoria, el servicio responderá con el código de estado 403 (PROHIBIDO).

Si la solicitud OPTIONS no contiene los encabezados de CORS necesarios (los encabezados Origin y Access-Control-Request-Method), el servicio responderá con el código de estado 400 (Solicitud incorrecta).

Tenga en cuenta que una solicitud preparatoria se evalúa en el servicio (Blob, Cola y Tabla), no en el recurso solicitado. El propietario de la cuenta debe haber habilitado CORS como parte de las propiedades del servicio de cuenta para que la solicitud se realice correctamente.

### Solicitud real

Una vez que la solicitud preparatoria se acepta y se devuelve una respuesta, el explorador enviará la solicitud real al recurso de almacenamiento. El explorador denegará inmediatamente la solicitud real si se rechazó la solicitud preparatoria.

La solicitud real se trata como una solicitud normal en el servicio de almacenamiento. La presencia del encabezado Origin indica que se trata de una solicitud CORS y el servicio comprobará las reglas de coincidencia de CORS. Si se encuentra una coincidencia, los encabezados Access-Control se agregan a la respuesta y se devuelven al cliente. Si no se encuentra ninguna coincidencia, no se devuelven los encabezados Access-Control de CORS.

## Habilitar CORS para los servicios de almacenamiento de Azure

Las reglas de CORS se establecen en el nivel de servicio, por lo que necesita habilitar o deshabilitar CORS para cada servicio (Blob, Cola y Tabla) por separado. De forma predeterminada, CORS está deshabilitado para todos los servicios. Para habilitar CORS, necesita establecer las propiedades del servicio correspondiente utilizando la versión 2013-08-15 o posterior, y agregar reglas de CORS a las propiedades del servicio. Para obtener más información acerca de cómo habilitar o deshabilitar CORS para un servicio y cómo establecer las reglas de CORS, consulte [Definición de las propiedades del servicio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definición de las propiedades del servicio Cola](https://msdn.microsoft.com/library/hh452232.aspx) y [Definición de las propiedades del servicio Tabla](https://msdn.microsoft.com/library/hh452240.aspx).

A continuación se muestra un ejemplo de una única regla de CORS que se ha especificado mediante una operación Set Service Properties:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

A continuación se describen todos los elementos incluidos en la regla de CORS:

- **AllowedOrigins**: los dominios de origen que pueden realizar una solicitud en el servicio de almacenamiento mediante CORS. El dominio de origen es el dominio desde el que se origina la solicitud. Tenga en cuenta que el origen debe tener una coincidencia exacta con distinción entre mayúsculas y minúsculas con el origen que el agente de usuario envía al servicio. También puede utilizar el carácter comodín '*' para permitir que todos los dominios de origen hagan solicitudes a través de CORS. En el ejemplo anterior, los dominios [http://www.contoso.com](http://www.contoso.com) y [http://www.fabrikam.com](http://www.fabrikam.com) puede hacer solicitudes al servicio mediante CORS.

- **AllowedMethods**: los métodos (verbos de solicitud HTTP) que el dominio de origen puede usar para una solicitud de CORS. En el ejemplo anterior, solo se permiten las solicitudes PUT y GET.

- **AllowedHeaders**: los encabezados de solicitud que el dominio de origen puede especificar en la solicitud de CORS. En el ejemplo anterior, se permiten todos los encabezados de metadatos que comienzan por x-ms-meta-data, mx-ms-meta-target y x-ms-meta-abcm. Tenga en cuenta que el carácter comodín '*' indica que se permite cualquier encabezado que empiece con el prefijo especificado.

- **ExposedHeaders**: los encabezados de respuesta que se pueden enviar en la respuesta a la solicitud de CORS y que el explorador expone al emisor de la solicitud. En el ejemplo anterior, se pide al explorador que exponga todos los encabezados que empiecen por x-ms-meta.

- **MaxAgeInSeconds**: el tiempo máximo que un explorador debe almacenar en memoria caché la solicitud preparatoria OPTIONS.

Los servicios de almacenamiento de Azure permiten especificar encabezados con prefijo para los elementos **AllowedHeaders** y **ExposedHeaders**. Para permitir una categoría de encabezados, puede especificar un prefijo común a esa categoría. Por ejemplo, si se especifica *x-ms-meta* como un encabezado con prefijo, se establece una regla que coincidirá con todos los encabezados que empiecen por x-ms-meta.

Se aplican las limitaciones siguientes a las reglas de CORS:

- Puede especificar hasta cinco reglas de CORS por servicio de almacenamiento (Blob, Tabla y Cola).
- El tamaño máximo de los valores de todas las reglas de CORS de la solicitud, excluidas las etiquetas XML, no debe ser mayor que 2 KB.
- La longitud de un encabezado permitido, un encabezado expuesto o un origen permitido no debe superar los 256 caracteres.
- Los encabezados permitidos y los encabezados expuestos pueden ser:
  - Encabezados literales, donde se proporciona el nombre exacto del encabezado, por ejemplo **x-ms-meta-processed**. Se puede especificar un máximo de 64 encabezados literales en la solicitud.
  - Encabezados con prefijo, donde se proporciona un prefijo del encabezado como, por ejemplo, **x-ms-meta-data**. Al especificar un prefijo de este modo se permite o se expone cualquier encabezado que comience con el prefijo indicado. Se puede especificar un máximo de dos encabezados con prefijo en la solicitud.
- Los métodos (o los verbos HTTP) especificados en el elemento **AllowedMethods** deben ser acordes con los métodos admitidos por las API del servicio de almacenamiento de Azure. Los métodos admitidos son DELETE, GET, HEAD, MERGE, POST, OPTIONS y PUT.

## Descripción de la lógica de evaluación de reglas de CORS

Cuando un servicio de almacenamiento recibe una solicitud preparatoria o real, evalúa esa solicitud según las reglas de CORS que ha establecido para el servicio mediante la operación Set Service Properties adecuada. Las reglas de CORS se evalúan en el orden en que se establecieron en el cuerpo de la solicitud de la operación Set Service Properties.

Las reglas de CORS se evalúan de la manera siguiente:

1. Primero, se comprueba el dominio de origen de la solicitud con los dominios enumerados del elemento **AllowedOrigins**. Si el dominio de origen está incluido en la lista, o si se permiten todos los dominios con el carácter comodín '*', la evaluación de las reglas continúa. Si el dominio de origen no está incluido, se produce un error en la solicitud.

2. A continuación, el método (o el verbo HTTP) de la solicitud se comprueba con los métodos enumerados en el elemento **AllowedMethods**. Si el método está incluido en la lista, la evaluación de las reglas continúa; de lo contrario, se produce un error en la solicitud.

3. Si la solicitud coincide con una regla en su dominio de origen y su método, se selecciona esa regla para procesar la solicitud y no se evalúa ninguna otra regla. Sin embargo, antes de que la solicitud se pueda realizar correctamente, todos los encabezados especificados en la solicitud se comparan con los encabezados incluidos en el elemento **AllowedHeaders**. Si los encabezados enviados no coinciden con los encabezados permitidos, se produce un error en la solicitud.

Puesto que las reglas se procesan en el orden en que se encuentran en el cuerpo de la solicitud, las prácticas recomendadas indican que debe especificar las reglas más restrictivas en cuanto a los orígenes al principio de la lista, para que se evalúen en primer lugar. Especifique al final de la lista las reglas que sean menos restrictivas; por ejemplo, una regla para permitir todos los orígenes.

### Ejemplo: evaluación de reglas de CORS

En el ejemplo siguiente se muestra un cuerpo de solicitud parcial correspondiente a una operación para establecer reglas de CORS para los servicios de almacenamiento. Consulte [Definición de las propiedades del servicio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definición de las propiedades del servicio Cola](https://msdn.microsoft.com/library/hh452232.aspx) y [Definición de las propiedades del servicio Tabla](https://msdn.microsoft.com/library/hh452240.aspx) para obtener más información acerca de cómo crear la solicitud.

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

A continuación, considere las solicitudes de CORS siguientes:

Solicitud||| Response||
---|---|---|---|---
**Método** |**Origen** |**Encabezados de solicitud** |**Coincidencia de regla** |**Resultado**
**PUT** | http://www.contoso.com |x-ms-blob-content-type | Primera regla |Correcto
**GET** | http://www.contoso.com| x-ms-blob-content-type | Segunda regla |Correcto
**GET** | http://www.contoso.com| x-ms-blob-content-type | Segunda regla | Error

La primera solicitud coincide con la primera regla: el dominio de origen coincide con los orígenes permitidos, el método coincide con los métodos permitidos y el encabezado coincide con los encabezados permitidos. Por tanto, es correcta.

La segunda solicitud no coincide con la primera regla porque el método no coincide con los métodos permitidos. Sin embargo, coincide con la segunda regla, por lo que se realiza correctamente.

La tercera solicitud coincide con la segunda regla en su dominio de origen y método, por lo que no se evalúa ninguna regla más. Sin embargo, *x-ms-client-request-id header* no está permitido por la segunda regla, por lo que se produce un error en la solicitud, a pesar de que la semántica de la tercera regla habría permitido que se realizara correctamente.

>[AZURE.NOTE] Aunque este ejemplo muestra una regla menos restrictiva antes que otra más restrictiva, en general la práctica recomendada es enumerar primero las reglas más restrictivas.

## Descripción de cómo se establece el encabezado Vary

El encabezado *Vary* es un encabezado HTTP/1.1 estándar que consta de un conjunto de campos de encabezado de solicitud que aconseja al explorador o al agente de usuario sobre los criterios seleccionados por el servidor para procesar la solicitud. El encabezado *Vary* se usa principalmente para el almacenamiento en memoria caché por parte de los servidores proxy, los exploradores y las redes CDN, que lo usan para determinar cómo se debe almacenar la respuesta en memoria caché. Para obtener información detallada, vea la especificación del [Encabezado Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Cuando el explorador u otro agente de usuario almacena en memoria caché la respuesta de una solicitud de CORS, el dominio de origen se almacena en memoria caché como el origen permitido. Cuando un segundo dominio emite la misma solicitud para un recurso de almacenamiento mientras la memoria caché está activa, el agente de usuario recupera el dominio de origen de la memoria caché. El segundo dominio no coincide con el dominio almacenado en memoria caché, por lo que se produce un error en la solicitud cuando de lo contrario se realizaría correctamente. En algunos casos, Almacenamiento de Azure configura el encabezado Vary como **Origin** para indicar al agente de usuario que envíe la solicitud de CORS subsiguiente al servicio cuando el dominio de la solicitud sea distinto del origen almacenado en memoria caché.

Almacenamiento de Azure configura el encabezado *Vary* como **Origin** para las solicitudes GET/HEAD reales en los casos siguientes:

- Cuando el origen de la solicitud coincide exactamente con el origen permitido definido por una regla de CORS. Para que sea una coincidencia exacta, la regla de CORS no puede incluir un carácter comodín '*'.

- No hay ninguna regla que coincida con el origen de la solicitud, pero CORS se ha habilitado para el servicio de almacenamiento.

En caso de que una solicitud GET/HEAD coincida con una regla de CORS que permita todos los orígenes, la respuesta indica que se permiten todos los orígenes y la memoria caché del agente de usuario permitirá solicitudes posteriores de cualquier dominio de origen mientras la memoria caché esté activa.

Tenga en cuenta que para las solicitudes que utilizan otros métodos distintos de GET/HEAD, los servicios de almacenamiento no establecerá el encabezado Vary, ya que los agentes de usuario no almacenan en memoria caché las respuestas a estos métodos.

En la tabla siguiente se indica cómo responderá Almacenamiento de Azure a las solicitudes GET/HEAD según los casos mencionados anteriormente:

Solicitud|Configuración de la cuenta y resultado de la evaluación de reglas|||Response|||
---|---|---|---|---|---|---|---|---
**Encabezado Origin presente en la solicitud** | **Reglas de CORS especificadas para este servicio** | **Existe una regla de coincidencia que permite todos los orígenes (*)** | **Existe una regla de coincidencia para una coincidencia exacta del origen** | **La respuesta incluye el encabezado Vary establecido en Origin** | **La respuesta incluye Access-Control-Allowed-Origin: "*"** | **La respuesta incluye Access-Control-Exposed-Headers**
No|No|No|No|No|No|No
No|Sí|No|No|Sí|No|No
No|Sí|Sí|No|No|Sí|Sí
Sí|No|No|No|No|No|No
Sí|Sí|No|Sí|Sí|No|Sí
Sí|Sí|No|No|Sí|No|No
Sí|Sí|Sí|No|No|Sí|Sí


## Facturación de las solicitudes de CORS

Las solicitudes preparatorias correctas se cobran si ha habilitado CORS para cualquiera de los servicios de almacenamiento de su cuenta (llamando a [Definición de las propiedades del servicio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definición de las propiedades del servicio Cola](https://msdn.microsoft.com/library/hh452232.aspx) o [Definición de las propiedades del servicio Tabla](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar los cargos, puede configurar el elemento **MaxAgeInSeconds** de las reglas de CORS con un valor grande para que el agente de usuario almacene la solicitud en la memoria caché.

Las solicitudes preparatorias erróneas no se cargan en cuenta.

## Pasos siguientes

[Definición de las propiedades del servicio Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Definición de las propiedades del servicio Cola](https://msdn.microsoft.com/library/hh452232.aspx)

[Definición de las propiedades del servicio Tabla](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificación de uso compartido de recursos entre orígenes de W3C](http://www.w3.org/TR/cors/)

<!---HONumber=AcomDC_0224_2016-->