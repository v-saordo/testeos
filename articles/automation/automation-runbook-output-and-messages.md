<properties
   pageTitle="Salidas de runbook y mensajes en la Automatización de Azure | Microsoft Azure"
   description="Describe cómo crear y recuperar los mensajes de salida y error de los runbooks en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
   ms.author="magoedte;bwren" />

# Salidas de runbook y mensajes en la Automatización de Azure

La mayoría de los runbooks de Automatización de Azure generan alguna forma de salida, como el mensaje de error que verá el usuario o un objeto complejo que será consumido por otro flujo de trabajo. Windows PowerShell le ofrece [varios flujos](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) para enviar la salida desde un script o flujo de trabajo. Automatización de Azure funciona con cada uno de estos flujos de forma diferente, y es aconsejable que siga los procedimientos recomendados sobre cómo usar cada uno de ellos cuando cree un runbook.

En la siguiente tabla verá una breve descripción de cada uno de los flujos y su comportamiento en el Portal de administración de Azure, cuando se ejecuta un runbook publicado y cuando se [prueba un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx). Asimismo, en las siguientes secciones se proporciona más información sobre cada flujo.

| Stream | Descripción | Publicado | Prueba|
|:---|:---|:---|:---|
|Salida|Objetos destinados a ser consumidos por otros runbooks.|Se escribe en el historial de trabajos.|Se muestra en el panel de salida de la prueba.|
|Warning (Advertencia)|Mensaje de advertencia destinado al usuario.|Se escribe en el historial de trabajos.|Se muestra en el panel de salida de la prueba.|
|Error|Mensaje de error destinado al usuario. A diferencia de lo que sucede con las excepciones, el runbook continúa después de un mensaje de error de forma predeterminada.|Se escribe en el historial de trabajos.|Se muestra en el panel de salida de la prueba.|
|Detallado|Mensajes que proporcionan información general o de depuración.|Solo se escribe en el historial de trabajos si el registro detallado del runbook está activado.|Solo se muestra en el panel de salida de la prueba si el elemento $VerbosePreference está establecido como “Continue” en el runbook.|
|Progreso|Registros que se generan automáticamente antes y después de cada actividad del runbook. El runbook no debe intentar crear sus propios registros de progreso, ya que están dirigidos a un usuario interactivo.|Solo se escribe en el historial de trabajos si el registro del progreso del runbook está activado.|No se muestra en el panel de salida de la prueba.|
|Depurar|Mensajes dirigidos a un usuario interactivo. No debe usarse en runbooks.|No se escribe en el historial de trabajos.|No se escribe en el panel de salida de la prueba.|

## Flujo de salida

