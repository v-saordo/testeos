<properties
   pageTitle="Escalado del servicio web | Microsoft Azure"
   description="Aprenda a aumentar la simultaneidad y a agregar nuevos puntos de conexión para escalar un servicio web."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="aprendizaje automático de azure, servicios web, operacionalización, escalado, punto de conexión, simultaneidad"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="02/04/2016"
   ms.author="neerajkh"/>

# Escalado del servicio web

## Aumentar la simultaneidad

De manera predeterminada, cada servicio web publicado está configurado para admitir 20 solicitudes simultáneas. Puede aumentar esta simultaneidad a 200 solicitudes simultáneas a través del [Portal de Azure clásico](https://manage.windowsazure.com/), tal como se muestra en la figura que aparece a continuación.

Vaya al [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en el icono Aprendizaje automático que se encuentra a la izquierda, seleccione el área de trabajo que se usa para publicar el servicio web, haga clic en el servicio web que desea, seleccione el punto de conexión donde se debe aumentar la simultaneidad y haga clic en **CONFIGURAR**. Use el control deslizante para aumentar la simultaneidad y, luego, haga clic en **GUARDAR** en el panel inferior.

Para aumentar la simultaneidad, consulte [Escalado de puntos de conexión de API](machine-learning-scaling-endpoints.md).

   ![Aprendizaje automático, escalado de puntos de conexión.][1]

## Agregar puntos de conexión nuevos para el mismo servicio web

El escalado del servicio web es una tarea común, para admitir más de 200 solicitudes simultáneas, aumentar la disponibilidad a través de varios puntos de conexión o proporcionar un punto de conexión independiente para un consumidor distinto del servicio web. El usuario puede aumentar la escala si agrega puntos de conexión adicionales para el mismo servicio web. El usuario puede agregar puntos de conexión adicionales en el [Portal de Azure clásico](https://manage.windowsazure.com/), tal como se muestra en la figura que aparece a continuación:

Vaya al [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en el icono Aprendizaje automático que se encuentra a la izquierda, seleccione el área de trabajo que se usa para publicar el servicio web, haga clic en el servicio web que desea, haga clic en **AGREGAR PUNTO DE CONEXIÓN** en el panel inferior y proporcione un nombre, una descripción y la simultaneidad deseada para el punto de conexión nuevo.

Para agregar puntos de conexión nuevos, consulte [Creación de puntos de conexión](machine-learning-create-endpoint.md).

   ![Aprendizaje automático, agregar extremos nuevos.][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0211_2016-->