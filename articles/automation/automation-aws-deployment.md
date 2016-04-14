<properties
   pageTitle="Automatización de Azure y administración de nube híbrida: Automatización de la implementación de una máquina virtual en Amazon Web Services"
   description="Automatización de Azure y administración de nube híbrida: Automatización de la implementación de una máquina virtual en Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Automatización de Azure y administración de nube híbrida: Automatización de la implementación de una máquina virtual en Amazon Web Services

Durante los eventos de nuestro [Cloud Roadshow](https://microsoftcloudroadshow.com/cities), en los que presenté la Automatización de Azure, hubo varias preguntas sobre si Automatización de Azure se diseñó solo para Azure. Por fortuna, tengo una demostración de la que voy a hablar en este artículo de blog, pero de todas formas, la respuesta clave es que **Automatización de Azure no es solo para Azure, sino para los recursos locales *Y* para administrar también entornos de nube híbrida.** Así que, por ejemplo, si es cliente de Amazon Web Services (AWS), Automatización de Azure es también para usted.

## Objetivos

En este artículo, hablaré sobre cómo puede aprovechar Automatización de Azure para aprovisionar una máquina virtual en AWS y asignarle un nombre específico, lo que en el mundo AWS se conoce como "etiquetar" la máquina virtual.

## Supuestos

Para los fines de este artículo, asumiremos que tiene una cuenta de Automatización de Azure ya configurada y que también tiene una suscripción AWS.

Para más información acerca de cómo configurar una cuenta de Automatización de Azure, consulte nuestra [página de documentación](automation-configuring.md).

## Preparación: Almacenamiento de credenciales AWS

Para que Automatización de Azure "hable" con AWS, tendrá que recuperar las credenciales AWS y almacenarlas como activos en Automatización de Azure.

Cuando tiene iniciada sesión en el portal AWS, haga clic en el pequeño triángulo debajo de su nombre y haga clic en **Security Credentials** (credenciales de seguridad).

![Credencial de seguridad AWS](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "Credencial de seguridad AWS")

Haga clic en **Access Keys** (claves de acceso).

![Claves de acceso AWS](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "Claves de acceso AWS")

Copie la clave de acceso y la clave de acceso secreta (si lo desea puede descargar el archivo de clave para almacenarlo en un lugar seguro).

![Crear claves de acceso AWS](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "Crear claves de acceso AWS")

## Activos de AWS en Automatización de Azure

En el paso anterior ha copiado y guardado su **Access Key ID** (identificador de clave de acceso) y **Secret Access Key** (clave de acceso secreta). Vamos a almacenarlas ahora en Automatización de Azure.

Diríjase a su cuenta de Automatización y seleccione **Activos** - **Credenciales**, agregue una nueva credencial y asígnele el nombre **AWScred**.

![imagen](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "imagen")

Proporcione una descripción opcional y asegúrese de que escribe su **identificador de acceso** en el campo **Nombre de usuario** y su **clave de acceso secreta** en el campo **Contraseña**. Guarde las credenciales AWS.

## Módulo Amazon Web Services de PowerShell

Nuestro runbook de aprovisionamiento de la máquina virtual aprovechará el módulo AWS de PowerShell para realizar su trabajo. El módulo AWS de PowerShell se incluye en [Galería de PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) y pueden agregarse fácilmente a una cuenta de Automatización con el botón **Implementar en Automatización de Azure**.

![Importación del módulo AWS de PS a AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "Importación del módulo AWS de PS a AA")

Al hacer clic en **Implementar automatización de Azure** se abrirá la página de inicio de sesión de Azure y una vez que haya proporcionado sus credenciales, verá la siguiente pantalla.

![Asistente para importación del módulo AWS](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Asistente para importación del módulo AWS")

Seleccione una nueva cuenta de Automatización o una ya existente. Tenga en cuenta que no hay ningún cuadro de lista desplegable para las cuentas ya existentes, así que asegúrese de que no comete errores tipográficos al escribir el nombre de la cuenta de Automatización, y de que la región y grupo de recursos de la misma son correctos. Siga los demás pasos para completar la configuración y, a continuación, haga clic en **Crear**.

Si se dirige a su cuenta de Automatización seleccionada y va a **Activos** - **Módulos**, ahora puede ver el módulo AWS con 1427 actividades; impresionante ¿no?

>[AZURE.NOTE] La importación de un módulo de PowerShell en Automatización de Azure consta de dos pasos:
>
> 1.  Importación del módulo
> 2.  Extracción de los cmdlets
>
>Las actividades no se mostrarán hasta que el módulo haya finalizado completamente la importación y extracción de los cmdlets, lo que puede tardar unos minutos.

## Aprovisionamiento del runbook de la máquina virtual de AWS

Ahora que ya tenemos todos los requisitos previos es el momento de crear el runbook que aprovisionará a la máquina virtual en AWS. Mostraremos también como puede aprovechar un script de PowerShell nativo en Automatización de Azure en lugar del flujo de trabajo de PowerShell.

Puede encontrar más información acerca de las diferencias entre PowerShell nativo y flujo de trabajo de PowerShell [aquí](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/). Puede encontrar información sobre la creación gráfica de runbooks [aquí](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/).

El script de PowerShell para nuestro runbook puede descargarse desde [Galería de PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).

Puede descargar el script abriendo una sesión de PowerShell y escribiendo: **Save-Script -Name New-AwsVM -Path <ruta de acceso>**

Cuando haya guardado el script de PowerShell puede agregarlo a Automatización de Azure como un runbook haciendo lo siguiente:

En su **cuenta de Automatización**, haga clic en **Runbooks** y seleccione **Agregar un runbook**. Seleccione **Creación rápida** (crear un nuevo runbook), proporcione un nombre para el runbook y en **Tipo de runbook** seleccione **PowerShell**. A continuación, haga clic en **Crear**.

![Crear un runbook de AWS](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "Crear un runbook de AWS")

Cuando el editor de runbook se abra, copie y pegue el script de PowerShell en el área de creación de runbooks.

![imagen](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "imagen")

Tenga en cuenta lo siguiente al utilizar el script de PowerShell descargado:

-   El runbook contiene un número de valores de parámetro predeterminados como se mencionó en la sección #ToDo. Evalúe todos los valores predeterminados y actualícelos cuando sea necesario.
-   Asegúrese de que ha almacenado las credenciales AWS en **Activos**, en **AWScred**.
-   La región que va a utilizar dependerá de su suscripción AWS. En el portal AWS, en donde también ha buscado las credenciales de seguridad, haga clic en la flecha situada junto a su cuenta para comprobar la región.

![Región AWS](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "Región AWS")

-   Averigüe [qué región](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) tiene que proporcionar en su runbook. Por ejemplo, mi región es US West (Oregon) que se traduce en us-west-2.

![Regiones AWS](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "Regiones AWS")

-   Puede obtener una lista de nombres de imagen si usa PowerShell ISE, importe el módulo, realice la autenticación con AWS reemplazando **Get-AutomationPSCredential** en su entorno ISE con **$AwsCred = Get-Credential.** Esto le pedirá las credenciales, puede proporcionar su **Access Key ID** (identificador de clave de acceso) y **Secret Access Key** (acceso de clave secreta) como nombre de usuario y contraseña. Utilice el siguiente ejemplo.


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


Con esto obtendrá el siguiente resultado:

![Obtener imágenes AWS](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "Obtener imágenes AWS")

Copie y pegue el nombre de la imagen de su agrado en una variable de automatización como aparece en la referencia en el runbook como **$InstanceType**. Puesto que estoy usando un nivel gratuito de AWS, he seleccionado **t2.micro** en mi runbook.

Obtenga más información acerca de los tipos de instancia de Amazon EC2 [aquí](https://aws.amazon.com/ec2/instance-types/).

## Prueba del aprovisionamiento del runbook de la máquina virtual de AWS

Lista de comprobaciones preparatorias:

-   Los recursos para autenticarse con AWS se han creado y se denominan **AWScred**
-   El módulo AWS de PowerShell se ha importado en Automatización de Azure
-   Se ha creado un nuevo runbook y los valores de parámetro se han comprobado y actualizado donde ha sido necesario
-   En la configuración del runbook **Registrar registros detallados** y, opcionalmente, **Registrar registros de progreso** se han establecido con el valor **activado**

Empecemos nuestro nuevo runbook proporcionando un **VMname.**

![Iniciar nuevo runbook AWS VM](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "Iniciar nuevo runbook AWS VM")

Puesto que hemos habilitado **Registrar registros detallados** y **Registrar registros de progreso** podemos ver la salida de nuestras **Transmisiones** en **Todos los registros**.

![Salida de transmisiones](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Salida de transmisiones")

Vamos a ver en AWS:

![Máquinas virtual implementada en consola AWS](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "Máquinas virtual implementada en consola AWS")

¡Estupendo!

Con este artículo, he querido proporcionar un ejemplo de cómo se puede usar Automatización de Azure para administrar nubes híbridas, como creando una máquina virtual en Amazon Web Services y aplicándole una etiqueta personalizada para darle un nombre.

¡Feliz automatización y hasta la próxima!

Tiander.

<!---HONumber=AcomDC_0204_2016-->