El flujo de salida está diseñado para la salida de los objetos creados por un script o flujo de trabajo, cuando se ejecuta correctamente. En Automatización de Azure, este flujo se usa principalmente para objetos destinados a ser consumidos por [runbooks primarios que llaman al runbook actual](automation-child-runbooks.md). Cuando se [llama a un runbook en línea](automation-child-runbooks.md/#InlineExecution) desde un runbook primario, este devuelve los datos del flujo de salida al runbook primario. Solo debe usar el flujo de salida para comunicar información general al usuario si sabe que ningún otro runbook nunca llamará a ese runbook. Le aconsejamos, como procedimiento recomendado, que use la opción de [Flujo detallado](#Verbose) para comunicar información general al usuario.

Puede escribir datos en el flujo de salida mediante [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) o colocando el objeto en su propia línea en el runbook.

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### Salida de una función

Cuando escribe en el flujo de salida de una función que está incluida en su runbook, la salida se devuelve al runbook. Si el runbook asigna esa salida a una variable, no se escribe en el flujo de salida. Asimismo, si se escribe en cualquier otro flujo desde la función, se escribirá en el flujo correspondiente del runbook.

Échele un vistazo al siguiente runbook de ejemplo.

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function

	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

El flujo de salida del trabajo del runbook sería:

	Output outside of function

El flujo detallado del trabajo del runbook sería:

	Verbose outside of function
	Verbose inside of function

### Declarar los tipos de datos de salida

Un flujo de trabajo puede especificar el tipo de datos de su salida mediante el [atributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Este atributo no tiene ningún efecto durante el tiempo de ejecución, pero proporciona al autor del runbook una indicación de la salida esperada en tiempo de diseño. A medida que el conjunto de herramientas de los runbooks evoluciona, aumenta la importancia de la declaración de tipos de datos de salida en tiempo de diseño. Como resultado, es recomendable incluir esta declaración en cualquier runbook que cree.

El siguiente runbook de ejemplo genera un objeto de cadena e incluye una declaración de su tipo de salida. Si su runbook genera una matriz de un tipo determinado, deberá especificar el tipo en lugar de una matriz de ese tipo.

	Workflow Test-Runbook
	{
	   [OutputType([string])]

	   $output = "This is some string output."
	   Write-Output $output
	}

## Flujos de mensajes

A diferencia del flujo de salida, los flujos de mensajes están diseñados para comunicar información al usuario. Hay varios flujos de mensajes para los diferentes tipos de información, y Automatización de Azure controla cada uno de ellos de forma diferente.

### Flujos de error y de advertencia

Los flujos de error y de advertencia están diseñados para registrar los problemas que se producen en un runbook. Se escriben en el historial de trabajos cuando se ejecuta un runbook y se incluyen en el panel de salida de la prueba del Portal de administración de Azure cuando se prueba un runbook. De forma predeterminada, el runbook continuará ejecutándose después de un error o advertencia. Para especificar que el runbook debe suspenderse en caso de advertencia o error, puede establecer una [variable de preferencia](#PreferenceVariables) en el runbook antes de crear el mensaje. Por ejemplo, para hacer que un runbook se suspenda en caso de error, tal y como haría una excepción, establezca **$ErrorActionPreference** en Stop.

Cree un mensaje de advertencia o de error mediante los cmdlets [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). También se pueden escribir actividades en estos flujos.

	#The following lines create a warning message and then an error message that will suspend the runbook.

	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### Flujo detallado

El flujo de mensajes detallados se usa para proporcionar información general acerca del funcionamiento del runbook. Como los runbooks no tienen disponible el [Flujo de depuración](#Debug), debe usar mensajes detallados para proporcionar información referente a la depuración. De forma predeterminada, los mensajes detallados de los runbooks publicados no se almacenarán en el historial de trabajos. Para almacenar mensajes detallados, configure los runbooks publicados mediante la opción “Escribir registros detallados” que se encuentra en la pestaña “Configurar” del runbook, en el Portal de administración de Azure. En la mayoría de los casos, deberá mantener la configuración predeterminada y no escribir los registros detallados de un runbook por motivos de rendimiento. Active esta opción solo para solucionar problemas o para depurar un runbook.

Cuando se [prueba un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), los mensajes detallados no se muestran aunque el runbook esté configurado para escribir registros detallados. Para mostrar mensajes detallados al [probar un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), debe establecer la variable $VerbosePreference en “Continue”. Cuando esa variable está establecida, los mensajes detallados se muestran en el panel de salida de la prueba del Portal de administración de Azure.

Cree un mensaje detallado mediante el cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx).

	#The following line creates a verbose message.

	Write-Verbose –Message "This is a verbose message."

### Flujo de depuración

El flujo de depuración está diseñado para usarse con un usuario interactivo y no debe usarse en runbooks.

## Registros de progreso

Si configura un runbook para escribir registros de progreso (en la pestaña Configurar del runbook del Portal de administración de Azure), se escribirá un registro en el historial de trabajos antes y después de la ejecución de cada actividad. En la mayoría de los casos, deberá mantener la configuración predeterminada y no escribir los registros de progreso de un runbook para así maximizar el rendimiento. Active esta opción solo para solucionar problemas o para depurar un runbook. Cuando se prueba un runbook, los mensajes de progreso no se muestran aunque el runbook esté configurado para escribir registros de progreso.

El cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) no es válido en los runbooks ya que está pensado para usarse con un usuario interactivo.

## Variables de preferencia

Windows PowerShell usa [variables de preferencia](http://technet.microsoft.com/library/hh847796.aspx) para determinar cómo responder a los datos que se hayan enviado a diferentes flujos de salida. Puede establecer estas variables en un runbook para controlar cómo responderá a los datos que se hayan enviado a diferentes flujos.

En la siguiente tabla se enumeran las variables de preferencia que pueden usarse en runbooks, junto con sus valores válidos y predeterminados. Tenga en cuenta que esta tabla solo incluye los valores que son válidos en un runbook Hay otros valores adicionales que son válidos para las variables de preferencia cuando se usan en Windows PowerShell fuera de Automatización de Azure.

| Variable| Valor predeterminado| Valores válidos|
|:---|:---|:---|
|WarningPreference|Continuar|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continuar|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

En la siguiente tabla se muestra el comportamiento de los valores de las variables de preferencia que son válidos en los runbooks.

| Valor| Comportamiento|
|:---|:---|
|Continuar|Registra el mensaje y continúa ejecutando el runbook.|
|SilentlyContinue|Continúa la ejecución del runbook sin registrar el mensaje. Hace que el mensaje se pase por alto.|
|Detención|Registra el mensaje y suspende el runbook.|

## Recuperar salidas de runbooks y mensajes

### Portal de administración de Azure

Puede ver los detalles de un trabajo de runbook en el Portal de administración de Azure de la pestaña llamada Trabajos de un runbook. La opción Resumen del trabajo le mostrará los parámetros de entrada y el [Flujo de salida](#Output), además de información general sobre el trabajo y las excepciones que se hayan producido. En la opción Historial se incluirán los mensajes del [Flujo de salida](#Output) y los [Flujos de error y de advertencia](#WarningError), además del [Flujo detallado](#Verbose) y los [Registros de progreso](#Progress), si el runbook está configurado para escribir registros detallados y de progreso.

### Windows PowerShell

En Windows PowerShell, puede recuperar la salida y los mensajes de un runbook con el cmdlet [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx). Este cmdlet requiere la identificación del trabajo y tiene un parámetro denominado Stream en el cual se especifica qué flujo hay que devolver. Puede elegir la opción “Cualquiera” para devolver todos los flujos del trabajo.

En el ejemplo siguiente se inicia un runbook y, a continuación, se espera a que finalice. Una vez completado, el flujo de salida se recopila del trabajo.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped")
	}

	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

### Creación gráfica

Para Runbooks gráficos, hay disponible un registro adicional en forma de seguimiento del nivel de actividad. Hay dos niveles de seguimiento: básico y detallado. En el seguimiento básico, puede ver la hora de inicio y de finalización de cada actividad en el Runbook, así como información relacionada con los reintentos de actividad, como el número de intentos y la hora de inicio de la actividad. En el seguimiento detallado, obtendrá un seguimiento básico y, además, datos de entrada y de salida para cada actividad. Tenga en cuenta que actualmente los registros de seguimiento se escriben usando el flujo detallado, por lo que debe habilitar el registro detallado cuando habilite el seguimiento. En el caso de los Runbooks gráficos con el seguimiento habilitado no es necesario registrar los registros de progreso, ya que el seguimiento básico tiene la misma finalidad y es más informativo.

![Vista de secuencias de trabajos de creación de gráficos](media/automation-runbook-output-and-messages/job_streams_view_blade.png)

Puede ver en la captura de pantalla anterior que, cuando se habilita el registro detallado y el seguimiento para los Runbooks gráficos, hay mucha más información disponible en la vista de secuencias de trabajos de producción. Esta información adicional puede ser esencial para solucionar problemas de producción con un Runbook. Por lo tanto, solo debería habilitarlo con este fin, y no hacerlo como regla general. Los registros de seguimiento pueden ser especialmente numerosos. Con el seguimiento de Runbooks gráficos puede obtener de dos a cuatro registros por actividad, en función de si configuró el seguimiento básico o detallado. A menos que necesite esta información para realizar un seguimiento del progreso de un Runbook para solucionar problemas, le conviene mantener el seguimiento desactivado.

**Para habilitar el seguimiento del nivel de actividad, siga estos pasos.**

 1. En el Portal de Azure, abra su cuenta de Automatización.

 2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.

 3. En la hoja Runbooks, haga clic para seleccionar un Runbook gráfico de la lista.

 4. En la hoja Configuración del Runbook seleccionado, haga clic en **Registro y seguimiento**.

 5. En la hoja Registro y seguimiento, en Registrar registros detallados, haga clic en **Activar** para habilitar el registro detallado. En Seguimiento del nivel de actividad, cambie el nivel de seguimiento a **Básico** o **Detallado**, en función del nivel de seguimiento que necesite.<br>

    ![Hoja de registro y seguimiento de creación de gráficos](media/automation-runbook-output-and-messages/logging_and_tracing_settings_blade.png)

## Artículos relacionados

- [Realizar el seguimiento de un trabajo de runbook](automation-runbook-execution.md)
- [Runbooks secundarios](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=AcomDC_0302_2016-->