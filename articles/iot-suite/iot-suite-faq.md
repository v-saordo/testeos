<properties
  pageTitle="Preguntas más frecuentes sobre el conjunto de aplicaciones Azure IoT | Microsoft Azure"
  description="Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="03/02/2016"
  ms.author="araguila"/>
   
# Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

### ¿Cuántas instancias de DocumentDB se pueden aprovisionar en una suscripción?

Cinco. Para aumentar este límite se puede crear una incidencia de soporte técnico, pero de forma predeterminada, solo se pueden aprovisionar cinco instancias de DocumentDB por suscripción. Como consecuencia, solo se pueden aprovisionar un máximo de cinco soluciones preconfiguradas de supervisión remota en una suscripción determinada.

### ¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo se pueden crear dos API de Mapas de Bing gratis en una suscripción. La solución de supervisión remota se aprovisiona de forma predeterminada con una API de Mapas de Bing gratis. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### ¿Cuál es la diferencia entre eliminar un grupo de recursos en el Portal de Azure y hacer clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

- Si elimina la solución preconfigurada en [azureiotsuite.com][lnk-azureiotsuite], eliminará todos los recursos aprovisionados cuando se creó la solución preconfigurada; si agregó recursos adicionales al grupo de recursos, estos también se eliminan. 

- Si elimina el grupo de recursos en el [Portal de Azure][lnk-azure-portal], solo eliminará los recursos de dicho grupo de recursos; también tendrá que eliminar la aplicación de Azure Active Directory asociada a la solución preconfigurada en el [Portal de Azure clásico][lnk-classic-portal].

### ¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Tutorial para la eliminación de inquilinos de Azure AD).


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0309_2016-->