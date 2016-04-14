<properties 
	pageTitle="¿Qué le ha ocurrido a mi proyecto Cordova (Visual Studio Connected Services)? | Microsoft Azure" 
	description="Describe dónde está mi proyecto de Azure Cordova después de agregar los servicios móviles de Azure mediante Visual Studio Connected Services" 
	services="mobile-services" 
	documentationCenter="na" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="mlearned"/>

# ¿Dónde está mi proyecto de Azure Cordova después de agregar los servicios móviles de Azure mediante Visual Studio Connected Services?

##Se han agregado referencias

Se ha habilitado el complemento de cliente de servicio móvil de Azure con todas las aplicaciones híbridas multidispositivo.
  
##Conexión de valores de cadena para Servicios móviles

En `services\mobileServices\settings`, se ha generado un nuevo archivo JavaScript (.js) con **MobileServiceClient** que contiene la dirección URL y la clave de aplicación del servicio móvil seleccionado. El archivo contiene la inicialización de un objeto de cliente de servicio móvil, similar al código siguiente.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Más información acerca de Servicios móviles](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->