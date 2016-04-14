<properties 
	pageTitle="Entorno de prueba de granja de SharePoint 2013 | Microsoft Azure" 
	description="Aprenda a crear una granja de intranet de SharePoint Server 2013 de dos niveles en un entorno de nube híbrida para desarrollo o pruebas de profesionales de TI." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.
 

En este tema se muestran los pasos para crear un entorno de nube híbrida para probar una granja de SharePoint de intranet hospedada en Microsoft Azure. Aquí está la configuración resultante.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
Esta configuración simula un entorno de producción de SharePoint en Azure desde su ubicación en Internet. Consta de:

- Una red local simplificada (la subred de red corporativa).
- Una red virtual entre locales hospedada en Microsoft Azure (TestVNET).
- Una conexión VPN de sitio a sitio.
- Una granja de SharePoint de dos niveles y un controlador de dominio secundario en la red virtual TestVNET.

Esta configuración proporciona una base y un punto de partida común desde el que puede:

- Desarrollar y probar aplicaciones en un entorno de intranet de SharePoint en un entorno de nube híbrida.
- Realizar pruebas de esta carga de trabajo de TI basada en la nube híbrida.

Hay tres fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración del entorno de nube híbrida para pruebas.
2.	Configuración del equipo con SQL server (SQL1).
3.	Configuración del servidor de SharePoint (SP1).

Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita en [Probar Azure](https://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: configuración del entorno de nube híbrida

Utilice las instrucciones del tema [Configuración de un entorno de nube híbrida para hacer pruebas](virtual-networks-setup-hybrid-cloud-environment-testing.md). Dado que este entorno de prueba no requiere la presencia del servidor APP1 en la subred de la red corporativa, no dude en cerrarlo por ahora.

Esta es su configuración actual.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)

> [AZURE.NOTE] Asimismo, puede configurar el entorno de prueba de nube híbrida simulada para la fase 1. Consulte [Configuración de un entorno simulado de nube híbrida para hacer pruebas](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) y así obtener más instrucciones.
 
## Fase 2: configuración del equipo con SQL Server (SQL1)

Desde el Portal de administración de Azure, inicie el equipo de DC2 si es necesario.

En primer lugar, cree una conexión de escritorio remoto a DC2 utilizando las credenciales de CORP\\User1.

A continuación, cree una cuenta de administrador de granja de servidores de SharePoint. Abra un símbolo del sistema de Windows PowerShell con nivel de administrador en DC2 y ejecute este comando.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Cuando se le pida que proporcione la contraseña de la cuenta SPFarmAdmin, escriba una contraseña segura y anótela en una ubicación segura.

En primer lugar, cree una máquina virtual de Azure de SQL1 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.


	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

A continuación, conéctese a la nueva máquina virtual de SQL1 *mediante la cuenta de administrador local*.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, en **En ejecución** en la columna Estado de SQL1.
2.	En la barra de tareas, haga clic en **Conectar**. 
3.	Cuando se le pida que abra SQL1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan credenciales, utilice estas:
	- Nombre: **SQL1\**[Nombre de la cuenta de administrador local]
	- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, configure reglas del Firewall de Windows para permitir el tráfico y probar la conectividad básica y SQL Server. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en SQL1, ejecute estos comandos.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando ping debería producir cuatro respuestas correctas desde la dirección IP 10.0.0.1.

A continuación, agregue el disco de datos adicional como un nuevo volumen con la letra de unidad F:.

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

Ejecute estos comandos en el símbolo del sistema de Windows PowerShell en SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

A continuación, configure SQL Server 2014 para usar la unidad F: para nuevas bases de datos y para los permisos de cuenta de usuario.

1.	Desde la pantalla Inicio, escriba **SQL Server Management** y haga clic en **SQL Server 2014 Management Studio**.
2.	En **Conectar con el servidor**, haga clic en **Conectar**.
3.	En el panel de árbol del Explorador de objetos, haga clic con el botón derecho en **SQL1** y, a continuación, haga clic en **Propiedades**.
4.	En la ventana **Propiedades del servidor**, haga clic en **Configuración de base de datos**.
5.	Busque las **Ubicaciones predeterminadas de la base de datos** y establezca estos valores: 
	- Para **Datos**, escriba la ruta de acceso **f:\Data**.
	- Para **Registro**, escriba la ruta de acceso **f:\Log**.
	- Para **Copia de seguridad**, escriba la ruta de acceso **f:\Backup**.
	- Observe que solo las bases de datos nuevas utilizan estas ubicaciones.
