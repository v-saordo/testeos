<properties 
   pageTitle="Creación de rutas y habilitación del reenvío de IP en Azure"
   description="Obtenga información acerca de cómo administrar rutas definidas por el usuario y el reenvío IP"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos" />

# Creación de rutas y habilitación del reenvío de IP en Azure
Puede utilizar aplicaciones virtuales de Azure para controlar el tráfico de la red virtual de Azure. Sin embargo, es preciso crear rutas que permitan que las máquinas virtuales y servicios en la nube de la red virtual envíen paquetes a una aplicación virtual, en lugar de al destino deseado para el paquete. También es preciso habilitar el reenvío IP en la máquina virtual del dispositivo virtual para que pueda recibir y reenviar los paquetes que no vayan dirigidos a la máquina virtual de la aplicación virtual real.

## Administración de rutas
En Azure puede agregar, quitar y cambiar las rutas mediante PowerShell. Para poder crear una ruta, antes es preciso crear una tabla de rutas que hospede la ruta.

### Creación de una tabla de rutas
Para crear una tabla de rutas denominada *FrontEndSubnetRouteTable*, ejecute el siguiente comando de PowerShell:

	```powershell
	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
		-Location uscentral `
		-Label "Route table for front end subnet"
	```

La salida del comando anterior debe tener el siguiente aspecto:

	Name                      Location   Label                          
	----                      --------   -----                          
	FrontEndSubnetRouteTable  West US    Route table for front end subnet

### Adición de una ruta a una tabla de rutas
Para agregar una ruta que establezca *10.1.1.10* como próximo salto de la subred *10.2.0.0/16* en la tabla de rutas creada anteriormente, ejecute el siguiente comando de PowerShell:

	```powershell
	Get-AzureRouteTable FrontEndSubnetRouteTable `
		|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
		-NextHopType VirtualAppliance `
		-NextHopIpAddress 10.1.1.10
	```

La salida del comando anterior debe tener el siguiente aspecto:

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### Asociación de una ruta a una subred
Para poder usar una tabla de rutas, esta debe estar asociada con una o varias subredes. Para asociar la tabla de rutas *FrontEndSubnetRouteTable* a una subred denominada *FrontEndSubnet* de la red virtual *ProductionVnet*, ejecute el siguiente comando de PowerShell:

	```powershell
	Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
		-SubnetName FrontEndSubnet `
		-RouteTableName FrontEndSubnetRouteTable
	```

### Vista de las rutas aplicadas en una máquina virtual
Puede consultar Azure para ver las rutas reales aplicadas a una instancia de máquina virtual o de rol específica. Las rutas que se muestran incluyen las rutas predeterminadas que proporciona Azure, así como las rutas anunciadas por una puerta de enlace de VPN. El límite de rutas que se muestran es 800.

Para ver las rutas asociadas a la NIC principal de una máquina virtual denominada *FWAppliance1*, ejecute el siguiente comando de PowerShell:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable
	```

La salida del comando anterior debe tener el siguiente aspecto:

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

Para ver las rutas asociadas a una NIC secundaria denominada *backendnic* de una máquina virtual denominada *FWAppliance1*, ejecute el siguiente comando de PowerShell:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
	```

Para ver las rutas asociadas a la NIC principal en una instancia de rol denominada *myRole* que forma parte de un servicio en la nube denominado *ProductionVMs*, ejecute el siguiente comando de PowerShell:

	```powershell
	Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
		-RoleInstanceName myRole
	```

## Administración del reenvío IP
Como ya se ha mencionado, es preciso habilitar el reenvío IP en todas las instancias de máquina virtual o de rol que vayan a actuar como aplicaciones virtuales.

### Habilitación del reenvío IP
Para habilitar el reenvío IP en una máquina virtual denominada *FWAppliance1*, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

Para habilitar el reenvío IP en una instancia de rol denominada *FWAppliance1* de un servicio en la nube denominado *DMZService*, ejecute el siguiente comando de PowerShell:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### Deshabilitación del reenvío IP
Para deshabilitar el reenvío IP en una máquina virtual denominada *FWAppliance1*, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName DMZService `
	| Set-AzureIPForwarding -Disable
```

Para deshabilitar el reenvío IP en una instancia de rol denominada *FWAppliance* de un servicio en la nube denominado *DMZService*, ejecute el siguiente comando de PowerShell:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### Vista del estado del reenvío IP
Para ver el estado del reenvío IP en una máquina virtual denominada *FWAppliance1*, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
``` 

<!---HONumber=AcomDC_1210_2015-->