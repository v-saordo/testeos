<properties 
	pageTitle="Tareas permitidas en diferentes condiciones o estados de Servicios de BizTalk | Microsoft Azure" 
	description="Las acciones o las operaciones admitidas en distintos estados de MABS: detener, iniciar, reiniciar, suspender, reanudar, eliminar, escalar, actualizar configuración y realizar copias de seguridad." 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="mandia"/>



# Servicios de BizTalk: gráfico del estado del servicio de BizTalk
En función del estado real del servicio de BizTalk, habrá operaciones que pueda realizar o no en el servicio de BizTalk.

Por ejemplo, supongamos que aprovisiona un servicio de BizTalk nuevo en el Portal de Azure clásico. Cuando finalice correctamente, el servicio de BizTalk se encuentra en estado Active. Puede detener el servicio de BizTalk si está en estado Active. Si se detiene correctamente, el servicio de BizTalk pasa al estado Stopped. Si no se detiene correctamente, el servicio de BizTalk pasa al estado StopFailed. Puede reiniciar el servicio de BizTalk si se encuentra en el estado StopFailed. Si intenta realizar una operación no permitida, como reanudar el servicio de BizTalk, se produce el error siguiente:

**Operación no permitida**

Para aprovisionar un servicio de BizTalk, vaya a [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Las tablas siguientes incluyen las operaciones que se pueden realizar cuando el servicio de BizTalk se encuentra en un estado concreto. Una ✔ significa que la operación está permitida en ese estado. y la entrada vacía indica que la operación no se puede realizar en ese estado.

## Operaciones de inicio, detención, reinicio, suspensión, reanudación y eliminación
<table border="1">
<tr>
        <th colspan="15">Operación o acción</th>
</tr>

<tr>
        <th rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Iniciar</th>
        <th>Detención</th>
        <th>Reinicio</th>
        <th>Suspensión</th>
        <th>Reanudación</th>
        <th>Eliminar</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Stopped</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## Operaciones de escala, actualización de la configuración y copia de seguridad
<table border="1">
<tr>
        <th colspan="15">Operación o acción</th>
</tr>

<tr>
        <th rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Escala</th>
        <th>Actualización de la configuración</th>
        <th>Copia de seguridad</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Stopped</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## Otras referencias
- [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servicios de BizTalk: nombre del emisor y clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 

<!---HONumber=AcomDC_0302_2016-->