6.	Haga clic en **Aceptar** para cerrar la ventana.
7.	En el panel de árbol del **Explorador de objetos**, abra **Seguridad**.
8.	Haga clic con el botón derecho en **Inicios de sesión** y, a continuación, haga clic en **Nuevo inicio de sesión**.
9.	En **Nombre de inicio de sesión**, escriba **CORP\User1**.
10.	En la página **Roles de servidor**, haga clic en **sysadmin** y, a continuación, haga clic en **Aceptar**.
11.	En el panel de árbol del **Explorador de objetos**, haga clic con el botón derecho en **Inicios de sesión** y, a continuación, haga clic en **Nuevo inicio de sesión**.
12.	En la página **General**, en **Nombre de inicio de sesión**, escriba **CORP\SPFarmAdmin**.
13.	En la página **Roles de servidor**, seleccione **dbcreator** y, a continuación, haga clic en **Aceptar**.
14.	Cierre Microsoft SQL Server Management Studio.

Esta es su configuración actual.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## Fase 3: configuración de los servidores de SharePoint (SP1)

En primer lugar, cree una máquina virtual de Azure de SP1 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SP1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

A continuación, conéctese a la máquina virtual de SP1 con las credenciales de CORP\User1.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en SP1, ejecute estos comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando ping debería producir cuatro respuestas correctas desde la dirección IP 10.0.0.1.

A continuación, configure SP1 para una nueva granja de SharePoint y un sitio de equipo predeterminado.

1.	Desde la pantalla Inicio, escriba **Productos de SharePoint 2013** y, a continuación, haga clic en **Asistente para la configuración de productos de SharePoint 2013**. Cuando se le pida que permita al programa realizar cambios en el equipo, haga clic en **Sí**.
2.	En la página de productos de SharePoint, haga clic en **Siguiente**. 
3.	En el cuadro de diálogo que le notifica si algunos servicios podrían tener que reiniciarse durante la configuración, haga clic en **Sí**.
4.	En la página Conexión a una granja de servidores, haga clic en **Crear una nueva granja de servidores** y, a continuación, haga clic en **Siguiente**.
5.	En la página Especificación de la configuración de la base de datos, introduzca **sql1.corp.contoso.com** en **Servidor de base de datos**, introduzca **CORP\\SPFarmAdmin** en **Nombre de usuario**, escriba la contraseña de la cuenta de SPFarmAdmin en **Contraseña** y, a continuación, haga clic en **Siguiente**.
6.	En la página Especificar la configuración de seguridad de la granja, escriba **P@ssphrase** tanto en **Frase de contraseña** como en **Confirmar frase de contraseña** y, a continuación, haga clic en **Siguiente**.
7.	En la página Configuración de la aplicación web de administración central de SharePoint, haga clic en **Siguiente**.
8.	En la página Finalización del Asistente para la configuración de productos de SharePoint, haga clic en **Siguiente**. El Asistente para configuración de productos de SharePoint puede tardar unos minutos en completarse.
9.	En la página Configuración realizada correctamente, haga clic en **Finalizar**. Una vez finalizado, Internet Explorer se inicia con una pestaña denominada Asistente de configuración de granja inicial.
10.	En el cuadro de diálogo **Ayudar a que SharePoint mejore**, haga clic en **No, no deseo participar** y, a continuación, haga clic en **Aceptar**.
11.	En **¿Cómo desea configurar la granja de SharePoint?**, haga clic en **Iniciar el asistente**.
12.	En la página Configuración de la granja de SharePoint, en **Cuenta de servicio**, haga clic en **Usar una cuenta administrada ya existente**.
13.	En **Servicios**, desactive las casillas, excepto la casilla junto a **Servicio de estado** y, a continuación, haga clic en **Siguiente**. La página Realizando la operación puede mostrarse durante un tiempo antes de que finalice.
14.	En la página Creación de colección de sitios, en **Título y descripción**, escriba **Contoso Corporation** en **Título**, especifique la dirección URL ****http://sp1**/ y, a continuación, haga clic en **Aceptar**. La página Realizando la operación puede mostrarse durante un tiempo antes de que finalice. En este paso se crea un sitio de equipo en la dirección URL http://sp1.
15.	En la página Esto completa el Asistente de configuración de granja, haga clic en **Finalizar**. La pestaña de Internet Explorer muestra el sitio de Administración central de SharePoint 2013.
16.	Inicie sesión en el equipo CLIENT1 con las credenciales de la cuenta de CORP\\User1 y, a continuación, inicie Internet Explorer.
17.	En la barra de direcciones, escriba **http://sp1/** y, a continuación, presione ENTRAR. Debería ver el sitio del equipo de SharePoint de Contoso Corporation. El sitio puede tardar un rato en mostrarse. 

Se trata de la configuración actual.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
Su granja de intranet de SharePoint en un entorno de nube híbrida ya está lista para realizar pruebas.

## Pasos siguientes

- Configure la [carga de trabajo de producción](../virtual-machines/virtual-machines-workload-intranet-sharepoint-overview.md).

<!---HONumber=AcomDC_0204_2016-->