<properties
   pageTitle="Aplicaciones lógicas como puntos de conexión invocables"
   description="Creación y configuración del agente de escucha HTTP y su uso en una aplicación lógica en el Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/17/2016"
   ms.author="stepsic"/>


# Aplicaciones lógicas como puntos de conexión invocables

La versión de esquema anterior de Aplicaciones lógicas (*2014-12-01-preview*) requiere una aplicación de API llamada **Agente de escucha HTTP** para exponer un punto de conexión HTTP que se puede llamar de forma sincrónica. Con el esquema más reciente (*2015-08-01-preview*), las Aplicaciones lógicas pueden exponer de forma nativa un punto de conexión HTTP sincrónico.

## Incorporación de un desencadenador a la definición
El primer paso consiste en agregar un desencadenador a la definición de aplicación lógica que puede recibir solicitudes entrantes. Hay tres tipos de desencadenadores que pueden recibir solicitudes: *manual *apiConnectionWebhook y *httpWebhook.

En el resto del artículo, usaremos como ejemplo **manual**, pero todas las entidades principales se aplican de forma idéntica a los otros dos tipos de desencadenadores. Al agregar este desencadenador a la definición de flujo de trabajo, el resultado será parecido a este:

```
{
    "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "triggers" : {
        "myendpointtrigger" : {
            "type" : "manual"
        }
    }
}
```

Esto creará un punto de conexión que puede invocar en una dirección URL, de manera parecida a como aquí se muestra:
 
```
https://prod-03.brazilsouth.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

Puede obtener este punto de conexión en la interfaz de usuario o mediante una llamada a:

```
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## Llamada al punto de conexión del desencadenador de la aplicación lógica
Una vez que tiene el punto de conexión del desencadenador, puede guardarlo en el sistema back-end e invocarlo mediante una instrucción `POST` a la dirección URL completa. Puede incluir parámetros de consulta, encabezados y cualquier contenido adicional en el cuerpo.

Si el tipo de contenido es `application/json`, podrá hacer referencia a las propiedades desde dentro de la solicitud. Si no, se tratará como una única unidad binaria que se puede pasar a otras API pero a la que no se puede hacer referencia dentro.

## Referencia al contenido de la solicitud entrante
La función `@triggerOutputs()` mostrará el contenido de la solicitud entrante. Por ejemplo, será similar a esto:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Puede usar el acceso directo `@triggerBody()` para acceder a la propiedad `body` específicamente.

Esto es ligeramente diferente de la versión *2014-12-01-preview*, donde se accedería al cuerpo de un agente de escucha HTTP mediante una función como: `@triggerOutputs().body.Content`.

## Respuesta a la solicitud
En el caso de algunas solicitudes que inician una aplicación lógica, es posible que quiera responder con algún contenido al autor de la llamada. Hay un nuevo tipo de acción llamado **respuesta** que se puede utilizar para construir el código de estado, el cuerpo y los encabezados de la respuesta. Tenga en cuenta que si no hay ninguna forma de **respuesta**, el punto de conexión de la aplicación lógica responderá *inmediatamente* con **202 - Aceptado** (que es el equivalente a *Enviar respuesta automáticamente* en el Agente de escucha HTTP).

```
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "conditions" : []
}
```

Las respuestas tienen los siguientes elementos:

| . | Descripción |
| -------- | ----------- |
| statusCode | El código de estado HTTP para responder a la solicitud entrante. Puede ser cualquier código de estado válido que comience con 2xx, 4xx o 5xx. No se permiten códigos de estado 3xx. | 
| body | Un objeto de cuerpo que puede ser una cadena, un objeto JSON o incluso contenido binario al que se hace referencia desde un paso anterior. | 
| encabezados | Puede definir cualquier número de encabezados que se incluirán en la respuesta | 

Todos los pasos de la aplicación lógica que se necesitan para la respuesta deben completarse en *60 segundos* para que la solicitud original reciba la solicitud. Si no se alcanza ninguna acción de respuesta en 60 segundos, la solicitud entrante agotará el tiempo de espera y recibirá una respuesta HTTP **408 - Tiempo de espera de cliente agotado**.

## Configuración avanzada del punto de conexión
Las Aplicaciones lógicas tienen compatibilidad integrada con el punto de conexión de acceso directo y siempre utilizan el método `POST` para iniciar la ejecución. La aplicación de API **Agente de escucha HTTP** anteriormente también admitía el cambio de los segmentos de dirección URL y el método HTTP. Incluso podía configurar seguridad adicional o un dominio personalizado agregándolo al host de aplicación de API (la aplicación web que hospeda la aplicación de API).

Esta funcionalidad está disponible a través de **Administración de API**, en las secciones para *[cambiar el método de la solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) *[cambiar los segmentos de dirección URL de la solicitud](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL) *configurar los dominios de Administración de API en la pestaña **Configurar** del Portal de Azure clásico y *configurar la directiva para comprobar la autenticación básica (**se necesita el vínculo**)

## Resumen de la migración desde 2014-12-01-preview

| 2014-12-01-preview | 2015-08-01-preview |
|---------------------|--------------------|
| Haga clic en la aplicación de API **Agente de escucha HTTP** | Haga clic en **Desencadenador manual** (no se necesita ninguna aplicación de API) |
| Configuración del agente de escucha HTTP "*Enviar respuesta automáticamente*" | Tanto si incluye una acción de **respuesta** en la definición del flujo de trabajo como si no |
| Configuración de la autenticación básica o OAuth | mediante Administración de API |
| Configuración del método HTTP | mediante Administración de API |
| Configuración de la ruta de acceso relativa | mediante Administración de API |
| Referencia al cuerpo entrante mediante `@triggerOutputs().body.Content` | Referencia mediante `@triggerOutputs().body` |
| Acción **Enviar respuesta HTTP** en el Agente de escucha HTTP | Haga clic en **Responder a la solicitud HTTP** (no se necesita ninguna aplicación de API)

<!---HONumber=AcomDC_0224_2016-->