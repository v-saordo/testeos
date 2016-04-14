<properties
	pageTitle="Uso de Caché en Redis de Azure con Python | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Python

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Python.


## Requisitos previos

Instale [redis-py](https://github.com/andymccurdy/redis-py).


## Crear una caché de Redis en Azure

En el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=398536), haga clic en **Nuevo**, **Datos y almacenamiento** y seleccione **Caché en Redis**.

  ![][1]

Escriba un nombre de host DNS. Tendrá el formato `<name>
  .redis.cache.windows.net`. Haga clic en **Crear**.

  ![][2]

  Cuando haya creado la caché, [acceda a ella](cache-configure.md#configure-redis-cache-settings) para ver su configuración. Necesitará:

  - **Nombre de host.** Escribió este nombre cuando creó la memoria caché.
  - **Puerto.** Haga clic en el vínculo bajo **Puertos** para ver los puertos. Use el puerto SSL.
  - **Clave de acceso.** Haga clic en el vínculo bajo **Claves** y copie la clave principal.

  ## Agregue algo a la memoria caché y recupérela.

  >>> import redis r = redis.StrictRedis(host='<name>.redis.cache.windows.net', port=6380, db=0, password='<key>', ssl=True) >>> r.set('foo', 'bar') True >>> r.get('foo') b'bar'

Reemplace *&lt;name&gt;* por el nombre de la memoria caché y *&lt;key&gt;* por la clave de acceso.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=AcomDC_0309_2016-->