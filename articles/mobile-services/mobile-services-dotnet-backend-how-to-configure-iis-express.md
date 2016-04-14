<properties
	pageTitle="Configuración de IIS Express para pruebas del servicio móvil local | Servicios móviles de Azure"
	description="Obtenga información acerca de cómo configurar IIS Express para permitir conexiones a un proyecto de servicio móvil local para pruebas."
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	documentationCenter=""
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>

# Configuración del servidor web local para permitir conexiones a un servicio móvil local

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Servicios móviles de Azure le permite crear su servicio móvil en Visual Studio con uno de los lenguajes .NET compatibles y, a continuación, publicarlo en Azure. Una de las ventajas principales de usar un back-end de .NET para el servicio móvil es la capacidad de ejecutar, realizar pruebas y depurar el servicio móvil en el equipo local o la máquina virtual antes de publicarlo en Azure.

Para realizar una prueba de un servicio móvil localmente con los clientes que se ejecutan en un emulador, una máquina virtual o una estación de trabajo independiente, tiene que configurar el servidor web local y el equipo host para permitir las conexiones al puerto y la dirección IP de la estación de trabajo. En este tema se muestra cómo configurar IIS Express para permitir conexiones al servicio móvil hospedado localmente.

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

<!---HONumber=AcomDC_0211_2016-->