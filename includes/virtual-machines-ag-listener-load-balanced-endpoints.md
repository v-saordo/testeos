Tienes que crear un extremo de carga equilibrada para cada máquina virtual que hospeda una réplica de Azure. Si tienes réplicas en varias regiones, cada réplica de esa región tiene que estar en el mismo servicio en la nube de la misma red virtual. La creación de réplicas de grupo de disponibilidad que abarcan varias regiones de Azure, requiere configurar varias redes virtuales. Para más información sobre cómo configurar la conectividad entre redes virtuales, consulta [Configurar conectividad de red virtual a red virtual](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. En el portal de Azure, navega a todas las máquinas virtuales que hospedas una réplica y consulta los detalles.

1. Haz clic en la pestaña **Extremos** para cada una de las máquinas virtuales.

1. Comprueba que el **Nombre** y el **Puerto público** del extremo del agente de escucha que quieres utilizar no está ya en uso. En el ejemplo siguiente, el nombre es "MyEndpoint" y el puerto es "1433".

1. En el cliente local, descarga e instala el [último módulo de PowerShell](https://azure.microsoft.com/downloads/).

1. Inicie **Azure PowerShell**. Se abrirá una nueva sesión de PowerShell con los módulos de administración de Azure cargados.

1. Ejecuta **Get-AzurePublishSettingsFile**. Este cmdlet te dirige a un explorador para descargar un archivo de configuración de publicación en un directorio local. Puede que tengas que escribir las credenciales de inicio de sesión de tu suscripción a Azure.

1. Ejecuta el comando **Import-AzurePublishSettingsFile** con la ruta de acceso del archivo de configuración de publicación que descargaste:

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	Una vez que el archivo de configuración de publicación se haya importado, puedes administrar tu suscripción a Azure en la sesión de PowerShell.

<!------HONumber=AcomDC_0128_2016-->