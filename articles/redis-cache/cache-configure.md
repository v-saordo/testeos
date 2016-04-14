<properties 
	pageTitle="Configuración de Caché en Redis de Azure"
	description="Descripción de la configuración predeterminada de Caché en Redis de Azure y más información sobre cómo configurar las instancias de Caché en Redis de Azure"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="dwrede"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="12/16/2015"
	ms.author="sdanie" />

# Configuración de Caché en Redis de Azure

En este tema se describe cómo revisar y actualizar la configuración de las instancias de Caché en Redis de Azure y se trata la configuración predeterminada del servidor Redis para las instancias de Caché en Redis de Azure.

>[AZURE.NOTE]Para obtener más información sobre la configuración y el uso de las características de caché Premium, vea [Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md), [Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md) y [Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

## Configuración de opciones de la memoria caché en Redis

Se puede tener acceso a las memorias caché en el [Portal de Azure](https://portal.azure.com) mediante la hoja **Examinar**.

![Caché en Redis de Azure - Hoja Examinar](./media/cache-configure/IC796920.png)

Haga clic en **Cachés en Redis** para ver sus cachés.

![Caché en Redis de Azure - Examinar lista en caché](./media/cache-configure/IC796921.png)

Seleccione la memoria caché que quiera para ver las propiedades de dicha memoria.

![Caché en Redis - Todas las configuraciones](./media/cache-configure/IC808312.png)

Haga clic en **Configuración** o en **Toda la configuración** para ver y configurar la memoria caché.

![Caché en Redis - Configuración](./media/cache-configure/IC808313.png)

## Propiedades

Haga clic en **Propiedades** para ver información sobre la memoria caché, incluidos los puertos y el extremo de la caché.

![Caché en Redis - Propiedades](./media/cache-configure/IC808314.png)

## Claves de acceso

Haga clic en **Claves de acceso** para ver o volver a generar las claves de acceso de la memoria caché. Estas claves, junto con el nombre de host y los puertos que aparecen en la hoja **Propiedades**, las usan los clientes que se conectan a la memoria caché.

![Caché en Redis - Claves de acceso](./media/cache-configure/IC808315.png)

## Puertos de acceso

El acceso no SSL está deshabilitado de forma predeterminada para las nuevas cachés. Para habilitar el puerto no SSL, haga clic en la hoja **Acceder a los puertos** y luego en **No**.

![Caché en Redis - Puertos de acceso](./media/cache-configure/IC808316.png)

## Nivel de precios

Haga clic en el **Nivel de precios** para ver o cambiar el plan de tarifa para su memoria caché. Para obtener más información sobre el escalado, vea [Escalado de Caché en Redis de Azure](cache-how-to-scale.md).

![Nivel de precios de Caché en Redis](./media/cache-configure/pricing-tier.png)

## Diagnóstico

Haga clic en **Diagnóstico** para configurar la cuenta de almacenamiento que se usa para almacenar diagnósticos de memoria caché.

![Caché en Redis - Diagnóstico](./media/cache-configure/IC808317.png)

Para obtener más información, vea [Supervisión de Caché en Redis de Azure](cache-how-to-monitor.md).

## Maxmemory-policy y maxmemory-reserved

Haga clic en la **directiva Maxmemory** para configurar las directivas de memoria para la memoria caché. La opción **maxmemory-policy** configura la directiva de expulsión para la memoria caché y **maxmemory-reserved** configura la memoria reservada para los procesos ajenos a la memoria caché.

![Caché en Redis - Directiva Maxmemory](./media/cache-configure/IC808318.png)

La **directiva Maxmemory** permite elegir entre las siguientes directivas de expulsión.

-	volatile-lru: se trata de la directiva predeterminada.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Para obtener más información sobre las directivas maxmemory, consulte las [directivas de expulsión](http://redis.io/topics/lru-cache#eviction-policies).

La opción **maxmemory-reserved** configura la cantidad de memoria en MB que se reserva para las operaciones ajenas a la memoria caché como, por ejemplo, la replicación durante la conmutación por error. También puede usarse cuando se tiene una alta relación de fragmentación. Esta opción le permite tener una experiencia más coherente de servidor Redis cuando varía la carga. Este valor debe establecerse más alto para cargas de trabajo con muchas operaciones de escritura. Cuando la memoria se reserva para dichas operaciones, no está disponible para el almacenamiento de datos en caché.

>[AZURE.IMPORTANT]La opción **maxmemory-reserved** solo está disponible para las memorias caché de nivel Standard y Premium.

## Notificaciones de espacio de claves (configuración avanzada)

Haga clic en **Configuración avanzada** para configurar las notificaciones de espacio de claves de Redis. Las notificaciones de espacio de claves permiten que los clientes reciban notificaciones cuando se producen determinados eventos.

![Caché en Redis - Configuración avanzada](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Las notificaciones de espacio de claves y el valor **notify-keyspace-event** solo están disponibles para las memorias caché de nivel Standard y Premium.

Para obtener más información, vea [Notificaciones de espacio de claves de Redis](http://redis.io/topics/notifications). Para obtener el código de ejemplo, vea el archivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) en el ejemplo [Hola a todos](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Persistencia de datos de Redis

Haga clic en **Persistencia de los datos en Redis** para habilitar, deshabilitar o configurar la persistencia de los datos para la caché premium.

![Persistencia de datos de Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar la persistencia en Redis, haga clic en **Habilitada** para habilitar la copia de seguridad RDB (base de datos de Redis). Para deshabilitar la persistencia en Redis, haga clic en **Deshabilitada**.

Para configurar el intervalo de copia de seguridad, seleccione una **Frecuencia de copia de seguridad** en la lista desplegable. Entre las opciones se incluyen **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** y **24 horas**. Este intervalo empieza la cuenta atrás cuando se completa correctamente la operación de copia de seguridad anterior y se inicia cuando se produce una nueva copia de seguridad.

Haga clic en **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento que se va a usar y elija la **Clave principal** o la **Clave secundaria** que se usará en la lista desplegable **Clave de almacenamiento**. Debe elegir una cuenta de almacenamiento en la misma región que la memoria caché y se recomienda una cuenta de **Almacenamiento Premium** porque el almacenamiento premium tiene un mayor rendimiento. En cualquier momento que se vuelva a generar la clave de almacenamiento para su persistencia, debe volver a elegir la clave que quiera en la lista desplegable **Clave de almacenamiento**.

Haga clic en **Aceptar** para guardar la configuración de persistencia.

>[AZURE.IMPORTANT]La persistencia de los datos en Redis solo está disponible para las memorias cachés premium. Para más información, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md).

<a name="cluster-size"></a>
## Tamaño del Clúster en Redis

Haga clic en **Tamaño del Clúster en Redis (VERSIÓN PRELIMINAR)** para cambiar el tamaño del clúster para una caché premium en ejecución con la agrupación de clústeres habilitada.

>[AZURE.NOTE]Tenga en cuenta que, a pesar de que el nivel Premium de Caché en Redis de Azure se publicó con disponibilidad general, la característica Tamaño del clúster en Redis está actualmente en la versión preliminar.

![Tamaño del clúster en Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para cambiar el tamaño del clúster, use el control deslizante o escriba un número entre 1 y 10 en el cuadro de texto **Número de particiones** y haga clic en **Aceptar** para guardar.

>[AZURE.IMPORTANT]La agrupación en clústeres de Redis solo está disponible para las memorias cachés premium. Para más información, vea [Cómo configurar la agrupación en clústeres de Redis para una Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).


## Usuarios y etiquetas

![Caché en Redis - Usuarios y etiquetas](./media/cache-configure/IC808320.png)

La sección **Usuarios** del Portal de Azure ofrece compatibilidad con el control de acceso basado en roles (RBAC) con el fin de que las organizaciones satisfagan sus requisitos de administración de acceso de forma simple y precisa. Para más información, vea [Control de acceso basado en roles en el Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

La sección **Etiquetas** le ayuda a organizar sus recursos. Para más información, vea [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

## Configuración predeterminada del servidor Redis

Las nuevas instancias de Caché en Redis de Azure se configuran con los siguientes valores de configuración de Redis predeterminados.

>[AZURE.NOTE]No se puede cambiar la configuración de esta sección con el método `StackExchange.Redis.IServer.ConfigSet`. Si se llama a este método con uno de los comandos de esta sección, se produce una excepción similar a la siguiente:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Es posible configurar aquellos valores que permitan esta opción, como **max-memory-policy**, a través del Portal de Azure o las herramientas de administración de la línea de comandos como la CLI de Azure o PowerShell.

|Configuración|Valor predeterminado|Descripción|
|---|---|---|
|bases de datos|16|La base de datos predeterminada es DB 0. Se puede seleccionar una diferente por conexión mediante connection.GetDataBase(dbid), donde dbid es un número entre 0 y 15.|
|maxclients|Depende del plan de tarifa<sup>1</sup>.|Se trata del número máximo de clientes conectados que se permiten al mismo tiempo. Una vez alcanzado el límite, Redis cerrará todas las nuevas conexiones y enviará un error de "número máximo de clientes alcanzado".|
|maxmemory-policy|volatile-lru|Directiva Maxmemory es la opción que configura el modo en que Redis seleccionará lo que se debe quitar cuando se alcanza el valor de maxmemory (el tamaño de la oferta de memoria caché que seleccionó al crear la memoria caché). Con Caché en Redis de Azure la opción predeterminada es volatile-lru, que quita las claves con una fecha de expiración definida mediante un algoritmo LRU. Esta opción puede configurarse en el Portal de Azure. Para más información, vea [Maxmemory-policy y maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Los algoritmos LRU y TTL mínimo no son precisos sino aproximados (con el fin de ahorrar memoria), para que también pueda seleccionar el tamaño de muestra para comprobar. Por ejemplo, Redis comprobará de manera predeterminada tres claves y seleccionará la usada menos recientemente.|
|lua-time-limit|5\.000|Tiempo máximo de ejecución de un script Lua en milisegundos. Si se alcanza el tiempo máximo de ejecución, Redis registrará que un script está aún en ejecución una vez transcurrido el tiempo máximo permitido y empezará a responder a las consultas con un error.|
|lua-event-limit|500|Se trata del tamaño máximo de la cola de eventos de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Los límites de búfer de salida de cliente pueden usarse para forzar la desconexión de clientes que, por algún motivo (un motivo habitual es que un cliente de Pub/Sub no puede consumir mensajes tan rápidamente como el publicador los crea), no leen datos del servidor con suficiente rapidez. Para más información, vea [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<sup>1</sup>`maxclients` es diferente para cada plan de tarifa de Caché en Redis de Azure.

-	Cachés Basic y Standard
	-	Memoria caché C0 (250 MB): hasta 256 conexiones
	-	Memoria caché C1 (1 GB): hasta 1000 conexiones
	-	Memoria caché C2 (2,5 GB): hasta 2000 conexiones
	-	Memoria caché C3 (6 GB): hasta 5000 conexiones
	-	Memoria caché C4 (13 GB): hasta 10 000 conexiones
	-	Memoria caché C5 (26 GB): hasta 15 000 conexiones
	-	Memoria caché C6 (53 GB): hasta 20 000 conexiones
-	Cachés Premium
	-	P1 (6 GB - 60 GB): hasta 7.500 conexiones
	-	P2 (13 GB - 130 GB): hasta 15.000 conexiones
	-	P3 (26 GB - 260 GB): hasta 30.000 conexiones
	-	P4 (53 GB - 530 GB): hasta 40.000 conexiones

## No se admiten comandos de Redis en Caché en Redis de Azure

>[AZURE.IMPORTANT]Dado que la configuración y la administración de instancias de Caché en Redis de Azure se administra por Microsoft, se deshabilitan los siguientes comandos. Si intenta invocarlos, recibirá un mensaje de error similar a `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	GUARDAR
>-	SHUTDOWN
>-	SLAVEOF

Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands).

## Consola de Redis

Puede emitir comandos de forma segura para sus instancias de Caché en Redis de Azure con la **consola de Redis**, que está disponible para las memorias caché Standard y Premium.

>[AZURE.IMPORTANT]La Consola de Redis no funciona con red virtual o agrupación en clústeres.
>
>-	[Red virtual](cache-how-to-premium-vnet.md): cuando la memoria caché forma parte de una red virtual, solo los clientes de la red virtual pueden tener acceso a la memoria caché. Dado que la Consola de Redis usa al cliente de redis-cli.exe hospedado en máquinas virtuales que no forman parte de su red virtual, no se puede conectar a su memoria caché.
>-	[Agrupación en clústeres](cache-how-to-premium-clustering.md): la Consola de Redis usa el cliente de redis-cli.exe que no es compatible con la agrupación en clústeres en este momento. La utilidad redis-cli de la rama [inestable](http://redis.io/download) del repositorio de Redis en GitHub implementa compatibilidad básica cuando se inicia con el conmutador `-c`. Para más información, vea [Jugar con el clúster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) en [http://redis.io](http://redis.io) en el [Tutorial del clúster de Redis](http://redis.io/topics/cluster-tutorial).

Para el acceso a la Consola de Redis, haga clic en **Consola** desde la hoja **Caché en Redis**.

![Consola de Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos con una instancia de la memoria caché, basta con escribir en el comando que desea en la consola.

![Consola de Redis](./media/cache-configure/redis-console.png)

Para una lista de los comandos de Redis deshabilitados para Caché en Redis de Azure, vea la sección anterior [No se admiten comandos de Redis en Caché en Redis de Azure](#redis-commands-not-supported-in-azure-redis-cache). Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands).

## Pasos siguientes
-	Para más información sobre cómo trabajar con los comandos de Redis, vea [¿Cómo puedo ejecutar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=AcomDC_1223_2015-->