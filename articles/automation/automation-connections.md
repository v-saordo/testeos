<properties 
   pageTitle="Recursos de conexión en Automatización de Azure | Microsoft Azure"
   description="Los activos de conexión en Automatización de Azure contienen la información necesaria para conectarse a una aplicación o a un servicio externo desde un runbook o una configuración de DSC. En este artículo se explican los detalles de las conexiones y cómo trabajar con ellas en la creación de texto y de gráficos."
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

# Recursos de conexión en Automatización de Azure

Un recurso de conexión de Automatización contiene la información necesaria para conectarse a una aplicación o a un servicio externos desde un runbook o una configuración de DSC. Esto puede incluir información solicitada para la autenticación, como un nombre de usuario y una contraseña, además de información de conexión, como una dirección URL o un puerto. El valor de una conexión es mantener todas las propiedades para conectarse a una aplicación determinada en un recurso, a diferencia de crear distintas variables. El usuario puede editar los valores de una conexión en un lugar y es posible pasar el nombre de una conexión a un runbook o una configuración de DSC en un solo parámetro. Es posible tener acceso a las propiedades de una conexión en el runbook o la configuración de DSC con la actividad **Get-AutomationConnection**.

Cuando crea una conexión, debe especificar un *tipo de conexión*. El tipo de conexión es una plantilla que define un conjunto de propiedades. La conexión define valores para cada propiedad definida en su tipo de conexión. Los tipos de conexión se agregan a Automatización de Azure en módulos de creación o se crean con la [API de Automatización de Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx). Los únicos tipos de conexión que se encuentran disponibles cuando se crea una conexión son los que están instalados en su cuenta de Automatización.

>[AZURE.NOTE] Los recursos protegidos en Automatización de Azure incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Automatización de Azure con una clave única que se genera para cada cuenta de automatización. Esta clave se cifra mediante un certificado maestro y se almacena en Automatización de Azure. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.

## Cmdlets de Windows PowerShell

Los cmdlets de la siguiente tabla se usan para crear y administrar conexiones de Automatización con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en los runbooks de Automatización y las configuraciones de DSC.

|Cmdlet|Descripción|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|Recupera una conexión. Incluye una tabla hash con los valores de los campos de la conexión.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|Crea una conexión nueva.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|Quita una conexión existente.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|Establece el valor de un campo determinado para una conexión existente.|

## Actividades

Las actividades de la siguiente tabla se usan para tener acceso a las conexiones de un runbook o de una configuración de DSC.

|Actividades|Descripción|
|---|---|
|Get-AutomationConnection|Obtiene una conexión para usar. Devuelve una tabla hash con las propiedades de la conexión.|

>[AZURE.NOTE] Debe evitar el uso de variables en el parámetro –Name de **Get- AutomationConnection**, debido a que esto podría complicar la detección de dependencias entre runbooks o configuraciones de DSC y activos de conexión en tiempo de diseño.

## Creación de una conexión nueva

### Para crear una conexión nueva con el Portal de Azure

1. En la cuenta de Automatización, haga clic en **Recursos** en la parte superior de la ventana.
1. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
1. Haga clic en **Agregar conexión**.
2. En el menú desplegable **Tipo de conexión**, seleccione el tipo de conexión que desea crear. El asistente presentará las propiedades de ese tipo específico.
1. Realice los pasos del asistente y haga clic en la casilla para guardar la nueva conexión.


### Para crear una conexión nueva con el Portal de vista previa de Azure

1. En la cuenta de Automatización, haga clic en la parte de **Recursos** para abrir la hoja **Recursos**.
1. Haga clic en la parte **Conexiones** para abrir la hoja **Conexiones**.
1. Haga clic en **Agregar una conexión** en la parte superior de la hoja.
2. En el menú desplegable **Tipo**, seleccione el tipo de conexión que desea crear. El formulario presentará las propiedades de ese tipo determinado.
1. Complete el formulario y haga clic en **Crear** para guardar la conexión nueva.



### Para crear una conexión nueva con Windows PowerShell

Cree una conexión nueva con Windows PowerShell con el cmdlet [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx). Este cmdlet tiene un parámetro denominado **ConnectionFieldValues** que espera una [tabla hash](http://technet.microsoft.com/library/hh847780.aspx) que define los valores de cada una de las propiedades definidas por el tipo de conexión.


Los comandos de ejemplo siguientes crean una conexión nueva para [Twilio](http://www.twilio.com), un servicio de telefonía que le permite enviar y recibir mensajes de texto. En [Centro de scripts](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) hay disponible un módulo de integración de ejemplo que incluye un tipo de conexión a Twilio. Este tipo de conexión define propiedades para el SID de la cuenta y el token de autorización, que se requieren para validar la cuenta cuando se conecta a Twilio. Para que este código de ejemplo funcione, debe [descargar este módulo](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) e instalarlo en su cuenta de Automatización.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Uso de una conexión en un runbook o una configuración de DSC

Puede recuperar una conexión de un runbook o una configuración de DSC con el cmdlet **Get-AutomationConnection**. Esta actividad recupera los valores de los distintos campos en la conexión y los devuelve como una [tabla hash](http://go.microsoft.com/fwlink/?LinkID=324844) que luego se puede usar con los comandos adecuados en el runbook o la configuración de DSC.

### Ejemplo de runbook de texto
Los comandos de ejemplo siguientes muestran cómo usar la conexión de Twilio del ejemplo anterior para enviar un mensaje de texto desde un runbook. La actividad Send-TwilioSMS que se usa aquí tiene dos conjuntos de parámetros y cada uno de ellos usa un método distinto de autenticación en el servicio de Twilio. Uno usa un objeto de conexión y el otro usa parámetros individuales para el SID de la cuenta y el token de autorización. En este ejemplo se muestran ambos métodos.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### Ejemplos de runbook gráfico

Para agregar una actividad **Get-AutomationConnection** a un runbook gráfico, haga clic con el botón derecho en la conexión en el panel Biblioteca del editor gráfico y, a continuación, seleccione **Agregar a lienzo**.

![](media/automation-connections/connection-add-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar una conexión en un runbook gráfico. Este es el mismo ejemplo mostrado anteriormente para enviar un mensaje de texto con Twilio desde un runbook de texto. Este ejemplo utiliza el parámetro **UseConnectionObject** definido para la actividad **Send-TwilioSMS** que utiliza un objeto de conexión para autenticación con el servicio. Aquí se usa un [vínculo de canalización](automation-graphical-authoring-intro.md#links-and-workflow), debido a que el parámetro Connection espera un solo objeto.

El motivo por el cual una expresión de PowerShell se usa para el valor en el parámetro **To** en lugar de un valor Constant es que este parámetro espera un tipo de valor de matriz de cadenas para que sea posible enviar varios números. Una expresión de PowerShell le permite proporcionar un solo valor o una matriz.

![](media/automation-connections/get-connection-object.png)

La imagen que aparece a continuación muestra el mismo ejemplo anterior, pero usa el conjunto de parámetros **SpecifyConnectionFields** que espera que los parámetros AccountSid y AuthToken se especifiquen de manera individual, en lugar de usar un objeto de conexión para la autenticación. En este caso, se especifican los campos de la conexión en lugar del objeto mismo.

![](media/automation-connections/get-connection-properties.png)



## Artículos relacionados

- [Vínculos de creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=AcomDC_0128_2016-->