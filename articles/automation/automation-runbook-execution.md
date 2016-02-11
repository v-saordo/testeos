<properties
   pageTitle="Ejecución de un runbook en Automatización de Azure"
   description="Describe los detalles de cómo se procesa un runbook en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="bwren" />

# Ejecución de un runbook en Automatización de Azure


Cuando se inicia un runbook en Automatización de Azure, se crea un trabajo. Un trabajo es una instancia única de ejecución de un runbook. Un trabajador de Automatización de Azure está asignado para ejecutar cada trabajo. Aunque los trabajadores se comparten por varias cuentas de Azure, los trabajos de diferentes cuentas de Automatización están aislados entre sí. No tiene el control sobre qué trabajador prestará servicio a la solicitud para el trabajo. Un único runbook puede tener varios trabajos que se ejecutan al mismo tiempo. Al ver la lista de runbooks en el portal de Azure, se mostrará el estado del última trabajo iniciado por cada runbook. Puede ver la lista de trabajos para cada runbook para hacer un seguimiento del estado de cada uno. Para obtener una descripción de los distintos estados de trabajo, consulte [ Estados del trabajo](#job-statuses).

En el siguiente diagrama se muestra el ciclo de vida de un trabajo de runbook para [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) y [Runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Estados de trabajo: flujo de trabajo de PowerShell](./media/automation-runbook-execution/job-statuses.png)

En el siguiente diagrama se muestra el ciclo de vida de un trabajo de runbook para [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks).

![Estados de trabajo: script de PowerShell](./media/automation-runbook-execution/job-statuses-script.png)


Los trabajos tendrán acceso a los recursos de Azure mediante una conexión a la suscripción de Azure. Solo tendrán acceso a los recursos de su centro de datos si estos recursos están accesibles desde la nube pública.

## Estados del trabajo

En la tabla siguiente se describen los diferentes estados posibles para un trabajo.

| Status| Descripción|
|:---|:---|
|Completed|El trabajo se completó correctamente.|
|Con error| Para [Runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md), no se pudo compilar el runbook. Para [Runbooks de script de PowerShell](automation-runbook-types.md), no se pudo iniciar el runbook o el trabajo encontró una excepción. |
|Error, esperando recursos|Se produjo un error en el trabajo porque ha alcanzado el límite de [distribución equilibrada](#fairshare) tres veces y se ha iniciado en el mismo punto de control o en el inicio del runbook cada vez.|
|En cola|El trabajo espera que los recursos en un trabajador de Automatización estén disponible para que se puede iniciar.|
|Iniciando|El trabajo se ha asignado a un trabajador y el sistema está en proceso de iniciarse.|
|Reanudando|El sistema está en proceso de reanudar el trabajo después de que se suspendió.|
|Ejecución|El trabajo se está ejecutando.|
|En ejecución, esperando recursos|El trabajo se ha descargado porque ha alcanzado el límite de [distribución equilibrada](#fairshare). Se reanudará en breve desde su último punto de control.|
|Stopped|El trabajo lo detuvo el usuario antes de completarse.|
|Deteniéndose|El sistema está en proceso de detener el trabajo.|
|Suspended|El trabajo lo ha suspendido el usuario, el sistema o un comando en el runbook. Un trabajo suspendido se puede iniciar de nuevo y se reanudará desde su último punto de control o desde el principio del runbook si no tiene ningún punto de control. El runbook solo lo suspenderá el sistema en el caso de una excepción. De forma predeterminada, se establece ErrorActionPreference en **Continuar**, lo que significa que el trabajo seguirá ejecutándose en un error. Si se establece esta variable de preferencia en **Detener**, se suspenderá el trabajo en un error. Solo se aplica a [Runbooks de flujo de trabajo de PowerShell y gráficos](automation-runbook-types.md).|
|Suspendiendo|El sistema está intentando suspender el trabajo a petición del usuario. El runbook debe alcanzar su siguiente punto de control antes de que se pueda suspender. Si ya ha pasado su último punto de comprobación, se completará antes de que se pueda suspender. Solo se aplica a [Runbooks de flujo de trabajo de PowerShell y gráficos](automation-runbook-types.md).|

## Visualización de un estado de trabajo con el Portal de administración de Azure

### Panel de Automatización

El panel de Automatización muestra un resumen de todos los runbooks de una cuenta de Automatización particular. También incluye la información general del uso de la cuenta. El gráfico de resumen muestra el número de trabajos totales de todos los runbooks que han pasado a cada estado en un número determinado de días u horas. Puede seleccionar el intervalo de tiempo en la esquina superior derecha del gráfico. El eje de tiempo del gráfico cambiará según el tipo de intervalo de tiempo que seleccione. Puede elegir si desea mostrar la línea de un estado determinado haciendo clic en él en la parte superior de la pantalla.

Puede utilizar los pasos siguientes para mostrar el panel Automatización.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Panel**.

### Panel de runbook

El panel de runbook muestra un resumen de un único runbook. El gráfico de resumen muestra el número de trabajos totales para el runbook que ha pasado a cada estado en un número determinado de días u horas. Puede seleccionar el intervalo de tiempo en la esquina superior derecha del gráfico. El eje de tiempo del gráfico cambiará según el tipo de intervalo de tiempo que seleccione. Puede elegir si desea mostrar la línea de un estado determinado haciendo clic en él en la parte superior de la pantalla.

Puede utilizar los pasos siguientes para mostrar el panel de runbook.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Haga clic en el nombre de un runbook.
1. Seleccione la pestaña **Panel**.

### Resumen del trabajo

Puede ver una lista de todos los trabajos que se han creado para un runbook determinado y su estado más reciente. Puede filtrar esta lista por estado del trabajo y el intervalo de fechas del último cambio del trabajo. Haga clic en el nombre de un trabajo para ver su información detallada y la salida. La vista detallada del trabajo incluye los valores para los parámetros de runbook que se proporcionaron con ese trabajo.

Puede utilizar los pasos siguientes para ver los trabajos de un runbook.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Haga clic en el nombre de un runbook.
1. Seleccione la pestaña **Trabajos**.
1. Haga clic en la columna **Trabajo creado** de un trabajo para ver sus detalles y la salida.

## Recuperación del estado del trabajo con Windows PowerShell

Puede utilizar [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para recuperar los trabajos creados para un runbook y los detalles de un trabajo determinado. Si inicia un runbook con Windows PowerShell mediante el uso [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx), devolverá el trabajo resultante. Utilice la salida [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para obtener la salida de un trabajo.

Los comandos de ejemplo siguientes recuperan el último trabajo para un runbook de ejemplo y muestra su estado, los valores proporcionan para los parámetros de runbook y la salida del trabajo.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Equitativamente

Para compartir recursos entre todos los runbooks en la nube, Automatización de Azure descargará temporalmente cualquier trabajo cuando haya estado ejecutándose durante 3 horas. Los runbook [Gráfico](automation-runbook-types.md#graphical-runbooks) y [Flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) se reanudarán desde su último [punto de control](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints). Durante este tiempo, el trabajo mostrará un estado En ejecución, Esperando recursos. Si el runbook no tiene puntos de control o el trabajo no había alcanzado el primer punto de control antes de la descarga, se reiniciará desde el principio. Los runbook de [PowerShell](automation-runbook-types.md#powershell-runbooks) siempre se reinician desde el principio, ya que no admiten los puntos de control.

Si el runbook se reinicia desde el mismo punto de control o desde el principio del runbook tres veces consecutivas, terminará con un estado Error, esperando recursos. De esta forma se impide que los runbooks se ejecuten de manera indefinida sin completarse, ya que no pueden llegar al siguiente punto de control sin que se descarguen de nuevo. En este caso, recibirá la siguiente excepción con el error.

*El trabajo no puede continuar ejecutándose porque se expulsó repetidamente del mismo punto de control. Asegúrese de que el runbook no realiza operaciones largas sin conservar su estado.*

Cuando se crea un runbook, debe asegurarse de que el tiempo para ejecutar las actividades entre dos puntos de control no supere las 3 horas. Puede que necesite agregar puntos de control a un runbook para asegurarse de que no alcanza este límite de 3 horas ni divide operaciones de ejecución prolongada. Por ejemplo, su runbook podría realizar una reindexación en una gran base de datos SQL. Si esta operación no se completa dentro del límite de distribución equilibrada, el trabajo se descargará y se reiniciará desde el principio. En este caso, debe dividir la operación de reindexación en varios pasos, como volver a indexar una tabla a la vez y, a continuación, inserte un punto de control después de cada operación, de modo que el trabajo se pueda reanudar después de la última operación para completar.



## Artículos relacionados

- [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md)

<!---HONumber=Nov15_HO3-->