<properties
	pageTitle="¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Visual Studio)? | Almacenamiento de Microsoft Azure"
	description="Describe lo que sucede después de conectarse a una cuenta de almacenamiento de Azure en un proyecto de ASP.NET 5 de Visual Studio mediante los servicios conectados de Visual Studio"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# ¿Qué le ha ocurrido a mi proyecto ASP.NET 5 (servicios conectados a Almacenamiento de Azure de Visual Studio)?

## Se han agregado referencias

El paquete NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio. Este paquete agrega las siguientes referencias. NET:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

Además, se agregó el paquete NuGet **Microsoft.Framework.Configuration.Json**.

## Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para más información, vea [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=AcomDC_0224_2016-->