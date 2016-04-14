<properties
	pageTitle="SDK de dispositivos IoT de Azure para C: serializador | Microsoft Azure"
	description="Más información sobre la biblioteca de serializador en el SDK de dispositivos IoT de Azure para C"
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

# SDK de dispositivo IoT de Microsoft Azure para C: más información sobre el serializador

En el [primer el artículo](iot-hub-device-sdk-c-intro.md) de esta serie se presentaba el **SDK de dispositivos IoT de Azure**. A este le siguió un artículo que proporcionaba una descripción más detallada de [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). En este artículo terminaremos de analizar el SDK y proporcionaremos una descripción más detallada del componente que queda: la biblioteca de **serializador**.

En el artículo de introducción se describía cómo usar la biblioteca de **serializador** para enviar eventos al Centro de IoT y recibir mensajes de él. En este artículo ampliaremos esta discusión con una explicación más completa de cómo modelar los datos con el macrolenguaje de **serializador**. También incluiremos en él más detalles sobre cómo la biblioteca serializa los mensajes (y, en algunos casos, cómo se puede controlar el comportamiento de serialización). Asimismo, describiremos algunos parámetros que se pueden modificar que determinan el tamaño de los modelos que se crean.

Terminaremos examinando de nuevo algunos de los temas tratados en artículos anteriores, como el control de mensajes y propiedades. Como veremos, estas características funcionan del mismo modo con la biblioteca de **serializador** que con la biblioteca de **IoTHubClient**.

Todo lo que se describe en este artículo se basa en los ejemplos del SDK del **serializador**. Si desea continuar, consulte las aplicaciones **simplesample\_amqp** y **simplesample\_http** que se incluyen en el SDK de dispositivo IoT de Azure para C.

## El lenguaje de modelado

En el [artículo de introducción](iot-hub-device-sdk-c-intro.md) de esta serie se presentaba el lenguaje de modelado del **SDK de dispositivos IoT de Azure para C** mediante el ejemplo proporcionado en la aplicación **simplesample\_amqp**:

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

Como puede observar, el lenguaje de modelado se basa en macros de C. La definición comienza siempre con **BEGIN\_NAMESPACE** y termina siempre con **END\_NAMESPACE**. Es habitual asignar un nombre al espacio de nombres de su compañía o, como en este ejemplo, al proyecto en el que trabaja.

Lo que hay dentro del espacio de nombres son definiciones de modelo. En este caso, hay un único modelo para un anemómetro. Una vez más, el modelo puede tener cualquier nombre pero, normalmente, se denomina para el dispositivo o el tipo de datos que quiere intercambiar con el Centro de IoT.

Los modelos contienen una definición de los eventos que puede insertar en el Centro de IoT (los *datos*), así como de los mensajes que puede recibir de él (las *acciones*). Como puede ver por el ejemplo, los eventos tienen un tipo y un nombre; las acciones tienen un nombre y parámetros opcionales (cada uno con un tipo).

Lo que no se demuestra en este ejemplo son tipos de datos adicionales que se admiten en el SDK. Los trataremos a continuación.

> [AZURE.NOTE] El Centro de IoT hace referencia a los datos que un dispositivo envía a dicho centro como *eventos*, mientras que el lenguaje de modelado hace referencia a ellos como *datos* (definidos mediante **WITH\_DATA**). De igual forma, el Centro de IoT hace referencia a los datos que se envían a los dispositivos como *mensajes*, mientras que el modelado de datos hace referencia a ellos como *acciones* (definidas mediante **WITH\_ACTION**). Tenga en cuenta que estos términos pueden usarse indistintamente en este artículo.

### Tipos de datos admitidos

Se admiten los siguientes tipos de datos en modelos creados con la biblioteca de **serializador**:

| Escriba | Descripción |
|-------------------------|----------------------------------------|
| double | número de punto flotante de doble precisión |
| int | entero de 32 bits |
| float | número de punto flotante de precisión simple |
| long | entero largo |
| int8\_t | entero de 8 bits |
| int16\_t | entero de 16 bits |
| int32\_t | entero de 32 bits |
| int64\_t | entero de 64 bits |
| booleano | boolean |
| ascii\_char\_ptr | Cadena ASCII |
| EDM\_DATE\_TIME\_OFFSET | desplazamiento de fecha y hora |
| EDM\_GUID | GUID |
| EDM\_BINARY | binary |
| DECLARE\_STRUCT | tipo de dato complejo |

