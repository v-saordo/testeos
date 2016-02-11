<properties 
	pageTitle="Información acerca de la limitación en Servicios de BizTalk | Microsoft Azure" 
	description="Obtenga información acerca de los umbrales de limitación y comportamientos en tiempo de ejecución resultantes para los Servicios de BizTalk. La limitación se basa en el uso de la memoria y el número de mensajes. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mandia"/>





# Servicios de BizTalk: limitaciones

Los servicios de BizTalk de Azure implementan limitaciones del servicio según dos condiciones: uso de memoria y el número de mensajes simultáneos que se procesan. En este tema se enumeran los umbrales de limitación y se describe el comportamiento del tiempo de ejecución cuando se produce una condición de limitación.

## Umbrales de limitación

La siguiente tabla muestra los orígenes y umbrales de limitación:

||Descripción|Umbral bajo|Umbral alto|
|---|---|---|---|
|Memoria|% de la memoria total disponible del sistema/PageFileBytes. <p><p>PageFileBytes disponible total es aproximadamente el doble de la memoria RAM del sistema.|60 %|70 %|
|Procesamiento de mensajes|Número de mensajes que se procesan simultáneamente|40 * número de núcleos|100 * número de núcleos|

Cuando se alcanza un umbral alto, Servicios de BizTalk de Azure empieza a limitarse. La limitación se detiene cuando se alcanza el umbral bajo. Por ejemplo, el servicio está utilizando un 65 % de la memoria del sistema. En esta situación, el servicio no se limita. El servicio empieza a usar un 70 % de la memoria del sistema. En esta situación, el servicio se limita y sigue limitándose hasta que el servicio utiliza un 60 % de la memoria del sistema (umbral bajo).

Los servicios de BizTalk de Azure realizan un seguimiento del estado de las limitaciones (estado normal frente a estado limitado) y de la duración de la limitación.


## Comportamiento del tiempo de ejecución

Cuando los servicios de BizTalk de Azure entran en un estado de limitación, ocurre lo siguiente:

- La limitación es por instancia de rol. Por ejemplo,<br/>
RoleInstanceA se está limitando. RoleInstanceB no se está limitando. En esta situación, los mensajes en RoleInstanceB se procesan según lo esperado. Los mensajes en RoleInstanceA se descartan y producen el siguiente error:<br/><br/>
**El servidor está ocupado. Vuelva a intentarlo.**<br/><br/>
- Ningún origen de extracción sondea ni descarga un mensaje. Por ejemplo,<br/>
una canalización extrae los mensajes desde un origen FTP externo. La instancia de rol que realiza la extracción entra en un estado de limitación. En esta situación, la canalización deja de descargar mensajes adicionales hasta que la instancia de rol detiene la limitación.
- Se envía una respuesta al cliente para que el cliente pueda volver a enviar el mensaje.
- Debe esperar a que se resuelva la limitación. Específicamente, debe esperar hasta que se alcance el umbral bajo.

## Notas importantes
- La limitación no se puede deshabilitar.
- Los umbrales de limitación no se pueden modificar.
- La limitación está implementada en todo el sistema.
- El servidor de Base de datos SQL de Azure también tiene limitaciones integradas.

## Otros temas acerca de los servicios de BizTalk de Azure

-  [Instalación del SDK de Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriales: Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## Otras referencias
- [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: nombre del emisor y clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=AcomDC_1203_2015-->
