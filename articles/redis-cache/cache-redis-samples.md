<properties 
	pageTitle="Ejemplos de Caché en Redis de Azure" 
	description="Obtener información acerca de cómo usar Caché en Redis de Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="sdanie"/>

# Ejemplos de Caché en Redis de Azure 

Este tema proporciona una lista de ejemplos de Caché en Redis de Azure y cubre escenarios como conexión a una caché, operaciones de lectura y escritura en una caché y uso de proveedores de Caché en Redis. Algunos de los ejemplos son proyectos que se pueden descargar y algunos otros proporcionan instrucciones paso a paso e incluyen fragmentos de código pero no vínculos a un proyecto que se puede descargar.

## Ejemplos Hello world

Los ejemplos de esta sección muestran los conceptos básicos de conexión a una instancia de Caché en Redis de Azure y de lectura y escritura de datos en la memoria caché usando una gran variedad de lenguajes y clientes de Redis.

El ejemplo [Hello world ](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)muestra cómo realizar diversas operaciones de caché mediante el cliente .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Este ejemplo lo siguiente:

-	Uso de distintas opciones de conexión
-	Lectura y escritura de objetos hacia y desde la memoria caché mediante operaciones sincrónicas y asincrónicas
-	Uso de comandos MGET/MSET de Redis para devolver valores de las claves especificadas
-	Realización de operaciones transaccionales de Redis
-	Trabajo con listas de Redis y conjuntos ordenados
-	Almacenamiento de objetos de .NET con serializadores JsonConvert
-	Uso de conjuntos de Redis para implementar el etiquetado
-	Trabajar con el Clúster en Redis

Para obtener más información, consulte la documentación de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) en github y para ver más escenarios de uso, consulte las pruebas unitarias [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

[Uso de Caché en Redis de Azure con Python](cache-python-get-started.md) muestra cómo comenzar a usar Caché en Redis de Azure con Python y el cliente r[edis-py](https://github.com/andymccurdy/redis-py).

El [ejemplo PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) muestra cómo comenzar a usar Caché en Redis de Azure con PHP y el cliente [predis](https://github.com/nrk/predis).

[Trabajar con objetos .NET en la memoria caché](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) muestra una forma de serializar objetos .NET de forma que pueda realizar operaciones de escritura y lectura con ellos en una instancia de Caché en Redis de Azure.

## Uso de Caché en Redis como un backplane de escalado horizontal para ASP.NET SignalRen

El ejemplo [Uso de Caché en Redis como un backplane de escalado horizontal para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) muestra cómo puede usar Caché en Redis de Azure como un backplane SignalR. Para obtener más información acerca de backplane, consulte [Escalado horizontal SignalR con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Ejemplo de consulta de cliente de Caché en Redis

Este ejemplo compara el rendimiento entre el acceso a datos desde una memoria caché y el acceso a datos desde almacenamiento de persistencia. Este ejemplo tiene dos proyectos.

-	[Demostración de cómo Caché en Redis puede mejorar el rendimiento almacenando datos en caché](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Inicialización de la base de datos y la memoria caché para la demostración](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Estado de sesión de ASP.NET y almacenamiento en caché de resultados

El ejemplo [Uso de Caché en Redis de Azure para almacenar SessionState y OutputCache de ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) muestra cómo usar Caché en Redis de Azure para almacenar la sesión y la memoria caché de salida usando los proveedores SessionState y OutputCache para Redis.

## Administración de Caché en Redis de Azure con Azure MAML

El ejemplo [Administración de Caché en Redis de Azure mediante bibliotecas de administración de Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) muestra cómo usar bibliotecas de administración de Azure para administrar (crear, actualizar y eliminar) su memoria caché.

## Ejemplo de personalización de supervisión

El [ejemplo de acceso a los datos de supervisión de Caché en Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) muestra cómo puede obtener acceso a datos de supervisión para su Caché en Redis de Azure fuera del Portal de Azure.

## Un clon de estilo Twitter escrito mediante PHP y Redis

El ejemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) es el Hello World de Redis. Es un clon de red social mínima de tipo Twitter escrito mediante Redis y PHP mediante el cliente [Predis](https://github.com/nrk/predis). El código fuente está diseñado para ser muy sencillo y al mismo tiempo para mostrar diferentes estructuras de datos de Redis.

## Supervisión del ancho de banda

El ejemplo [Supervisión del ancho de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite supervisar el ancho de banda utilizado en el cliente. Para medir el ancho de banda, ejecute el ejemplo en el equipo cliente de la caché, realice llamadas a la memoria caché y observe el ancho de banda notificado por el ejemplo de supervisión de ancho de banda.

<!---HONumber=AcomDC_1223_2015-->