Comencemos con el último tipo de datos. **DECLARE\_STRUCT** permite definir tipos de datos complejos, que son agrupaciones de los otros tipos primitivos. Estas agrupaciones nos permiten definir un modelo parecido a este:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nuestro modelo contiene un evento de datos único de tipo **TestType**. **TestType** es un tipo complejo que incluye varios miembros que, de forma colectiva, muestran los tipos primitivos admitidos por el lenguaje de modelado de **serializador**.

Con un modelo como este podemos escribir código para enviar datos al Centro de IoT, que tendría este aspecto:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Básicamente, lo que hacemos es asignar un valor a todos los miembros de la estructura **Test** y, luego, llamar a **SendAsync** para enviar el evento de datos de **Test** a la nube. **SendAsync** es una función auxiliar que envía un solo evento de datos al Centro de IoT:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
	unsigned char* destination;
	size_t destinationSize;
	if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
	{
		// null terminate the string
		char* destinationAsString = (char*)malloc(destinationSize + 1);
		if (destinationAsString != NULL)
		{
			memcpy(destinationAsString, destination, destinationSize);
			destinationAsString[destinationSize] = '\0';
			IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
			if (messageHandle != NULL)
			{
				IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

				IoTHubMessage_Destroy(messageHandle);
			}
			free(destinationAsString);
		}
		free(destination);
	}
}
```

Esta función serializa el evento de datos dado y lo envía al Centro de IoT mediante **IoTHubClient\_SendEventAsync**. Este es el mismo código que hemos examinado en artículos anteriores (**SendAsync** encapsula la lógica en una función adecuada).

Otra función auxiliar que se usa en el código anterior es **GetDateTimeOffset**. Esta función transforma el tiempo especificado en un valor de tipo **EDM\_DATE\_TIME\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
	struct tm newTime;
	gmtime_s(&newTime, &time);
	EDM_DATE_TIME_OFFSET dateTimeOffset;
	dateTimeOffset.dateTime = newTime;
	dateTimeOffset.fractionalSecond = 0;
	dateTimeOffset.hasFractionalSecond = 0;
	dateTimeOffset.hasTimeZone = 0;
	dateTimeOffset.timeZoneHour = 0;
	dateTimeOffset.timeZoneMinute = 0;
	return dateTimeOffset;
}
```

Si ejecuta este código, se envía el siguiente mensaje al Centro de IoT:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observe que la serialización es JSON, que es el formato generado por la biblioteca de **serializador**. Observe también que cada miembro del objeto JSON serializado coincide con los miembros de **TestType** que definimos en nuestro modelo. Los valores también coinciden exactamente con los usados en el código. Sin embargo, observe que los datos binarios están codificados en base64: "AQID" es la codificación en base64 de {0 x 01, 0 x 02, 0 x 03}.

En este ejemplo se demuestra la ventaja de usar la biblioteca de **serializador**, y es que nos permite enviar JSON a la nube, sin tener que tratar explícitamente con la serialización en nuestra aplicación. De lo único de lo que nos tenemos que preocupar es de configurar los valores de los eventos de datos de nuestro modelo y, luego, llamar a API sencillas para enviar esos eventos a la nube.

Con esta información podemos definir modelos que comprendan el intervalo de tipos de datos compatibles, incluidos los tipos complejos (podríamos incluso incluir tipos complejos dentro de otros tipos complejos). No obstante, el JSON serializado generado por el ejemplo anterior nos lleva a un punto importante. *El modo en que* enviamos datos con la biblioteca de **serializador** determina exactamente cómo se forma el JSON. Este punto en particular es de lo que hablaremos a continuación.

## Más información sobre la serialización

En la sección anterior se resalta un ejemplo de la salida generada por la biblioteca de **serializador**. En esta sección explicaremos cómo la biblioteca serializa los datos y cómo puede controlar este comportamiento mediante las API de serialización.

Para avanzar en la discusión sobre la serialización, trabajaremos con un nuevo modelo basado en un termostato. Primero vamos a proporcionar alguna información de contexto sobre el escenario que tratamos de abordar.

Queremos modelar un termostato que mida la temperatura y la humedad. Cada fragmento de datos se va a enviar al Centro de IoT de una manera diferente. De forma predeterminada, el termostato introduce un evento de temperatura cada 2 minutos y un evento de humedad, cada 15 minutos. Cuando se introduce uno de estos eventos, se debe incluir una marca de tiempo que indique el tiempo durante el cual se midió la temperatura o humedad correspondientes.

