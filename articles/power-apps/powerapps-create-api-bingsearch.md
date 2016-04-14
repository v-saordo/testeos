<properties
	pageTitle="Agregar la API de Búsqueda de Bing a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Búsqueda de Bing en el entorno del servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="gautamt"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Crear una nueva API de Búsqueda de Bing en el entorno del servicio de aplicaciones de la organización

## Crear la API en el Portal de Azure

1. En el [Portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta profesional. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][4]

3. En la lista, desplácese para encontrar PowerApps o escriba *powerapps*: ![][5]

4. En **PowerApps**, seleccione **Administrar APIs**: ![Examinar las API registradas][2]

2. En **Administrar APIs**, seleccione **Agregar** para agregar la nueva API: ![Add API][3]

3. Escriba un **nombre** descriptivo para la API.

4. En **Origen**, seleccione **API disponible** para seleccionar las API preconfiguradas y seleccione **Búsqueda de Bing**:

	a) Seleccione **Configuración - Configurar valores obligatorios**.
	
	b) Escriba *Clave de cuenta*. Si no tiene una clave de Búsqueda de Bing, cree una [oferta de Búsqueda de Bing][1] gratuita para obtener una clave.

	c) Seleccione **Aceptar**.

5. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Búsqueda de Bing en su entorno del servicio de aplicaciones.


## Resumen y pasos siguientes
En este tema, la API de Búsqueda de Bing ya está agregada a su servicio PowersApps Enterprise. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->