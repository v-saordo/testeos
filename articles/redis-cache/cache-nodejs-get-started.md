<properties
	pageTitle="Uso de Caché en Redis de Azure con Node.js | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Node.js y node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché en Redis de Azure le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Node.js. Para obtener otro ejemplo de uso de Caché en Redis de Azure con Node.js, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure][].


## Requisitos previos

Instale [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node\_redis](https://github.com/mranney/node_redis), pero puede usar cualquier cliente de Node.js enumerado en [http://redis.io/clients](http://redis.io/clients).

## Crear una caché de Redis en Azure

En el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=398536), haga clic en **Nuevo**, **Datos y almacenamiento** y seleccione **Caché en Redis**.

  ![][1]

Escriba un nombre de host DNS. Tendrá el formato `<name>.redis.cache.windows.net`. Haga clic en **Crear**.

  ![][2]


  Cuando haya creado la caché, [acceda a ella](cache-configure.md#configure-redis-cache-settings) para ver su configuración. Haga clic en el vínculo bajo **Claves** y copie la clave principal. Necesita esto para autenticar solicitudes.

  ![][4]

## Agregar algo a la memoria caché y recuperarlo

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

client.set("foo", "bar", function(err, reply) {
  console.log(reply);
});

client.get("foo",  function(err, reply) {
  console.log(reply);
});
```

Salida:

	OK
	bar


## Pasos siguientes

- [Habilite los diagnósticos de caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el estado de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation) oficial.


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=AcomDC_0309_2016-->