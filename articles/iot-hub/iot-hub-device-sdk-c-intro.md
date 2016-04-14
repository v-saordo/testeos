<properties
	pageTitle="Uso del SDK de dispositivo de IoT de Azure para C | Microsoft Azure"
	description="Obtenga más información y comience a trabajar con el código de ejemplo del SDK de dispositivo IoT de Azure para C."
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/23/2016"
     ms.author="obloch"/>

# Introducción al SDK de dispositivo IoT de Azure

El **SDK de dispositivo IoT de Azure** es un conjunto de bibliotecas diseñadas para simplificar el proceso de envío de eventos y recepción de mensajes desde el servicio **Centro de IoT de Azure**. Hay distintas variantes del SDK, cada una dirigida a una plataforma concreta, pero este artículo describe el **SDK de dispositivo IoT de Azure para C**.

El SDK de dispositivo IoT de Azure para C está escrito en ANSI C (C99) para maximizar su portabilidad. Resulta idóneo para operar en diferentes plataformas y dispositivos, especialmente si la prioridad es minimizar la carga para el espacio de disco y la memoria.

Existe una amplia gama de plataformas en las que se ha probado el SDK (consulte [Compatibilidad de hardware y de plataformas de sistema operativo con SDK de dispositivos](iot-hub-tested-configurations.md) para obtener más información). Aunque este artículo incluye tutoriales con código de ejemplo que se ejecuta en la plataforma Windows, tenga en cuenta que el código descrito en este artículo es exactamente el mismo en todas las plataformas admitidas.

En este artículo se presentarán la arquitectura del SDK de dispositivo IoT de Azure para C. Demostraremos cómo inicializar la biblioteca de dispositivos, enviar eventos al Centro de IoT y como recibir mensajes de él. La información de este artículo debería ser suficiente para comenzar a usar el SDK, pero también se proporcionan referencias a información adicional sobre las bibliotecas.

## Arquitectura del SDK

Encontrará el **SDK de dispositivo IoT de Azure para C** en el repositorio de GitHub siguiente:

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

