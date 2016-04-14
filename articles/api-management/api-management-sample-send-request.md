<properties
	pageTitle="Uso del servicio de administración de API para generar solicitudes HTTP"
	description="Aprenda a usar las directivas de solicitud y respuesta en Administración de API para llamar a servicios externos de su API"
	services="api-management"
	documentationCenter=""
	authors="darrelmiller"
	manager=""
	editor=""/>

<tags
	ms.service="api-management"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/19/2016"
	ms.author="v-darmi"/>


# Uso de servicios externos del servicio de administración de API de Azure

Las directivas disponibles en el servicio de administración de API de Azure pueden llevar a cabo una gran variedad de trabajo útil basado exclusivamente en la solicitud entrante, la respuesta saliente y la información de configuración básica. Pero la interacción con servicios externos de las directivas de Administración de API brinda muchas más oportunidades.

Anteriormente hemos visto cómo podemos interactuar con el [servicio del Centro de eventos de Azure con fines de registro, supervisión y análisis](api-management-log-to-eventhub-sample.md). En este artículo encontrará las directivas que permiten interactuar con cualquier servicio basado en HTTP externo. Dichas directivas se pueden usar para desencadenar eventos remotos o recuperar información que se utilizará para manipular en cierto modo la solicitud y respuesta originales.

## Send-One-Way-Request
Es posible que la interacción externa más sencilla sea el estilo de fire and forget de solicitud que permite que se notifique a un servicio externo de algún tipo de evento importante. Se puede usar la directiva de flujo de control `choose` para detectar cualquier tipo de condición que suscite interés y después, si se cumple esta, se puede realizar una solicitud HTTP externa mediante la directiva [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest). Podría tratarse de una solicitud para un sistema de mensajería como Hipchat o Slack o una API de Correo como SendGrid o MailChimp, o bien para incidentes de soporte técnico críticos como PagerDuty. Todos estos sistemas de mensajería tienen API HTTP sencillas que se pueden invocar fácilmente.

### Alerta con Slack
En el siguiente ejemplo puede ver cómo enviar un mensaje a un salón de chat de Slack si el código de estado de respuesta HTTP es mayor o igual que 500. Un error de intervalo 500 indica un problema con nuestra API de back-end que el cliente de la misma no puede resolver por sí mismo. Normalmente requiere algún tipo de intervención por nuestra parte.

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Slack tiene la noción de enlaces web entrantes. Al configurar un enlace web entrante, Slack genera una dirección URL especial que permite hacer una sencilla solicitud POST y pasar un mensaje al canal de Slack. El cuerpo JSON creado se basa en un formato definido por Slack.

