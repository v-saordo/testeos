<properties 
	pageTitle="Uso de PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo | Microsoft Azure"
	description="En este tema se describe y se le guiará por la implementación y la configuración de un servidor de informes de modo nativo de SQL Server Reporting Services en una máquina virtual de Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/11/2015"
	ms.author="jroth" />

# Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.
 

En este tema se describe y se le guiará por la implementación y la configuración de un servidor de informes de modo nativo de SQL Server Reporting Services en una máquina virtual de Azure. Los pasos de este documento usan una combinación de pasos manuales para crear la máquina virtual y un script de Windows PowerShell para configurar Reporting Services en la máquina virtual. El script de configuración incluye la apertura de un puerto de firewall para HTTP o HTTPS.

>[AZURE.NOTE] Si no necesita **HTTPS** en el servidor de informes, **omita el paso 2**.
>
>Después de crear la máquina virtual en el paso 1, vaya a la sección Usar script para configurar el HTTP y el servidor de informes. Después de ejecutar el script, el servidor de informes estará listo para su uso.

## Requisitos previos y suposiciones

- **Suscripción de Azure**: compruebe el número de núcleos disponibles en su suscripción de Azure. Si crea el tamaño recomendado de máquina virtual de **A3**, necesita **4** núcleos disponibles. Si usa un tamaño de máquina virtual de **A2**, necesita **2** núcleos disponibles.
	
	- Para comprobar el límite de núcleos de su suscripción, en el Portal de Azure clásico, haga clic en CONFIGURACIÓN en el panel izquierdo y luego en USO en el menú superior.
	
	- Para aumentar la cuota de núcleos, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/). Para obtener más información sobre el tamaño de la máquina virtual, consulte [Tamaños de máquinas virtuales para Azure](virtual-machines-size-specs.md).