Dado este escenario, demostraremos dos formas diferentes de modelar los datos y explicaremos el efecto que tiene el modelado en el resultado serializado.

### Modelo 1

Esta es la primera versión de un modelo que admite el escenario anterior:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Observe que el modelo incluye dos eventos de datos: **Temperature** y **Humidity**. A diferencia de los ejemplos anteriores, el tipo de cada evento es una estructura definida mediante **DECLARE\_STRUCT**. **TemperatureEvent** incluye una medida de la temperatura y una marca de tiempo; **HumidityEvent** contiene una medida de humedad y una marca de tiempo. Este modelo nos proporciona una manera natural de modelar los datos para el escenario descrito anteriormente. Cuando enviemos un evento a la nube, enviaremos un par temperatura/marca de tiempo o humedad/marca de tiempo.

Podemos enviar un evento de temperatura a la nube mediante un código similar al siguiente:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Usaremos valores codificados de forma rígida para temperatura y humedad en el código de ejemplo; pero imagine que en realidad vamos a recuperar estos valores mediante el muestreo de los sensores correspondientes en el termostato.

El código anterior usa la aplicación auxiliar **GetDateTimeOffset** que se especificó anteriormente. Por motivos que más tarde aclararemos, este código separa explícitamente la tarea de serializar y enviar el evento. El código anterior serializa el evento de temperatura en un búfer. Entonces, **sendMessage** es una función auxiliar (incluida en **simplesample\_amqp**) que envía el evento al Centro de IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Este código es un subconjunto de la aplicación auxiliar **SendAsync** descrita en la sección anterior, por lo que no volveremos aquí sobre ella de nuevo.

Al ejecutar el código anterior para enviar el evento de temperatura, esta forma serializada del evento se envía al Centro de IoT:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vamos a enviar una temperatura que es de tipo **TemperatureEvent** y esa estructura contiene un miembro **Temperature** y **Time**. Esto se refleja directamente en los datos serializados.

Del mismo modo, podemos enviar un evento de humedad con este código:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

La forma serializada que se envía al Centro de IoT tiene este aspecto:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Nuevamente, es lo que se esperaba.

Con este modelo, puede imaginar lo fácil que se podrían agregar eventos adicionales. Define más estructuras mediante **DECLARE\_STRUCT** e incluye el evento correspondiente en el modelo mediante **WITH\_DATA**.

Ahora vamos a modificar el modelo para que incluya los mismos datos pero con una estructura diferente.

### Modelo 2

Considere este modelo como una alternativa al anterior:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

En este caso hemos eliminado las macros **DECLARE\_STRUCT** y simplemente va a definir los elementos de datos de nuestro escenario con tipos simples del lenguaje de modelado.

Solo por el momento, omitiremos el evento **Time**. Dejando este a un lado, este es el código para especificar **Temperature**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envía el siguiente evento serializado al Centro de IoT:

```
{"Temperature":75}
```

Y el código para enviar el evento de humedad tiene este aspecto:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envía esto al Centro de IoT:

```
{"Humidity":45}
```

Hasta ahora, no hay novedades. Ahora vamos a cambiar cómo usamos la macro SERIALIZE.

La macro **SERIALIZE** puede tomar varios eventos de datos como argumentos. Esto nos permite serializar los eventos **Temperature** y **Humidity** juntos y enviarlos al Centro de IoT en una llamada:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Podría suponer que el resultado de este código es que dos eventos de datos se envían al Centro de IoT:

[

{"Temperature":75},

{"Humidity":45}

]

En otras palabras, puede esperar que este código sea lo mismo que enviar **Temperature** y **Humidity** por separado. Solo por comodidad se pasan ambos eventos a **SERIALIZE** en la misma llamada. Sin embargo, ese no es el caso. Por el contrario, el código anterior envía este único evento de datos al Centro de IoT:

{"Temperature":75, "Humidity":45}

Esto puede parecer extraño debido a que nuestro modelo define **Temperature** y **Humidity** como dos eventos *independiente*:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Más concretamente, no modelamos estos eventos cuando **Temperature** y **Humidity** están en la misma estructura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Si usáramos este modelo, sería más fácil entender cómo **Temperature** y **Humidity** se enviarían en el mismo mensaje serializado. No obstante, es posible que no quede claro por qué esto funciona de esa forma cuando se pasan ambos eventos de datos a **SERIALIZE** con el modelo 2.

