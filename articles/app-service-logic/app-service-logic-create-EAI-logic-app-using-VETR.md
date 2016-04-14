<properties
   pageTitle="Creación de una aplicación lógica de EAI con VETR en las aplicaciones lógicas del Servicio de aplicaciones de Azure | Microsoft Azure"
   description="Las características Validar, Codificar y Transformar de los servicios XML de BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="rajram"/>


# Creación de una aplicación lógica de EAI con VETR

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

La mayoría de los escenarios de Integración de aplicaciones empresariales (EAI) median datos entre un origen y un destino. Tales escenarios suelen tener un conjunto común de requisitos:

- Se aseguran de que los datos procedentes de distintos sistemas tienen el formato correcto.
- Realizan un “búsqueda” en los datos entrantes para tomar decisiones.
- Convierten los datos de un formato a otro. Por ejemplo, convierten los datos del formato de un sistema de CRM al de un sistema de ERP.
- Enrutan los datos a la aplicación o sistema deseados.

En este artículo nos centramos en un patrón de integración común: "mediación de mensaje unidireccional" o VETR (Validar, Enriquecer, Transformar, Enrutar). El patrón VETR media datos entre una entidad de origen y una entidad de destino. Normalmente, el origen y destino son orígenes de datos.

Imagine un sitio web que acepta pedidos. Los usuarios exponen pedidos en el sistema mediante HTTP. En segundo plano, el sistema valida los datos entrantes comprobando si son correctos, los normaliza y conserva en una cola de Bus de servicio para su posterior procesamiento. El sistema extrae pedidos de la cola, esperando que tengan un formato concreto. De este modo, el flujo de un extremo a extremo es:

**HTTP** → **Validar** → **Transformar** → **Bus de servicio**

![Flujo VETR básico][1]

Las siguientes aplicaciones de API de BizTalk ayudan a crear este patrón:

* **Desencadenador de HTTP**: origen de evento de mensaje de desencadenador
* **Validar**: valida la corrección de datos de entrada
* **Transformar**: transforma los datos del formato de entrada al formato requerido por el sistema de bajada
* **Conector de Bus de servicio**: entidad de destino donde se envían los datos


## Construcción del patrón VETR básico
### Conceptos básicos

En el Portal de Azure, seleccione **+Nuevo**, **Web y móvil**, y, a continuación, **Aplicación lógica**. Elija un nombre, una ubicación, una suscripción, un grupo de recursos y una ubicación que funcionen. Los grupos de recursos actúan como contenedores para las aplicaciones; todos los recursos de la aplicación van al mismo grupo de recursos.

A continuación, vamos a agregar desencadenadores y acciones.


## Adición de un desencadenador HTTP
1. En **Plantillas de aplicación lógica**, seleccione **Crear desde cero**.
1. Seleccione **Agente de escucha HTTP** desde la galería para crear un nuevo agente de escucha. Llámelo **HTTP1**.
2. Establezca la opción **¿Enviar respuesta automáticamente?** en false. Para configurar la acción desencadenadora, establezca _Método HTTP_ en _POST_ y _URL relativa_ en _/OneWayPipeline_:![Desencadenador HTTP][2].
3. Seleccione la marca de verificación verde para completar el desencadenador.

## Adición de la acción Validar

Ahora, vamos a especificar las acciones que se ejecutan cada vez que se activa el desencadenador, es decir, cuando se recibe una llamada en el punto de conexión HTTP.

1. Agregue **Validador XML de BizTalk** desde la galería y asígnele el nombre _(Validate1)_ para crear una instancia.
2. Configure un esquema XSD para validar los mensajes XML entrantes. Seleccione la acción _Validar_ y seleccione _triggers(‘httplistener’).outputs.Content_ como el valor para el parámetro _inputXml_.

Ahora, la acción de validación es la primera acción después del agente de escucha HTTP:

![Validador XML de BizTalk][3]

De forma similar, vamos a agregar el resto de las acciones.

## Acción Agregar transformación
Vamos a configurar transformaciones para normalizar los datos de entrada.

1. Agregue **Servicio de transformación de BizTalk** desde la galería.
2. Para configurar una transformación para que transforme los mensajes XML entrantes, seleccione que la acción **Transformar** se lleve a cabo cuando se llame a esta API. Seleccione ```triggers(‘httplistener’).outputs.Content``` como valor de _inputXml_. *Map* es un parámetro opcional, ya que los datos de entrada se hacen corresponder con todas las transformaciones configuradas y se aplican solo aquellos que coinciden con el esquema.
3. Por último, la transformación se ejecuta solamente si la validación se realiza correctamente. Para configurar esta condición, haga clic en el icono del engranaje de la parte superior derecha y seleccione _Agregar condición que debe cumplirse_. Establezca la condición en ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![Transformaciones de BizTalk][4]


## Adición del conector de Bus de servicio
A continuación, vamos a agregar el destino (una cola de Bus de servicio) en el que se escriben los datos.

1. Agregue un **conector de Bus de servicio** desde la galería. Establezca **Nombre** en _Servicebus1_, **Cadena de conexión** en la cadena de conexión para la instancia de bus de servicio, **Nombre de entidad** en _Cola_ y omita **Nombre de suscripción**.
2. Seleccione la acción **Enviar mensaje** y establezca el campo **Mensaje** de la acción en _actions('transformservice').outputs.OutputXml_.
3. Establezca el campo **Tipo de contenido** en *aplicación/XML*.  

![Bus de servicio][5]


## Envío de respuesta HTTP
Una vez que se realiza el procesamiento de canalización, se devuelve una respuesta HTTP tanto para operación correcta como para error con los pasos siguientes:

1. Agregue un **agente de escucha HTTP** desde la galería y seleccione la acción **Enviar respuesta HTTP**.
2. Establezca **Id. de respuesta** en Enviar *mensaje*.
2. Establezca **Contenido de respuesta** en *Procesamiento de canalización completado*.
3. **Código de estado de respuesta** en *200* para indicar HTTP 200 OK.
4. Haga clic en el menú desplegable de la parte superior derecha y seleccione **Agregar condición que debe cumplirse**. Establezca la condición en la siguiente expresión: ```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
5. Repita estos pasos para enviar una respuesta HTTP en caso de error también. Cambie **Condición** por la siguiente expresión: ```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Seleccione **Aceptar** y, a continuación, **Crear**.



## Finalización
Cada vez que alguien envía un mensaje al extremo HTTP, desencadena la aplicación y ejecuta las acciones que acabamos de crear. Para administrar las aplicaciones lógicas que cree, haga clic en **Examinar** en el Portal de Azure y seleccione **Aplicaciones lógicas**. Seleccione su aplicación para ver más información.

Algunos temas útiles:

[Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md) <br/> [Supervisión de las aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0302_2016-->