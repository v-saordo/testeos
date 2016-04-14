<properties
   pageTitle="Ejemplo de red perimetral: Creación de una red perimetral para proteger las aplicaciones con un firewall y un grupo de seguridad de red"
   description="Creación de una red perimetral con un firewall y grupos de seguridad de red (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# Ejemplo 2: Creación de una red perimetral para proteger las aplicaciones con un Firewall y grupos de seguridad de red

[Volver a la página de procedimientos recomendados de límites de seguridad][HOME]

En este ejemplo se creará una red perimetral con un firewall, cuatro servidores Windows y grupos de seguridad de red. También le guiará por cada uno de los comandos pertinentes para que comprenda mejor cada paso. Hay también una sección Escenario de tráfico para proporcionar información detallada paso a paso de cómo pasa el tráfico a través de los niveles de defensa de la red perimetral. Por último, en la sección de referencias está el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios.

![Red perimetral de entrada con dispositivo virtual de red y grupo de seguridad de red][1]

## Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
- Una red virtual, “CorpNetwork”, con dos subredes, “FrontEnd” y “BackEnd”
- Un único grupo de seguridad de red que se aplica a ambas subredes
- Un dispositivo virtual de red, en este ejemplo un firewall Barracuda NextGen, conectado a la subred front-end
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones (“AppVM01”, “AppVM02”)
- Un servidor Windows que representa un servidor DNS ("DNS01")

>[AZURE.NOTE] Aunque este ejemplo usa un firewall Barracuda NextGen, muchos de los distintos dispositivos virtuales de red podrían usarse para este ejemplo.

En la sección de referencias siguiente hay un script de PowerShell que compilará la mayor parte del entorno descrito anteriormente. Aunque la creación de las máquinas virtuales y las redes virtuales la realiza el script de ejemplo, no se describe en detalle en este documento.
 
Para crear el entorno:

  1.	Guarde el archivo xml de configuración de red incluido en la sección de referencias (actualizado con nombres, ubicación y direcciones IP para que coincidan con el escenario dado).
  2.	Actualice las variables de usuario en el script para que coincida con el entorno en el que se va a ejecutar el script (suscripciones, nombres de servicio, etc.).
  3.	Ejecución del script en PowerShell

**Nota**: la región indicada en el script de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.

Cuando el script se ejecuta correctamente, se pueden realizar los siguientes pasos posteriores al script:

1.	Configure las reglas de firewall, esto se trata en la sección siguiente denominada Reglas de firewall.
2.	Opcionalmente, en la sección de referencias hay dos scripts para configurar el servidor web y el servidor de aplicaciones con una aplicación web sencilla para permitir las pruebas con esta configuración de red perimetral.

La siguiente sección explica la mayor parte de las instrucciones de scripts relativas a los grupos de seguridad de red.

## Grupos de seguridad de red (NSG)
En este ejemplo, se crea un grupo de grupos de seguridad de red y después se carga con seis reglas.

>[AZURE.TIP] Por lo general, debe crear primero las reglas específicas de "Permitir" y, por último, las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1.	Se permite el tráfico DNS interno (puerto 53)
2.	Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual
3.	Se permite el tráfico HTTP (puerto 80) desde Internet a NVA (firewall)
4.	Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5.	Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6.	Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si una solicitud HTTP era entrante desde Internet al servidor web, se aplicarían ambas reglas, la 3 (permitir) y la 5 (denegar), pero como la regla 3 tiene mayor prioridad, solo se aplicaría esa y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al firewall. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pase al servidor. La regla 6 (denegar) impide que la subred front-end se comunique con la subred back-end (excepto el tráfico permitido en las reglas 1 y 4) y esto protege la red back-end en el caso de que un atacante ponga en peligro la aplicación web en el front-end, y el atacante tendría acceso limitado a la red back-end "protegida" (solo a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario, que se explora en otro ejemplo que se puede encontrar en el [documento de límites de seguridad principales][HOME].

Las reglas del grupo de seguridad de red tratadas anteriormente son muy similares a las reglas del grupo de seguridad de red del [Ejemplo 1: Creación de una red perimetral simple con grupos de seguridad de red][Example1]. Revise la descripción del grupo de seguridad de red en ese documento para una visión detallada de cada regla del grupo de seguridad de red y sus atributos.

## Reglas de firewall
Deberá instalarse un cliente de administración en el equipo para administrar el firewall y crear las configuraciones necesarias. Consulte la documentación del proveedor de su firewall (o de otro dispositivo virtual de red) acerca de cómo administrar el dispositivo. El resto de esta sección describe la configuración del mismo firewall, a través del cliente de administración de proveedores (es decir, no el Portal de Azure o PowerShell).

Puede encontrar instrucciones para descargar el cliente y conectarse al firewall Barracuda usado en este ejemplo aquí: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin).

