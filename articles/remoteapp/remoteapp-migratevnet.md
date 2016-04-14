<properties
    pageTitle="Aprenda a migrar desde una red virtual de RemoteApp a una red virtual de Azure | Microsoft Azure"
    description="Aprenda a migrar desde una red virtual de RemoteApp a una red virtual de Azure"
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/07/2016"
    ms.author="elizapo" />



# Migración de una colección híbrida de una red virtual de RemoteApp a una red virtual de Azure

¡Buenas noticias! Ahora tiene la posibilidad de implementar colecciones híbridas de RemoteApp directamente en sus redes virtuales de Azure (VNET) existentes, con lo que deja de ser necesario crear redes virtuales específicas para RemoteApp. Esto le permite aprovechar las últimas características de la red virtual (por ejemplo, ExpressRoute) y proporcionar a las colecciones híbridas acceso de red directo a otros servicios y máquinas virtuales de Azure implementados en esa red virtual. (Esto le permite disfrutar de un mejor rendimiento y mayor facilidad de configuración con respecto a las instalaciones de red virtual a red virtual).


Supongamos que ya ha creado una colección de RemoteApp híbrida denominada *OriginalCollection* con una red virtual de RemoteApp llamada *RemoteAppVNET*. Estos son los pasos para migrarla a una nueva red virtual de Azure denominada *AzureVNET*.

1.	En la pestaña **Redes** del [Portal de administración](http://manage.windowsazure.com/), cree una red virtual denominada *AzureVNET* usando los mismos valores de ubicación, configuración de DNS y espacio de direcciones (para al menos una de las subredes de *AzureVNET*) que utilizó para *RemoteAppVNET*.
2.	Configure *AzureVNET* para hospedar o tener conectividad de red para la implementación de Active Directory a la que la colección *OriginalCollection* está unida mediante dominio.
3.	En la pestaña **RemoteApps**, cree una nueva colección de RemoteApp denominada *New Collection*. (Use la opción **Crear con red virtual**, no **Creación rápida**).
3.	Configure *NewCollection* para que se implemente en una subred en *AzureVNET*.
4.	Configure *NewCollection* para que utilice la misma imagen y la misma información de unión a un dominio que las empleadas para *OriginalCollection*.
5.	Después de unas horas, *NewCollection* aparecerá en la lista de colecciones con un estado activo.

Ahora, si NO es necesario migrar ninguna información de usuario de la colección original a la nueva colección, siga estos pasos a continuación:

6.	Elimine *OriginalCollection*.
7.	Elimine *RemoteAppVNET*.

¡Y ya está!

Como alternativa, en caso de que SÍ tenga que migrar información de usuario de la colección original a la colección nueva, siga estos pasos a continuación:

6.	Envíe un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) con el identificador de la suscripción de Azure, el nombre de la colección original y el nombre de la nueva colección, y solicite la migración de la información de usuario.
7.	En el plazo de dos días hábiles, el equipo de RemoteApp moverá la lista de acceso de usuarios y todos los documentos y configuraciones de usuario de la colección original a la nueva colección.
8.	Elimine *OriginalCollection*.
9.	Elimine *RemoteAppVNET*.

Y ahora, ¡ya ha terminado!

Si tiene alguna pregunta o necesita ayuda especial, envíe un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).

<!---HONumber=AcomDC_0114_2016-->