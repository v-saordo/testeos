<properties 
   pageTitle="Archivo de mensajes del conector AS2 | Servicio de aplicaciones de Microsoft Azure" 
   description="Cómo archivar o almacenar mensajes del conector AS2 en Servicio de aplicaciones de Azure" 
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
   ms.date="02/18/2016"
   ms.author="rajram"/>


# Información general del archivo de mensajes del conector AS2

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El [conector AS2](app-service-logic-connector-as2.md) expone la posibilidad de archivar mensajes. El archivado almacena el mensaje en el **contenedor de blobs de Azure** que forma parte de la configuración del paquete.

El archivado se expone en dos puntos tanto para mensajes como para confirmaciones (MDN):

1. **Desencadenador de recepción/decodificación**: el mensaje se archiva en cuanto se recibe en la instancia de aplicación de API 
2. **Acción de codificación/envío**: el mensaje codificado se archiva después de que todo el procesamiento está completo y justo antes de enviarse al socio 

## Procedimientos: Recuperación de la URL archivada de un mensaje

Vaya a la instancia de la aplicación de API del Conector AS2 y haga clic en ’Seguimiento’. Limite la información de seguimiento usando los parámetros de filtro. Una vez que el mensaje se ve, haga clic para ver su vista detallada. La URL archivada del mensaje se muestra en esta vista detallada: ![][1]

## Procedimientos: Recuperación de un mensaje archivado

Use la URL recuperada anteriormente para recuperar el mensaje archivado del Almacenamiento de blobs de Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=AcomDC_0224_2016-->