En el firewall, deberán crearse reglas de reenvío. Como en este ejemplo solo se enruta el tráfico entrante de Internet al firewall y después al servidor web, únicamente se necesita un regla NAT de reenvío. En el firewall de Barracuda NextGen usado en este ejemplo, la regla sería una regla NAT de destino ("Dst NAT") para pasar este tráfico.

Para crear la siguiente regla (o comprobar las reglas predeterminadas existentes), a partir del panel de cliente de Barracuda NG Admin, vaya a la pestaña de configuración, en la sección de configuración funcional, y haga clic en el conjunto de reglas. Una cuadrícula denominada "Main rules" (Reglas principales) mostrará las reglas activas existentes y las desactivadas en el firewall. En la esquina superior derecha de esta cuadrícula hay un pequeño botón "+" de color verde; haga clic aquí para crear una nueva regla (Nota: el firewall puede estar "bloqueado" para los cambios; si ve un botón marcado con "Lock" (Bloquear) y no puede crear o editar reglas, haga clic en este botón para "desbloquear" el conjunto de reglas y permitir la modificación). Si quiere modificar una regla existente, seleccione esa regla, haga clic con el botón derecho y seleccione Edit Rule (Editar regla).

Cree una nueva regla y asígnele un nombre, como "WebTraffic".

El icono de regla de NAT de destino tiene este aspecto: ![Icono de NAT de destino][2]

La regla debe tener un aspecto similar al siguiente:

![Regla de firewall][3]

Aquí cualquier dirección entrante que alcanza el firewall que intenta llegar a HTTP (puerto 80 o 443 para HTTPS) se enviará fuera de interfaz de "DHCP1 Local IP" del firewall y se redirigirá al servidor web con la dirección IP de 10.0.1.5. Puesto que el tráfico entra en el puerto 80 y se dirige al servidor web en el puerto 80, no se necesita ningún cambio de puerto. Sin embargo, la lista de destino podría haber sido 10.0.1.5:8080 si nuestro servidor web escucha en el puerto 8080, convirtiendo, por tanto, el puerto de entrada 80 del firewall en el puerto 8080 de entrada del servidor web.

También se debe indicar un método de conexión, para el que regla de destino desde Internet, "Dynamic SNAT", sea la más adecuada.

Aunque se creó una regla, es importante que su prioridad se establezca correctamente. Si esta nueva regla se encuentra en la parte inferior de la cuadrícula de todas las reglas en el firewall (debajo de la regla "BLOCKALL"), nunca se activará. Asegúrese de que la regla recién creada para el tráfico web está encima de la regla BLOCKALL.

Una vez que se crea la regla, debe insertarse en el firewall y, después, activarse, pues en caso contrario el cambio de regla no tendrá efecto. En la siguiente sección se describe el proceso de activación y de inserción.

## Activación de reglas
Con el conjunto de reglas modificado para agregar esta regla, el conjunto de reglas debe cargarse en el firewall y activarse.

![Activación de reglas de firewall][4]

En la esquina superior derecha del cliente de administración hay un grupo de botones. Haga clic en el botón "Send Changes" (Enviar cambios) para enviar las reglas modificadas al firewall y, después, haga clic en el botón "Activate" (Activar).

Con la activación del conjunto de reglas de firewall finaliza la compilación del entorno de ejemplo. Opcionalmente, se pueden ejecutar los scripts posteriores a la compilación en la sección de referencias para agregar una aplicación a este entorno para probar los escenarios de tráfico siguientes.

>[AZURE.IMPORTANT] Es esencial tener en cuenta que no alcanzará el servidor web directamente. Cuando un explorador solicita una página HTTP desde FrontEnd001.CloudApp.Net, el extremo HTTP (puerto 80) traslada este tráfico al firewall, no al servidor web. El firewall, según la regla creada anteriormente, envía varios NAT que solicitan al servidor web.

## Escenarios de tráfico

