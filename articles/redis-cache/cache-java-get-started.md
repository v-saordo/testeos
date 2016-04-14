<properties
   pageTitle="Uso de Caché en Redis de Azure con Java | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Java

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché en Redis de Azure le proporciona acceso a una caché en Redis dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Java.


## Requisitos previos

[Jedis](https://github.com/xetorthio/jedis) - Cliente de Java para Redis

Este tutorial usa Jedis, pero puede usar cualquier cliente de Java enumerado en [http://redis.io/clients](http://redis.io/clients).


## Crear una caché de Redis en Azure

En el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=398536), haga clic en **Nuevo**, **Datos y almacenamiento** y seleccione **Caché en Redis**.

  ![][1]

Escriba un nombre de host DNS. Tendrá el formato `<name>.redis.cache.windows.net`. Haga clic en **Crear**.

  ![][2]


Una vez creada la memoria caché, haga clic en ella en el Portal de Azure para ver su configuración. Haga clic en el vínculo bajo **Claves** y copie la clave principal. Necesita esto para autenticar solicitudes.

  ![][4]


## Habilitar el extremo no SSL


Haga clic en el vínculo bajo **Puertos** y, a continuación, haga clic en **No** para "Permitir acceso solo a través de SSL". Esto habilita el puerto no SSL para la memoria caché. El cliente Jedis actualmente no admite SSL.

  ![][3]


## Agregar algo a la memoria caché y recuperarlo

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Pasos siguientes

- [Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation) oficial.


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=AcomDC_0309_2016-->