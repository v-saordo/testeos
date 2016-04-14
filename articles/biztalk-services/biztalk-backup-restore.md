<properties 
	pageTitle="Creación y restauración de una copia de seguridad en Servicios de BizTalk | Microsoft Azure" 
	description="Entre los Servicios de BizTalk se incluye Copias de seguridad y restauración. Obtenga información acerca de cómo crear y restaurar una copia de seguridad y aprenda a determinar el contenido del que se realizan dichas copias. MABS, WABS" 
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


# Servicios de BizTalk: copias de seguridad y restauración

Servicios de BizTalk de Azure incluye las capacidades de copia de seguridad y restauración. En este tema se describe cómo realizar la copia de seguridad y la restauración de los servicios de BizTalk con el Portal de Azure clásico.

También puede realizar la copia de seguridad de los Servicios de BizTalk mediante la [API REST de Servicios de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584).

> [AZURE.NOTE] NO se realiza ninguna copia de seguridad de las conexiones híbridas, independientemente de la edición. Debe volver a crear las conexiones híbridas.

## Introducción

- Puede que las copias de seguridad y restauración no estén disponible para todas las ediciones. Consulte [Servicios de BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md).

- En el Portal de Azure clásico puede crear una copia de seguridad bajo demanda o crear una copia de seguridad programada.

- El contenido de las copias de seguridad se puede restaurar en el mismo servicio de BizTalk o en uno nuevo. Para restaurar el servicio de BizTalk con el mismo nombre, es preciso eliminar el servicio de BizTalk existente y el nombre debe estar disponible. Después de eliminar un servicio de BizTalk, puede tardar más tiempo del deseado para que el mismo nombre esté disponible. Si no puede esperar a que el mismo nombre esté disponible, restaure a un nuevo servicio de BizTalk.

- Servicios de BizTalk se puede restaura en la misma edición o en una posterior. No se puede realizar la restauración de los Servicios de BizTalk en una edición anterior con respecto a la usada para realizar la copia de seguridad.

	Por ejemplo, una copia de seguridad que usa la Basic Edition se puede restaurar a la Premium Edition. Sin embargo, una copia de seguridad que usa la Premium Edition no se puede restaurar a la Standard Edition.

- A fin de mantener la continuidad de los números de control del intercambio electrónico de datos (EDI), estos se incluyen en una copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad y se restaura el contenido de esta copia de seguridad, se pueden duplicar los números de control.

- Si un lote tiene mensajes activos, procéselo **antes** de ejecutar una copia de seguridad. Al crear una copia de seguridad (según sea necesario o según se programe), no se almacenan nunca los mensajes en lotes.

	**Si se realiza una copia de seguridad con mensajes activos en un lote, estos mensajes no se incluyen en la copia de seguridad y, por lo tanto, se pierden.**

- Opcional: en el Portal de Servicios de BizTalk, detenga todas las operaciones de administración.


## Creación de una copia de seguridad

Puede realizar una copia de seguridad en cualquier momento y controlarla por completo. Esta sección muestra los pasos para crear copias de seguridad mediante el Portal de Azure clásico, entre los que se incluyen:

