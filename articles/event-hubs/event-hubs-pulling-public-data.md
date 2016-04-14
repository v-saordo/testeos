<properties
   pageTitle="Extracción de datos públicos para Centros de eventos de Azure | Microsoft Azure"
   description="Información general de la importación de Centros de eventos desde un ejemplo web"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="spyros;sethm" />

# Extracción de datos públicos para Centros de eventos de Azure

En situaciones de Internet de las cosas (IoT), es habitual contar con dispositivos que se pueden programar para insertar datos en Azure, ya sea en un Centro de eventos de Azure o en un Centro de IoT. Estos dos tipos de centros son puntos de entrada a Azure para almacenar, analizar y visualizar con una gran variedad de herramientas que se encuentran disponibles en Microsoft Azure. Sin embargo, ambos requieren que el usuario inserte datos en ellos, con formato JSON y protegidos de manera específica. Esto plantea la siguiente pregunta: ¿qué puede hacer si desea recurrir a fuentes públicas o privadas para utilizar datos que se exponen como un servicio web o fuente de algún tipo, pero no tiene la posibilidad de cambiar el modo en que se publican los datos? Piense por ejemplo en el tiempo, el tráfico o la bolsa: no es posible pedirle a la agencia nacional de meteorología, la dirección de tráfico o el índice de la bolsa que configuren una inserción para su Centro de eventos. Para solucionar este problema, hemos escrito y publicado como código abierto un pequeño ejemplo en la nube que puede editar y modificar que extraerá los datos de algunas de estas fuentes y los insertará en su Centro de eventos. Cuando los tenga ahí, podrá hacer lo que quiera con ellos, con sujeción, por supuesto, a los términos de licencia del productor. Puede encontrar la aplicación [aquí](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Tenga en cuenta que el código de este ejemplo muestra solo cómo extraer datos de las típicas fuentes web y cómo escribir en un Centro de eventos de Azure. Esto NO pretende ser una aplicación de producción y no se ha intentado en modo alguno hacerlo apto para su uso en un entorno; se trata simplemente de un ejemplo dirigido al desarrollador para que lo pruebe por sí mismo. Además, la existencia de este ejemplo NO implica la recomendación de que deba **extraer** datos para Azure en lugar de **insertarlos** . Debe revisar los factores relacionados con la seguridad, el rendimiento, la funcionalidad y el costo antes de decidirse por una arquitectura integral.

## Estructura de la aplicación

La aplicación está escrita en C# y [la descripción del ejemplo](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contiene toda la información que necesita para modificar, crear y publicar la aplicación. En las secciones siguientes se proporciona información general de alto nivel sobre lo que hace la aplicación.

Comenzamos con la suposición de que tiene acceso a una fuente de datos. Por ejemplo, es posible que desee extraer los datos de tráfico del organismo competente o la información del tiempo de la agencia nacional de meteorología, ya sea para mostrar informes personalizados o para combinar estos con otros datos de la aplicación. También debe configurar un Centro de eventos de Azure y conocer la cadena de conexión necesaria para tener acceso al mismo.

Cuando se inicia la solución GenericWebToEH, esta lee un archivo de configuración (App.config) para obtener diferentes elementos:

1. La dirección URL, o una lista de direcciones URL, para el sitio de publicación de datos. Idealmente, se trata de un sitio que publica datos en JSON, como los que hacen referencia al Departamento de Transporte del Estado de Washington [aquí](http://www.wsdot.wa.gov/Traffic/api/). 
2. Las credenciales de la dirección URL, si fuera necesario. Muchas fuentes públicas no necesitan credenciales, o pueden poner las credenciales en la cadena de la dirección URL. Otras requieren que se proporcionen por separado. (Tenga en cuenta que solo puede especificar un conjunto de credenciales en esta aplicación, por lo que solo funcionará si especifica solo una dirección URL, no una lista de direcciones URL).
3. La cadena de conexión del Bus de servicio y el nombre del Centro de eventos en ese espacio de nombres de Bus de servicio, en el que insertará los datos. Puede encontrar esta información en el Portal de Azure clásico.
4. Un intervalo de espera, en milisegundos, para el intervalo entre sondeos del sitio de datos público. Es necesario pensar detenidamente esta configuración. Si sondea con muy poca frecuencia, se pueden perder datos; por otro lado, si sondea con demasiada frecuencia, se pueden obtener muchos datos repetitivos o, aún peor, se le podría bloquear como a un bot malintencionado. Tenga en cuenta la frecuencia con la que se actualiza el origen de datos: es posible que los datos sobre el tiempo o el tráfico se actualicen cada 15 minutos; sin embargo, las cotizaciones de la bolsa pueden actualizarse cada pocos segundos, dependiendo del lugar de donde se obtengan. 
5. Una marca para indicar a la aplicación si los datos se reciben como JSON o XML. Dado que es necesario insertar los datos en un Centro de eventos, la aplicación tiene un módulo para convertir XML en JSON antes de enviar.

Después de leer el archivo de configuración, la aplicación entra en un bucle: acceso al sitio web público, conversión de datos si fuera necesario, escritura en el Centro de eventos y luego espera durante el intervalo de inactividad antes de repetirlo de nuevo. Concretamente:

  * Lectura del sitio web público. Para recibir datos listos para enviar, la instancia de la clase RawXMLWithHeaderToJsonReader se usa desde Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Lee la transmisión de origen en el método GetData() y, a continuación, lo divide en partes más pequeñas (es decir, registros) con GetXmlFromOriginalText. Este método leerá datos XML, así como JSON o matriz de JSON bien formados. El procesamiento se inicia a continuación con la configuración MergeToXML desde App.config (predeterminado = vacío).
  * Los datos de envío y recepción se implementan como un bucle en el método Process() en Program.cs. Después de recibir resultados de GetData(), el método pone en cola los valores por separado en el Centro de eventos.

## Pasos siguientes

Para implementar la solución, clone o descargue la aplicación [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/), edite el archivo App.config, compílelo y finalmente, publíquelo. Una vez publicada la aplicación, podrá verla en ejecución en el Portal de Azure clásico (en el apartado de Servicios en la nube). Podrá cambiar algunos de los valores de configuración (como el destino del Centro de eventos y el intervalo de inactividad) en la pestaña **Configurar**.

Vea más ejemplos de Centros de eventos en la [galería de ejemplos de Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) y en [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).

<!---HONumber=AcomDC_0211_2016-->