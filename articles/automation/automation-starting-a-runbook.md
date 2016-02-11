<properties 
   pageTitle="Inicio de un runbook en Automatización de Azure | Microsoft Azure"
   description="Resume los distintos métodos que pueden usarse para iniciar un runbook de Automatización de Azure y proporciona detalles sobre cómo utilizar el Portal de Azure y Windows PowerShell."
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
   ms.date="01/19/2016"
   ms.author="bwren;sngun"/>

# Inicio de un runbook en Automatización de Azure

La tabla siguiente le ayudará a determinar el método para iniciar el runbook de Automatización de Azure que sea más adecuado a su escenario concreto. Este artículo incluye detalles acerca de cómo iniciar un runbook con el portal de Azure y Windows PowerShell. En otra documentación a la que puede acceder desde los siguientes vínculos se proporcionan detalles sobre los otros métodos.

<table>
 <tr>
  <td>MÉTODO</td>
  <td>CARACTERÍSTICAS</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Portal de Azure</a></td>
  <td>
   <ul>
    <li>Método más sencillo con la interfaz de usuario interactiva.</li>
    <li>Forma para proporcionar valores de parámetro simples.</li>
    <li>Seguimiento sencillo del estado del trabajo.</li>
    <li>Acceso autenticado con inicio de sesión de Azure.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Permite llamar desde la línea de comandos con los cmdlets de Windows PowerShell.</li>
     <li>Puede incluirse en una solución automatizada con varios pasos.</li>
     <li>La solicitud se autentica con el certificado o el usuario de OAuth principal/servicio principal.</li>
     <li>Permite proporcionar valores de parámetro simples y complejos.</li>
     <li>Permite realizar el seguimiento del estado del trabajo.</li>
     <li>Cliente necesario para admitir los cmdlets de PowerShell.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">API de Automatización de Azure</a></td>
  <td>
   <ul>
    <li>El método más flexible, pero también más complejo.</li>
    <li>Permite llamar desde cualquier código personalizado que pueda realizar solicitudes HTTP.</li>
    <li>La solicitud se autentica con el certificado o el usuario de OAuth principal/servicio principal.</li>
    <li>Permite proporcionar valores de parámetro simples y complejos.</li>
    <li>Permite realizar el seguimiento del estado del trabajo.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Permite iniciar el runbook desde una única solicitud HTTP.</li>
    <li>Autenticado con el token de seguridad de la dirección URL.</li>
    <li>Cliente no puede invalidar los valores de parámetro especificados al crear webhook. El runbook puede definir un único parámetro que se rellena con los detalles de la solicitud HTTP.</li>
    <li>No tiene capacidad de realizar un seguimiento del estado del trabajo a través de la dirección URL de webhook.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Respuesta a una alerta de Azure</a></td>
  <td>
   <ul>
    <li>Permite iniciar un runbook en respuesta a una alerta de Azure.</li>
    <li>Permite configurar webhook para el runbook y vincular a la alerta.</li>
    <li>Autenticado con el token de seguridad de la dirección URL.</li>
    <li>Actualmente solo admite la alerta para métricas.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Programación</a></td>
  <td>
   <ul>
    <li>Permite iniciar automáticamente el runbook en una programación cada hora, diaria o semanal.</li>
    <li>Permite manipular la programación a través del portal de Azure, los cmdlets de PowerShell o la API de Azure.</li>
    <li>Permite proporcionar valores de parámetro que se usarán con la programación.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-child-runbooks/">Desde otro runbook</a></td>
  <td>
   <ul>
    <li>Permite usar un runbook como una actividad en otro runbook</li>
    <li>Es útil para la funcionalidad utilizada por varios runbooks.</li>
    <li>Permite proporcionar valores de parámetro a runbooks secundarios y utilizar la salida de un runbook principal.</li>
   </ul>
  </td>
 </tr>
</table>
<br>


En la imagen siguiente se ilustra el proceso paso a paso detallado en el ciclo de vida de un runbook. Incluye maneras diferentes de iniciar un runbook en Automatización de Azure, los componentes necesarios para que un equipo local ejecute runbooks de Automatización de Azure y las interacciones entre componentes diferentes. Para obtener información sobre cómo ejecutar runbooks de Automatización en su centro de datos, vea [Trabajos híbridos de runbook](automation-hybrid-runbook-worker.md)

![Arquitectura de runbook](media/automation-starting-runbook/runbooks-architecture.png)

## Inicio de un runbook con el Portal de Azure