- **Scripting de Windows PowerShell**: en el tema se supone que cuenta con conocimientos prácticos básicos de Windows PowerShell. Para obtener más información sobre el uso de Windows PowerShell, vea lo siguiente:

	- [Inicio de Windows PowerShell en Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
	
	- [Introducción a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## Paso 1: Aprovisionar una máquina virtual de Azure

1. Vaya al Portal de Azure clásico.

1. Haga clic en **Máquinas virtuales** en el panel izquierdo.

	![máquinas virtuales de microsoft azure](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC660124.gif)

1. Haga clic en **Nuevo**.

	![botón nuevo](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC692019.gif)

1. Haga clic en **De la galería**.

	![nueva máquina virtual de la galería](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC692020.gif)

1. Haga clic en **SQL Server 2014 RTM Standard: Windows Server 2012 R2** y luego en la flecha para continuar.

	![siguiente](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC692021.gif)

	Si necesita la característica de suscripciones controladas por datos de Reporting Services, elija **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para más información sobre las ediciones de SQL Server y la compatibilidad de características, consulte [Características admitidas por las ediciones de SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. En la página **Configuración de la máquina virtual**, edite los siguientes campos:
									
	- Si hay más de una **FECHA DE LANZAMIENTO DE LA VERSIÓN**, seleccione la versión más reciente.
	
	- **Nombre de máquina virtual**: el nombre de la máquina también se usa en la siguiente página de configuración como el nombre DNS del servicio en la nube predeterminado. El nombre DNS debe ser único en el servicio de Azure. Considere la posibilidad de configurar la máquina virtual con un nombre de equipo que describa para qué se usa la máquina virtual. Por ejemplo, ssrsnativecloud.
	
	- **Nivel**: estándar
	
	- **Tamaño: A3** es el tamaño de máquina virtual recomendado para cargas de trabajo de SQL Server. Si una máquina virtual solo se usa como servidor de informes, un tamaño de A2 es suficiente a menos que el servidor de informes experimente una gran carga de trabajo. Para más información sobre precios de máquinas virtuales, consulte [Precios de Máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).
	
	- **Nuevo nombre de usuario**: el nombre que ofrece se crea como administrador en la máquina virtual.
	
	- **Nueva contraseña** y **Confirmar**. Esta contraseña se usa para la nueva cuenta de administrador y se recomienda usar una contraseña segura.
	
	- Haga clic en **Siguiente**. ![siguiente](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC692021.gif)

1. En la página siguiente, edite los siguientes campos:

	- **Servicio en la nube**: seleccione **Crear un nuevo servicio en la nube**.
	
	- **Nombre DNS de servicio en la nube**: este es el nombre DNS público del servicio en la nube que está asociado a la máquina virtual. El nombre predeterminado es el nombre que escribió para el nombre de la máquina virtual. Si en los pasos posteriores del tema, crea un certificado SSL de confianza y luego el nombre DNS se usa para el valor de "**Emitido a**" del certificado.
	
	- **Región/grupo de afinidad/red virtual**: elija la región más cercana a sus usuarios finales.
	
	- **Cuenta de almacenamiento**: use una cuenta de almacenamiento generada automáticamente
	
	- **Conjunto de disponibilidad**: ninguno.
	
	- **PUNTOS DE CONEXIÓN** Mantenga los puntos de conexión **Escritorio remoto** y **PowerShell** y después agregue un punto de conexión HTTP o HTTPS, en función del entorno.

		- **HTTP**: los puertos públicos y privados predeterminados son **80**. Tenga en cuenta que si usa un puerto privado distinto de 80, tendrá que modificar **$HTTPport = 80** en el script de http.

		- **HTTPS**: los puertos públicos y privados predeterminados son **443**. Una práctica recomendada de seguridad consiste en cambiar el puerto privado y configurar el firewall y el servidor de informes para usar el puerto privado. Para más información sobre los puntos de conexión, consulte [Configuración de extremos en una máquina virtual](virtual-machines-set-up-endpoints.md). Tenga en cuenta que si usa un puerto distinto de 443, debe cambiar el parámetro **$HTTPsport = 443** en el script de HTTPS.
	
	- Haga clic en Siguiente.![siguiente](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC692021.gif)

1. En la última página del asistente, mantenga el valor predeterminado de **Instalar el agente de VM** seleccionado. Los pasos descritos en este tema no usan al agente de máquina virtual, pero si piensa mantener esta máquina virtual, el agente de máquina virtual y las extensiones le permitirán mejorar la máquina virtual. Para más información sobre el agente de máquina virtual, consulte [VM Agent and Extensions – Part 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Una de las extensiones predeterminadas instaladas y en ejecución es la extensión "BGINFO" que se muestra en el escritorio de la máquina virtual, información del sistema como la dirección IP interna y el espacio libre en disco.

1. Haga clic en Completo. ![aceptar](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC660122.gif)

1. El **estado** de la máquina virtual se muestra como **Iniciando (aprovisionamiento)** durante el proceso de aprovisionamiento y luego se muestra como **En ejecución** cuando la máquina virtual está aprovisionada y lista para usarse.

## Paso 2: Crear un certificado de servidor

>[AZURE.NOTE] Si no requiere HTTPS en el servidor de informes, puede **omitir el paso 2** e ir a la sección **Uso del script para configurar el servidor de informes y HTTP**. Use el script de HTTP para configurar rápidamente el servidor de informes y el servidor de informes estará listo para su uso.

Para usar HTTPS en la máquina virtual, necesitará un certificado SSL de confianza. Según el escenario, puede usar uno de los dos métodos siguientes:

- Un certificado SSL válido emitido por una entidad de certificación (CA) y de confianza de Microsoft. Se requiere la distribución de los certificados raíz de CA mediante el programa de certificados raíz de Microsoft. Para más información sobre este programa, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Member CAs)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [Introduction to The Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificado autofirmado. No se recomiendan los certificados autofirmados para entornos de producción.

### Para usar un certificado creado por una entidad de certificación (CA) de confianza

1. **Solicitar un certificado de servidor para el sitio web desde una entidad de certificación**. 

	Puede utilizar el Asistente para certificados de servidor Web para generar un archivo de solicitud de certificado (Certreq.txt) que se envía a una entidad de certificación o para generar una solicitud para una entidad de certificación en línea. Por ejemplo, los Servicios de certificados de Microsoft en Windows Server 2012. Según el nivel de garantía de identificación ofrecido por el certificado del servidor, se tarda entre varios días o varios meses en que la entidad de certificación apruebe su solicitud y le envíe un archivo de certificado.

	Para obtener más información sobre la solicitud de certificados a un servidor, vea lo siguiente:
	
	- Use [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
	
	- Herramientas de seguridad para administrar Windows Server 2012.

	[Herramientas de seguridad para administrar Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

	>[AZURE.NOTE] El campo **Emitido a** del certificado SSL de confianza debe ser el mismo que el **Nombre DNS de servicio en la nube** que usó para la nueva máquina virtual.

1. **Instale el certificado de servidor en el servidor web**. En este caso, el servidor web es la máquina virtual que hospeda el servidor de informes y el sitio web se crea en pasos posteriores al configurar Reporting Services. Para obtener más información sobre la instalación del certificado de servidor en el servidor web mediante el complemento MMC de certificado, consulte [Instalar un certificado de servidor](https://technet.microsoft.com/library/cc740068).
	
	Si quiere usar el script incluido con este tema, para configurar el servidor de informes, se requiere el valor de los certificados **Huella digital** como parámetro del script. Vea la sección siguiente para obtener más información sobre cómo obtener la huella digital del certificado.

1. Asigne el certificado de servidor al servidor de informes. La asignación se completa en la sección siguiente al configurar el servidor de informes.

### Para usar el certificado autofirmado de máquinas virtuales

Se creó un certificado autofirmado en la máquina virtual cuando se aprovisionó la máquina virtual. El certificado tiene el mismo nombre que el nombre de DNS de la máquina virtual. Para evitar errores de certificado, es necesario que el certificado sea de confianza en la propia máquina virtual y también de la confianza de todos los usuarios del sitio.

1. Para confiar en la entidad de certificación raíz del certificado en la máquina virtual local, agregue el certificado a las **Entidades de certificación raíz de confianza**. A continuación se encuentra un resumen de los pasos necesarios. Para obtener pasos detallados sobre cómo confiar en la entidad de certificación, consulte [Install a Server Certificate](https://technet.microsoft.com/library/cc740068).

	1. En el Portal de Azure clásico, seleccione la máquina virtual y haga clic en Conectar. En función de la configuración del explorador, es posible que se le solicite guardar un archivo .rdp para conectarse a la máquina virtual.
	
		![conectarse a máquina virtual de azure](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC650112.gif) Use el nombre de la máquina virtual del usuario, el nombre de usuario y la contraseña que configuró al crear la máquina virtual.
	
		Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre del usuario es **testuser**.
		
		![el inicio de sesión incluye la máquina virtual](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC764111.png)
	
	1. Ejecute mmc.exe. Para más información, consulte [Visualización de certificados con el complemento MMC](https://msdn.microsoft.com/library/ms788967.aspx).
	
	1. En el menú de la aplicación de consola **Archivo**, agregue el complemento **Certificados**, seleccione **Cuenta de equipo** cuando se le pida y luego haga clic en **Siguiente**.
	
	1. Seleccione **Equipo local** para administrar y luego haga clic en **Finalizar**.
	
	1. Haga clic en **Aceptar** y expanda los nodos **Certificados - Personal**; luego haga clic en **Certificados**. El certificado toma el nombre DNS de la máquina virtual y agrega **cloudapp.net** al final. Haga clic con el botón derecho en el nombre del certificado y haga clic en **Copiar**.
	
	1. Expanda el nodo **Entidades de certificación raíz de confianza**, haga clic con el botón derecho en **Certificados** y luego haga clic en **Pegar**.
	
	1. Para validar, haga doble clic en el nombre del certificado en **Entidades de certificación raíz de confianza** y compruebe que no haya ningún error; de este modo, verá el certificado. Si quiere usar el script de HTTPS incluido con este tema, para configurar el servidor de informes se requiere el valor de la **huella digital** de los certificados como parámetro del script. **Para obtener el valor de la huella digital**, complete los pasos siguientes. También hay un ejemplo de PowerShell para recuperar la huella digital en la sección [Usar el script para configurar el servidor de informes y HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
		
		1. Haga doble clic en el nombre del certificado, por ejemplo, ssrsnativecloud.cloudapp.net.
		
		1. Haga clic en la pestaña **Detalles**.
		
		1. Haga clic en **Huella digital**. El valor de la huella digital se muestra en el campo de detalles, por ejemplo, ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
		
		1. Copie la huella digital y guarde el valor para más adelante o edite el script ahora.
		
		1. (*) Antes de ejecutar el script, quite los espacios entre los pares de valores. Por ejemplo, la huella digital que se ha indicado antes ahora sería ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
		
		1. Asigne el certificado de servidor al servidor de informes. La asignación se completa en la sección siguiente al configurar el servidor de informes.

Si está usando un certificado SSL autofirmado, el nombre del certificado ya coincide con el nombre de host de la máquina virtual. Por lo tanto, el DNS de la máquina ya está registrado globalmente y se puede obtener acceso a él desde cualquier cliente.

## Paso 3: configurar el Servidor de informes

En esta sección se explica cómo configurar la máquina virtual como servidor de informes de modo nativo de Reporting Services. Puede usar uno de los métodos siguientes para configurar el servidor de informes:

- Use el script para configurar el servidor de informes

- Use el Administrador de configuración para configurar el servidor de informes.

Para ver pasos más detallados, consulte la sección [Conectarse a la máquina virtual e iniciar el Administrador de configuración de Reporting Services](virtual-machines-sql-server-business-intelligence.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota de autenticación:** la autenticación de Windows es el método de autenticación recomendado y es la autenticación predeterminada de Reporting Services. Solo los usuarios que están configurados en la máquina virtual pueden obtener acceso a Reporting Services y asignarse a los roles de Reporting Services.

### Use el script para configurar el servidor de informes y HTTP

Para usar el script de Windows PowerShell para configurar el servidor de informes, complete los siguientes pasos. La configuración incluye HTTP, no HTTPS:

1. En el Portal de Azure clásico, seleccione la máquina virtual y haga clic en Conectar. En función de la configuración del explorador, es posible que se le solicite guardar un archivo .rdp para conectarse a la máquina virtual.

	![conectarse a máquina virtual de azure](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC650112.gif) Use el nombre de la máquina virtual del usuario, el nombre de usuario y la contraseña que configuró al crear la máquina virtual.

	Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre del usuario es **testuser**.
	
	![el inicio de sesión incluye la máquina virtual](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC764111.png)

1. En la máquina virtual, abra **Windows PowerShell ISE** con privilegios administrativos. El PowerShell ISE se instala de forma predeterminada en Windows Server 2012. Se recomienda utilizar el ISE en lugar de una ventana de Windows PowerShell estándar, por lo que puede pegar el script en el ISE, modificar el script y luego ejecutar el script.

1. En Windows PowerShell ISE, haga clic en el menú **Vista** y luego haga clic en **Mostrar panel de scripts**.

1. Copie el siguiente script y pegue el script en el panel de scripts de Windows PowerShell ISE.

		## This script configures a Native mode report server without HTTPS
		$ErrorActionPreference = "Stop"
		
		$server = $env:COMPUTERNAME
		$HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
		
		## Set PowerShell execution policy to be able to run scripts
		Set-ExecutionPolicy RemoteSigned -Force
		
		## Utility method for verifying an operation's result
		function CheckResult
		{
		    param($wmi_result, $actionname)
		    if ($wmi_result.HRESULT -ne 0) {
		        write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
		    }
		}
		
		$starttime=Get-Date
		write-host -foregroundcolor DarkGray $starttime StartTime
		
		## ReportServer Database name - this can be changed if needed
		$dbName='ReportServer'
		
		## Register for MSReportServer_ConfigurationSetting
		## Change the version portion of the path to "v11" to use the script for SQL Server 2012
		$RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
		
		## Report Server Configuration Steps
		
		## Setting the web service URL ##
		write-host -foregroundcolor green "Setting the web service URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for ReportServer site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
		    CheckResult $r "SetVirtualDirectory for ReportServer"
		
		## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
		    write-host "Calling ReserveURL port $HTTPport"
		    $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
		    CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
		   
		## Setting the Database ##
		write-host -foregroundcolor green "Setting the Database"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## GenerateDatabaseScript - for creating the database
		    write-host "Calling GenerateDatabaseCreationScript for database $dbName"
		    $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
		    CheckResult $r "GenerateDatabaseCreationScript"
		    $script = $r.Script
		
		## Execute sql script to create the database
		    write-host 'Executing Database Creation Script'
		    $savedcvd = Get-Location
		    Import-Module SQLPS              ## this automatically changes to sqlserver provider
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		  
		## GenerateGrantRightsScript 
		    $DBUser = "NT Service\ReportServer"
		    write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
		    $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
		    CheckResult $r "GenerateDatabaseRightsScript"
		    $script = $r.Script
		
		## Execute grant rights script
		    write-host 'Executing Database Rights Script'
		    $savedcvd = Get-Location
		    cd sqlserver:\
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		
		## SetDBConnection - uses Windows Service (type 2), username is ignored
		    write-host "Calling SetDatabaseConnection server $server, DB $dbName"
		    $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
		    CheckResult $r "SetDatabaseConnection"  
		
		## Setting the Report Manager URL ##
		
		write-host -foregroundcolor green "Setting the Report Manager URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for Reports (Report Manager) site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
		    CheckResult $r "SetVirtualDirectory"
		
		## ReserveURL for ReportManager  - port $HTTPport
		    write-host "Calling ReserveURL for ReportManager, port $HTTPport"
		    $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
		    CheckResult $r "ReserveURL for ReportManager port $HTTPport"
		
		write-host -foregroundcolor green "Open Firewall port for $HTTPport"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## Open Firewall port for $HTTPport
		    New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
		    write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
		
		write-host 'Operations completed, Report Server is ready'
		write-host -foregroundcolor DarkGray $starttime StartTime
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time

1. Si ha creado la máquina virtual con un puerto HTTP distinto de 80, modifique el parámetro $HTTPport = 80.

1. El script está configurado actualmente para Reporting Services. Si quiere ejecutar el script para Reporting Services, modifique la parte de la versión de la ruta de acceso al espacio de nombres "v11", en la instrucción Get-WmiObject.

1. Ejecute el script.

**Validación**: para comprobar que la funcionalidad del servidor de informes básica es correcta, vea la sección [Comprobar la configuración](#verify-the-configuration) más adelante en este tema.

### Usar el script para configurar el servidor de informes y HTTPS

Para usar Windows PowerShell para configurar el servidor de informes, complete los siguientes pasos. La configuración incluye HTTPS, no HTTP.

1. En el Portal de Azure clásico, seleccione la máquina virtual y haga clic en Conectar. En función de la configuración del explorador, es posible que se le solicite guardar un archivo .rdp para conectarse a la máquina virtual.

	![conectarse a máquina virtual de azure](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC650112.gif) Use el nombre de la máquina virtual del usuario, el nombre de usuario y la contraseña que configuró al crear la máquina virtual.

	Por ejemplo, en la siguiente imagen, el nombre de la máquina virtual es **ssrsnativecloud** y el nombre del usuario es **testuser**.

	![el inicio de sesión incluye la máquina virtual](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC764111.png)

1. En la máquina virtual, abra **Windows PowerShell ISE** con privilegios administrativos. El PowerShell ISE se instala de forma predeterminada en Windows Server 2012. Se recomienda utilizar el ISE en lugar de una ventana de Windows PowerShell estándar, por lo que puede pegar el script en el ISE, modificar el script y luego ejecutar el script.

1. Para habilitar la ejecución de scripts, ejecute el siguiente comando de Windows PowerShell:

		Set-ExecutionPolicy RemoteSigned

	Luego puede ejecutar lo siguiente para comprobar la directiva:

		Get-ExecutionPolicy

1. En **Windows PowerShell ISE**, haga clic en el menú **Vista** y luego haga clic en **Mostrar panel de scripts**.

1. Copie el siguiente script y péguelo en el panel de scripts de Windows PowerShell ISE.

		## This script configures the report server, including HTTPS
		$ErrorActionPreference = "Stop"
		$httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
		
		# You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
		#dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
		#
		# The certifacte hash is a REQUIRED parameter
		$certificatehash="" 
		# the certificate hash should not contain spaces
		
		if ($certificatehash.Length -lt 1) 
		{
		    write-error "certificatehash is a required parameter"
		} 
		# Certificates should be all lower case
		$certificatehash=$certificatehash.ToLower()
		$server = $env:COMPUTERNAME
		# If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
		$DNSName="+"
		#$DNSName="$server.cloudapp.net"
		$DNSNameAndPort = $DNSName + ":$httpsport"
		
		## Utility method for verifying an operation's result
		function CheckResult
		{
		    param($wmi_result, $actionname)
		    if ($wmi_result.HRESULT -ne 0) {
		        write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
		    }
		}
		
		$starttime=Get-Date
		write-host -foregroundcolor DarkGray $starttime StartTime
		
		## ReportServer Database name - this can be changed if needed
		$dbName='ReportServer'
		
		write-host "The script will use $DNSNameAndPort as the DNS name and port" 
		
		## Register for MSReportServer_ConfigurationSetting
		## Change the version portion of the path to "v11" to use the script for SQL Server 2012
		$RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
		
		## Reporting Services Report Server Configuration Steps
		
		## 1. Setting the web service URL ##
		write-host -foregroundcolor green "Setting the web service URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for ReportServer site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
		    CheckResult $r "SetVirtualDirectory for ReportServer"
		
		## ReserveURL for ReportServerWebService - port 80 (for local usage)
		    write-host 'Calling ReserveURL port 80'
		    $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
		    CheckResult $r "ReserveURL for ReportServer port 80" 
		
		## ReserveURL for ReportServerWebService - port $httpsport
		    write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
		    $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
		    CheckResult $r "ReserveURL for ReportServer port $httpsport" 
		
		## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
		    write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
		    $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
		    CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
		    
		## 2. Setting the Database ##
		write-host -foregroundcolor green "Setting the Database"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## GenerateDatabaseScript - for creating the database
		    write-host "Calling GenerateDatabaseCreationScript for database $dbName"
		    $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
		    CheckResult $r "GenerateDatabaseCreationScript"
		    $script = $r.Script
		
		## Execute sql script to create the database
		    write-host 'Executing Database Creation Script'
		    $savedcvd = Get-Location
		    Import-Module SQLPS                    ## this automatically changes to sqlserver provider
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		  
		## GenerateGrantRightsScript 
		    $DBUser = "NT Service\ReportServer"
		    write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
		    $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
		    CheckResult $r "GenerateDatabaseRightsScript"
		    $script = $r.Script
		
		## Execute grant rights script
		    write-host 'Executing Database Rights Script'
		    $savedcvd = Get-Location
		    cd sqlserver:\
		    Invoke-SqlCmd -Query $script
		    Set-Location $savedcvd
		
		## SetDBConnection - uses Windows Service (type 2), username is ignored
		    write-host "Calling SetDatabaseConnection server $server, DB $dbName"
		    $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
		    CheckResult $r "SetDatabaseConnection"  
		
		## 3. Setting the Report Manager URL ##
		
		write-host -foregroundcolor green "Setting the Report Manager URL"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## SetVirtualDirectory for Reports (Report Manager) site
		    write-host 'Calling SetVirtualDirectory'
		    $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
		    CheckResult $r "SetVirtualDirectory"
		
		## ReserveURL for ReportManager  - port 80
		    write-host 'Calling ReserveURL for ReportManager, port 80'
		    $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
		    CheckResult $r "ReserveURL for ReportManager port 80"
		
		## ReserveURL for ReportManager - port $httpsport
		    write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
		    $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
		    CheckResult $r "ReserveURL for ReportManager port $httpsport" 
		
		## CreateSSLCertificateBinding for ReportManager port $httpsport
		    write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
		    $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
		    CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
		
		write-host -foregroundcolor green "Open Firewall port for $httpsport"
		write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time
		
		## Open Firewall port for $httpsport
		    New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
		    write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
		
		write-host 'Operations completed, Report Server is ready'
		write-host -foregroundcolor DarkGray $starttime StartTime
		$time=Get-Date
		write-host -foregroundcolor DarkGray $time

1. Modifique el parámetro **$certificatehash** en el script:

	- Se trata de un parámetro **obligatorio**. Si no ha guardado el valor de certificado de los pasos anteriores, use uno de los siguientes métodos para copiar el valor hash del certificado de la huella digital de certificados:

		En la máquina virtual, abra Windows PowerShell ISE y ejecute el siguiente comando:

			dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

		El resultado será similar al siguiente. Si el script devuelve una línea en blanco, la máquina virtual no tiene un certificado configurado; por ejemplo, consulte la sección [Para usar el certificado autofirmado de máquinas virtuales](#to-use-the-virtual-machines-self-signed-certificate).
	
	OR
	
	- En la máquina virtual, ejecute mmc.exe y luego agregue el complemento **Certificados**.
	
	- En el nodo **Entidades de certificación raíz de confianza**, haga doble clic en el nombre del certificado. Si está usando el certificado autofirmado de la máquina virtual, el certificado toma el nombre DNS de la máquina virtual y finaliza con **cloudapp.net**.
	
	- Haga clic en la pestaña **Detalles**.
	
	- Haga clic en **Huella digital**. El valor de la huella digital se muestra en el campo de detalles, por ejemplo, af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48.
	
	- **Antes de ejecutar el script**, quite los espacios entre los pares de valores. Por ejemplo, af1160b64b288d890a8212ff6ba9c3664f319048.

1. Modifique el parámetro **$httpsport**:

	- Si usa el puerto 443 para el extremo HTTPS, no necesita actualizar este parámetro en el script. En caso contrario, use el valor de puerto que ha seleccionado al configurar el extremo privado de HTTPS en la máquina virtual.

1. Modifique el parámetro **$DNSName**:

	- El script se configura para un certificado comodín $DNSName="+". Si no que quiere configurar para un enlace de certificado comodín, comente $DNSName ="+" y habilitar la siguiente línea, la referencia $DNSNAme completa, $DNSName="$server.cloudapp.net ##".

		Cambie el valor de $DNSName si no quiere usar el nombre DNS de la máquina virtual para Reporting Services. Si usa el parámetro, el certificado también debe usar este nombre y registrar el nombre globalmente en un servidor DNS.

1. El script está configurado actualmente para Reporting Services. Si quiere ejecutar el script para Reporting Services, modifique la parte de la versión de la ruta de acceso al espacio de nombres "v11", en la instrucción Get-WmiObject.

1. Ejecute el script.

**Validación**: para comprobar que la funcionalidad del servidor de informes básica es correcta, consulte la sección [Comprobar la configuración](#verify-the-connection) más adelante en este tema. Para comprobar el enlace de certificado, abra un símbolo del sistema con privilegios administrativos y luego ejecute el siguiente comando:

	netsh http show sslcert

El resultado incluirá lo siguiente:

	IP:port                      : 0.0.0.0:443

	Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### Usar el Administrador de configuración para configurar el Servidor de informes

Si no quiere ejecutar el script de PowerShell para configurar el servidor de informes, siga los pasos de esta sección para usar el administrador de configuración del modo nativo de Reporting Services para configurar el servidor de informes.

1. En el Portal de Azure clásico, seleccione la máquina virtual y haga clic en Conectar. Use el nombre de usuario y la contraseña que configuró al crear la máquina virtual.

	![conectarse a máquina virtual de azure](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC650112.gif)

1. Ejecute Windows Update e instale las actualizaciones en la máquina virtual. Si se requiere un reinicio de la máquina virtual, reinicie la máquina virtual y vuelva a conectarse a la máquina virtual desde el Portal de Azure clásico.

1. En el menú Inicio de la máquina virtual, escriba **Reporting Services** y abra el **Administrador de configuración de Reporting Services**.

1. Deje los valores predeterminados para **Nombre del servidor** e **Instancia del servidor de informes**. Haga clic en **Conectar**.

1. En el panel izquierdo, haga clic en **URL de servicio web** .

1. De forma predeterminada, RS está configurado para el puerto 80 HTTP con la IP "Todas asignadas". Para agregar HTTPS:

	1. En **Certificado SSL**, seleccione el certificado que quiere usar, por ejemplo, [nombre de máquina virtual].cloudapp.net. Si no aparece ningún certificado, consulte la sección **Paso 2: crear un certificado de servidor** para más información sobre cómo instalar y confiar en el certificado en la máquina virtual.
	
	1. En **Puerto SSL**: elija 443. Si ha configurado el extremo privado HTTPS en la máquina virtual con otro puerto privado, use ese valor aquí.
	
	1. Haga clic en **Aplicar** y espere a que finalice la operación.

1. En el panel izquierdo, haga clic en **Base de datos** .

	1. Haga clic en **Cambiar base de datos**.
	
	1. Haga clic en **Crear una nueva base de datos del servidor de informes** y luego en **Siguiente**.
	
	1. Deje el valor de **nombre de servidor** predeterminado como el nombre de la máquina virtual y deje el valor de **tipo de autenticación** predeterminado como **Usuario actual** – **Seguridad integrada**. Haga clic en **Siguiente**.
	
	1. Deje el **Nombre de base de datos** predeterminado como **ReportServer** y haga clic en **Siguiente**.
	
	1. Deje el valor de **Tipo de autenticación** predeterminado como **Credenciales de servicio** y haga clic en **Siguiente**.
	
	1. Haga clic en **Siguiente** en la página **Resumen**.
	
	1. Cuando haya completado la configuración, haga clic en **Finalizar**.

1. En el panel izquierdo, haga clic en **URL de Administrador de informes**. Deje el valor de **Directorio virtual** predeterminado como **Informes** y haga clic en **Aplicar**.

1. Haga clic en **Salir** para cerrar el Administrador de configuración de Reporting Services.

## Paso 4: abrir el puerto de Firewall de Windows

>[AZURE.NOTE] Si ha usado uno de los scripts para configurar el servidor de informes, puede omitir esta sección. El script incluía un paso para abrir el puerto de firewall. El valor predeterminado era el puerto 80 para HTTP y el puerto 443 para HTTPS.

Para conectarse de forma remota al Administrador de informes o al Servidor de informes en la máquina virtual, se requiere un extremo TCP en la máquina virtual. Se requiere abrir el mismo puerto en el firewall de la máquina virtual. El extremo se creó cuando se aprovisionó la máquina virtual.

En esta sección se ofrece información básica sobre cómo abrir el puerto de firewall. Para más información, consulte [Configurar un firewall para el acceso del Servidor de informes](https://technet.microsoft.com/library/bb934283.aspx).

>[AZURE.NOTE] Si ha usado el script para configurar el servidor de informes, puede omitir esta sección. El script incluía un paso para abrir el puerto de firewall.

Si configuró un puerto privado para HTTPS distinto de 443, modifique el siguiente script correctamente. Para abrir el puerto **443** en el Firewall de Windows, realice los pasos siguientes:

1. Abra la ventana de Windows PowerShell con privilegios administrativos.

1. Si ha usado un puerto distinto de 443 al configurar el extremo HTTPS en la máquina virtual, actualice el puerto en el siguiente comando y luego ejecute el comando:

		New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Cuando finalice el comando, aparece **Aceptar** en el símbolo del sistema.

Para comprobar que se abre el puerto, abra una ventana de Windows PowerShell y ejecute el siguiente comando:

	get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## Comprobar la configuración

Para comprobar que la funcionalidad del servidor de informes básica funciona, abra su explorador con privilegios administrativos y luego vaya hasta las siguientes URL del administrador de informes y del servidor de informes:

- En la máquina virtual, examine hasta la dirección URL del servidor de informes:

		http://localhost/reportserver

- En la máquina virtual, examine hasta la dirección URL del administrador de informes:

		http://localhost/Reports

- Desde el equipo local, vaya al Administrador de informes **remoto** en la máquina virtual. Actualice el nombre DNS en el ejemplo siguiente, según corresponda. Cuando se le pida una contraseña, use las credenciales de administrador que creó cuando se aprovisionó la máquina virtual. El nombre de usuario se encuentra en el formato [Dominio] [nombre de usuario], donde el dominio es el nombre de equipo de la máquina virtual, por ejemplo, ssrsnativecloud\\testuser. Si no está usando HTTP**S**, quite la **s** de la dirección URL. Vea la siguiente sección para obtener información sobre cómo crear usuarios adicionales en la máquina virtual.

		https://ssrsnativecloud.cloudapp.net/Reports

- Desde el equipo local, vaya a la URL del servidor de informes remoto. Actualice el nombre DNS en el ejemplo siguiente, según corresponda. Si no está usando HTTPS, quite la s de la dirección URL.

		https://ssrsnativecloud.cloudapp.net/ReportServer

## Crear usuarios y asignar roles

Después de configurar y comprobar el servidor de informes, una tarea administrativa común es crear uno o más usuarios y asignar usuarios a roles de Reporting Services. Para obtener más información, consulte los temas siguientes:

- [Crear una cuenta de usuarios local](https://technet.microsoft.com/library/cc770642.aspx)

- [Conceder a un usuario acceso a un servidor de informes (Administrador de informes)](https://msdn.microsoft.com/library/ms156034.aspx)

- [Crear y administrar asignaciones de roles](https://msdn.microsoft.com/library/ms155843.aspx)

## Para crear y publicar informes en la máquina virtual de Azure

En la tabla siguiente se resumen algunas de las opciones disponibles para publicar los informes existentes desde un equipo local al servidor de informes hospedados en la máquina virtual de Microsoft Azure:

- **Script de RS.exe**: use el script de RS.exe para copiar elementos de informes desde la máquina virtual de Microsoft Azure y el servidor de informes existente en ella. Para más información, consulte la sección "Modo nativo a modo nativo: máquina virtual de Microsoft Azure" en [Script de rs.exe de Reporting Services de ejemplo para migrar contenido entre servidores de informes](https://msdn.microsoft.com/library/dn531017.aspx).

- **Generador de informes**: la máquina virtual incluye la versión de un solo clic del Generador de informes de Microsoft SQL Server. Para iniciar el Generador de informes por primera vez en la máquina virtual:

	1. Inicie el explorador con privilegios administrativos.
	
	1. Vaya hasta el administrador de informes en la máquina virtual y haga clic en el **Generador de informes** en la cinta de opciones.

	Para más información, consulte [Instalar, desinstalar y asistencia del Generador de informes](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools: máquina virtual**: si ha creado la máquina virtual con SQL Server 2012, SQL Server Data Tools se instala en la máquina virtual y se puede usar para crear **proyectos del Servidor de informes** e informes en la máquina virtual. SQL Server Data Tools puede publicar los informes en el servidor de informes en la máquina virtual.
	
	Si ha creado la máquina virtual con SQL server 2014, puede instalar SQL Server Data Tools- BI para Visual Studio. Para obtener más información, consulte los temas siguientes:

	- [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

	- [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

	- [SQL Server Data Tools y SQL Server Business Intelligence (SSDT-BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools: remoto**: en el equipo local, cree un proyecto de Reporting Services en SQL Server Data Tools que contenga informes de Reporting Services. Configure el proyecto para que se conecte a la dirección URL del servicio web.

	![propiedades del proyecto de ssdt para proyecto SSRS](./media/virtual-machines-sql-server-create-native-mode-report-server-powershell/IC650114.gif)

- **Usar script**: use el script para copiar el contenido del servidor de informes. Para obtener más información, vea [Script de rs.exe de Reporting Services de ejemplo para migrar contenido entre servidores de informes](https://msdn.microsoft.com/library/dn531017.aspx).

## Minimizar el costo si no está usando la máquina virtual

>[AZURE.NOTE] Para minimizar los costos de las máquinas virtuales de Azure cuando no estén en uso, apague la máquina virtual desde el Portal de Azure clásico. Si usa las opciones de energía de Windows dentro de una máquina virtual para apagar la máquina virtual, se le seguirá cobrando el mismo importe para la máquina virtual. Para reducir los costos, deberá apagar la máquina virtual en el Portal de Azure clásico. Si ya no necesita la máquina virtual, recuerde eliminar la máquina virtual y los archivos .vhd asociados para evitar costos de almacenamiento. Para más información, consulte la sección de preguntas más frecuentes en [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

## Más información

### Recursos

- Para consultar contenido similar relacionado con una implementación de servidor único de SQL Server Business Intelligence y SharePoint 2013, consulte [Usar Windows PowerShell para crear una máquina virtual de Azure con SQL Server BI y SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Para consultar contenido similar relacionado con una implementación de varios servidores de SQL Server Business Intelligence y SharePoint 2013, consulte [Implementar SQL Server Business Intelligence en máquinas virtuales de Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Para consultar información similar relacionada con implementaciones de SQL Server Business Intelligence en Máquinas virtuales de Azure, consulte [SQL Server Business Intelligence en Máquinas virtuales de Azure](virtual-machines-sql-server-business-intelligence.md).

- Para más información sobre el costo de procesos de Azure, consulte la pestaña Máquinas virtuales de la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### Contenido de la Comunidad

- Para ver instrucciones paso a paso sobre cómo crear un servidor de informes de modo nativo de Reporting Services sin usar el script, consulte [Hospedar el servicio de SQL Reporting en la máquina virtual de Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### Vínculos a otros recursos para SQL Server en máquinas virtuales de Azure

[Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md)

<!---HONumber=AcomDC_0128_2016-->