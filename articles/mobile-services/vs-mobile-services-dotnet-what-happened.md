<properties
	pageTitle="¿Dónde está mi proyecto .NET después de agregar los servicios móviles mediante Visual Studio Connected Services? | Microsoft Azure"
	description="Describe dónde está su proyecto de Visual Studio .NET después de agregar los servicios móviles de Azure mediante Connected Services"
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="mlearned"/>

# ¿Dónde está mi proyecto de Visual Studio .NET después de agregar los servicios móviles de Azure mediante Connected Services?

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Se han agregado referencias

El paquete de NuGet de Servicios móviles de Azure se agregó a su proyecto. Como resultado, se agregaron las siguientes referencias de .NET a su proyecto:

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## Conexión de valores de cadena para Servicios móviles

En el archivo App.xaml.cs, se ha creado un objeto **MobileServiceClient** con la dirección URL y la clave de aplicación del servicio móvil seleccionado.

## Proyecto de Servicios móviles agregado

Si se crea un servicio móvil .NET en el proveedor de servicios conectado, se crea un proyecto de servicios móviles y se agrega a la solución.


[Más información acerca de Servicios móviles](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->