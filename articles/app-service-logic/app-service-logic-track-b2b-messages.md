<properties 
   pageTitle="Seguimiento de mensajes B2B en las aplicaciones lógicas en el Servicio de aplicaciones de Azure | Microsoft Azure" 
   description="En este tema, se describe el seguimiento del procesamiento B2B." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="12/07/2015"
   ms.author="rajram"/>


# Seguimiento de mensajes B2B

## Información sobre el seguimiento B2B
La comunicación B2B (del inglés Business-to-Business) implica el procesamiento de mensajes entre socios comerciales. Las relaciones se definen como "acuerdos entre dos socios comerciales". Una vez establecida la comunicación, hay que supervisar si la comunicación se produce según lo esperado.

Hemos implementado el seguimiento de mensajes para los siguientes escenarios B2B: AS2, EDIFACT y X12.

## AS2
Después de crear una instancia de una aplicación de API de AS2, vaya a esa instancia y seleccione **Seguimiento**. Aquí puede ver y filtrar toda la información de seguimiento de AS2:

![][1]

## EDIFACT
Después de crear una instancia de una aplicación de API de EDIFACT, vaya a esa instancia y seleccione **Seguimiento**. Aquí puede ver y filtrar toda la información de seguimiento de EDIFACT. Además, puede ver el nivel de intercambio, el nivel de grupo y los datos del nivel de conjunto de transacciones, todo ello en una sola vista.

Si los lotes se crean como parte de los contratos de EDIFACT en la aplicación de API de administración de socios comerciales asociada, la sección Procesamiento por lotes muestra una lista con todos los lotes. Puede seleccionar un lote para ver el mensaje activo (si hubiera) así como la información para los lotes que se hayan completado:

![][2]

## X12
Después de crear una instancia de una aplicación de API de X12, vaya a esa instancia y seleccione **Seguimiento**. Aquí puede ver y filtrar toda la información de seguimiento de X12. Además, puede ver el nivel de intercambio, el nivel de grupo y los datos del nivel de conjunto de transacciones, todo ello en una sola vista.

Si los lotes se crean como parte de los contratos de X12 en la aplicación de API asociada de administración de socios comerciales, la sección Procesamiento por lotes muestra una lista con todos los lotes. Puede seleccionar un lote para ver el mensaje activo (si hubiera) así como la información para los lotes que se hayan completado.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_1210_2015-->