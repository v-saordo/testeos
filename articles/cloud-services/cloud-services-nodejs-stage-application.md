<properties 
	pageTitle="Ensayo de implementación de un servicio en la nube (Node.js) | Microsoft Azure" 
	description="Vea cómo implementar su aplicación de Azure en un entorno de ensayo y después en un entorno de producción mediante el intercambio de IP virtuales (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>



# Ensayo de una aplicación en Azure

Se puede implementar una aplicación en paquete en el entorno de ensayo de Azure para probarla antes de que pase al entorno de producción, lugar desde el que se podrá obtener acceso a la aplicación por Internet. El entorno de ensayo es exactamente igual al entorno de producción, salvo que solo puede tener acceso a la aplicación de ensayo con una URL confusa que se genera mediante Azure. Una vez que haya comprobado que la aplicación funciona correctamente, se puede implementar en el entorno de producción mediante un intercambio de IP virtual (VIP).

> [AZURE.NOTE]Los pasos descritos en este artículo solo se aplican a las aplicaciones Node hospedadas como un servicio en la nube de Azure.

## Paso 1: realizar el ensayo de una aplicación

En esta tarea se aborda cómo realizar el ensayo de una aplicación con **Microsoft Azure PowerShell**.

1.  Para publicar un servicio, simplemente pase el parámetro **-Slot** al cmdlet **Publish-AzureServiceProject**.

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Inicie sesión en el [Portal de Azure clásico] y seleccione **Servicios en la nube**. Una vez que se crea el servicio en la nube y que el estado de la columna **Ensayo** se actualiza a **En ejecución**, haga clic en el nombre del servicio.

	![portal que muestra un servicio en ejecución][cloud-service]

3.  Seleccione el **Panel** y, a continuación, seleccione **Ensayo**.

	![panel de servicio en la nube][cloud-service-dashboard]

4. Anote el valor de la entrada **Dirección URL del sitio** que aparece a la derecha. El nombre DNS es un identificador interno confuso que ha generado Azure.

    ![URL del sitio][cloud-service-staging-url]

Ahora puede comprobar que la aplicación funciona correctamente en el entorno de ensayo mediante la utilización de la URL del sitio de ensayo.

## Paso 2: Actualizar una aplicación en producción mediante el intercambio de VIP

Una vez que haya comprobado la versión de actualización de una aplicación en el entorno de ensayo, puede hacer que esté disponible rápidamente en producción mediante el intercambio de las IP virtuales (VIP) de los entornos de ensayo y producción.

> [AZURE.NOTE]En este paso se supone que ya ha implementado una aplicación en producción y que ha almacenado provisionalmente la versión actualizada de la aplicación.

1.  Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios en la nube**, y, a continuación, seleccione el nombre del servicio.

2.  En el **Panel**, seleccione **Ensayo** y haga clic en **Intercambiar** en la parte inferior de la página. De este modo se abre el cuadro de diálogo Intercambio de VIP.

    ![cuadro de diálogo Intercambio de VIP][vip-swap-dialog]

3.  Revise la información y, a continuación, haga clic en **Aceptar**. Ambas implementaciones empiezan a actualizarse cuando la implementación de ensayo pasa a producción y la implementación de producción pasa a ensayo.

Ha realizado correctamente el ensayo de la implementación y la actualización de la implementación de producción mediante el intercambio de las VIP con la implementación en ensayo.

## Recursos adicionales

- [Implementación de una actualización de servicio en producción intercambiando las VIP en Azure]

[Portal de Azure clásico]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Implementación de una actualización de servicio en producción intercambiando las VIP en Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production

<!---HONumber=AcomDC_0114_2016-->