[Copia de seguridad bajo demanda](#backupnow)

[Programación de una copia de seguridad](#backupschedule)

#### <a name="backupnow"></a>Copia de seguridad bajo demanda
1. En el Portal de Azure clásico, seleccione **Servicios de BizTalk** y luego el servicio de BizTalk del que quiera realizar la copia de seguridad.
2. En la pestaña **Panel**, seleccione **Hacer una copia de seguridad** en la parte inferior de la página.
3. Escriba un nombre para la copia de seguridad. Por ejemplo, escriba *myBizTalkService*BU*Fecha*.
4. Elija una cuenta de almacenamiento de blobs y seleccione la marca de verificación para iniciar la copia de seguridad.

Una vez que finalice la copia de seguridad, en la cuenta de almacenamiento se creará un contenedor con el nombre de copia de seguridad que escriba. Este contenedor contiene la configuración de la copia de seguridad del servicio de BizTalk.

#### <a name="backupschedule"></a>Programación de una copia de seguridad

1. En el Portal de Azure clásico, seleccione **Servicios de BizTalk**, seleccione el nombre del servicio de BizTalk del que quiere programar la copia de seguridad y luego seleccione la pestaña **Configurar**.
2. Establezca **Estado de la copia de seguridad** en **Automático**. 
3. Seleccione la **Cuenta de almacenamiento** para almacenar la copia de seguridad, especifique la **Frecuencia** con la que desea crear las copias de seguridad y cuánto tiempo quiere conservar las copias de seguridad (**Días de retención**):

	![][AutomaticBU]

	**Notas**
	- En **Días de retención**, el período de retención debe ser superior a la frecuencia de las copias de seguridad.
	- Seleccione **Conservar siempre al menos una copia de seguridad**, incluso aunque haya pasado el período de retención.
	

4. Seleccione **Guardar**.


Cuando se ejecuta un trabajo de copia de seguridad programada, se crea un contenedor (para almacenar los datos de la copia de seguridad) en la cuenta de almacenamiento que haya especificado. El nombre del contenedor se denomina *BizTalk Service Name-date-time*.

Si el panel del Servicios de BizTalk muestra un estado **Con error**:

![Estado de la última copia de seguridad programada][BackupStatus]

El vínculo abre los Registros de operaciones de Servicios de administración para ayudar a solucionar problemas. Consulte [Servicios de BizTalk: solución de problemas mediante registros de operaciones](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## Restauración

Puede restaurar las copias de seguridad desde el Portal de Azure clásico o desde la [API REST para la restauración del Servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). En esta sección se muestran los pasos para realizar la restauración mediante el portal clásico.

#### Antes de restaurar una copia de seguridad

- Se pueden especificar los nuevos almacenes de seguimiento, archivado y supervisión al restaurar un servicio de BizTalk.

- Se restauran los mismos datos del tiempo de ejecución de EDI. La copia de seguridad del tiempo de ejecución de EDI almacena los números de control. Los números de control se restauran en secuencias desde la hora de la copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad y se restaura el contenido de esta copia de seguridad, se pueden duplicar los números de control.

#### Restauración de una copia de seguridad

1. En el Portal de Azure clásico, seleccione **Nuevo** > **Servicios de aplicaciones** > **Servicio de BizTalk** > **Restaurar**:

	![Restauración de una copia de seguridad][Restore]

2. En **URL de copia de seguridad**, seleccione el icono de la carpeta y expanda la cuenta de almacenamiento de Azure que almacena la copia de seguridad de configuración del Servicio de BizTalk. Expanda el contenedor y, en el panel derecho, seleccione el archivo .txt de copia de seguridad correspondiente. <br/><br/> Seleccione **Abrir**.

3. En la página **Restaurar servicio de BizTalk**, escriba un **Nombre del servicio de BizTalk** y compruebe la **URL de dominio**, **Edición** y la **Región** para el Servicio de BizTalk restaurado. **Cree una nueva instancia de base de datos SQL** para la base de datos de seguimiento:

	![][RestoreBizTalkService]

	Seleccione la flecha siguiente.

4. 	Compruebe el nombre de la base de datos SQL, especifique el servidor físico en el que se creará la base de datos SQL, y el nombre de usuario y la contraseña de dicho servidor.


	Si desea configurar la edición de la base de datos SQL, el tamaño y otras propiedades, seleccione **Configurar las opciones avanzadas de base de datos**.

	Seleccione la flecha siguiente.

5. Cree una nueva cuenta de almacenamiento o especifique una cuenta de almacenamiento existente para los Servicios de BizTalk.

7. Seleccione la marca de verificación para iniciar la restauración.

Cuando se haya realizado correctamente la restauración, se incluye un nuevo servicio de BizTalk cuyo estado está suspendido en la página de servicios de BizTalk del Portal de Azure clásico.



### <a name="postrestore"></a>Después de restaurar una copia de seguridad

El servicio de BizTalk siempre está restaurado en un estado **Suspendido**. En este estado, puede realizar cambios de configuración antes de que el nuevo entorno sea funcional, incluyendo:

- Si ha creado aplicaciones del servicio de BizTalk mediante el SDK de los Servicios de BizTalk de Azure, puede que tenga que actualizar las credenciales de control de acceso (ACS) en dichas aplicaciones para que puedan funcionar con el entorno restaurado.

- Restaure un Servicio de BizTalk para replicar un entorno de Servicio de BizTalk existente. En esta situación, si hay contratos configurados en el portal de Servicios de BizTalk original que usa una carpeta FTP de origen, quizá desee actualizar los contratos del entorno recién restaurado para que usen una carpeta FTP con otro origen. De lo contrario, puede haber dos contratos diferentes intentando extraer el mismo mensaje.

- Si ha llevado a cabo la restauración para tener varios entornos del Servicio de BizTalk, asegúrese de elegir el entorno correcto en las aplicaciones de Visual Studio, los cmdlets de PowerShell, las API de REST o las API de OM de administración de socios comerciales.

- Es recomendable configurar copias de seguridad automatizadas en el entorno del Servicio de BizTalk recién restaurado.

Para iniciar el Servicio de BizTalk en el Portal de Azure clásico, seleccione el Servicio de BizTalk restaurado y seleccione **Reanudar** en la barra de tareas.



## ¿Qué se incluye en la copia de seguridad?

Se incluyen los elementos siguientes al crear una copia de seguridad:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementos incluidos en la copia de seguridad</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de Servicios de BizTalk de Azure</strong></td>
</tr> 
<tr>
<td>Configuración y tiempo de ejecución</td> 
<td>
<ul>
<li>Detalles del asociado y perfil</li>
<li>Acuerdos de socios</li>
<li>Ensamblados personalizados implementados</li>
<li>Puentes implementados</li>
<li>Certificados</li>
<li>Transformaciones implementadas</li>
<li>Procesos</li>
<li>Plantillas creadas y guardadas en el portal de servicios de BizTalk</li>
<li>Asignaciones X12 ST01 y GS01</li>
<li>Números de control (EDI)</li>
<li>Valores MIC de mensaje AS2</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Servicio de BizTalk de Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Datos del certificado SSL</li>
<li>Contraseña del certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configuración del servicio de BizTalk</td> 
<td>
<ul>
<li>Recuento de unidades de escalado</li>
<li>Edition</li>
<li>Versión del producto</li>
<li>Región/Centro de datos</li>
<li>Clave y espacio de nombres del servicio de control de acceso (ACS)</li>
<li>Cadena de conexión de la base de datos de seguimiento</li>
<li>Cadena de conexión de la cuenta de almacenamiento de archivado</li>
<li>Cadena de conexión de la cuenta de almacenamiento de supervisión</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Elementos adicionales</strong></td>
</tr> 
<tr>
<td>Base de datos de seguimiento</td> 
<td>Cuando se crea el Servicio de BizTalk, se definen los detalles de la base de datos de seguimiento, entre los que se incluyen el servidor de Base de datos SQL de Azure y el nombre de la base de datos de seguimiento. No se realiza la copia de seguridad de la base de datos de seguimiento de forma automática.
<br/><br/>
<strong>Importante</strong><br/>
Si la base de datos de seguimiento se elimina y hay que recuperarla, debe existir una copia de seguridad previa. Si no la hay, no se podrán recuperar la base de datos de seguimiento ni sus datos. En esta situación, cree una nueva base de datos de seguimiento con el mismo nombre. Además, se recomienda la replicación geográfica.</td>
</tr> 
</table>

## Pasos siguientes

Para crear los Servicios de BizTalk de Azure en el Portal de Azure clásico, vaya a [Creación de Servicios de BizTalk mediante el Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

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

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 

<!---HONumber=AcomDC_0302_2016-->