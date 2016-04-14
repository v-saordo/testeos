<properties
	pageTitle="¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)? | Microsoft Azure"
	description="Describe lo que ha ocurrido en un proyecto WebJob de Azure después de conectarse a una cuenta de almacenamiento de Azure mediante los servicios conectados de Visual Studio"
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

# ¿Qué le ha ocurrido a mi proyecto WebJob (servicio conectado a Almacenamiento de Azure de Visual Studio)?

## Se han agregado referencias

El paquete de NuGet de Almacenamiento de Azure se agregó al proyecto de Visual Studio o se actualizó en él. Este paquete agrega las siguientes referencias. NET:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Se ha agregado la cadena de conexión para Almacenamiento de Azure.
En el archivo App.config del proyecto, se actualizaron las entradas **AzureWebJobsStorage** y **AzureWebJobsDashboard** con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [Recursos de documentación de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0224_2016-->