<properties
	pageTitle="Definición de un extremo de API personalizada en un servicio móvil back-end de .NET| Servicios móviles de Azure"
	description="Más información sobre cómo definir un extremo de API personalizada en un servicio móvil back-end de .NET"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>


# Definición de un extremo de API personalizada en un servicio móvil back-end de .NET

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [JavaScript backend](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET backend](./mobile-services-dotnet-backend-define-custom-api.md)

En este tema se muestra cómo definir un extremo de API personalizada en un servicio móvil back-end de .NET. Una API personalizada le permite definir extremos personalizados con funcionalidad de servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura de base de datos. Mediante una API personalizada, tiene más control sobre la mensajería, incluidos los encabezados HTTP y el formato del cuerpo.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Para obtener información sobre cómo invocar una API personalizada en la aplicación con una biblioteca de cliente de Servicios móviles, vea [Llamada a una API personalizada](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) en la referencia del SDK de cliente.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0211_2016-->