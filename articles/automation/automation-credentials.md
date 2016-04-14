<properties 
   pageTitle="Recursos de credenciales en Automatización de Azure | Microsoft Azure"
   description="Los recursos de credenciales en Automatización de Azure contienen credenciales de seguridad que se pueden usar para realizar la autenticación a los recursos a los que tiene acceso el runbook o configuración de DSC. En este artículo se describe cómo crear recursos de credenciales y usarlos en un runbook o una configuración de DSC."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Recursos de credenciales en Automatización de Azure

Un recurso de credencial de Automatización contiene un objeto [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) que contiene credenciales de seguridad, como un nombre de usuario y una contraseña. Los runbooks y las configuraciones de DSC pueden usar cmdlets que aceptan un objeto PSCredential para autenticación, o bien pueden extraer el nombre de usuario y la contraseña el objeto PSCredential para proporcionarlos a alguna aplicación o servicio que requiere autenticación. Las propiedades de una credencial se almacenan de manera segura en Automatización de Azure y es posible tener acceso a ellas en el runbook o la configuración de DSC con la actividad [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx).

>[AZURE.NOTE] Los recursos protegidos en Automatización de Azure incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Automatización de Azure con una clave única que se genera para cada cuenta de automatización. Esta clave se cifra mediante un certificado maestro y se almacena en Automatización de Azure. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.

## Cmdlets de Windows PowerShell

Los cmdlets de la tabla siguiente se usan para crear y administrar recursos de credenciales de Automatización con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en las configuraciones de DSC y los runbooks de Automatización.

|Cmdlets|Descripción|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|Recupera información acerca de un recurso de credencial. Solo puede recuperar la credencial misma desde la actividad **Get-AutomationCredential**.|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Crea una nueva credencial de Automatización.|
|[Remove- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Quita una credencial de Automatización.|
|[Set- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Establece las propiedades para una credencial de Automatización existente.|

## Actividades de runbook

Las actividades de la tabla siguiente se usan para tener acceso a las credenciales de un runbook y configuraciones de DSC.

|Actividades|Descripción|
|:---|:---|
|Get-AutomationPSCredential|Obtiene una credencial para usarla en un runbook o una configuración de DSC. Devuelve un objeto [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential).|

>[AZURE.NOTE] Debe evitar el uso de variables en el parámetro –Name de Get-AutomationPSCredential, debido a que esto podría complicar la detección de las dependencias entre runbooks o configuraciones de DSC, y recursos de credenciales en tiempo de diseño.

## Creación de una nueva credencial


### Para crear una nueva variable con el Portal de Azure clásico

1. En la cuenta de Automatización, haga clic en **Recursos** en la parte superior de la ventana.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar credencial**.
2. En el menú desplegable **Tipo de credencial**, seleccione **Credencial de PowerShell**.
1. Realice los pasos del asistente y haga clic en la casilla para guardar la nueva credencial.


### Para crear una nueva credencial con el Portal de Azure

1. En la cuenta de Automatización, haga clic en la parte de **Recursos** para abrir la hoja **Recursos**.
1. Haga clic en la parte de **Credenciales** para abrir la hoja **Credenciales**.
1. Haga clic en **Agregar una credencial** en la parte superior de la hoja.
1. Complete el formulario y haga clic en **Crear** para guardar la nueva credencial.


### Para crear una nueva credencial de PowerShell con Windows PowerShell

Los comandos de ejemplo siguientes muestran cómo crear una nueva credencial de automatización. Primero se crea un objeto PSCredential con el nombre y la contraseña y, a continuación, se usa para crear el recurso de credencial. De manera alternativa, podría usar el cmdlet **Get-Credential** para que se le solicite escribir un nombre y una contraseña.

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## Uso de una credencial de PowerShell

Puede recuperar un activo de credencial en un runbook o una configuración de DSC con la actividad **Get-AutomationPSCredential**. Esto devuelve un [objeto PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que puede usar con una actividad o cmdlet que requiere un parámetro PSCredential. También puede recuperar las propiedades del objeto de credencial para usarlas individualmente. El objeto tiene una propiedad para el nombre de usuario y la contraseña segura, o bien puede usar el método **GetNetworkCredential** para devolver un objeto [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) que proporcionará una versión no protegida de la contraseña.

### Ejemplo de runbook de texto

Los comandos de ejemplo siguientes muestran cómo usar una credencial de PowerShell en un runbook. En este ejemplo, se recupera la contraseña y su nombre de usuario y contraseña se asignan a variables.

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### Ejemplo de runbook gráfico

Para agregar una actividad **Get-AutomationPSCredential** a un runbook gráfico, haga clic con el botón derecho en la credencial en el panel Biblioteca del editor gráfico y, a continuación, seleccione **Agregar a lienzo**.


![Agregar credencial a lienzo](media/automation-credentials/credential-add-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar una credencial en un runbook gráfico. En este caso, se usa para proporcionar la autenticación de un runbook a los recursos de Azure, tal como se describe en [Configuración de la autenticación a los recursos de Azure](automation-configuring.md). La primera actividad recupera la credencial que tiene acceso a la suscripción de Azure. A continuación, la actividad **Add-AzureAccount** usa esta credencial para proporcionar autenticación para cualquier actividad que venga después. Aquí se encuentra un [vínculo de canalización](automation-graphical-authoring-intro.md#links-and-workflow), debido a que **Get-AutomationPSCredential** espera un solo objeto.

![Agregar credencial a lienzo](media/automation-credentials/get-credential.png)

## Uso de una credencial de PowerShell en DSC
Mientras las configuraciones DSC en Automatización de Azure pueden hacer referencia a los activos de credenciales a través de **Get-AutomationPSCredential**, los activos de credenciales también se pueden pasar, si se desea, a través de los parámetros. Para obtener más información, consulte [Compilación de configuraciones en DSC de Automatización de Azure](automation-dsc-compile.md#credential-assets).

## Artículos relacionados

- [Vínculos de creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow)

 

<!---HONumber=AcomDC_0204_2016-->