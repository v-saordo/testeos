<properties 
	pageTitle="Solución de problemas de Servicios de BizTalk mediante registros de operaciones | Microsoft Azure" 
	description="Solucione los problemas de los Servicios de BizTalk con el uso de registros de operaciones. MABS, WABS" 
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


# Servicios de BizTalk: solución de problemas mediante registros de operaciones

## Qué son los registros de operaciones
Registros de operaciones es una característica de los Servicios de administración, disponible en el Portal de Azure clásico, que permite consultar registros históricos realizados en los servicios de Azure, como Servicios de BizTalk. Esta característica le permite consultar datos históricos relacionados con las operaciones de administración en su suscripción a Servicios de BizTalk de hasta 180 días de antigüedad.

> [AZURE.NOTE]Esta característica únicamente captura registros para operaciones de administración de Servicios de BizTalk, como cuándo se ha iniciado el servicio, una copia de seguridad, y así sucesivamente. Se realiza un seguimiento de dichas operaciones independientemente de si se realizan desde el Portal de Azure clásico o mediante la [API de REST de Servicios de BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obtener una lista completa de las operaciones que se siguen mediante los Servicios de administración, consulte [Seguimiento de operaciones mediante los Servicios de administración de Azure](#bizops).<br/><br/> No captura los registros de actividades relacionadas con el tiempo de ejecución de los Servicios de BizTalk (como mensajes procesados por puentes, etc.). Para consultar estos registros, debe utilizar la vista de seguimiento desde el portal de Servicios de BizTalk. Para obtener más información, consulte [Mensajes de seguimiento](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## Consulta de los registros de operaciones de los Servicios BizTalk
1. En el Portal de Azure clásico, seleccione **Servicios de administración** y, a continuación, seleccione la pestaña **Registros de operaciones**.
2. Puede filtrar los registros en función de diferentes parámetros, como suscripción, intervalo de fechas, tipo de servicio (por ejemplo, Servicios de BizTalk), nombre de servicio o estado (de la operación, por ejemplo, Succeeded, Failed).
3. Seleccione la marca de verificación para consultar la lista filtrada. La imagen siguiente muestra las actividades relacionadas con testbiztalkservice. ![Consulta de registros de operaciones][ViewLogs] 
4. Para obtener más información sobre una operación específica, seleccione la fila y haga clic en **Detalles** en la barra de tareas situada en la parte inferior.


## <a name="bizops"></a>Seguimiento de operaciones mediante Servicios de administración de Azure
En la tabla siguiente se enumeran las operaciones cuyo seguimiento se realiza utilizando Servicios de administración de Azure.

Nombre de la operación | Tarea
--- | ---
CreateBizTalkService | Operación para crear un nuevo Servicio BizTalk
DeleteBizTalkService | Operación para eliminar un Servicio BizTalk
RestartBizTalkService | Operación para reiniciar un Servicio BizTalk
StartBizTalkService | Operación para iniciar un Servicio BizTalk
StopBizTalkService | Operación para detener un Servicio BizTalk
DisableBizTalkService | Operación para deshabilitar un Servicio BizTalk
EnableBizTalkService | Operación para habilitar un Servicio BizTalk
BackupBizTalkService | Operación para hacer una copia de seguridad de un Servicio BizTalk
RestoreBizTalkService | Operación para restaurar un Servicio BizTalk a partir de una copia de seguridad especificada
SuspendBizTalkService | Operación para suspender un Servicio BizTalk
ResumeBizTalkService | Operación para reanudar un Servicio BizTalk
ScaleBizTalkService | Operación para escalar o reducir verticalmente un Servicio BizTalk
ConfigUpdateBizTalkService | Operación para actualizar la configuración de un Servicio BizTalk
ServiceUpdateBizTalkService | Operación para actualizar o degradar un Servicio BizTalk a una versión diferente
PurgeBackupBizTalkService | Operación para purgar copias de seguridad del Servicio BizTalk fuera el período de retención


## Otras referencias
- [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Servicios de BizTalk: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servicios de BizTalk: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Servicios de BizTalk: nombre del emisor y clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 

<!---HONumber=AcomDC_1203_2015-->