Este comportamiento es más fácil de entender si sabe las suposiciones que realiza la biblioteca de **serializador**. Para que esto tenga sentido, volvamos a nuestro modelo:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Considere este modelo en términos de orientación a objetos. En este caso estamos modelando un dispositivo físico (un termostato) y ese dispositivo incluye atributos como **Temperature** y **Humidity**.

Podemos enviar todo el estado de nuestro modelo con código como el siguiente:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Suponiendo que los valores de Temperature, Humidity y Time estén establecidos, veríamos que se envía un evento como éste al Centro de IoT:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

A veces quizás solo quiera enviar *algunas* propiedades del modelo a la nube (esto es especialmente cierto si el modelo contiene un gran número de eventos de datos). Resulta útil enviar solo un subconjunto de eventos de datos, como en el ejemplo anterior:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Se genera exactamente el mismo evento serializado que si hubiéramos definido **TemperatureEvent** con un miembro **Temperature** y **Time**, como hicimos con el modelo 1. En este caso, pudimos generar exactamente el mismo evento serializado con un modelo diferente (modelo 2) porque llamamos a **SERIALIZE** de una manera diferente.

Lo importante aquí es que si se pasan varios eventos de datos a **SERIALIZE,**, se supone que cada evento es una propiedad en un único objeto JSON.

El mejor enfoque dependerá de usted y de su forma de pensar sobre el modelo. Si envía "eventos" a la nube y cada evento contiene un conjunto definido de propiedades, entonces el primer enfoque cobra mucho sentido. En ese caso, usaría **DECLARE\_STRUCT** para definir la estructura de cada evento y luego incluirlos en el modelo con la macro **WITH\_DATA**. A continuación, enviaría cada evento como hicimos en el primer ejemplo anterior. En este enfoque, solo pasaría un evento de datos a **SERIALIZADOR**.

Si piensa en su modelo como orientado a objetos, entonces el segundo enfoque puede ser adecuado para usted. En este caso, los elementos definidos mediante **WITH\_DATA** son las "propiedades" del objeto. Pasaría cualquier subconjunto de eventos de su elección a **SERIALIZE**, según la cantidad de estado del "objeto" que quiera enviar a la nube.

Ningún enfoque es bueno ni malo. Lo importante es saber cómo funciona la biblioteca de **serializador** y seleccionar el enfoque de modelado que mejor se ajuste a sus necesidades.

## Administración de mensajes

Hasta ahora solo hemos examinado en este artículo el envío de eventos al Centro de IoT y no hemos hablado de la recepción de mensajes. La razón de ello es que lo que necesitamos saber sobre la recepción de mensajes se ha tratado en gran medida en un [artículo anterior](iot-hub-device-sdk-c-intro.md). Como se explicaba en ese artículo, los mensajes se procesan mediante el registro de una función de devolución de llamada de mensaje:

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

Esta implementación de **IoTHubMessage** llama a la función específica para cada acción del modelo. Por ejemplo, si el modelo define esta acción:

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

Se llama a **SetAirResistance** cuando ese mensaje se envía al dispositivo.

Lo que no hemos explicado aún es cuál es el aspecto de la versión serializada del mensaje. En otras palabras, si desea enviar un mensaje **SetAirResistance** a su dispositivo, ¿cómo se hará?

Si va a enviar un mensaje a un dispositivo, debería hacerlo a través del SDK del servicio IoT de Azure. Necesitará saber aun así qué cadena se envía para invocar una acción determinada. El formato general para enviar un mensaje aparece del modo siguiente:

```
{"Name" : "", "Parameters" : "" }
```

Si envía un objeto JSON serializado con dos propiedades: **Name** es el nombre de la acción (mensaje) y **Parameters** contiene los parámetros de esa acción.

Por ejemplo, para invocar **SetAirResistance**, puede enviar este mensaje a un dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

El nombre de la acción debe coincidir exactamente con una acción definida en el modelo. Los nombres de los parámetros deben coincidir también. Tenga en cuenta que existe una distinción de mayúsculas y minúsculas. **Name** y **Parameters** van siempre en mayúsculas. Asegúrese de que las mayúsculas y minúsculas coincidan en el nombre de la acción y los parámetros del modelo. En este ejemplo, el nombre de la acción es "SetAirResistance" y no "setairresistance".

