<properties 
	pageTitle="Cómo configurar la persistencia de datos para una Caché en Redis de Azure Premium" 
	description="Obtener información sobre cómo configurar y administrar la persistencia de datos de sus instancias de Caché en Redis de Azure de nivel Premium" 
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
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Cómo configurar la persistencia de datos para una Caché en Redis de Azure Premium

Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché, incluido el nuevo nivel Premium.

El nivel premium de Caché en Redis de Azure incluye la agrupación en clústeres, la persistencia y la compatibilidad de red virtual. En este artículo se describe cómo configurar la persistencia en una instancia de Caché en Redis de Azure premium.

Para obtener información sobre otras características de caché premium, vea [Cómo configurar la agrupación en clústeres para una Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md) y [Cómo configurar la compatibilidad de redes virtuales para una Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

## ¿Qué es la persistencia de datos?
La persistencia de Redis le permite conservar los datos almacenados en Redis. También puede tomar instantáneas y realizar copias de seguridad de los datos que puede cargar en el caso de un error de hardware. Se trata de una inmensa ventaja sobre el nivel Básico o Estándar, donde todos los datos se almacenan en la memoria y puede haber una posible pérdida de datos en caso de error donde los nodos de la memoria caché están inactivos.

Caché en Redis de Azure ofrece persistencia de Redis usando el [modelo RDB](http://redis.io/topics/persistence), donde los datos se almacenan en una cuenta de almacenamiento de Azure. Cuando se configura la persistencia, Caché en Redis de Azure conserva una instantánea de la memoria caché de Redis en un formato binario de Redis en el disco según una frecuencia de copia de seguridad configurable. Si se produce un evento catastrófico que deshabilita tanto la caché de réplica como la principal, se reconstruye la memoria caché con la instantánea más reciente.

Se puede configurar la persistencia de la hoja **Nueva caché en Redis** durante la creación de la caché y en la hoja **Configuración** para las memorias caché premium existentes.

## Crear una caché Premium

Para crear una caché y configurar la persistencia, inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Nuevo**->**Datos y almacenamiento**>**Caché en Redis**.

![Creación de una caché en Redis][redis-cache-new-cache-menu]

Para configurar la persistencia, seleccione primero una de las cachés **Premium** en la hoja **Elija su plan de tarifa**.

![Elegir su plan de tarifa][redis-cache-premium-pricing-tier]

Una vez se selecciona un plan de tarifa premium, haga clic en **Persistencia de Redis**.

![Persistencia de Redis][redis-cache-persistence]

En la siguiente sección se describe cómo configurar la persistencia de Redis en su nueva caché premium. Una vez configurada la persistencia de Redis, haga clic en **Crear** para crear su nueva caché premium con persistencia de Redis.

## Configurar la persistencia de Redis

La persistencia de Redis se configura en la hoja **Persistencia de datos de Redis**. Para nuevas caché, a esta hoja se obtiene acceso durante el proceso de creación de la caché, como se describe en la sección anterior. Para las memorias caché existentes, a la hoja **Persistencia de los datos en Redis** se obtiene acceso en la hoja **Configuración** para la caché.

![Configuración de Redis][redis-cache-settings]

Para habilitar la persistencia en Redis, haga clic en **Habilitada** para habilitar la copia de seguridad RDB (base de datos de Redis). Para deshabilitar la persistencia en Redis en una caché premium habilitada anteriormente, haga clic en **Deshabilitada**.

Para configurar el intervalo de copia de seguridad, seleccione una **Frecuencia de copia de seguridad** en la lista desplegable. Entre las opciones se incluyen **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** y **24 horas**. Este intervalo empieza la cuenta atrás cuando se completa correctamente la operación de copia de seguridad anterior y se inicia cuando se produce una nueva copia de seguridad.

Haga clic en **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento que se va a usar y elija la **Clave principal** o la **Clave secundaria** que se usará en la lista desplegable **Clave de almacenamiento**. Debe elegir una cuenta de almacenamiento en la misma región que la memoria caché y se recomienda una cuenta de **Almacenamiento Premium** porque el almacenamiento premium tiene un mayor rendimiento.

>[AZURE.IMPORTANT]Si se vuelve a generar la clave de almacenamiento para su persistencia, debe volver a elegir la clave que quiera en la lista desplegable **Clave de almacenamiento**.

![Persistencia de Redis][redis-cache-persistence-selected]

Haga clic en **Aceptar** para guardar la configuración de persistencia.

La siguiente copia de seguridad (o la primera copia de seguridad para las nuevas cachés) se inicia cuando transcurre el intervalo de frecuencia de copia de seguridad.



## P+F de persistencia

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre la persistencia de Caché en Redis de Azure.

## ¿Puedo habilitar la persistencia en una memoria caché creada anteriormente?

Sí, la persistencia de Redis puede configurarse tanto durante la creación de la memoria caché como en las memorias caché premium existente.

## ¿Puedo cambiar la frecuencia de copia de seguridad después de crear la memoria caché?

Sí, puede cambiar la frecuencia de copia de seguridad en la hoja **Persistencia de los datos en Redis**. Para instrucciones, vea [Configurar la persistencia de Redis](#configure-redis-persistence).

## ¿Por qué si tengo una frecuencia de copia de seguridad de 60 minutos, hay más de 60 minutos entre las copias de seguridad?

El intervalo de frecuencia de copia de seguridad no se inicia hasta que el proceso de copia de seguridad anterior se ha completado correctamente. Si la frecuencia de copia de seguridad es de 60 minutos y realiza un proceso de copia de seguridad en 15 minutos para completarse correctamente, la siguiente copia de seguridad no se iniciará hasta pasados 75 minutos de la hora de inicio de la copia de seguridad anterior.

## Qué ocurre con las copias de seguridad antiguas cuando se realiza una nueva copia de seguridad

Todas las copias de seguridad excepto la más reciente se eliminan automáticamente. Es posible que esta eliminación no se produzca inmediatamente pero las copias de seguridad anteriores no se guardan de manera indefinida.

## Pasos siguientes
Obtenga información sobre cómo usar más características de la memoria caché del nivel Premium.

-	[Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md)
-	[Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_1210_2015-->