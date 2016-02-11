<properties 
   pageTitle="Aprendizaje del flujo de trabajo de PowerShell"
   description="Este artículo está destinado como una lección rápida para que los autores familiarizados con PowerShell comprendan las diferencias específicas entre Powershell y el flujo de trabajo de PowerShell."
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# Aprendizaje del flujo de trabajo de Windows PowerShell

Los runbooks de Automatización de Azure se implementan como flujos de trabajo de Windows PowerShell. Un flujo de trabajo de Windows PowerShell es similar a un script de Windows PowerShell, pero presenta algunas diferencias importantes que pueden resultar confusas para un usuario nuevo. Este artículo está destinado a usuarios que ya están familiarizados con PowerShell y en él se explican brevemente los conceptos que son necesarios si va a convertir un script de PowerShell a un flujo de trabajo de PowerShell para su uso en un runbook.

Un flujo de trabajo es una secuencia de pasos programados y conectados que realizan tareas de larga duración o requieren la coordinación de varios pasos en varios dispositivos o nodos administrados. Las ventajas de un flujo de trabajo en un script normal incluyen la capacidad de realizar una acción en varios dispositivos simultáneamente y la capacidad de recuperarse automáticamente de los errores. Un flujo de trabajo de Windows PowerShell es un script de Windows PowerShell que se aprovecha de Windows Workflow Foundation. Aunque el flujo de trabajo está escrito con sintaxis de Windows PowerShell y se inicia mediante Windows PowerShell, se procesa mediante Windows Workflow Foundation.

