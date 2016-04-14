<properties
    pageTitle="¿Qué le ha ocurrido a mi proyecto de servicio en la nube? | Microsoft Azure | Servicios conectados de Visual Studio"
	description="Describe lo que sucede en un proyecto de servicios en la nube después de conectarse a una cuenta de almacenamiento de Azure mediante los servicios conectados de Visual Studio"
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

# ¿Qué le ha ocurrido a mi proyecto de servicios en la nube (servicio conectado a Almacenamiento de Azure de Visual Studio)?

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

## Se ha agregado la cadena de conexión para Almacenamiento de Azure.
Se han creado elementos con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada. Se han hecho modificaciones en los archivos siguientes:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=AcomDC_0224_2016-->