![Enlace web de Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### ¿Es fire and forget lo suficientemente bueno?
Existen ciertos compromisos cuando se usa un estilo de fire and forget de solicitud. Si por algún motivo se produce un error en la solicitud, este no se notificará. En esta situación concreta, no se garantizan la complejidad de tener un sistema de informe de errores secundario ni el costo de rendimiento adicional de esperar la respuesta. En aquellos escenarios donde sea esencial comprobar la respuesta, la directiva [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) constituye una mejor opción.

## Send-Request
La directiva `send-request` permite usar un servicio externo para realizar funciones complejas de procesamiento y devolver datos al servicio de Administración de API que pueden usarse para un posterior procesamiento de directivas.

### Autorización de tokens de referencia
Una de las funciones principales de Administración de API es proteger los recursos de back-end. Si el servidor de autorización que usa su API crea [tokens de JWT](http://jwt.io/) como parte de su flujo de OAuth2, igual que hace [Azure Active Directory](../active-directory/active-directory-aadconnect.md), puede usar la directiva `validate-jwt` para comprobar la validez del token. Pero algunos servidores de autorización crean lo que se denomina [tokens de referencia](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/), que no se pueden comprobar sin llamar de vuelta al servidor de autorización.

### Introspección estandarizada
En el pasado no existía una forma estandarizada de comprobar un token de referencia con un servidor de autorización. Pero IETF publicó un estándar propuesto recientemente, [RFC 7662](https://tools.ietf.org/html/rfc7662), que define cómo un servidor de recursos puede comprobar la validez de un token.

### Extracción del token
El primer paso es extraer el token del encabezado de autorización. El formato del valor de encabezado debe constar del esquema de autorización `Bearer`, un espacio y el token de autorización según [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Desafortunadamente, hay casos donde se omite el esquema de autorización. Para tener esto en cuenta cuando se analiza, se divide el valor de encabezado en un espacio y se selecciona la última cadena en la matriz de cadenas devuelta. Esto proporciona una solución alternativa para los encabezados de autorización con formato incorrecto.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### Realización de la solicitud de validación
Una vez que tenemos el token de autorización, podemos realizar la solicitud para validarlo. RFC 7662 llama introspección a este proceso y requiere que `POST` un formulario HTML en el recurso de introspección. El formulario HTML debe contener al menos un par clave-valor con la clave `token`. Esta solicitud para el servidor de autorización también debe autenticarse a fin de garantizar que los clientes malintencionados no puedan rastrear tokens válidos.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### Comprobación de la respuesta
El atributo `response-variable-name` sirve para proporcionar acceso a la respuesta devuelta. El nombre definido en esta propiedad se puede usar como clave en el diccionario `context.Variables` para acceder al objeto `IResponse`.

En el objeto de respuesta, se puede recuperar el cuerpo, y RFC 7622 indica que la respuesta debe ser un objeto JSON que contenga al menos una propiedad denominada `active`, que es un valor booleano. Cuando `active` es true, el token se considera válido.

### Notificación de error
Se usa una directiva `<choose>` para detectar si el token no es válido y, en caso de no serlo, devolver una respuesta 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Según [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3), que describe cómo se deben usar los tokens de `bearer`, también se devuelve un encabezado `WWW-Authenticate` con la respuesta 401. WWW-Authenticate está pensado para indicar a un cliente cómo crear una solicitud debidamente autorizada. Debido a la gran variedad de enfoques posibles con el marco OAuth2, es difícil comunicar toda la información necesaria. Por fortuna, se están adoptando medidas para enseñar a los [clientes a autorizar debidamente las solicitudes a un servidor de recursos](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### Solución final
Al unir todas las piezas, se obtiene la siguiente directiva:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
  			<!-- Check active property in response -->
  			<when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
  				<!-- Return 401 Unauthorized with http-problem payload -->
  				<return-response response-variable-name="existing response variable">
  					<set-status code="401" reason="Unauthorized" />
  					<set-header name="WWW-Authenticate" exists-action="override">
  						<value>Bearer error="invalid_token"</value>
  					</set-header>
  				</return-response>
  			</when>
  		</choose>
      <base />
    </inbound>

Este ejemplo es solo uno de los muchos que hay sobre cómo puede usarse la directiva `send-request` para integrar servicios externos útiles en el proceso de solicitudes y respuestas que fluyen a través del servicio de Administración de API.

## Composición de respuesta
La directiva `send-request` se puede emplear para mejorar una solicitud principal a un sistema de back-end, como vimos en el ejemplo anterior, o bien se puede usar como una sustitución íntegra de la llamada de back-end. Gracias a esta técnica se pueden crear fácilmente recursos compuestos que se agregan desde varios sistemas diferentes.

### Creación de un panel   
A veces le gustaría exponer información existente en varios sistemas de back-end, por ejemplo, para realizar un panel. Los KPI proceden de los distintos back-end, pero prefiere no proporcionarles acceso directo y sería mejor si se pudiera recuperar toda la información en una única solicitud. Es posible que parte de la información de back-end deba segmentarse, desglosarse y corregirse un poco primero. Poder almacenar en caché ese recurso compuesto sería útil para reducir la carga de back-end, pues ya sabe que los usuarios tienen la costumbre de recurrir a la tecla F5 para ver si pueden cambiar sus métricas de déficit de rendimiento.

### Emulación del recurso
El primer paso para crear nuestro recurso del panel es configurar una nueva operación en el portal del publicador de Administración de API. Será una operación de marcador de posición usada para configurar nuestra directiva de composición a fin de crear nuestro recurso dinámico.

![Operación del panel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### Realización de las solicitudes
Una vez creada la operación de `dashboard`, se puede configurar una directiva para esa operación en concreto.

![Operación del panel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

El primer paso es extraer los parámetros de consulta de la solicitud entrante, de modo que puedan reenviarse al back-end. En este ejemplo, nuestro panel muestra información basada en un período de tiempo y, por tanto, tiene un parámetro `fromDate` y `toDate`. Se puede usar la directiva `set-variable` para extraer la información de la dirección URL de la solicitud.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Una vez que se tiene esta información, se pueden realizar solicitudes a todos los sistemas de back-end. Cada solicitud construye una nueva dirección URL con la información de los parámetros y llama a su servidor correspondiente y almacena la respuesta en una variable de contexto.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Estas solicitudes se ejecutarán en secuencia, que no es lo ideal. En una próxima versión, se introducirá una nueva directiva llamada `wait` que permitirá la ejecución en paralelo de todas estas solicitudes.

### Respuesta

Para construir la respuesta compuesta, se puede usar la directiva [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse). El elemento `set-body` puede usar una expresión para construir un nuevo elemento `JObject` con todas las representaciones de componentes insertadas como propiedades.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

Este es el aspecto de la directiva completa:

    <policies>
    	<inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
    	</inbound>
    	<backend>
    		<base />
    	</backend>
    	<outbound>
    		<base />
    	</outbound>
    </policies>

En la configuración de la operación de marcador de posición se puede configurar el recurso del panel que se va a almacenar en caché durante al menos una hora porque conocemos la naturaleza de los datos, que aunque lleven una hora sin actualizarse seguirán siendo lo suficientemente efectivos para transmitir información valiosa a los usuarios.

## Resumen
El servicio de administración de API de Azure proporciona directivas flexibles que se pueden aplicar de forma selectiva al tráfico HTTP y permite la composición de servicios de back-end. Si desea mejorar la puerta de enlace de la API con funciones de alerta, comprobación, capacidades de validación o crear nuevos recursos compuestos basados en varios servicios de back-end, la directiva `send-request` y otras relacionadas ofrecen todo un mundo de posibilidades.

## Ver un vídeo de información general de estas directivas
Para más información sobre las directivas [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) y [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) que se describen en este artículo, vea el siguiente vídeo.

> [AZURE.VIDEO send-request-and-return-response-policies]

<!---HONumber=AcomDC_0302_2016-->