1. En el Portal de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Runbooks**.
1. Seleccione un runbook y, a continuación, haga clic en **Iniciar**.
1. Si el runbook tiene parámetros, se le pedirá que proporcione los valores de cada parámetro con un cuadro de texto. Consulte [Parámetros de runbook](#Runbook-parameters) a continuación para obtener más información sobre los parámetros.
1. Seleccione **Ver trabajo** junto al mensaje del runbook **inicial** o seleccione la pestaña **Trabajos** para que el runbook vea el estado del trabajo de runbook.

## Inicio de un runbook con el Portal de vista previa de Azure

1. En la cuenta de Automatización, haga clic en la parte **Runbooks** para abrir la hoja **Runbooks**.
1. Haga clic en un runbook para abrir su hoja **Runbook**.
2. Haga clic en **Iniciar**.
1. Si el runbook no tiene parámetros, se le pedirá que confirme si desea iniciarlo. Si el runbook tiene parámetros, se abrirá la hoja **Iniciar Runbook** para que pueda proporcionar los valores de parámetros. Consulte [Parámetros de runbook](#Runbook-parameters) a continuación para obtener más información sobre los parámetros.
3. La hoja **Trabajo** se abre para que pueda seguir el estado del trabajo.


## Inicio de un runbook con Windows PowerShell

Puede utilizar [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) para iniciar un runbook con Windows PowerShell. El código de ejemplo siguiente inicia un runbook llamado Test-Runbook.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook devuelve un objeto de trabajo que puede usar para realizar un seguimiento de su estado una vez que se inicia el runbook. A continuación, puede utilizar este objeto de trabajo con [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para determinar el estado del trabajo y con [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) para obtener su salida. El código de ejemplo siguiente inicia un runbook llamado Test-Runbook, espera hasta que se ha completado y después muestra la salida.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Si el runbook requiere parámetros, debe proporcionarlos como una [tabla hash](http://technet.microsoft.com/library/hh847780.aspx) donde la clave de la tabla hash coincide con el nombre del parámetro y el valor es el valor del parámetro. En el ejemplo siguiente se muestra cómo iniciar un runbook con dos parámetros de cadena denominados FirstName y LastName, un número entero denominado RepeatCount y un parámetro booleano denominado Show. Para obtener información adicional sobre los parámetros, consulte [Parámetros de runbook](#Runbook-parameters) a continuación.

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Parámetros de runbook

Cuando se inicia un runbook mediante el Portal de administración de Azure o Windows PowerShell, la instrucción se envía a través del servicio web de Automatización de Azure. Este servicio no admite parámetros con tipos de datos complejos. Si necesita proporcionar un valor para un parámetro complejo, se debe llamar en línea desde otro runbook, tal como se describe en [Runbooks secundarios en la Automatización de Azure](automation-child-runbooks.md).

El servicio web de Automatización de Azure proporciona una funcionalidad especial para parámetros mediante ciertos tipos de datos, tal como se describe en las secciones siguientes.

### Valores con nombre

Si el parámetro tiene el tipo de datos [object], puede usar entonces el siguiente formato JSON para enviarle una lista de valores con nombre: *{"Name1":Value1, "Name2":Value2, "Name3":Value3}*. Estos valores deben ser tipos simples. El runbook recibirá el parámetro como un [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) con propiedades que se corresponden a cada valor con nombre.

Considere el siguiente runbook de prueba que acepta un parámetro denominado user.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][object]$user
	   )
	    if ($user.Show) {
	        foreach ($i in 1..$user.RepeatCount) {
	            $user.FirstName
	            $user.LastName
	        }
	    } 
	}

El texto siguiente podría utilizarse para el parámetro de usuario.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Esta acción devuelve la siguiente salida:

	Joe
	Smith
	Joe
	Smith

### Matrices

Si el parámetro es una matriz como [array] o [string], puede usar entonces el siguiente formato JSON para enviarle una lista de valores: *[Value1,Value2,Value3]*. Estos valores deben ser tipos simples.

Considere el siguiente runbook de prueba que acepta un parámetro denominado *user*.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][array]$user
	   )
	    if ($user[3]) {
	        foreach ($i in 1..$user[2]) {
	            $ user[0]
	            $ user[1]
	        }
	    } 
	}

El texto siguiente podría utilizarse para el parámetro de usuario.

	["Joe","Smith",2,true]

Esta acción devuelve la siguiente salida:

	Joe
	Smith
	Joe
	Smith

### Credenciales

Si el parámetro es de tipo de datos **PSCredential**, puede proporcionar el nombre de un [recurso de credenciales](automation-credentials.md) de Automatización de Azure. El runbook recuperará la credencial con el nombre que especifique.

Considere el siguiente runbook de prueba que acepta un parámetro denominado credential.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

Podría utilizar el siguiente texto para el parámetro de usuario suponiendo que ha habido un recurso de credenciales denominado *My Credential*.

	My Credential

Suponiendo que el nombre de usuario de la credencial era *jsmith*, esto da como resultado la salida siguiente.

	jsmith

## Pasos siguientes

- La arquitectura de runbook en el artículo actual proporciona una descripción detallada sobre los runbooks híbridos; para obtener más información, vea [Runbooks secundarios en la Automatización de Azure](automation-child-runbooks.md) 

<!---HONumber=AcomDC_0128_2016-->