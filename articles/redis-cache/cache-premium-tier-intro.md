<properties 
	pageTitle="Introducción al nivel Premium de Caché en Redis de Azure" 
	description="Aprenda a crear y a administrar la persistencia de Redis, la agrupación en clústeres de Redis y la compatibilidad de red virtual para las instancias de Caché en Redis de Azure de nivel Premium." 
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
	ms.date="01/09/2016" 
	ms.author="sdanie"/>

# Introducción al nivel Premium de Caché en Redis de Azure
Caché en Redis de Azure es una memoria caché distribuida y administrada que ayuda a compilar aplicaciones muy útiles y altamente escalables mediante el acceso ultrarrápido a los datos.

Premium es un nuevo nivel destinado a las empresas que incluye todas las características del nivel Standard y otras adicionales, como un mejor rendimiento, cargas de trabajo más grandes, recuperación ante desastres y seguridad mejorada. Siga leyendo para obtener más información acerca de las características adicionales de la memoria caché del nivel Premium.

## Mejor rendimiento en comparación con el nivel Estándar o Básico
**Mejor rendimiento respecto a los niveles Standard o Basic.** Las memorias caché de nivel Premium se implementan en hardware con procesadores más rápidos que ofrece un mejor rendimiento en comparación con el nivel Standard o Basic. Además, el rendimiento de dichas memorias caché es superior y sus latencias son más bajas.

**El rendimiento de una memoria caché de nivel Premium es superior al de una memoria caché de nivel Standard del mismo tamaño .** Por ejemplo, el rendimiento de una memoria caché de 53 GB P4 (Premium) es de 250 000 solicitudes por segundo, en comparación con 150 000 para una memoria C6 (Standard).

Consulte el artículo [P+F de Caché en Redis de Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obtener más detalles sobre el tamaño, el rendimiento y el ancho de banda de las memorias caché Premium.

## Persistencia de datos de Redis
El nivel Premium permite conservar los datos de la memoria caché en una cuenta de almacenamiento de Azure. En las memorias caché de nivel Basic o Standard, todos los datos se almacenan únicamente en la memoria. En caso de problemas con la infraestructura subyacente, podría producirse una pérdida de los datos. Se recomienda usar la característica de persistencia de datos de Redis del nivel Premium para aumentar la resistencia frente a la pérdida de datos. Caché en Redis de Azure ofrece las opciones RDB y AOF (próximamente) en la [persistencia de Redis](http://redis.io/topics/persistence).

Para obtener instrucciones acerca de cómo configurar la persistencia, consulte [Configuración de la persistencia para una Caché en Redis de Azure de nivel Premium](cache-how-to-premium-persistence.md).

##Clúster Redis
Si desea crear memorias caché de más de 53 GB o particionar los datos entre varios nodos de Redis, puede usar la agrupación en clústeres Redis, disponible en el nivel Premium. Cada nodo consta de un par de cachés principal-réplica administrado por Azure para ofrecer una alta disponibilidad.

**La agrupación en clústeres de Redis proporciona una escalabilidad y un rendimiento máximos.** El rendimiento aumenta de manera lineal a medida que aumenta el número de particiones (nodos) del clúster. Por ejemplo, si se crea un clúster P4 de 10 particiones, el rendimiento posible es de 250 000 * 10 = 2,5 millones de solicitudes por segundo. Consulte el artículo [P+F de Caché en Redis de Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obtener más detalles sobre el tamaño, el rendimiento y el ancho de banda de las memorias caché Premium.

Para empezar a usar la agrupación en clústeres, consulte [Configuración de la agrupación en clústeres para Caché en Redis de Azure de nivel Premium](cache-how-to-premium-clustering.md).

##Aislamiento y seguridad mejorados
Las memorias caché creadas en los niveles Basic o Standard están disponibles en Internet. El acceso a las mismas se restringe mediante la clave de acceso. Con el nivel Premium puede asegurarse de que solo los clientes de una red determinada puedan tener acceso a la memoria caché. Puede implementar la Caché en Redis en una [Red Virtual de Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Además, puede usar todas las características de la red virtual, como las subredes y las directivas de control de acceso, entre otras, para restringir aún más el acceso a Redis.

Para obtener más información, vea [Configuración de la compatibilidad de Red virtual para una Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

## Pasos siguientes

Cree una memoria caché y explore las nuevas características del nivel Premium.

-	[Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md)
-	[Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md)
-	[Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md)
  

<!---HONumber=AcomDC_0128_2016-->