En esta sección se describe todo lo que necesita saber al enviar eventos y recibir mensajes con la biblioteca de **serializador**. Antes de continuar, vamos a examinar algunos parámetros que puede configurar que controlan lo grande que es su modelo.

## Configuración de macros

Si está usando la biblioteca de **serializador**, una parte importante del SDK que se debe tener en cuenta se encuentra en la biblioteca azure-c-shared-utility: Si ha clonado el repositorio Azure-iot-sdks desde GitHub con la opción recursiva, encontrará esta biblioteca de utilidad compartida aquí:

```
.\\c\\azure-c-shared-utility
```

Si no ha clonado la biblioteca, podrá encontrarla [aquí](https://github.com/Azure/azure-c-shared-utility).

Dentro de la biblioteca de utilidad compartida, encontrará la siguiente carpeta:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta carpeta contiene una solución de Visual Studio denominada **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

El programa de esta solución genera el archivo **macro\_utils.h**. Hay un archivo macro\_utils.h predeterminado incluido con el SDK. Esta solución le permite modificar algunos parámetros y luego volver a crear el archivo de encabezado en función de ellos.

Los dos parámetros clave que se deben tener en cuenta son **nArithmetic** y **nMacroParameters**, que se definen en estas dos líneas que se encuentran en macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Estos valores son los parámetros predeterminados incluidos con el SDK. Cada parámetro tiene el significado siguiente:

-   nMacroParameters: controla el número de parámetros que puede tener en una definición de macro DECLARE\_MODEL.

-   nArithmetic: controla el número total de miembros permitidos en un modelo.

El motivo de que estos parámetros sean importantes es que controlan lo grande que puede ser el modelo. Por ejemplo, tenga en cuenta esta definición de modelo:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como se mencionó antes, **DECLARE\_MODEL** es simplemente una macro de C. El nombre del modelo y la declaración **WITH\_DATA** (otra macro más) son parámetros de **DECLARE\_MODEL**. **nMacroParameters** define el número de parámetros que se pueden incluir en **DECLARE\_MODEL**. Esto permite definir de manera efectiva cuántas declaraciones de evento y acción puede tener. Con el límite predeterminado de 124, significa que puede definir un modelo con una combinación de unas 60 acciones y eventos de datos. Si intenta superar este límite, obtendrá errores de compilación parecidos a estos:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

El parámetro **nArithmetic** tiene más que ver con el funcionamiento interno del lenguaje de macros que con la aplicación. Controla el número total de miembros que puede tener en el modelo, incluidas las macros **DECLARE\_STRUCT**. Si comienza a ver errores del compilador como este, debería intentar aumentar el valor de **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Si desea cambiar estos parámetros, modifique los valores del archivo macro\_utils.tt, vuelva a compilar la solución macro\_utils\_h\_generator.sln y ejecute el programa compilado. Al hacerlo, se genera un nuevo archivo macro\_utils.h y se coloca en el directorio .\\common\\inc.

Para poder usar la nueva versión de macro\_utils.h, quite el paquete NuGet de **serializador** de la solución y, en su lugar, incluya el proyecto de Visual Studio de **serializador**. Esto permite a su código compilarse con el código fuente de la biblioteca de serializador. Incluye la macro\_utils.h actualizada. Si desea hacer esto para **simplesample\_amqp**, empiece quitando el paquete NuGet para la biblioteca de serializador de la solución:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Luego agregue este proyecto a la solución de Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj

Cuando haya terminado, su solución debe tener este aspecto:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Ahora, cuando se compila la solución, la macro\_utils.h actualizada se incluye en el archivo binario.

Tenga en cuenta que, si se aumentan estos valores mucho, se podrían exceder los límites del compilador. En este punto, **nMacroParameters** es el parámetro principal del que nos tenemos que ocupar. La especificación C99 especifica que se permiten 127 parámetros como mínimo en una definición de macro. El compilador de Microsoft sigue la especificación exactamente (y tiene un límite de 127) por lo que no podrá aumentar **nMacroParameters** por encima de su valor predeterminado. Es posible que otros compiladores le permitan hacerlo (por ejemplo, el compilador GNU admite un límite más alto).

Hasta ahora hemos visto casi todo lo que necesita saber sobre cómo escribir código con la biblioteca de **serializador**. Antes de concluir, vamos a revisar algunos temas de artículos anteriores sobre los que es posible que se esté preguntando.

## API de nivel inferior

La aplicación de ejemplo en la que se centró este artículo es **simplesample\_amqp**. Este ejemplo usa las API de nivel superior (las no "LL") para enviar eventos y recibir mensajes. Si usa estas API, se ejecuta un subproceso en segundo plano que se encarga de enviar eventos y recibir mensajes. Sin embargo, puede usar las API de nivel inferior (LL) para eliminar este subproceso en segundo plano y tener un control explícito sobre cuándo envía eventos o recibe mensajes de la nube.

Como se describió en un [artículo anterior](iot-hub-device-sdk-c-iothubclient.md), hay conjuntos de funciones que constan de API de nivel superior:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Estas API se muestran en **simplesample\_amqp**.

Hay un conjunto similar de API de nivel inferior.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

Tenga en cuenta que las API de nivel inferior funcionan exactamente como se describió en los artículos anteriores. Puede usar el primer conjunto de API, si quiere que un subproceso en segundo plano controle el envío de eventos y la recepción de mensajes. Si quiere un control explícito sobre cuándo envía y recibe datos del Centro de IoT, usará el segundo conjunto de API. Cualquier conjunto de API funciona igual de bien con la biblioteca de **serializador**.

Para ver un ejemplo de cómo se usan las API de nivel inferior con la biblioteca de **serializador**, vea la aplicación **simplesample\_http**.

## Otros temas

Algunos otros temas que merece la pena mencionar de nuevo son el control de propiedades, el uso de credenciales de dispositivo alternativas y las opciones de configuración. Todos estos temas se trataron en un [artículo anterior](iot-hub-device-sdk-c-iothubclient.md). Lo que hay que destacar es que todas estas características funcionan igual con la biblioteca de **serializador** que con la biblioteca de **IoTHubClient**. Por ejemplo, si desea adjuntar propiedades a un evento del modelo, use **IoTHubMessage\_Properties** y **Map**\_**AddorUpdate** de la misma forma que se describió anteriormente:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Da igual que el evento se generara con la biblioteca de **serializador** o se creara de forma manual con la biblioteca de **IoTHubClient**.

En cuanto a las credenciales de dispositivo alternativas, el uso de **IoTHubClient\_LL\_Create** funciona igual de bien que **IoTHubClient\_CreateFromConnectionString** para asignar **IOTHUB\_CLIENT\_HANDLE**.

Por último, si usa la biblioteca de **serializador**, puede establecer opciones de configuración con **IoTHubClient\_LL\_SetOption** lo mismo que hizo al usar la biblioteca de **IoTHubClient**.

Una característica que es exclusiva de la biblioteca de **serializador** son las API de inicialización. Antes de empezar a trabajar con la biblioteca, debe llamar a **serializer\_init**:

```
serializer_init(NULL);
```

Esto se hace justo antes de llamar a **IoTHubClient\_CreateFromConnectionString**.

Del mismo modo, cuando termine de trabajar con la biblioteca, la última llamada que hará es a **serializer\_deinit**:

```
serializer_deinit();
```

Por lo demás, todas las demás características enumeradas anteriormente funcionan igual en la biblioteca de **serializador** que en la biblioteca de **IoTHubClient**. Para más información sobre cualquiera de estos temas, consulte el [artículo anterior](iot-hub-device-sdk-c-iothubclient.md) de esta serie.

## Pasos siguientes

En este artículo se describe en detalle los aspectos únicos de la biblioteca de **serializador** contenida en el **SDK de dispositivo IoT de Azure para C**. Con la información proporcionada, habrá comprendido perfectamente cómo usar los modelos para enviar eventos y recibir mensajes del Centro de IoT.

Con esto también concluye la serie de tres partes sobre cómo desarrollar aplicaciones con el **SDK de dispositivo IoT de Azure para C**. Esta información debería ser suficiente no solo para ayudarle en sus primeros pasos sino también para proporcionarle una comprensión profunda de cómo funcionan las API. Para obtener información adicional, existen algunos ejemplos en el SDK que no se tratan aquí. Por lo demás, la [documentación del SDK](https://github.com/Azure/azure-iot-sdks) es un buen recurso para conseguir información adicional.

<!---HONumber=AcomDC_0302_2016-->