Encontrará la versión más reciente de las bibliotecas en la bifurcación **principal** del repositorio:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Este repositorio contiene toda la familia de SDK de dispositivo IoT de Azure. Sin embargo, este artículo trata sobre el SDK de dispositivo IoT de Azure *para C* que puede encontrarse en la carpeta **c**.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* La implementación básica del SDK puede encontrarse en la carpeta **iothub\_client**, que contiene la implementación de la capa inferior de la API del SDK: la biblioteca **IoTHubClient**. La biblioteca **IoTHubClient** contiene las API que implementan mensajería sin formato para enviar mensajes al Centro de IoT, así como recibir mensajes del mismo. Cuando se utiliza esta biblioteca, el usuario es responsable de la implementación de la serialización de mensajes (eventualmente mediante el ejemplo de serializador que se describe a continuación), pero otros detalles de la comunicación con el Centro de IoT se administran automáticamente.
* La carpeta del **serializador** contiene funciones auxiliares y ejemplos que muestran cómo serializar los datos antes de realizar el envío al Centro de IoT de Azure mediante la biblioteca de cliente. Tenga en cuenta que el uso del serializador no es obligatorio y solo se proporciona como un recurso que puede resultarle práctico. Si usa la biblioteca **serializer**, se empieza por definir un modelo que especifica los eventos que quiere enviar al Centro de IoT, así como los mensajes que espera recibir de él. Una vez definido el modelo, el SDK le proporciona una superficie de API que le permite trabajar fácilmente con eventos y mensajes sin tener que preocuparse por los detalles de la serialización. La biblioteca depende de otras bibliotecas de código abierto que implementan el transporte mediante varios protocolos (AMQP, MQTT).
* La biblioteca **IoTHubClient** depende de otras bibliotecas de código abierto:
   * La biblioteca [Azure C shared utility](https://github.com/Azure/azure-c-shared-utility), que proporciona la funcionalidad común para las tareas básicas (como cadena, manipulación de listas, E/S, etc.) necesarias para diversos SDK de C relacionados con Azure.
   * La biblioteca [uAMQP Azure](https://github.com/Azure/azure-uamqp-c), que es la implementación del lado cliente de AMQP optimizada para los dispositivos de restricción de recursos.
   * La biblioteca [uMQTT Azure](https://github.com/Azure/azure-umqtt-c), que es una biblioteca de uso general que implementa el protocolo MQTT y está optimizada para los dispositivos de restricción de recursos.

Todo esto es más fácil de entender con un código de ejemplo. Las secciones siguientes le guiarán a través de un par de aplicaciones de ejemplo incluidas en el SDK. Esto le dará una buena idea de las distintas funcionalidades de las capas de arquitectura del SDK, así como una introducción al funcionamiento de las API.

## Antes de ejecutar los ejemplos

Antes de ejecutar los ejemplos en el SDK de dispositivo IoT de Azure para C, debe crear una instancia del servicio en su suscripción de Azure si no tiene ninguna y completar dos tareas:
* Preparar el entorno de desarrollo
* Obtener las credenciales del dispositivo

Si necesita crear una instancia del Centro de IoT de Azure en su suscripción de Azure, siga las instrucciones [aquí](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

El [archivo Léame](https://github.com/Azure/azure-iot-sdks/tree/master/c) incluido con el SDK ofrece instrucciones para preparar el entorno de desarrollo y obtener las credenciales del dispositivo. Las secciones siguientes incluyen algunos comentarios adicionales sobre esas instrucciones.

### Preparación del entorno de desarrollo

Aunque se proporcionan paquetes para algunas plataformas (como NuGet para Windows o apt\_get para Debian y Ubuntu) y los ejemplos utilizan estos paquetes si están disponibles, las instrucciones siguientes explican cómo generar la biblioteca y los ejemplos directamente desde el código.

En primer lugar, necesitará obtener una copia del SDK de GitHub y, a continuación, compilar el código fuente. Debe obtener una copia del código fuente en la rama **master** del [repositorio de GitHub](https://github.com/Azure/azure-iot-sdks).

Cuando haya descargado una copia del código fuente, debe completar los pasos descritos en el artículo del SDK [Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) (Preparación del entorno de desarrollo).


Estas son algunas sugerencias que le ayudarán a completar el procedimiento descrito en la guía de preparación:

-   Al instalar la utilidad **CMake**, elija la opción de agregar **CMake** a la ruta de acceso del sistema para **todos los usuarios** (y agregar también a los trabajos del **usuario actual**):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Antes de abrir el **símbolo del sistema para desarrolladores de VS2015**, instale las herramientas de línea de comandos de Git. Para instalar estas herramientas, complete los pasos siguientes:

	1. Inicie el programa de instalación de **Visual Studio 2015** (o elija **Microsoft Visual Studio 2015** en el panel de control de **Programas y características** y seleccione **Cambiar**).
	
	2. Asegúrese de que la característica **Git para Windows** esté seleccionada en el programa de instalación; también puede ser recomendable activar la opción **Extensión de GitHub para Visual Studio** para proporcionar integración de IDE:

  		![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

	3. Complete el Asistente para instalación para instalar las herramientas.

	4. Agregue el directorio **bin** de las herramientas de Git a la variable de entorno **PATH** del sistema. En Windows, tendrá el siguiente aspecto:

  		![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Cuando se completen todos los pasos descritos en la página [Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) (Preparación del entorno de desarrollo), estará listo para compilar las aplicaciones de ejemplo.

### Obtención de las credenciales del dispositivo

Ahora que el entorno de desarrollo está configurado, lo siguiente que debe hacer es obtener un conjunto de credenciales de dispositivo. Para que un dispositivo pueda acceder a un Centro de IoT, primero debe agregar el dispositivo al Registro de dispositivos del Centro de IoT. Al agregar el dispositivo, se obtiene un conjunto de credenciales de dispositivo que necesita para que el dispositivo se pueda conectar a un Centro de IoT. Las aplicaciones de ejemplo que veremos en la siguiente sección esperan estas credenciales en forma de una **cadena de conexión de dispositivo**.

En el repositorio de código abierto del SDK se proporcionan un par de herramientas para ayudarle a administrar el Centro de IoT. Una es una aplicación de Windows denominada Device Explorer, la otra es una herramienta CLI multiplataforma basada en node.js denominada iothub-explorer. Puede obtener más información acerca de estas herramientas [aquí](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Como en este artículo estamos viendo el proceso de ejecución de los ejemplos en Windows, estamos utilizando la herramienta Device Explorer. Sin embargo, también puede utilizar iothub-explorer si prefiere herramientas CLI.

La herramienta [Device Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) usa las bibliotecas del servicio IoT de Azure para realizar varias funciones en el Centro de IoT, incluida la adición de dispositivos. Si usa el Explorador de dispositivos para agregar un dispositivo, obtendrá una cadena de conexión correspondiente. Necesita esta cadena de conexión para hacer que estas aplicaciones de ejemplo se ejecuten.

Si no está familiarizado con el proceso, el siguiente procedimiento describe cómo usar Device Explorer para agregar un dispositivo y obtener la cadena de conexión del dispositivo.

Puede encontrar un programa de instalación de Windows para la herramienta Device Explorer en la [página de la versión de SDK](https://github.com/Azure/azure-iot-sdks/releases). No obstante, también puede ejecutar la herramienta directamente desde su apertura de código **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** en **Visual Studio 2015** y compilar la solución.

Al ejecutar el programa verá esta interfaz:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Escriba su **cadena de conexión de Centro de IoT** en el primer campo y haga clic en el botón **Update** (Actualizar). Esto configura la herramienta para que pueda comunicarse con el Centro de IoT.

Una vez configurada la cadena de conexión del Centro de IoT, haga clic en la pestaña **Management** (Administración):

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Aquí podrá administrar los dispositivos registrados en su Centro de IoT.

Para crear un dispositivo, haga clic en el botón **Create** (Crear). Aparece un cuadro de diálogo con un conjunto de claves (principal y secundaria) previamente rellenadas. Todo lo que tiene que hacer es escribir un **identificador de dispositivo** y hacer clic en **Create** (Crear).

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Una vez creado el dispositivo, la lista de dispositivos se actualiza con todos los dispositivos registrados, incluido el que acaba de crear. Si hace clic con el botón derecho en el nuevo dispositivo, verá este menú:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Si elige la opción **Copy connection string for selected device** (Copiar la cadena de conexión para el dispositivo seleccionado), la cadena de conexión de su dispositivo se copia en el Portapapeles. Mantenga una copia de la cadena de conexión. La necesitará cuando ejecute las aplicaciones de ejemplo que se describen en las secciones siguientes.

Una vez completados los pasos anteriores, está listo para iniciar la ejecución de código. Ambos ejemplos tienen una constante en la parte superior del archivo de código fuente principal, que permite especificar una cadena de conexión. Por ejemplo, la línea correspondiente de la aplicación **iothub\_client\_sample\_amqp** aparece del modo siguiente.

```
static const char* connectionString = "[device connection string]";
```

Si quiere seguir, escriba aquí la cadena de conexión de dispositivo, vuelva a compilar la solución y podrá ejecutar el ejemplo.

## IoTHubClient

Dentro de la carpeta **iothub\_client**, en el repositorio azure-iot-sdks, hay una carpeta **samples** que contiene una aplicación denominada **iothub\_client\_sample\_amqp**.

La versión de Windows de la aplicación **iothub\_client\_sample\_ampq** incluye la solución de Visual Studio siguiente:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Esta solución contiene un proyecto. Cabe mencionar que existen cuatro paquetes de NuGet instalados en esta solución:

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

El paquete **Microsoft.Azure.C.SharedUtility** siempre es necesario al trabajar con el SDK. Dado que este ejemplo se basa en AMQP, también es necesario incluir los paquetes **Microsoft.Azure.uamqp** y **Microsoft.Azure.IoTHub.AmqpTransport** (existen paquetes equivalentes para HTTP y MQTT). Como el ejemplo usa la biblioteca **IoTHubClient**, se debe incluir también el paquete **Microsoft.Azure.IoTHub.IoTHubClient** en la solución.

Encontrará la implementación de la aplicación de ejemplo en el archivo de código fuente **iothub\_client\_sample\_amqp.c**.

Vamos a usar esta aplicación de ejemplo para guiarle por los requisitos necesarios para usar la biblioteca **IoTHubClient**.

### Inicialización de la biblioteca

Para empezar a trabajar con las bibliotecas, primero debe asignar un identificador de cliente del Centro de IoT:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Observe que se pasa una copia de nuestra cadena de conexión de dispositivo a esta función (la misma que se obtuvo del Explorador de dispositivos). También se designa el protocolo que se va a usar. Este ejemplo utiliza AMQP, pero MQTT y HTTP también son una opción.

Cuando tenga un **IOTHUB\_CLIENT\_HANDLE** válido, puede empezar a llamar a las API para enviar eventos y recibir mensajes del Centro de IoT. Veamos esto ahora.

### Envío de eventos

Para enviar eventos al Centro de IoT es necesario completar los pasos siguientes:

Primero, cree un mensaje:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Después, envíe el mensaje:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Cada vez que envía un mensaje, se especifica una referencia a una función de devolución de llamada que se invoca cuando se envían los datos:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe la llamada a la función **IoTHubMessage\_Destroy** cuando termine con el mensaje. Debe hacer esta llamada para liberar los recursos asignados al crear el mensaje.

### Recepción de mensajes

La recepción de mensajes es una operación asincrónica. En primer lugar, se registra una devolución de llamada que se invocará cuando el dispositivo recibe un mensaje:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

El último parámetro es un puntero nulo a lo que quiera. En el ejemplo, es un puntero a un entero, pero podría ser un puntero a una estructura de datos más compleja. Esto permite que la función de devolución de llamada funcione en un estado compartido con el autor de la llamada de esta función.

Cuando el dispositivo recibe un mensaje, se invoca la función de devolución de llamada registrada:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que se usa la función **IoTHubMessage\_GetByteArray** para recuperar el mensaje, que en este ejemplo es una cadena.

### Desinicialización de la biblioteca

Cuando haya terminado con el envío de datos y la recepción de mensajes, puede anular la inicialización de la biblioteca IoT. Para ello, emita la siguiente llamada de función:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Esto libera los recursos asignados previamente por la función **IoTHubClient\_CreateFromConnectionString**.

Como puede ver, es fácil enviar eventos y recibir mensajes con la biblioteca **IoTHubClient**. La biblioteca controla los detalles de la comunicación con el Centro de IoT, incluido el protocolo que debe usar (desde la perspectiva del desarrollador, es una opción de configuración simple).

La biblioteca **IoTHubClient** también ofrece un control preciso sobre el modo de serializar los eventos que el dispositivo envía al Centro de IoT. En algunos casos esto es una ventaja, pero en otros esto es un detalle de implementación con los que no desea preocuparse. Si ese es el caso, podría considerar el uso la biblioteca **serializer**, que describiremos en la sección siguiente.

## Serializer

Conceptualmente, la biblioteca **serializer** se encuentra encima de la biblioteca **IoTHubClient** en el SDK. Usa la biblioteca **IoTHubClient** para la comunicación subyacente con el Centro de IoT, pero incorpora funcionalidades de modelado que eliminan la carga de tratar con la serialización de mensajes del desarrollador. Un ejemplo ilustra mejor el funcionamiento de esta biblioteca.

Dentro de la carpeta **serializer**, en el repositorio azure-iot-sdks, hay una carpeta **samples** que contiene una aplicación denominada **simplesample\_amqp**. La versión de Windows de este ejemplo incluye la solución de Visual Studio siguiente:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Al igual que con el ejemplo anterior, esta incluye varios paquetes de NuGet:

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

Se han analizado la mayoría de ellos en el ejemplo anterior, pero **Microsoft.Azure.IoTHub.Serializer** es nuevo. Se requiere cuando se usa la biblioteca **serializer**.

Encontrará la implementación de la aplicación de ejemplo en el archivo **simplesample\_amqp.c**:

Las secciones siguientes le guiarán por las partes principales de este ejemplo.

### Inicialización de la biblioteca

Para empezar a trabajar con la biblioteca **serializer**, debe llamar a las API de inicialización:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

La llamada a la función **serializer\_init** es una llamada única usada para inicializar la biblioteca subyacente. Después, llame a la función **IoTHubClient\_CreateFromConnectionString**, que es la misma API del ejemplo **IoTHubClient**. Esta llamada establece la cadena de conexión del dispositivo (aquí se elige también el protocolo que desea usar). Observe que en este ejemplo se usa AMQP como transporte, pero se podría haber usado HTTP.

Por último, llame a la función **CREATE\_MODEL\_INSTANCE**. **WeatherStation** es el espacio de nombres del modelo y **ContosoAnemometer** es el nombre del modelo. Una vez que se crea la instancia del modelo, esta se puede usar para iniciar el envío de eventos y la recepción de mensajes. Sin embargo, es importante entender qué es un modelo.

### Definición del modelo

Un modelo de la biblioteca **serializer** define los eventos que el dispositivo puede enviar al Centro de IoT y los mensajes, denominados *acciones* en el lenguaje de modelado, que puede recibir. Un modelo se define mediante un conjunto de macros de C como en la aplicación de ejemplo **simplesample\_amqp**:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Ambas macros **BEGIN\_NAMESPACE** y **END\_NAMESPACE** toman el espacio de nombres del modelo como argumento. Se espera que cualquier cosa entre estas macros sea la definición de los modelos y las estructuras de datos que los modelos usan.

En este ejemplo, hay un único modelo denominado **ContosoAnemometer**. Este modelo define dos eventos que el dispositivo puede enviar al Centro de IoT: **DeviceId** y **WindSpeed**. También define tres acciones (mensajes) que el dispositivo puede recibir: **TurnFanOn**, **TurnFanOff** y **SetAirResistance**. Cada evento tiene un tipo y cada acción tiene un nombre (y opcionalmente un conjunto de parámetros).

Los eventos y las acciones definidos en el modelo definen una superficie de API que puede usar para enviar eventos al Centro de IoT, así como responder a mensajes que se envían al dispositivo. Esto se explica mejor con un ejemplo.

### Envío de eventos

El modelo define los eventos que puede enviar al Centro de IoT. En este ejemplo, eso significa que uno de los dos eventos se define mediante la macro **WITH\_DATA**. Por ejemplo, el envío de un evento **WindSpeed** a un Centro de IoT implica la realización de algunos pasos. El primero es establecer los datos que desea enviar:

```
myWeather->WindSpeed = 15;
```

El modelo que definimos anteriormente nos permite hacerlo estableciendo un miembro de una **estructura**. Luego, se serializa el evento que queremos enviar:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Este código serializa el evento a un búfer (al que hace referencia **destination**). Por último, se envía el evento al Centro de IoT con este código:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Se trata de una función auxiliar de la aplicación de ejemplo que envía el evento serializado al Centro de IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Este código es muy similar a lo que vimos en la aplicación **iothub\_client\_sample\_amqp**, donde se creó un mensaje a partir de una matriz de bytes y después se usó **IoTHubClient\_SendEventAsync** para enviarlo al Centro de IoT. Después, solo tenemos que liberar el identificador de mensaje y búfer de datos serializado que asignamos anteriormente.

Los parámetros segundo a último de **IoTHubClient\_SendEventAsync** es una referencia a una función de devolución de llamada a la que se llama cuando los datos se envían correctamente. Este es un ejemplo de una función de devolución de llamada:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

El segundo parámetro es un puntero al contexto de usuario, el mismo puntero que se pasa a **IoTHubClient\_SendEventAsync**. En este caso, el contexto es un contador sencillo, pero puede ser lo que quiera.

Esto es todo lo que se necesita para enviar eventos. Lo único que queda describir es cómo recibir mensajes.

### Recepción de mensajes

La recepción de un mensaje es similar a cómo funcionan los mensajes en la biblioteca **IoTHubClient**. En primer lugar, se registra una función de devolución de llamada de mensaje:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Luego, se escribe la función de devolución de llamada que se invoca cuando se recibe un mensaje:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Este código es repetitivo, lo que significa que es el mismo para cualquier solución. Esta función recibe el mensaje y se encarga de enrutarlo a la función adecuada mediante la llamada a **EXECUTE\_COMMAND**. La función a la que se llame en este punto depende de la definición de las acciones de nuestro modelo.

Cuando se define una acción en el modelo, es necesario implementar una función a la que se llama cuando el dispositivo recibe el mensaje correspondiente. Por ejemplo, si el modelo define esta acción:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Debe definir una función con esta firma:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Observe que el nombre de la función coincide con el nombre de la acción en el modelo y que los parámetros de la función coinciden con los parámetros especificados para la acción. El primer parámetro siempre es necesario y contiene un puntero a la instancia de nuestro modelo.

Cuando el dispositivo recibe un mensaje que coincide con esta firma, se llama a la función correspondiente. Por lo tanto, aparte de tener que incluir el código reutilizable de **IoTHubMessage**, para recibir mensajes solo hay que definir una función sencilla para cada acción definida en el modelo.

### Desinicialización de la biblioteca

Cuando haya terminado el envío de datos y la recepción de mensajes, puede desinicializar la biblioteca IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada una de estas tres funciones se corresponden con las tres funciones de inicialización descritas anteriormente. Llamar a estas API garantiza que se liberan los recursos asignados previamente.

## Pasos siguientes

En este artículo se han tratado los aspectos básicos del uso de las bibliotecas en el **SDK de dispositivo IoT de Azure para C**. Se le ha proporcionado información suficiente para comprender qué incluye el SDK, su arquitectura y cómo empezar a trabajar con los ejemplos de Windows. El siguiente artículo continúa la descripción del SDK y explica [más información sobre la biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

<!---HONumber=AcomDC_0302_2016-->