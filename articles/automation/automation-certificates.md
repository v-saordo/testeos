<properties 
   pageTitle="Activos de certificados en Automatización de Azure | Microsoft Azure"
   description="Los certificados se pueden almacenar de manera segura en Automatización de Azure, de manera tal que los runbooks o configuraciones de DSC pueden tener acceso a ellos para realizar la autenticación respecto de Azure y recursos de terceros. Este artículo explica los detalles de los certificados y cómo trabajar con ellos en la creación de textos y de gráficos."
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

# Activos de certificados en Automatización de Azure

Los certificados se pueden almacenar de manera segura en Automatización de Azure de manera que los runbooks o las configuraciones de DSC pueden tener acceso a ellos mediante el uso de la actividad **Get-AutomationCertificate**. Esto le permite crear runbooks y configuraciones de DSC que usan certificados para autenticación o agregarlos a Azure o a recursos de terceros.

>[AZURE.NOTE] Los recursos protegidos en Automatización de Azure incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Automatización de Azure con una clave única que se genera para cada cuenta de automatización. Esta clave se cifra mediante un certificado maestro y se almacena en Automatización de Azure. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.

## Cmdlets de Windows PowerShell

Los cmdlets de la tabla siguiente se usan para crear y administrar variables de Automatización con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en las configuraciones de DSC y los runbooks de Automatización.

|Cmdlets|Descripción|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|Recupera información acerca de un certificado. Solo puede recuperar el certificado mismo desde la actividad Get-AutomationCertificate.|
|[New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|Importa un certificado nuevo a Automatización de Azure.|
|[Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|Quita un certificado a Automatización de Azure.|
|[Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|Establece las propiedades de un certificado existente, incluyendo la carga del archivo de certificado y el establecimiento de la contraseña de un .pfx.|

## Actividades para tener acceso a certificados

Las actividades de la tabla siguiente se usan para obtener acceso a los certificados en un runbook o una configuración de DSC.

|Actividades|Descripción|
|:---|:---|
|Get-AutomationCertificate|Obtiene un certificado para usarlo en un runbook o una configuración de DSC.|

>[AZURE.NOTE] Debe evitar el uso de variables en el parámetro –Name de GetAutomationCertificate, debido a que esto podría complicar la detección de las dependencias entre runbooks o configuraciones de DSC, y recursos de certificados en tiempo de diseño.

## Creación de un certificado nuevo

Cuando crea un certificado nuevo, debe cargar un archivo cer o pfx a Automatización de Azure. Si marca el certificado como exportable, podrá transferirlo fuera del almacén de certificados de Automatización de Azure. Si no es exportable, solo se puede usar para firmar dentro del runbook o la configuración de DSC.

### Para crear un certificado nuevo con el portal de Azure

1. En la cuenta de Automatización, haga clic en **Recursos** en la parte superior de la ventana.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar credencial**.
2. En el menú desplegable **Tipo de credencial**, seleccione **Certificado**.
3. Escriba un nombre para el certificado en el cuadro **Nombre** y, a continuación, haga clic en la flecha derecha.
4. Busque un archivo .cer o .pfx. Si selecciona un archivo .pfx, especifique una contraseña y si se permitir o no su exportación.
1. Haga clic en la marca de verificación para cargar el archivo de certificado y guarde el recurso de certificado nuevo.


### Para crear un certificado nuevo con el Portal de vista previa de Azure

1. En la cuenta de Automatización, haga clic en la parte de **Recursos** para abrir la hoja **Recursos**.
1. Haga clic en la parte de **Certificados** para abrir la hoja **Certificados**.
1. Haga clic en **Agregar un certificado** en la parte superior del cuadro.
2. Escriba un nombre para el certificado en el cuadro **Nombre**.
2. Haga clic en **Seleccionar un archivo** bajo **Cargar un archivo de certificado** para buscar un archivo .cer o .pfx. Si selecciona un archivo .pfx, especifique una contraseña y si se permitir o no su exportación.
1. Haga clic en **Crear** para guardar el recurso de certificado nuevo.


### Para crear un certificado nuevo con Windows PowerShell

Los siguientes comandos de ejemplo muestran cómo crear un certificado de automatización nuevo y lo marcan como exportable. Esta acción importa un archivo pfx exportable.

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## Uso de un certificado

Debe utilizar la actividad **Get-AutomationCertificate** para usar un certificado. No puede usar el cmdlet [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx), debido a que devuelve información acerca del recurso de certificado, pero no el certificado mismo.

### Ejemplo de runbook de texto

El código de ejemplo siguiente muestra cómo agregar un certificado a un servicio en la nube en un runbook. En este ejemplo, la contraseña se recupera a partir de una variable de automatización cifrada.

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### Ejemplo de runbook gráfico

Para agregar un cmdlet **Get-AutomationCertificate** a un runbook gráfico, haga clic con el botón derecho en el certificado en el panel Biblioteca del editor gráfico y, a continuación, seleccione **Agregar a lienzo**.

![](media/automation-certificates/certificate-add-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar un certificado en un runbook gráfico. Este es el mismo ejemplo anteriormente mostrado para agregar un certificado a un servicio en la nube desde un runbook textual.

Este ejemplo utiliza el parámetro **UseConnectionObject** definido para la **actividad Send-TwilioSMS** que utiliza un objeto de conexión para autenticación con el servicio. Aquí se debe usar un [vínculo de canalización](automation-graphical-authoring-intro.md#links-and-workflow), debido a que un vínculo de secuencia devolvería una recopilación que contiene un objeto único que el parámetro Connection no espera.

![](media/automation-certificates/add-certificate.png)


## Otras referencias

- [Vínculos de creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=AcomDC_0128_2016-->