<properties
	pageTitle="Entorno de prueba de la configuración base"
	description="Obtenga información acerca de cómo crear un entorno de desarrollo/prueba sencillo que simula una intranet simplificada en Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="josephd"/>

# Entorno de prueba de la configuración básica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-base-configuration-test-environment-resource-manager.md).

En este artículo se proporcionan instrucciones paso a paso para crear el entorno de prueba de la configuración básica en una red virtual de Azure.

Puede utilizar el entorno de prueba resultante:

- Para el desarrollo de aplicaciones y pruebas.
- Para el [entorno de nube híbrida simulado](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
- Para ampliarlo con máquinas virtuales y servicios de Azure adicionales para un entorno de prueba de su propio diseño.

El entorno de prueba de la configuración básica consta de la subred de la red corporativa en una red virtual solo en la nube denominada TestLab que simula una intranet privada simplificada conectada a Internet.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Contiene:

- Una máquina virtual de Azure ejecuta un Windows Server 2012 R2 llamado DC1, que está configurado como un servidor de sistema de nombres de dominio (DNS) y un controlador de dominio de la intranet.
- Una máquina virtual de Azure que ejecuta Windows Server 2012 R2 llamado APP1, que está configurado como un servidor web y de aplicaciones generales.
- Una máquina virtual de Azure ejecuta el Windows Server 2012 R2 denominado CLIENT1, que actúa como cliente de la intranet.

Esta configuración permite a DC1, APP1, CLIENT1 y otros equipos de subred de la red corporativa:

- Conectarse a Internet para instalar las actualizaciones, obtener acceso a recursos en Internet en tiempo real y participar en las tecnologías de nube pública, como Microsoft Office 365 y otros servicios de Azure.
- Administrarse de forma remota con Conexiones de escritorio remoto desde el equipo que está conectado a Internet o la red de su organización.

Hay cuatro fases de configuración de la subred de la red corporativa del entorno de pruebas de la configuración base de Windows Server 2012 R2 en Azure.

1.	Creación de la red virtual.
2.	Configuración de DC1.
3.	Configuración de APP1.
4.	Configuración de CLIENT1.

Si no dispone de ninguna cuenta de Azure, puede registrarse para obtener una prueba gratuita en [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a las plataformas de MSDN, consulte [Crédito mensual de Azure para suscriptores de Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/).

> [AZURE.NOTE] Las máquinas virtuales en Azure suponen en un costo económico constante cuando se están ejecutando. Este costo se factura en la evaluación gratuita, la suscripción a las plataformas de MSDN o la suscripción de pago. Para obtener más información acerca de los costos de ejecutar máquinas virtuales de Azure, consulte [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/). Para reducir los costos, consulte [Reducción del costo de las máquinas virtuales del entorno de prueba en Azure](#costs).

## Fase 1: creación de la red virtual

En primer lugar, cree la red virtual TestLab, que va a hospedar la subred de la red corporativa de la configuración básica.

1.	En la barra de tareas del [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo > Servicios de red > Red virtual > Creación personalizada**.
2.	En la página Detalles de redes virtuales, escriba **TestLab** en **Nombre**.
3.	En **Ubicación**, seleccione la región adecuada.
4.	Haga clic en la flecha Siguiente.
5.	En la página Servidores DNS y conectividad VPN, en **Servidores DNS**, introduzca **DC1** en **Seleccione o introduzca un nombre**, escriba **10.0.0.4** en **Dirección IP** y, a continuación, haga clic en la flecha Siguiente.
6.	En la página Espacios de direcciones de la red virtual, en **Subredes**, haga clic en **Subred 1** y reemplace el nombre por **Corpnet**.
7.	En la columna **CIDR (recuento de direcciones)** para la subred de la red corporativa, haga clic en **/24 (256)**.
8.	Haga clic en el icono Completar. Espere hasta que se cree la red virtual antes de continuar.

A continuación, siga las instrucciones en [Cómo instalar y configurar Azure PowerShell](../install-configure-powershell.md) para instalar Azure PowerShell en el equipo local. Abra un símbolo del sistema de Azure PowerShell.

En primer lugar, seleccione la suscripción de Azure correcta con estos comandos. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre correcto.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Puede obtener el nombre de la suscripción de la propiedad **SubscriptionName** de la visualización del comando **Get-AzureSubscription**.

A continuación, cree un servicio en la nube de Azure. El servicio en la nube actúa como un límite de seguridad y un contenedor lógico para las máquinas virtuales situadas en la red virtual. También proporciona una manera de conectar y administrar remotamente las máquinas virtuales en la subred de la red corporativa.

Debe elegir un nombre único para el servicio en la nube. *El nombre del servicio en la nube solo puede contener letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.*

Por ejemplo, podría asignar el nombre TestLab-*UniqueSequence* al servicio en la nube, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría asignar el nombre del servicio de nube TestLab-Tailspin.

Se puede comprobar la exclusividad del nombre con este comando de Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. A continuación, cree el servicio en la nube con estos comandos.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Registre el nombre real del servicio en la nube.

A continuación, configure una cuenta de almacenamiento que contendrá los discos para las máquinas virtuales y los discos de datos adicionales. *Debe elegir un nombre único que contenga solo letras minúsculas y números.* Se puede comprobar la exclusividad del nombre de cuenta de almacenamiento con este comando de Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único. A continuación, cree la cuenta de almacenamiento y establezca la suscripción para utilizar con estos comandos.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Se trata de la configuración actual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## Fase 2: Configuración de DC1

DC1 es un controlador de dominio para el dominio de servicios de dominio de Active Directory (AD DS) corp.contoso.com y un servidor DNS para las máquinas virtuales de la red virtual TestLab.

En primer lugar, proporcione el nombre del servicio en la nube y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina Virtual de Azure para DC1.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

A continuación, conéctese a la máquina virtual DC1.

1.	En el Portal de Azure clásico, haga clic en **Máquinas virtuales** en el panel izquierdo y luego en **Iniciado** en la columna **Estado** para la máquina virtual DC1.  
2.	En la barra de tareas, haga clic en **Conectar**.
3.	Cuando se le pida que abra DC1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan las credenciales, utilice las siguientes:
- Nombre: **DC1\**[Nombre de la cuenta de administrador local]
- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, agregue un disco de datos adicional como un nuevo volumen con la letra de unidad F:.

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC1 como un controlador de dominio y servidor DNS para el dominio corp.contoso.com. Ejecute estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Una vez reiniciado DC1, vuelva a conectar la máquina virtual de DC1.

1.	En la página de máquinas virtuales del Portal de Azure clásico, haga clic en **En ejecución** en la columna **Estado** de la máquina virtual de DC1.
2.	En la barra de tareas, haga clic en **Conectar**.
3.	Cuando se le pida que abra DC1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan las credenciales, utilice las siguientes:
- Nombre: **CORP\**[Nombre de la cuenta de administrador local]
- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando se lo solicite un cuadro de mensaje de conexión a Escritorio remoto que haga referencia a certificados, haga clic en **Sí**.

A continuación, cree una cuenta de usuario en Active Directory que se utilizará al iniciar sesión en equipos miembros del dominio corporativo. Ejecute estos comandos uno a uno en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Tenga en cuenta que el primer comando genera un símbolo del sistema para proporcionar la contraseña de la cuenta User1. Puesto que esta cuenta se utilizará para las conexiones a escritorio remotos para todos los equipos de miembros de dominio CORP, elija una contraseña segura. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registre la contraseña de la cuenta User1 y almacénela en una ubicación segura.

Vuelva a conectarse a la máquina virtual DC1 con la cuenta CORP\\User1.

A continuación, para permitir el tráfico de la herramienta Ping, ejecute este comando en un símbolo de Windows PowerShell con nivel de administrador.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Se trata de la configuración actual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## Fase 3: Configuración de APP1

App1 proporciona servicios de uso compartido de archivos y web.

En primer lugar, proporcione el nombre del servicio en la nube y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina Virtual de Azure para APP1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

A continuación, conéctese a la máquina virtual APP1 con las credenciales CORP\\User1 y abra un símbolo de Windows PowerShell de nivel de administrador.

Para comprobar la comunicación de red y la resolución de nombres entre APP1 y DC1, ejecute el comando **ping dc1.corp.contoso.com** y compruebe que haya cuatro respuestas.

A continuación, convierta APP1 un en servidor web con este comando en el símbolo del sistema de Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

A continuación, cree una carpeta compartida y un archivo de texto dentro de la carpeta en APP1 con estos comandos.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Se trata de la configuración actual.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## Fase 4: Configuración de CLIENT1

Client1 actúa como un equipo portátil, tableta o de escritorio en la intranet de Contoso.

En primer lugar, proporcione el nombre del servicio en la nube y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina Virtual de Azure para CLIENT1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

A continuación, conéctese a la máquina virtual CLIENT1 con las credenciales CORP\\User1.

Para comprobar la comunicación de la red y la resolución de nombres entre CLIENT1 y DC1, ejecute el comando **ping dc1.corp.contoso.com** en un símbolo del sistema de Windows PowerShell y compruebe que hay cuatro respuestas.

A continuación, compruebe que puede tener acceso a recursos compartidos de archivos y web en APP1 de CLIENT1.

1.	En el Administrador de servidores, en el panel de árbol, haga clic en **Servidor Local**.
2.	En **Propiedades de CLIENT1**, haga clic en **Activo** al lado de **Configuración de seguridad mejorada de IE**.
3.	En **Configuración de seguridad mejorada de IE**, haga clic en **Desactivar** para **Administradores** y **Usuarios** y, a continuación, haga clic en **Aceptar**.
4.	En la pantalla Inicio, haga clic en **Internet Explorer** y, a continuación, en **Aceptar**.
5.	En la barra de direcciones, escriba ****http://app1.corp.contoso.com/** y, a continuación, presione ENTRAR. Debe ver la página web de Internet Information Services de forma predeterminada para APP1.
6.	En la barra de tareas del escritorio, haga clic en el icono Explorador de archivos.
7.	En la barra de direcciones, escriba **\\\app1\\Files** y, a continuación, presione ENTRAR.
8.	Debería ver una ventana de carpeta con el contenido de la carpeta compartida Archivos.
9.	En la ventana de carpeta compartida **Archivos** , haga doble clic en el archivo **Example.txt**. Debería ver el contenido del archivo Example.txt.
10.	Cierre las ventanas de carpetas compartidas **Example.tx - Bloc de notas** y **Archivos**.

Se trata de la configuración final.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

La configuración base de Azure ya está lista para entornos de pruebas y desarrollo de aplicaciones o para entornos de prueba adicionales.

## Paso siguiente

- Configure el [entorno de nube híbrida simulada](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) para probar las configuraciones híbridas.

## <a id="costs"></a>Reducción del costo de las máquinas virtuales del entorno de prueba en Azure

Para minimizar el costo de ejecutar las máquinas virtuales de entorno de prueba, puede realizar una de las acciones siguientes:

- Crear el entorno de prueba y realizar las pruebas y demostraciones necesarias lo más rápido posible. Cuando haya finalizado, elimine las máquinas virtuales del entorno de prueba.
- Apagar sus máquinas virtuales del entorno de prueba en un estado desasignado.

Para cerrar las máquinas virtuales con Azure PowerShell, escriba el nombre del servicio en la nube y ejecute estos comandos.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Para asegurarse de que las máquinas virtuales funcionan correctamente al iniciar todos ellos desde el estado Detenido (desasignado), debe iniciarlos en el orden siguiente:

1.	DC1
2.	APP1
3.	CLIENT1

Para iniciar las máquinas virtuales en orden con Azure PowerShell, escriba el nombre del servicio en la nube y ejecute estos comandos.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=AcomDC_0128_2016-->