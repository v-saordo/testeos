## Cómo crear una red virtual con un archivo de configuración de red desde PowerShell

Azure utiliza un archivo xml para definir todas las redes virtuales disponibles para una suscripción. Puede descargar este archivo y editarlo para modificar o eliminar las redes virtuales existentes y crear otras nuevas. En este documento, obtendrá información sobre cómo descargar este archivo, denominado archivo de configuración de red (o netcgf) y editarlo para crear una red virtual nueva. Compruebe el [esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para obtener más información sobre el archivo de configuración de red.

Para crear una red virtual mediante un archivo netcfg desde PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. Desde la consola de Azure PowerShell, use el cmdlet **AzureVnetConfig Get** para descargar el archivo de configuración de red ejecutando el siguiente comando. 

		Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

	Resultado esperado:

		XMLConfiguration                                                                                                     
		----------------                                                                                                     
		<?xml version="1.0" encoding="utf-8"?>...  

3. Abra el archivo que guardó en el paso 2 anterior con cualquier aplicación de edición de texto o XML y busque el elemento **<VirtualNetworkSites>**. Si ya tiene otras redes creadas, cada una de las redes se mostrará como su propio elemento **<VirtualNetworkSite>**.
4. Para crear la red virtual que se describe en este escenario, agregue el siguiente código XML justo debajo del elemento **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

9.  Guarde el archivo de configuración de red.
10. Desde la consola de Azure PowerShell, use el cmdlet **Set-AzureVnetConfig** para cargar el archivo de configuración de red ejecutando el siguiente comando. Observe el resultado del comando; debería ver **Succeeded** en **OperationStatus**. Si no es así, compruebe si hay errores en el archivo xml.

		Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

	Este es el resultado esperado del comando anterior:

		OperationDescription OperationId                          OperationStatus
		-------------------- -----------                          ---------------
		Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
	
11. Desde la consola de Azure PowerShell, use el cmdlet **Get-AzureVnetSite** para comprobar que se ha agregado la nueva red ejecutando el siguiente comando.

		Get-AzureVNetSite -VNetName TestVNet

	Este es el resultado esperado del comando anterior:

		AddressSpacePrefixes : {192.168.0.0/16}
		Location             : Central US
		AffinityGroup        : 
		DnsServers           : {}
		GatewayProfile       : 
		GatewaySites         : 
		Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
		InUse                : False
		Label                : 
		Name                 : TestVNet
		State                : Created
		Subnets              : {FrontEnd, BackEnd}
		OperationDescription : Get-AzureVNetSite
		OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
		OperationStatus      : Succeeded

<!---HONumber=Oct15_HO3-->