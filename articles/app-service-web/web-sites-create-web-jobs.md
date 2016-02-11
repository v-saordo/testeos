<properties 
	pageTitle="Ejecutar tareas en segundo plano con trabajos web" 
	description="Obtenga información sobre cómo ejecutar tareas en segundo plano en aplicaciones web de Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="tdykstra"/>

# Ejecutar tareas en segundo plano con trabajos web

## Información general

Puede ejecutar programas o scripts en trabajos web en su aplicación web del [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) de tres maneras: a petición, de forma continua o según una programación. No hay ningún coste adicional en el uso de trabajos web.

En este artículo se muestra cómo implementar Webjobs con el [Portal de Azure](https://portal.azure.com). Para obtener información sobre cómo implementar con Visual Studio o con un proceso de entrega continua, consulte [Implementación de Webjobs de Azure en aplicaciones web](websites-dotnet-deploy-webjobs.md).

El SDK de trabajos web de Azure simplifica muchas de las tareas de programación de trabajos web. Para obtener más información, consulte [¿Qué es el SDK de Webjobs?](websites-dotnet-webjobs-sdk.md)

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="acceptablefiles"></a>Tipos de archivo válidos para scripts o programas

Se aceptan los siguientes tipos de archivo:

* .cmd, .bat, .exe (con windows cmd)
* .ps1 (con powershell)
* .sh (con bash)
* .php (con php)
* .py (con python)
* .js (con node)
* .jar (con java)

## <a name="CreateOnDemand"></a>Creación de un trabajo web a petición en el portal

1. En la hoja **Aplicación web** del [Portal de Azure](https://portal.azure.com), haga clic en **Toda la configuración > Webjobs** para mostrar la hoja **Webjobs**.
	
	![Hoja de WebJobs](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Haga clic en **Agregar**. Aparecerá el cuadro de diálogo **Agregar Webjob**.
	
	![Adición de hoja de WebJobs](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. En **Nombre**, escriba un nombre para el trabajo web. El nombre debe comenzar con una letra o un número y no puede contener ningún carácter especial salvo "-" y "\_".
	
4. En el cuadro **Cómo ejecutar**, elija **Ejecutar a petición**.
	
3. En el cuadro **Carga de archivos**, haga clic en el icono de la carpeta y busque el archivo zip que contiene el script. El archivo zip debe contener el ejecutable (.exe, .cmd, .bat, .sh, .php, .py, .js) así como cualquier archivo complementario necesario para ejecutar el programa o script.
	
5. Active **Crear** para cargar el script de su aplicación web.
	
	El nombre especificado para el trabajo web aparece en la lista de la hoja **WebJobs**.
	
6. Para ejecutar el trabajo web, haga clic en su nombre en la lista y haga clic en **Ejecutar**.
	
	![Ejecución de WebJobs](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Creación de un trabajo web de ejecución continua

1. Para crear un trabajo web de ejecución continua, siga los pasos descritos para crear un trabajo web que se ejecuta solo una vez, pero en el cuadro **Cómo ejecutar**, elija **Continua**.

2. Para iniciar o detener un trabajo web continuo, haga clic en el trabajo web en la lista y haga clic en **Iniciar** o **Detener**.
	
> [AZURE.NOTE] Si la aplicación web se ejecuta en más de una instancia, se ejecutará un trabajo web continuamente en todas las instancias. Los Trabajos web a petición o programados se ejecutan en una única instancia seleccionada por Microsoft Azure para el equilibrio de carga.
	
> Para que los WebJobs continuos sigan ejecutándose en todas las instancias y de forma confiable, habilite la configuración Siempre activado* de la aplicación web, en caso contrario, es posible que dejen de funcionar cuando el sitio host SCM esté inactivo durante demasiado tiempo.

## <a name="CreateScheduledCRON"></a>Creación de un trabajo web programado utilizando una expresión CRON

Esta técnica está disponible para aplicaciones web que se ejecutan en modo Estándar o Premium y requiere que la opción **AlwaysOn** esté habilitada en la aplicación.

Para convertir un trabajo web a petición en un trabajo web programado, basta con incluir un archivo `settings.job` en la raíz del archivo ZIP del trabajo web. Este archivo JSON debe incluir una propiedad `schedule` con una [expresión CRON](https://en.wikipedia.org/wiki/Cron) como la siguiente.

La expresión CRON se compone de 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`.

Por ejemplo, para desencadenar el trabajo web cada 15 minutos, la `settings.job` tendría:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

Otros ejemplos de programación CRON:

- Cada hora (es decir, siempre que el número de minutos sea 0): `0 0 * * * *` 
- Cada hora de 9 a 17 horas: `0 0 9-17 * * *` 
- A las 9:30 todos los días: `0 30 9 * * *`
- A las 9:30 cada día de lunes a viernes: `0 30 9 * * 1-5`

**Nota**: al implementar un trabajo web desde Visual Studio, asegúrese de marcar las propiedades de archivo `settings.job` como "Copiar si es posterior".


## <a name="CreateScheduled"></a>Creación de un trabajo web programado mediante el programador de Azure

La técnica alternativa siguiente hace uso del programador de Azure. En este caso, el trabajo web no tiene ningún conocimiento directo de la programación. En su lugar, se debe configurar el programador de Azure para desencadenar el trabajo web según una programación.

El Portal de Azure aún no tiene la capacidad para crear un trabajo web programado, pero hasta que se agregue esa característica, puede hacerlo con el [Portal clásico](http://manage.windowsazure.com).

1. En el [Portal clásico](http://manage.windowsazure.com), vaya a la página del trabajo web y haga clic en **Agregar**.

1. En el cuadro **Cómo ejecutar**, seleccione **Ejecutar según una programación**.
	
	![Nuevo trabajo programado][NewScheduledJob]
	
2. Elija una región en la opción **Región del programador** para el trabajo y luego haga clic en la flecha de la parte inferior derecha del cuadro de diálogo para continuar a la pantalla siguiente.

3. En el cuadro de diálogo **Crear trabajo**, elija el tipo de **periodicidad** que quiere: **Trabajo único** o **Trabajo periódico**.
	
	![Periodicidad de la programación][SchdRecurrence]
	
4. Elija también una **Hora** de inicio: **Ahora** o **En un momento determinado**.
	
	![Hora de inicio de la programación][SchdStart]
	
5. Si desea comenzar a una hora determinada, elija los valores de la hora de inicio en **Iniciar a las**.
	
	![Inicio de la programación a una hora determinada][SchdStartOn]
	
6. Si elige un trabajo periódico, en la opción **Repetir cada** podrá especificar la frecuencia de la periodicidad y en la opción **Finalizar a las** podrá especificar una hora de finalización.
	
	![Periodicidad de la programación][SchdRecurEvery]
	
7. Si elige **Semanas**, puede seleccionar la casilla **Según un programa determinado** y especificar los días de la semana en los que quiere que se ejecute el trabajo.
	
	![Días de la semana de la programación][SchdWeeksOnParticular]
	
8. Si elige **Meses** y selecciona la casilla **Según un programa determinado**, podrá establecer que el trabajo se ejecute en un número de días determinado del mes seleccionándolos en la opción **Días**.
	
	![Fechas determinadas del mes de la programación][SchdMonthsOnPartDays]
	
9. Si elige **Días de la semana**, podrá seleccionar el día o días de la semana del mes en los que desea que se ejecute el trabajo.
	
	![Programación en días determinados de la semana de un mes][SchdMonthsOnPartWeekDays]
	
10. Finalmente, también puede utilizar la opción **Repeticiones** para elegir en qué semana del mes (primera, segunda, tercera, etc.) desea que se ejecute el trabajo en los días de la semana especificados.
	
	![Programación en días de la semana específicos de semanas determinadas][SchdMonthsOnPartWeekDaysOccurences]
	
11. Después de haber creado uno o varios trabajos, sus nombres aparecerán en la pestaña WebJobs con su estado, tipo de programación y otra información. Se conserva la información histórica de los últimos 30 trabajos web.
	
	![Lista de trabajos][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabajos programados y Programador de Azure

Los trabajos programados se pueden configurar adicionalmente en las páginas del Programador de Azure del [Portal clásico](http://manage.windowsazure.com).

1.	En la página WebJobs, haga clic en el vínculo **Programar** para desplazarse a la página del portal del Programador de Azure. 
	
	![Vínculo al Programador de Azure][LinkToScheduler]
	
2. En la página del Programador, haga clic en el trabajo.
	
	![Trabajo en la página del portal del Programador][SchedulerPortal]
	
3. Se abre la página **Acción del trabajo**, donde puede configurar adicionalmente el trabajo.
	
	![Página de acción del trabajo en el Programador][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Visualización del historial de trabajos

1. Para ver el historial de ejecución de un trabajo, incluidos los trabajos creados con el SDK de WebJobs, haga clic en su vínculo correspondiente en la columna **Registros** de la hoja WebJobs. (Puede utilizar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).
	
	![Vínculo de registros](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. Al hacer clic en el vínculo, se abre la página de detalles del trabajo web. En esta página se muestra el nombre de la ejecución del comando, las últimas veces que se ha ejecutado y si se ha ejecutado correctamente o no. En **Ejecuciones del trabajo recientes**, haga clic una vez para ver más detalles.
	
	![Detalles del trabajo web][WebJobDetails]
	
3. Se muestra la página **Detalles de ejecución del trabajo web**. Haga clic en **Alternar salida** para ver el texto del contenido del registro. El registro de resultados se muestra en formato de texto.
	
	![Detalles de ejecución del trabajo web][WebJobRunDetails]
	
4. Para ver el texto de salida en otra ventana del explorador, haga clic en el vínculo de **descarga**. Para descargar el texto propiamente dicho, haga clic con el botón secundario en el vínculo y utilice las opciones del explorador para guardar el contenido del archivo.
	
	![Resultado del registro de descarga][DownloadLogOutput]
	
5. El vínculo **Trabajos web** situado en la parte superior de la página ofrece una forma cómoda de obtener una lista de los trabajos web en el panel del historial.
	
	![Vínculo a la lista de WebJobs][WebJobsLinkToDashboardList]
	
	![Lista de WebJobs el panel del historial][WebJobsListInJobsDashboard]
	
	Al hacer clic en uno de estos vínculos, obtendrá acceso a la página de detalles de WebJob para el trabajo que ha seleccionado.


## <a name="WHPNotes"></a>Notas
	
- Las aplicaciones web en modo libre pueden agotar su tiempo de espera tras 20 minutos si no se realizan solicitudes al sitio de scm (implementación) y el portal de la aplicación web no está abierto en Azure. Las solicitudes al sitio real no restablecerán esto.
- Debe escribirse el código para un trabajo continuo para que se ejecute en un bucle infinito.
- Los trabajos continuos solo se ejecutan continuamente cuando la aplicación web está activa.
- Los modos básico y estándar ofrecen la característica Siempre disponible que, cuando está habilitada, impide que las aplicaciones web se vuelvan inactivas.
- Solo se pueden depurar WebJobs que se ejecutan continuamente. No se admite la depuración de WebJobs programada o a petición.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="NextSteps"></a>Pasos siguientes
 
Para obtener más información, consulte [Recursos recomendados de WebJobs de Azure][WebJobsRecommendedResources].

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]: http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]: http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
 

<!---HONumber=AcomDC_0128_2016-->