#### (Permitido) Servidor web a web a través de firewall
1.	El usuario de Internet solicita la página HTTP desde FrontEnd001.CloudApp.Net (servicio en la nube accesible desde Internet).
2.	El servicio en la nube pasa el tráfico por un extremo abierto en el puerto 80 hacia la interfaz local del firewall en 10.0.1.4:80.
3.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3.	Se aplica la regla 3 (Internet a firewall) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4.	El tráfico llega a dirección IP interna del firewall (10.0.1.4).
5.	La regla de reenvío de firewall que ve esto es el tráfico del puerto 80, lo redirige al servidor web IIS01.
6.	IIS01 escucha el tráfico web, recibe esta solicitud y comienza a procesarla.
7.	IIS01 pide información al servidor SQL Server en AppVM01
8.	No hay reglas de salida en la subred front-end, se permite el tráfico.
9.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3.	No se aplica la regla 3 (Internet a firewall) de grupo de seguridad de red, pasar a la regla siguiente.
  4.	No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
10.	AppVM01 recibe la consulta SQL y responde.
11.	Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
12.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2.	La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
13.	El servidor IIS recibe la respuesta SQL y completa la respuesta HTTP y la envía al solicitante.
14.	Como es una sesión NAT del firewall, el destino de respuesta (inicialmente) es para el firewall.
15.	El firewall recibe la respuesta del servidor web y lo reenvía al usuario de Internet.
16.	Puesto que no hay reglas de salida en la subred front-end, se permite la respuesta y el usuario de Internet recibe la página web solicitada.

#### (Permitido) RDP a back-end
1.	El administrador del servidor en Internet solicita la sesión RDP para AppVM01 en BackEnd001.CloudApp.Net:xxxxx donde xxxxx es el número de puerto asignado de forma aleatoria para RDP a AppVM01 (el puerto asignado puede encontrarse en el Portal de Azure o a través de PowerShell).
2.	Como el firewall solo está escuchando en la dirección FrontEnd001.CloudApp.Net, no participa en este flujo de tráfico.
3.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Se aplica la regla 2 (RDP) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4.	No hay reglas de salida, se aplican las reglas predeterminadas y se permite el tráfico de retorno.
5.	La sesión RDP está habilitada.
6.	AppVM01 solicita la contraseña del nombre de usuario.

#### (Permitido) Búsqueda de DNS del servidor web en el servidor DNS
1.	El servidor web, IIS01, necesita una fuente de datos en www.data.gov, pero debe resolver la dirección.
2.	La configuración de red de la red virtual incluye DNS01 (10.0.2.4 en la subred back-end) como servidor DNS principal, IIS01 envía la solicitud DNS a DNS01.
3.	No hay reglas de salida en la subred front-end, se permite el tráfico.
4.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	 Se aplica la regla 1 (DNS) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
5.	El servidor DNS recibe la solicitud.
6.	El servidor DNS no tiene la dirección en caché y solicita un servidor DNS raíz en Internet.
7.	No hay reglas de salida en la subred back-end, se permite el tráfico.
8.	El servidor DNS de Internet responde, puesto que esta sesión se inició internamente, se permite la respuesta.
9.	El servidor DNS almacena en caché la respuesta y responde a la solicitud inicial a IIS01.
10.	No hay reglas de salida en la subred back-end, se permite el tráfico.
11.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2.	La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
12.	IIS01 recibe la respuesta de DNS01.

#### (Permitido) Archivo de acceso del servidor web en AppVM01
1.	IIS01 solicita un archivo en AppVM01
2.	No hay reglas de salida en la subred front-end, se permite el tráfico.
3.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3.	No se aplica la regla 3 (Internet a firewall) de grupo de seguridad de red, pasar a la regla siguiente.
  4.	No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4.	AppVM01 recibe la solicitud y responde con el archivo (suponiendo que se autoriza el acceso).
5.	Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
6.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2.	La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
7.	El servidor IIS recibe el archivo.

#### (Denegado) Web directo al servidor web
Dado que el servidor web, IIS01 y el firewall se encuentran en el mismo servicio en la nube, comparten la misma dirección IP pública. Por lo tanto todo el tráfico HTTP se dirigirá al firewall. Aunque la solicitud se atendió correctamente, no puede ir directamente al servidor web y, pasa primero, como está previsto, a través del firewall. Consulte el primer escenario de esta sección para ver el flujo de tráfico.

