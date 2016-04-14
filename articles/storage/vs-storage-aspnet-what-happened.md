<properties
	pageTitle="¿Qué le ha ocurrido a mi proyecto ASP.NET? | Microsoft Azure | Servicios conectados de Visual Studio"
	description="Describe lo que sucede después de agregar Almacenamiento de Azure a un proyecto ASP.NET con servicios conectados de Visual Studio"
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

# ¿Qué le ha ocurrido a mi proyecto ASP.NET (servicio conectado a Almacenamiento de Azure de Visual Studio)?

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

##Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo web.config de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET](http://www.asp.net).

<!---HONumber=AcomDC_0224_2016-->