<properties 
	pageTitle="Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium" 
	description="Aprenda a crear y a administrar la compatibilidad con la red Virtual para las instancias de la Caché en Redis de Azure de nivel Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="sdanie"/>

# Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium
Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché, incluido el nuevo nivel Premium.

El nivel premium de Caché en Redis de Azure incluye la agrupación en clústeres, la persistencia y la compatibilidad de red virtual (VNET). Una red virtual es una representación de su propia red en la nube. Cuando una instancia de Caché en Redis de Azure se configure con una red virtual, no será accesible públicamente y solo se podrá tener acceso a ella desde clientes dentro de la red virtual. En este artículo se describe cómo configurar la compatibilidad de red virtual para una instancia de Caché en Redis de Azure premium.

Para obtener información sobre otras características de caché premium, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md) y [Cómo configurar la agrupación en clústeres para Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).

## ¿Por qué la red virtual?
La implementación de la [Red virtual de Azure](https://azure.microsoft.com/services/virtual-network/) ofrece seguridad mejorada y aislamiento para su Caché en Redis de Azure, así como subredes, directivas de control de acceso y otras características para restringir aún más el acceso a Caché en Redis de Azure.

## Compatibilidad con redes virtuales
La compatibilidad de la red virtual está configurada en la hoja **Nueva caché en Redis** durante la creación de la memoria caché. Para crear una caché, inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Nuevo**->**Datos y almacenamiento**>**Caché en Redis**.

![Creación de una caché en Redis][redis-cache-new-cache-menu]

Para configurar la compatibilidad de redes virtuales, seleccione primero una de las caché **Premium** en la hoja **Elija su plan de tarifa**.

![Elegir su plan de tarifa][redis-cache-premium-pricing-tier]

La integración con red virtual de Caché en Redis de Azure se configura en la hoja **Red virtual (clásica)**. Desde aquí puede seleccionar una red virtual clásica existente. Para usar una red virtual nueva, siga los pasos descritos en [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) y luego regrese a la hoja de **Red virtual de Caché en Redis** para seleccionarla.

>[AZURE.NOTE] Caché en Redis de Azure funciona con redes virtuales clásicas. Para información sobre la creación de una red virtual clásica, consulte [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obtener información sobre cómo conectar redes virtuales clásicas a redes virtuales ARM, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).

Haga clic en **Red virtual (clásica)** en la hoja **Nueva caché en Redis** y seleccione la red virtual deseada de la lista desplegable para configurarla.

![Red virtual][redis-cache-vnet]

Seleccione la región deseada de la lista despegable **Subred**.

![Red virtual][redis-cache-vnet-ip]

El campo **Dirección IP estática** es opcional. Si no se especifica aquí, se elegirá una desde la subred seleccionada. Si quiere una dirección IP estática precisa, escriba la **Dirección IP estática** que quiera y haga clic en **Aceptar** para guardar la configuración de la red virtual. Si la dirección IP estática seleccionada ya está en uso, se mostrará un mensaje de error.

Una vez creada la memoria caché, puede ver la dirección IP y otra información de la red virtual si hace clic en **Red virtual** en la hoja **Configuración**.

![Red virtual][redis-cache-vnet-info]

>[AZURE.IMPORTANT] Para obtener acceso a su instancia de caché en Redis de Azure al usar una red virtual, pase la dirección IP estática de la memoria caché de la red virtual como el primer parámetro y pase un parámetro `sslhost` con el punto de conexión de la memoria caché. En el ejemplo siguiente, la dirección IP estática es `172.160.0.99` y el punto de conexión de la memoria caché es `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## P+F de red virtual de Caché en Redis de Azure

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Caché en Redis de Azure.

## ¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Caché en Redis de Azure y las redes virtuales?

Cuando la Caché en Redis de Azure se hospeda en una red virtual, se usan los puertos de la tabla siguiente. Si estos puertos están bloqueados, puede que la memoria caché no funcione correctamente. Tener bloqueados uno o varios de estos puertos es el problema más común de una configuración incorrecta cuando se utiliza la Caché en Redis de Azure en una red virtual.

| Puertos | Dirección | Protocolo de transporte | Propósito | Dirección IP remota |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Salida | TCP | Dependencias de Redis en Almacenamiento de Azure/PKI (Internet) | * |
| 53 | Salida | TCP/UDP | Dependencias de Redis en DNS (Internet y red virtual) | * |
| 6379, 6380 | Entrada | TCP | Comunicación del cliente con Redis, equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | Entrante o saliente | TCP | Detalles de implementación para Redis | VIRTUAL\_NETWORK |
| 8500 | Entrada | TCP/UDP | Equilibrio de carga de Azure | AZURE\_LOADBALANCER |
| 10221-10231 | Entrante o saliente | TCP | Detalle de implementación para Redis (puede restringir el punto de conexión remoto a VIRTUAL\_NETWORK) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | Entrada | TCP | Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | Entrada | TCP | Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | Entrada | TCP/UDP | Equilibrio de carga de Azure | AZURE\_LOADBALANCER |
| 20226 | Entrante + saliente | TCP | Detalle de implementación de clústeres de Redis | VIRTUAL\_NETWORK |




## ¿Puedo usar las redes virtuales con una memoria caché básica o estándar?

Las redes virtuales solo se pueden usar con memoria caché premium.

## Pasos siguientes
Obtenga información acerca de cómo usar más características de la memoria caché del nivel Premium.

-	[Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md)
-	[Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0211_2016-->