#### (Denegado) Web a servidor back-end
1.	El usuario de Internet intenta acceder a un archivo en AppVM01 a través del servicio BackEnd001.CloudApp.Net.
2.	Puesto que no hay ningún extremo abierto para el uso compartido de archivos, no pasaría por el servicio en la nube y no llegaría al servidor.
3.	Si hubiera extremos abiertos por alguna razón, la regla 5 (Internet a la red virtual) de grupo de seguridad de red bloquearía este tráfico.

#### (Denegado) Búsqueda de DNS web en el servidor DNS
1.	El usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio BackEnd001.CloudApp.Net.
2.	Puesto que no hay ningún extremo abierto para DNS, no pasaría por el servicio en la nube y no llegaría al servidor.
3.	Si hubiera extremos abiertos por alguna razón, la regla 5 (Internet a red virtual) de grupo de seguridad de red bloquearía este tráfico. (Nota: esa regla 1 (DNS) no se aplicaría por dos motivos; en primer lugar, la dirección de origen es Internet, esta regla solo se aplica a la red virtual local como origen. También se trata una regla de permiso, por lo que nunca denegaría tráfico).

#### (Denegado) Web a acceso SQL a través de firewall
1.	El usuario de Internet solicita los datos SQL desde FrontEnd001.CloudApp.Net (servicio en la nube accesible desde Internet).
2.	Puesto que no hay ningún extremo abierto para SQL, no pasaría por el servicio en la nube y no llegaría al firewall.
3.	Si hubiera extremos abiertos por alguna razón, la subred front-end comenzaría el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
  3.	Se aplica la regla 2 (Internet a firewall) del grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4.	El tráfico llega a dirección IP interna del firewall (10.0.1.4).
5.	El firewall no tiene ninguna regla de reenvío para SQL y descarta el tráfico.

## Conclusión
Se trata de una manera relativamente sencilla de proteger la aplicación con un firewall y de aislar la subred back-end del tráfico entrante.

Puede encontrar más ejemplos y una descripción general de los límites de seguridad de red [aquí][HOME].

## Referencias
### Script principal y configuración de red
Guarde el script completo en un archivo de script de PowerShell. Guarde la configuración de red en un archivo llamado "NetworkConf2.xml". Modifique las variables definidas por el usuario que sean necesarias. Ejecute el script y siga las instrucciones de configuración de reglas de firewall anteriores.

#### Script completo
En función de las variables definidas por el usuario, este script realizará las siguientes acciones:

1.	Conexión a una suscripción de Azure
2.	Creación de una cuenta de almacenamiento nueva
3.	Creación de una red virtual nueva y dos subredes, como se define en el archivo de configuración de red
4.	Creación de cuatro máquinas virtuales de servidor Windows
5.	Configuración del grupo de seguridad de red, incluido lo siguiente:
  -	Creación de un grupo de seguridad de red
  -	Rellenado con reglas
  -	Enlace del grupo de seguridad de red a las subredes adecuadas

Este script de PowerShell debe ejecutarse localmente en un equipo o servidor conectado a Internet.