Para obtener información detallada sobre los temas de este artículo, vea [Introducción al flujo de trabajo de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## Tipos de runbook

Hay tres tipos de runbook en Automatización de Azure: *flujo de trabajo de PowerShell*, *PowerShell* y *gráfico*. El tipo de runbook se define cuando se crea el runbook, y no puede convertir un runbook en el otro tipo una vez creado.

Los runbooks de flujo de trabajo de PowerShell y de PowerShell están destinados a los usuarios que prefieren trabajar directamente con el código de PowerShell mediante el editor de texto de Automatización de Azure o un editor sin conexión como PowerShell ISE. Debe comprender la información de este artículo si va a crear un runbook de flujo de trabajo de PowerShell.

Los runbooks gráficos permiten crear un runbook con las mismas actividades y cmdlets pero mediante una interfaz gráfica que oculta la complejidad del flujo de trabajo subyacente de PowerShell. Los conceptos de este artículo, como los puntos de comprobación y la ejecución en paralelo se siguen aplican a los runbooks gráficos, pero no tendrá que preocuparse por la sintaxis detallada.

## Estructura básica de un flujo de trabajo

El primer paso para convertir un script de PowerShell en un flujo de trabajo de PowerShell es delimitarlo con la palabra clave **Workflow**. Un flujo de trabajo comienza con la palabra clave **Workflow**, seguida del cuerpo del script entre llaves. El nombre del flujo de trabajo sigue a la palabra clave **Workflow**, tal como se muestra en la sintaxis siguiente.

    Workflow Test-Workflow
    {
       <Commands>
    }

El nombre del flujo de trabajo debe coincidir con el nombre del runbook de Automatización. Si se va a importar el runbook, el nombre de archivo debe coincidir con el nombre del flujo de trabajo y debe terminar en. ps1.

Para agregar parámetros al flujo de trabajo, use la palabra clave **Param** palabra clave igual que lo haría para un script.

## Cambios de código

El código de flujo de trabajo de PowerShell es casi idéntico al código de script de PowerShell salvo por algunos cambios importantes. En las secciones siguientes se describen los cambios que deberá realizar en un script de PowerShell para que se ejecute en un flujo de trabajo.

### Actividades

Una actividad es una tarea específica de un flujo de trabajo. Al igual que un script se compone de uno o varios comandos, un flujo de trabajo se compone de una o varias actividades que se realizan en secuencia. El flujo de trabajo de Windows PowerShell convierte automáticamente muchos de los cmdlets de Windows PowerShell en actividades cuando se ejecuta un flujo de trabajo. Cuando se especifica uno de estos cmdlets en el runbook, Windows Workflow Foundation ejecuta realmente la actividad correspondiente. Para los cmdlets sin actividad correspondiente, el flujo de trabajo de Windows PowerShell ejecuta automáticamente el cmdlet dentro de una actividad [InlineScript](#inlinescript). Hay un conjunto de cmdlets que están excluidos y no se pueden usar en un flujo de trabajo a menos que incluya explícitamente en un bloque de InlineScript. Para obtener más información sobre estos conceptos, consulte [Uso de actividades en los flujos de trabajo de scripts](http://technet.microsoft.com/library/jj574194.aspx).

Las actividades de flujo de trabajo comparten un conjunto de parámetros comunes para configurar su funcionamiento. Para obtener más información acerca de los parámetros comunes del flujo de trabajo, consulte [about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### Parámetros posicionales

No puede usar parámetros posicionales con actividades y cmdlets en un flujo de trabajo. Todo esto significa que debe usar nombres de parámetros.

Por ejemplo, considere el siguiente código que obtiene todos los servicios en ejecución.

	 Get-Service | Where-Object {$_.Status -eq "Running"}

Si intenta ejecutar este mismo código en un flujo de trabajo, obtendrá un mensaje parecido a "El conjunto de parámetros no se puede resolver mediante los parámetros con nombre especificados". Para corregir este problema, basta con proporcionar el nombre del parámetro de la forma siguiente.

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### Objetos deserializados

Los objetos de los flujos de trabajo están deserializados. Esto significa que sus propiedades siguen estando disponibles, pero no sus métodos. Por ejemplo, considere el siguiente código de PowerShell que detiene un servicio mediante el método Stop del objeto Service.

	$Service = Get-Service -Name MyService
	$Service.Stop()

Si intenta ejecutar esto en un flujo de trabajo, obtendrá un error que indica "La invocación del método no se admite en un flujo de trabajo de Windows PowerShell".

Una opción es ajustar estas dos líneas de código en un bloque [InlineScript](#InlineScript), en cuyo caso $Service sería un objeto de servicio dentro del bloque.

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

Otra opción es usar otro cmdlet que realiza la misma funcionalidad que el método, si hay uno disponible. En nuestro ejemplo, el cmdlet Stop-Service proporciona la misma funcionalidad que el método Stop, y podría usar lo siguiente para un flujo de trabajo.

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

La actividad **InlineScript** es útil cuando necesita ejecutar uno o más comandos tradicional como script tradicional de PowerShell en lugar de como flujo de trabajo de PowerShell. Mientras que los comandos de un flujo de trabajo se envían a Windows Workflow Foundation para su procesamiento, los comandos de un bloque de InlineScript se procesan mediante Windows PowerShell.

InlineScript usa la sintaxis que se muestra a continuación.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Puede devolver resultados de InlineScript asignando el resultado a una variable. El siguiente ejemplo detiene un servicio y, a continuación, envía el nombre de servicio.

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


Puede pasar valores en un bloque de InlineScript, pero debe usar el modificador de ámbito **$Using**. El ejemplo siguiente es idéntico al ejemplo anterior salvo que se proporciona el nombre de servicio mediante una variable.

	Workflow Stop-MyService
	{
		$ServiceName = "MyService"
	
		$Output = InlineScript {
			$Service = Get-Service -Name $Using:ServiceName
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


Aunque las actividades InlineScript pueden ser críticas en algunos flujos de trabajo, no son compatibles con las construcciones de flujo de trabajo y solo se deben usar cuando sea necesario por las razones siguientes:

- No puede usar [puntos de comprobación](#Checkpoints) dentro de un bloque de InlineScript. Si se produce un error dentro del bloque, se debe reanudar desde el principio del bloque.
- No puede usar la [ejecución en paralelo](#parallel-execution) dentro de un InlineScriptBlock.
- InlineScript afecta a la escalabilidad del flujo de trabajo, ya que retiene la sesión de Windows PowerShell durante todo el bloque de InlineScript.

Para obtener más información acerca del uso de InlineScript, consulte [Ejecución de comandos de Windows PowerShell en un flujo de trabajo](http://technet.microsoft.com/library/jj574197.aspx) y [about\_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## Procesamiento en paralelo

Una ventaja de los flujos de trabajo de Windows PowerShell es la capacidad para realizar un conjunto de comandos en paralelo en lugar de hacerlo secuencialmente como con un script típico.

Puede utilizar la palabra clave **Parallel** para crear un bloque de scripts con varios comandos que se ejecutarán simultáneamente. Usa la sintaxis que se muestra a continuación. En este caso, Activity1 y Activity2 se iniciarán al mismo tiempo. Activity3 se iniciará después de que se hayan completado Activity1 y Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por ejemplo, considere los siguientes comandos de PowerShell que copian varios archivos a un destino de red. Estos comandos se ejecutan secuencialmente de modo que un archivo debe terminar de copiarse antes de que comience el siguiente.

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

El flujo de trabajo siguiente ejecuta estos mismos comandos en paralelo para que todos ellos empiezan a copiarse al mismo tiempo. Una vez que todos se han copiado completamente, se muestra el mensaje de finalización.

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


Puede utilizar la construcción **ForEach-Parallel** para procesar comandos para cada elemento de una colección simultáneamente. Los elementos de la colección se procesan en paralelo mientras que los comandos del bloque de scripts se ejecutan secuencialmente. Usa la sintaxis que se muestra a continuación. En este caso, Activity1 se iniciará al mismo tiempo para todos los elementos de la colección. Para cada elemento, Activity2 se iniciará una vez completada Activity1. Activity3 se iniciará únicamente después de que se hayan completado Activity1 y Activity2 para todos los elementos.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

El ejemplo siguiente es similar al ejemplo anterior, en el que se copian archivos en paralelo. En este caso, se muestra un mensaje para cada archivo después de copiarse. Solo después de que todos se hayan copiado completamente, aparecerá el mensaje de finalización.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]  No se recomienda ejecutar runbooks secundarios en paralelo, ya que se ha demostrado que no tiene unos resultados confiables. A veces el resultado del runbook secundario no se mostrará, y la configuración de un runbook secundario puede afectar al resto de runbooks secundarios en paralelo.


## Puntos de control

Un *punto de control* es una instantánea del estado actual del flujo de trabajo que incluye el valor actual de las variables y cualquier salida generada en ese punto. Si un flujo de trabajo termina en error o se [suspende](suspending-a-workflow), la próxima vez que se ejecute comenzará desde su último punto de comprobación en lugar del inicio del flujo de trabajo. Puede establecer un punto de control en un flujo de trabajo con la actividad **Checkpoint-Workflow**.

En el código de ejemplo siguiente, se produce una excepción después de Activity2 que provoca la finalización del flujo de trabajo. Cuando se vuelve a ejecutar el flujo de trabajo, se inicia ejecutando Activity2, ya que estaba justo después del último punto de comprobación establecido.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Debe establecer los puntos de comprobación en un flujo de trabajo después de las actividades que puedan ser propensas a la excepción y que no deben repetirse si se reanuda el flujo de trabajo. Por ejemplo, el flujo de trabajo puede crear una máquina virtual. Puede establecer un punto de control antes y después de los comandos para crear la máquina virtual. Si se produce un error en la creación, los comandos se repetirían si el flujo de trabajo se vuelve a iniciar. Si el flujo de trabajo produce un error después de que la creación se haya realizado correctamente, la máquina virtual no se volverá a crear cuando se reanude el flujo de trabajo.

El siguiente ejemplo copia varios archivos en una ubicación de red y establece un punto de comprobación después de cada archivo. Si la ubicación de red se pierde, el flujo de trabajo finalizará con error. Cuando se vuelva a iniciar, se reanudará en el último punto de comprobación, lo que significa que solo se omitirán los archivos que ya se hayan copiado.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}



Para obtener más información acerca de los puntos de control, consulte [Adición de puntos de control a un flujo de trabajo de scripts](http://technet.microsoft.com/library/jj574114.aspx).



## Artículos relacionados

- [Introducción al flujo de trabajo de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx) 

<!---HONumber=AcomDC_0128_2016-->