>[AZURE.IMPORTANT] Cuando se ejecuta este script, puede haber advertencias u otros mensajes informativos que se muestran en PowerShell. Solo los mensajes de error indicados en rojo son motivo de preocupación.


	<# 
	 .SYNOPSIS
	  Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Two new cloud services
	   - Two Subnets (FrontEnd and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
	   - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
	   - Three Servers on the BackEnd Subnet
	   - Network Security Groups to allow/deny traffic patterns as declared
	  
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Security requirements are different for each use case and can be addressed in a
	  myriad of ways. Please be sure that any sensitive data or applications are behind
	  the appropriate layer(s) of protection. This script serves as an example of some
	  of the techniques that can be used, but should not be used for all scenarios. You
	  are responsible to assess your security needs and the appropriate protections
	  needed, and then effectively implement those protections.
	
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   myFirewall - 10.0.1.4
	   IIS01      - 10.0.1.5
	 
	  BackEnd Service (BackEnd subnet 10.0.2.0/24)
	   DNS01      - 10.0.2.4
	   AppVM01    - 10.0.2.5
	   AppVM02    - 10.0.2.6
	
	#>
	
	# Fixed Variables
	    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
	    $VMName = @()
	    $ServiceName = @()
	    $VMFamily = @()
	    $img = @()
	    $size = @()
	    $SubnetName = @()
	    $VMIP = @()
	
	# User Defined Global Variables
	  # These should be changes to reflect your subscription and services
	  # Invalid options will fail in the validation section
	
	  # Subscription Access Details
	    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	
	  # VM Account, Location, and Storage Details
	    $LocalAdmin = "theAdmin"
	    $DeploymentLocation = "Central US"
	    $StorageAccountName = "vmstore02"
	
	  # Service Details
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure proper NSG Rule creation later in this script:
	    #       - The Web Server must be VM 1
	    #       - The AppVM1 Server must be VM 2
	    #       - The DNS server must be VM 4
	    #
	    #       Otherwise the NSG rules in the last section of this
	    #       script will need to be changed to match the modified
	    #       VM array numbers ($i) so the NSG Rule IP addresses
	    #       are aligned to the associated VM IP addresses.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.5"
	
	    # VM 2 - The First Appliaction Server
	      $VMName += "AppVM01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.5"
	
	    # VM 3 - The Second Appliaction Server
	      $VMName += "AppVM02"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.6"
	
	    # VM 4 - The DNS Server
	      $VMName += "DNS01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.4"
	
	# ----------------------------- #
	# No User Defined Varibles or   #
	# Configuration past this point #
	# ----------------------------- #
	
	  # Get your Azure accounts
	    Add-AzureAccount
	    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
	    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
	
	  # Create Storage Account
	    If (Test-AzureName -Storage -Name $StorageAccountName) { 
	        Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
	        Return}
	    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
	          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
	
	  # Update Subscription Pointer to New Storage Account
	    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
	    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
	
	# Validation
	$FatalError = $false
	
	If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
	     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
	     $FatalError = $true}
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $BackEndService) { 
	    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
	
	If (-Not (Test-Path $NetworkConfigFile)) { 
	    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The network config file was found" -ForegroundColor Green
	        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
	            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
	            $FatalError = $true}
	        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
	
	If ($FatalError) {
	    Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
	    Return}
	Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
	
	# Create VNET
	    Write-Host "Creating VNET" -ForegroundColor Cyan 
	    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
	
	# Create Services
	    Write-Host "Creating Services" -ForegroundColor Cyan
	    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
	
	# Build VMs
	    $i=0
	    $VMName | Foreach {
	        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
	        If ($VMFamily[$i] -eq "Firewall") 
	            { 
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Set up all the EndPoints we'll need once we're up and running
	            # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
	            #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
	            #       forwarding rule, so the HTTP endpoint here will have the same public and local
	            #       port and the firewall will do the NATing and redirection as declared in the
	            #       firewall rule.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
	            }
	        $i++
	    }
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	    
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rules
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
	        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
	        -SourceAddressPrefix Internet -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
	        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
	        -Protocol *
	    
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
	        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
	        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
	        -Protocol *
	
	    # Assign the NSG to the Subnets
	        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
	
	# Optional Post-script Manual Configuration
	  # Configure Firewall
	  # Install Test Web App (Run Post-Build Script on the IIS Server)
	  # Install Backend resource (Run Post-Build Script on the AppVM01)
	  Write-Host
	  Write-Host "Build Complete!" -ForegroundColor Green
	  Write-Host
	  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
	  Write-Host " - Configure Firewall" -ForegroundColor Gray
	  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
	  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
	  Write-Host


#### Archivo de configuración de red
Guarde este archivo xml con la ubicación actualizada y agregue el vínculo a este archivo a la variable $NetworkConfigFile en el script anterior.
	
	<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
	  <VirtualNetworkConfiguration>
	    <Dns>
	      <DnsServers>
	        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
	        <DnsServer name="Level3" IPAddress="209.244.0.3" />
	      </DnsServers>
	    </Dns>
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
	        <AddressSpace>
	          <AddressPrefix>10.0.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="FrontEnd">
	            <AddressPrefix>10.0.1.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="BackEnd">
	            <AddressPrefix>10.0.2.0/24</AddressPrefix>
	          </Subnet>
	        </Subnets>
	        <DnsServersRef>
	          <DnsServerRef name="DNS01" />
	          <DnsServerRef name="Level3" />
	        </DnsServersRef>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>
	  </VirtualNetworkConfiguration>
	</NetworkConfiguration>

#### Scripts de aplicación de ejemplo
Si desea instalar una aplicación de ejemplo para este y otros ejemplos de red perimetral, hay una en el siguiente vínculo: [Script de aplicación de ejemplo][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Red perimetral de entrada con grupo de seguridad de red"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icono de NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regla de firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activación de reglas de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

<!---HONumber=AcomDC_0204_2016-->