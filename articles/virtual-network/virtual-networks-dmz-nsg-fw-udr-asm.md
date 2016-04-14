<properties
   pageTitle="Ejemplo de red perimetral: Creación de una red perimetral para proteger redes con un firewall, enrutamiento definido por el usuario y un grupo de seguridad de red | Microsoft Azure"
   description="Creación de una red perimetral con un firewall, enrutamiento definido por el usuario (UDR) y grupos de seguridad de red (NSG)"
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

# Ejemplo 3: Creación de una red perimetral para proteger las redes con un firewall, enrutamiento definido por el usuario y grupo de seguridad de red

[Volver a la página de procedimientos recomendados de límites de seguridad][HOME]

En este ejemplo se creará una red perimetral con un firewall, cuatro servidores Windows, enrutamiento definido por el usuario y grupos de seguridad de red. También le guiará por cada uno de los comandos pertinentes para que comprenda mejor cada paso. Hay también una sección Escenario de tráfico para proporcionar información detallada paso a paso de cómo pasa el tráfico a través de los niveles de defensa de la red perimetral. Por último, en la sección de referencias está el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios.

![Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario][1]

## Configuración del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Tres servicios en la nube: “SecSvc001”, “FrontEnd001” y “BackEnd001”
- Una red virtual, “CorpNetwork”, con tres subredes, “SecNet”, “FrontEnd” y “BackEnd”
- Un dispositivo virtual de red, en este ejemplo, un firewall, conectado a la subred SecNet
- Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores Windows que representan servidores back-end de aplicaciones (“AppVM01”, “AppVM02”)
- Un servidor Windows que representa un servidor DNS ("DNS01")

En la sección de referencias siguiente hay un script de PowerShell que compilará la mayor parte del entorno descrito anteriormente. Aunque la creación de las máquinas virtuales y las redes virtuales la realiza el script de ejemplo, no se describe en detalle en este documento.

Para crear el entorno:

  1.	Guarde el archivo xml de configuración de red incluido en la sección de referencias (actualizado con nombres, ubicación y direcciones IP para que coincidan con el escenario dado).
  2.	Actualice las variables de usuario en el script para que coincida con el entorno en el que se va a ejecutar el script (suscripciones, nombres de servicio, etc.).
  3.	Ejecución del script en PowerShell

**Nota**: la región indicada en el script de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.

Cuando el script se ejecuta correctamente, se pueden realizar los siguientes pasos posteriores al script:

1.	Configure las reglas de firewall; este tema se trata en la sección Descripción de las reglas de firewall.
2.	Opcionalmente, en la sección de referencias hay dos scripts para configurar el servidor web y el servidor de aplicaciones con una aplicación web sencilla para permitir las pruebas con esta configuración de red perimetral.

Una vez que el script ejecuta correctamente el firewall, se deben completar las reglas; este tema se trata en la sección Reglas de firewall.

## Enrutamiento definido por el usuario (UDR)
De forma predeterminada, las siguientes rutas de sistema se definen como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal es siempre el prefijo de dirección definido de la red virtual para esa red específica (es decir, cambiará de una red virtual a otra según cómo se defina cada red virtual específica). Las rutas de sistema restantes son estáticas y tienen el valor predeterminado indicado anteriormente.

En cuanto a la prioridad, las rutas se procesan según el método de coincidencia de prefijo más largo (LPM); por lo tanto, se aplicaría la ruta más específica de la tabla a una dirección de destino determinada.

Por lo tanto, el tráfico (por ejemplo, al servidor DNS01 10.0.2.4) dirigido a la red local (10.0.0.0/16) se enrutaría a su destino a través de la red virtual debido a la ruta 10.0.0.0/16. En otras palabras, para 10.0.2.4, la ruta 10.0.0.0/16 es la más específica, incluso aunque se pudieran aplicar también 10.0.0.0/8 y 0.0.0.0/0, pero como son menos específicas, no afectan a este tráfico. Por lo tanto, el tráfico hacia 10.0.2.4 tendría un salto siguiente de la red virtual local y simplemente se enrutaría al destino.

Si el tráfico tuviera 10.1.1.1 como destino, por ejemplo, no se aplicaría la ruta 10.0.0.0/16, pero 10.0.0.0/8 sería la más específica y el tráfico se descartaría (se enviaría a un "agujero negro"), ya que el siguiente salto es Null.

Si el destino no era aplicable a ninguno de los prefijos Null o los prefijos VNETLocal, seguiría la ruta menos específica 0.0.0.0/0, y se enrutaría fuera de Internet como el próximo salto y, por lo tanto, fuera del perímetro de Internet de Azure.

Si hay dos prefijos idénticos en la tabla de enrutamiento, el orden de preferencia en función del atributo "source" de las rutas es el siguiente:

1.	<blank> = una ruta definida por el usuario agregada manualmente a la tabla.
2.	"VPNGateway" = una ruta dinámica (BGP cuando se usa con redes híbridas), agregada por un protocolo de red dinámico; estas rutas pueden cambiar con el tiempo a medida que el protocolo dinámico refleja automáticamente los cambios de red del mismo nivel.
3.	"Default" = las rutas del sistema, la red virtual local y las entradas estáticas, tal y como se muestra en la tabla de enrutamiento anterior.

>[AZURE.NOTE] Hay una limitación en el uso del enrutamiento definido por el usuario (UDR) y ExpressRoute debido a la complejidad del enrutamiento dinámico que se usa en la puerta de enlace virtual de Azure. Las subredes que se comunican con la puerta de enlace de Azure y que proporcionan la conexión de ExpressRoute no deben tener aplicado enrutamiento definido por el usuario. Además, la puerta de enlace de Azure no puede ser dispositivo NextHop para otras subredes enlazadas con enrutamiento definido por el usuario. La capacidad de integrar totalmente el enrutamiento definido por el usuario y ExpressRoute se habilitará en una futura versión de Azure.

#### Creación de las rutas locales

En este ejemplo, se necesitan dos tablas de enrutamiento para las subredes front-end y back-end. Cada tabla se carga con rutas estáticas adecuadas para la subred indicada. En este ejemplo, cada tabla tiene tres rutas:

1. Tráfico de subred local sin próximo salto definido para que el tráfico de la subred local pase por alto el firewall.
2. Tráfico de red virtual con un próximo salto definido como firewall; esto invalida la regla predeterminada que permite que el tráfico de la red virtual local se enrute directamente.
3. Todo el tráfico restante (0/0) con un próximo salto definido como firewall.

Una vez creadas las tablas de enrutamiento se enlazan a sus subredes. Una vez creada la tabla de enrutamiento de la subred front-end y enlazada a la subred, debe tener el siguiente aspecto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


En este ejemplo, se usan los comandos siguientes para crear la tabla de enrutamiento, se agrega una ruta definida por el usuario y, a continuación, se enlaza la tabla de enrutamiento a una subred. (Nota: los elementos siguientes que comienzan con un signo de dólar (p. ej.: $BESubnet) son variables definidas por el usuario del script en la sección de referencias de este documento):

1.	Primero se debe crear la tabla de enrutamiento base. Este fragmento de código muestra la creación de la tabla para la subred back-end. En el script, también se crea la tabla correspondiente para la subred front-end.

		New-AzureRouteTable -Name $BERouteTableName `
		    -Location $DeploymentLocation `
		    -Label "Route table for $BESubnet subnet"

2.	Una vez creada la tabla de enrutamiento, se pueden agregar rutas específicas definidas por el usuario. En este fragmento, todo el tráfico (0.0.0.0/0) se enrutarán a través del dispositivo virtual (se usa una variable, $VMIP [0], para pasar la dirección IP asignada cuando se creó el dispositivo virtual anteriormente en el script). En el script, también se crea la tabla correspondiente en la tabla front-end.

		Get-AzureRouteTable $BERouteTableName |`
		    Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
		    -NextHopType VirtualAppliance `
		    -NextHopIpAddress $VMIP[0]

3. La entrada de la ruta anterior invalidará la ruta predeterminada "0.0.0.0/0", pero la regla predeterminada 10.0.0.0/16 aún existe y permitiría que el tráfico dentro de la red virtual se enrute directamente al destino y no al dispositivo virtual de red. Para corregir este comportamiento, se debe agregar la siguiente regla.

	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]

4. En este momento hay que elegir una opción. Con las dos rutas anteriores, todo el tráfico se enrutará al firewall para su evaluación, incluso el tráfico dentro de una sola subred. Esto puede ser deseable. Sin embargo, para permitir que el tráfico dentro de una subred se enrute localmente sin la participación del firewall, se puede agregar una tercera regla muy específica. Esta ruta establece que las direcciones con destino a la subred local se pueden enrutar a ella directamente (NextHopType = VNETLocal).

	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal

5.	Por último, una vez creada la tabla de enrutamiento y rellenada con las rutas definidas por el usuario, la tabla se debe enlazar una subred. En el script, la tabla de enrutamiento front-end también se enlaza a la subred front-end. Este es el script de enlace para la subred back-end.

		Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
		   -SubnetName $BESubnet `
		   -RouteTableName $BERouteTableName

## Reenvío IP
Una característica complementaria del enrutamiento definido por el usuario es el reenvío IP. Es una opción de configuración de los dispositivos virtuales que les permite recibir tráfico no dirigido específicamente al dispositivo y, después, reenviar ese tráfico a su destino final.

Por ejemplo, si el tráfico de AppVM01 realiza una solicitud al servidor DNS01, el enrutamiento definido por el usuario los enrutará al firewall. Con el reenvío IP habilitado, el dispositivo (10.0.0.4) aceptará el tráfico dirigido a DNS01 (10.0.2.4) y, a continuación, se reenviará a su destino final (10.0.2.4). Sin el reenvío IP habilitado en el firewall, el dispositivo no aceptará el tráfico aunque la tabla de enrutamiento tenga el firewall como próximo salto.

>[AZURE.IMPORTANT] Es fundamental acordarse de habilitar el reenvío IP junto con el enrutamiento definido por el usuario.

Para configurar el reenvío IP se usa un solo comando y puede realizarse durante la creación de la máquina virtual. En el flujo de este ejemplo, el fragmento de código está hacia el final del script y se agrupa con los comandos de enrutamiento definido por el usuario:

1.	Llame a la instancia de máquina virtual que es el dispositivo virtual, el firewall en este caso, y habilite el reenvío IP. (Nota: todos los elementos en rojo que comienzan con un signo de dólar (p. ej.: $VMName[0]) son variables definidas por el usuario del script en la sección de referencias de este documento. El cero entre corchetes, [0], representa la primera máquina virtual de la matriz de máquinas virtuales. Para que el script funcione sin modificaciones, la primera máquina virtual (VM 0) debe ser el firewall):

		Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
		   |Set-AzureIPForwarding -Enable

## Grupos de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se carga después una única regla. Este grupo se enlaza solo a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

1.	Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (todas las subredes)

Aunque en este ejemplo se usan grupos de seguridad de red, su principal objetivo es constituir un segundo nivel de defensa frente a errores de configuración manual. Deseamos bloquear todo el tráfico entrante de Internet a las subredes front-end o back-end, el tráfico solo debe fluir a través de la subred SecNet al firewall (y, si es necesario, a las subredes front-end o back-end). Además, con las reglas de enrutamiento definido por el usuario vigentes, el tráfico que llegue a las subredes front-end o back-end se redirigirá al firewall (gracias al enrutamiento definido por el usuario). El firewall lo consideraría un flujo asimétrico y descartaría el tráfico saliente. Por lo tanto, hay tres niveles de seguridad que protegen las subredes front-end y back-end: (1) ningún extremo abierto en los servicios en la nube FrontEnd001 y BackEnd001; (2) los grupos de seguridad de red deniegan el tráfico de Internet; (3) el firewall descarta el tráfico asimétrico.

Un punto interesante sobre el grupo de seguridad de red de este ejemplo es que contiene solo una regla, que se muestra a continuación, que consiste en denegar el tráfico de Internet a toda la red virtual, lo que incluiría la subred de seguridad.

	Get-AzureNetworkSecurityGroup -Name $NSGName | `
	    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
	    from the Internet" `
	    -Type Inbound -Priority 100 -Action Deny `
	    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	    -DestinationAddressPrefix VIRTUAL_NETWORK `
	    -DestinationPortRange '*' `
	    -Protocol *

Sin embargo, como el grupo de seguridad de red solo está enlazado a las subredes front-end y back-end, la regla no se procesa en el tráfico entrante a la subred de seguridad. Como resultado, aunque la regla de grupo de seguridad de red deniegue el tráfico de Internet a cualquier dirección de la red virtual, como el grupo de seguridad de red nunca estuvo enlazado a la subred de seguridad, el tráfico se dirigirá a la subred de seguridad.

	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $FESubnet -VirtualNetworkName $VNetName
	
	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $BESubnet -VirtualNetworkName $VNetName

## Reglas de firewall
En el firewall, deberán crearse reglas de reenvío. Dado que el firewall bloquea o reenvía todo el tráfico entrante, saliente y entre redes virtuales, se necesitan muchas reglas de firewall. Además, todo el tráfico entrante llegará a la dirección IP pública del servicio de seguridad (en puertos diferentes) para ser procesado por el firewall. El procedimiento recomendado es crear un diagrama de los flujos lógicos antes de configurar las subredes y las reglas de firewall para evitar tener que modificar más adelante. La siguiente ilustración es una vista lógica de las reglas de firewall para este ejemplo:
 
![Vista lógica de las reglas de firewall][2]

>[AZURE.NOTE] Según el dispositivo virtual de red usado, los puertos de administración varían. En este ejemplo se hace referencia a un firewall Barracuda NextGen que utiliza los puertos 22, 801 y 807. Consulte la documentación del proveedor del dispositivo para buscar los puertos exactos usados para la administración del dispositivo que se va a usar.

### Descripción de las reglas de firewall
En el diagrama lógico anterior, no se muestra la subred de seguridad debido a que el firewall es el único recurso de la subred y este diagrama muestra las reglas de firewall y cómo permiten o deniegan lógicamente los flujos de tráfico, no la ruta enrutada real. Además, los puertos externos seleccionados para el tráfico RDP están en un intervalo más alto (8014 – 8026) y se han seleccionado para que se correspondan en cierto modo con los dos últimos octetos de la dirección IP local y facilitar así su legibilidad (por ejemplo, la dirección de servidor local 10.0.1.4 está asociada al puerto externo 8014); sin embargo, podría usarse cualquier puerto superior que no planteara conflictos.

En este ejemplo, necesitamos siete tipos de reglas que se describen a continuación:

- Reglas externas (para el tráfico entrante):
  1.	Regla de administración de firewall: esta regla de redirección de aplicación permite que el tráfico pase a los puertos de administración del dispositivo virtual de red.
  2.	Reglas de RDP (para cada servidor Windows): estas cuatro reglas (una para cada servidor) permitirán la administración de los servidores individuales a través de RDP. También se puede agrupar en una regla según las capacidades del dispositivo virtual de red que se use.
  3.	Reglas de tráfico de aplicación: hay dos reglas de tráfico de aplicación, la primera para el tráfico web front-end y la segunda para el tráfico back-end (p. ej. servidor web a capa de datos). La configuración de estas reglas dependerá de la arquitectura de red (donde están situados los servidores) y los flujos de tráfico (en qué dirección fluye el tráfico y qué puertos se usan).
      - La primera regla permitirá que el tráfico de aplicación real llegue al servidor de aplicaciones. Mientras que las demás reglas permiten seguridad, administración, etc., las reglas de aplicación son las que permiten a los usuarios o servicios externos obtener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80, por lo tanto, una única regla de aplicación de firewall redirigirá el tráfico entrante a la dirección IP externa y a la dirección IP interna de los servidores web. La dirección de la sesión de tráfico redirigido se traduciría al servidor interno.
      - La segunda regla de tráfico de aplicación es la regla back-end que permite que el servidor web se comunique con el servidor AppVM01 (pero no AppVM02) a través de cualquier puerto.
- Reglas internas (para el tráfico entre redes virtuales)
  4.	Regla de saliente a Internet: esta regla permitirá que el tráfico de cualquier red pase a las redes seleccionadas. Normalmente, esta es una regla predeterminada que ya existe en el firewall, pero en estado deshabilitado. Esta regla debe habilitarse para este ejemplo.
  5.	Regla DNS: esta regla permite que pase solo el tráfico DNS (puerto 53) al servidor DNS. En este entorno, se bloquea la mayor parte del tráfico desde la subred front-end a la subred back-end. Esta regla permite específicamente DNS desde cualquier subred local.
  6.	Regla de subred a subred: esta regla permite que cualquier servidor en la subred back-end conecte con cualquier servidor en la subred front-end (pero no a la inversa).
- Regla para notificaciones de error (para el tráfico que no cumple ninguna de las anteriores):
  7.	Denegar todas las reglas de tráfico: esta debe ser siempre la última regla (en términos de prioridad) y, como tal, si un flujo de tráfico no coincide con ninguna de las reglas anteriores, esta regla lo descartará. Esta es una regla predeterminada y normalmente está activada; normalmente no se necesitan modificaciones.

>[AZURE.TIP] En la segunda regla de tráfico de aplicación, se permite cualquier puerto para facilitar el ejemplo; en un escenario real, se usarían el puerto más específico e intervalos de direcciones para reducir la superficie de ataque de esta regla.

<br />

>[AZURE.IMPORTANT] Una vez creadas todas las reglas anteriores, es importante revisar la prioridad de cada una para asegurarse de que el tráfico se permitirá o se denegará según se desee. En este ejemplo, las reglas están en orden de prioridad. Es fácil quedar bloqueado fuera del firewall si las reglas están mal ordenadas. Como mínimo, asegúrese de que la administración del firewall siempre es la regla de máxima prioridad.

### Requisitos previos de las reglas
Un requisito previo para la máquina virtual que ejecuta el firewall son extremos públicos. Para que el firewall pueda procesar el tráfico, deben estar abiertos los extremos públicos adecuados. Hay tres tipos de tráfico en este ejemplo: 1) tráfico de administración para controlar el firewall y las reglas de firewall; 2) tráfico RDP para controlar los servidores Windows; y 3) tráfico de aplicación. Estas son las tres columnas de tipos de tráfico en la mitad superior de la vista lógica de las reglas de firewall anteriores.

>[AZURE.IMPORTANT] Es importante recordar que **todo** el tráfico vendrá a través del firewall. Por lo tanto, para conectarse mediante Escritorio remoto al servidor IIS01, aunque esté en el servicio front-end en la nube y en la subred front-end, será necesario enviar RDP al firewall en el puerto 8014 y, a continuación, permitir que el firewall enrute la solicitud RDP internamente al puerto RDP de IIS01. El botón “Conectar” del Portal de Azure no funcionará porque no hay una ruta RDP directa a IIS01 (no una que el portal pueda ver). Esto significa que todas las conexiones desde Internet serán al servicio de seguridad y a un puerto, por ejemplo, secscv001.cloudapp.net:xxxx (consulte el diagrama anterior para ver la correspondencia entre los puertos externos y las direcciones IP y puertos internos).

Un extremo puede abrirse durante la creación de la máquina virtual o después de la compilación, tal y como se hace en el fragmento de código. (Nota: todos los elementos que comienzan con un signo de dólar (p. ej.: $VMName[$i]) son variables definidas por el usuario del script en la sección de referencias de este documento. La variable “$i” entre corchetes, [$i] representa el número de matriz de una máquina virtual específica en una matriz de máquinas virtuales):

	Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
	    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
	    Update-AzureVM

Aunque no se muestra aquí claramente debido al uso de variables, los extremos **solo** se abren en el servicio de seguridad en la nube. Esto es para asegurarse de que el firewall controla todo el tráfico entrante (se enruta, sus direcciones se traducen o se descarta).

Deberá instalarse un cliente de administración en el equipo para administrar el firewall y crear las configuraciones necesarias. Consulte la documentación del proveedor de su firewall (o de otro dispositivo virtual de red) acerca de cómo administrar el dispositivo. El resto de esta sección y la siguiente, Creación de reglas de firewall, describe la configuración del firewall mediante el cliente de administración de proveedores (es decir, no el Portal de Azure ni PowerShell).

Puede encontrar instrucciones para descargar el cliente y conectarse al firewall Barracuda usado en este ejemplo aquí: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin).

Una vez iniciada sesión en el firewall pero antes de crear las reglas de firewall, hay dos clases de objeto que son requisitos previos y que pueden facilitar la creación de las reglas, los objetos de red y de servicio.

En este ejemplo, se deben definir tres objetos de red con nombre (uno para la subred front-end, uno para subred back-end y otro para la dirección IP del servidor DNS). Para crear una red con nombre, en el panel del cliente de Barracuda NG Admin, vaya a la pestaña de configuración; en la sección Operational Configuration (Configuración funcional), haga clic en Ruleset (Conjunto de reglas), haga clic en “Networks” (Redes) en el menú Firewall Objects (Objetos de firewall) y haga clic en New (Nuevo) en el menú Edit Networks (Editar redes). Ya puede crearse el objeto de red agregando el nombre y el prefijo:

![Creación de un objeto de red front-end][3]
 
Esto creará una red con nombre para la subred front-end. Se debe crear un objeto similar también para la subred back-end. Ahora se puede hacer referencia a las subredes más fácilmente por nombre en las reglas de firewall.

Para el objeto de servidor DNS:

![Creación de un objeto de servidor DNS][4]

Esta referencia a la dirección IP única se usará en una regla DNS más adelante en el documento.

Los segundos objetos que son requisitos previos son los objetos de servicio. Representan los puertos de conexión RDP para cada servidor. Como el objeto de servicio RDP existente está enlazado al puerto RDP predeterminado, 3389, se pueden crear nuevos objetos de servicio para permitir el tráfico desde los puertos externos (8014-8026). Los nuevos puertos también podrían agregarse al servicio RDP existente pero, para facilitar la demostración, se puede crear una regla individual para cada servidor. Para crear una nueva regla RDP para un servidor, en el panel del cliente de Barracuda NG Admin, vaya a la pestaña de configuración; en la sección Operational Configuration (Configuración funcional), haga clic en Ruleset (Conjunto de reglas), haga clic en “Services” (Servicios) en el menú Firewall Objects (Objetos de firewall), desplácese por la lista de servicios y seleccione el servicio “RDP”. Haga con el botón derecho y seleccione Copiar; después, haga clic con el botón derecho y seleccione Pegar. Ahora hay un objeto de servicio RDP-Copy1 que se puede editar. Haga clic con el botón derecho en RDP-Copy1 y seleccione Edit (Editar); se abrirá la ventana Edit Service Object (Editar objeto de servicio) que muestra aquí:

![Copia de la regla predeterminada de RDP][5]

Los valores pueden modificarse para representar el servicio RDP para un servidor específico. Para AppVM01, la regla RDP predeterminada anterior debe modificarse para que refleje el nuevo nombre de servicio, la descripción y el puerto RDP externo usado en el diagrama de la ilustración 8. (Nota: los puertos se cambian del puerto 3389 predeterminado de RDP al puerto externo que se usa para este servidor específico; en el caso de AppVM01, el puerto externo es 8025). El servicio modificado se muestra a continuación :

![Regla de AppVM01][6]
 
Este proceso debe repetirse para crear servicios RDP para los demás servidores: AppVM02, DNS01 e IIS01. La creación de estos servicios hará que la creación de reglas sea más sencilla y más obvia en la sección siguiente.

>[AZURE.NOTE] No se necesita un servicio RDP para el firewall por dos razones: (1) la máquina virtual del firewall es una imagen basada en Linux por lo que se usaría SSH en el puerto 22 para la administración de máquinas virtuales en lugar de RDP; y (2) en la primera regla de administración que se describe más adelante se permiten el puerto 22 y otros dos puertos de administración para permitir la conectividad de administración.

### Creación de reglas de firewall
En este ejemplo se usan tres tipos de reglas de firewall, todas ellas con iconos distintos:

La regla Redirección de aplicación: ![Icono de redirección de aplicación][7]

La regla NAT de destino: ![Icono de NAT de destino][8]

La regla Paso: ![Icono de Paso][9]

Encontrará más información sobre estas reglas en el sitio web de Barracuda.

Para crear las siguientes reglas (o comprobar las reglas predeterminadas existentes), en el panel de cliente de Barracuda NG Admin, vaya a la pestaña de configuración y, en la sección Operational Configuration (Configuración funcional), haga clic en Ruleset (Conjunto de reglas). Una cuadrícula denominada "Main rules" (Reglas principales) mostrará las reglas activas e inactivas existentes en el firewall. En la esquina superior derecha de esta cuadrícula hay un pequeño botón "+" de color verde; haga clic aquí para crear una nueva regla. (Nota: el firewall puede estar "bloqueado" para los cambios; si ve un botón marcado con "Lock" (Bloquear) y no puede crear o editar reglas, haga clic en este botón para "desbloquear" el conjunto de reglas y permitir la modificación). Si quiere modificar una regla existente, seleccione esa regla, haga clic con el botón derecho y seleccione Edit Rule (Editar regla).

Una vez creadas o modificadas las reglas, deben insertarse en el firewall y, después, activarse; en caso contrario, los cambios en las reglas no tendrán efecto. El proceso de activación e inserción se describe después de las descripciones detalladas de las regla.

A continuación se describen los detalles de cada regla necesarios para completar este ejemplo:

- **Regla de administración de firewall**: esta regla de redirección de aplicación permite que el tráfico pase a los puertos de administración del dispositivo virtual de red, Barracuda NextGen Firewall en este ejemplo. Los puertos de administración son 801, 807 y, opcionalmente, 22. Los puertos internos y externos son los mismos (es decir, sin traducción de puertos). SETUP-MGMT-ACCESS es una regla predeterminada y está habilitada de forma predeterminada (en Barracuda NextGen Firewall versión 6.1).

	![Regla de administración del firewall][10]

>[AZURE.TIP] El espacio de direcciones de origen de esta regla es Any (cualquiera). Si se conocen los intervalos de direcciones IP de administración, reducir este ámbito también reduciría la superficie de ataque a los puertos de administración.

- **Reglas RDP**: estas reglas NAT de destino permitirán la administración de los servidores individuales mediante RDP. Hay cuatro campos críticos necesarios para crear esta regla:
  1.	Origen: para permitir RDP desde cualquier lugar, se usa la referencia "Any" (cualquiera) en el campo de origen.
  2.	Servicio: use el objeto de servicio adecuado creado anteriormente, en este caso, "AppVM01 RDP". Los puertos externos se redirigen a la dirección IP local de los servidores y al puerto 3386 (puerto RDP predeterminado). Esta regla concreta es para el acceso de RDP a AppVM01.
  3.	Destino: debe ser el *puerto local del firewall*, "DCHP 1 Local IP" o eth0 si usa direcciones IP estáticas. El número ordinal (eth0, eth1, etc.) puede ser diferente si el dispositivo de red tiene varias interfaces locales. Este es el puerto que el firewall usa para enviar (puede ser el mismo que el puerto de recepción). El destino enrutado real está en el campo de lista de destinos.
  4.	Redirección: esta sección indica al dispositivo virtual dónde debe redirigir este tráfico en última instancia. La redirección más sencilla es colocar la dirección IP y el puerto (opcional) en el campo de lista de destinos. Si no se usa ningún puerto en el puerto de destino de la solicitud entrante (es decir, no se realiza ninguna traducción), si se designa un puerto se realizará también su traducción junto con la dirección IP.

	![Regla de RDP de firewall][11]

    Deberán crearse un total de cuatro reglas de RDP:

    | Nombre de la regla | Server | Servicio | Lista de destinos |
    |----------------|---------|-------------|---------------|
    | RDP a IIS01 | IIS01 | IIS01 RDP | 10\.0.1.4:3389 |
    | RDP a DNS01 | DNS01 | DNS01 RDP | 10\.0.2.4:3389 |
    | RDP a AppVM01 | AppVM01 | AppVM01 RDP | 10\.0.2.5:3389 |
    | RDP a AppVM02 | AppVM02 | AppVm02 RDP | 10\.0.2.6:3389 |
  
>[AZURE.TIP] Limitar el ámbito de los campos Origen y Servicio reducirá la superficie de ataque. Se debe usar el ámbito más limitado que permita la funcionalidad.

- **Reglas de tráfico de aplicación**: hay dos reglas de tráfico de aplicación, la primera para el tráfico web front-end y la segunda para el tráfico back-end (p. ej. servidor web a capa de datos). Estas reglas dependerán de la arquitectura de red (donde están situados los servidores) y los flujos de tráfico (en qué dirección fluye el tráfico y qué puertos se usan).

	Primero se describe la regla front-end para el tráfico web:

	![Regla web de firewall][12]
 
	Esta regla NAT de destino permite que el tráfico de aplicación real llegue al servidor de aplicaciones. Mientras que las demás reglas permiten seguridad, administración, etc., las reglas de aplicación son las que permiten a los usuarios o servicios externos obtener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80, por lo tanto, una única regla de aplicación de firewall redirigirá el tráfico entrante a la dirección IP externa y a la dirección IP interna de los servidores web.

	**Nota**: no hay ningún puerto asignado en el campo de lista de destinos, por lo que se usará el puerto 80 (o 443 para el servicio seleccionado) de entrada en la redirección del servidor web. Si el servidor web está escuchando en un puerto diferente, por ejemplo, el puerto 8080, se podría actualizar el campo de lista de destinos a 10.0.1.4:8080 para permitir también la redirección de puertos.

	La siguiente regla de tráfico de aplicación es la regla back-end que permite que el servidor web se comunique con el servidor AppVM01 (pero no AppVM02) mediante cualquier servicio.

	![Regla de AppVM01 de firewall][13]

	Esta regla de paso permite que cualquier servidor IIS en la subred front-end llegue a AppVM01 (dirección IP 10.0.2.5) en cualquier puerto, usando cualquier protocolo para acceder a los datos que la aplicación web necesita.

	En esta captura de pantalla, se usa "<explicit-dest>" se usa en el campo de destino para indicar 10.0.2.5 como destino. Podría ser explícito, tal y como se muestra, o un objeto de red con nombre (como se hizo en los requisitos previos del servidor DNS). El método que se use es decisión del administrador del firewall. Para agregar 10.0.2.5 como destino explícito, haga doble clic en la primera fila vacía debajo de <explicit-dest> y escriba la dirección en la ventana que aparece.

    Con esta regla de paso, no se necesita traducción NAT porque se trata de tráfico interno, por lo que el método de conexión puede establecerse en "No SNAT".

	**Nota**: en esta regla, la red de origen es cualquier recurso de la subred front-end, si solo habrá una, o un número específico conocido de servidores web. Se podría crear un recurso de objeto de red para especificar las direcciones IP exactas en lugar de toda la subred front-end.

>[AZURE.TIP] Esta regla usa el servicio "Any" para facilitar la configuración y el uso de la aplicación de ejemplo. Esto también permitirá ICMPv4 (ping) en una sola regla. Sin embargo, este no es el procedimiento recomendado. Los puertos y protocolos ("servicios") se deben reducir al mínimo posible que permita el funcionamiento de la aplicación para reducir la superficie de ataque.

<br />

>[AZURE.TIP] Aunque esta regla muestra que se está usando una referencia de destino explícito, debe usarse un enfoque coherente en toda la configuración del firewall. Se recomienda usar siempre el objeto de red con nombre para facilitar la legibilidad y la compatibilidad. El destino explícito se usa aquí solo para mostrar un método de referencia alternativo y, por lo general, no se recomienda (especialmente para configuraciones complejas).

- **Regla de saliente a Internet**: esta regla de paso permitirá que el tráfico de cualquier red de origen pase a las redes de destino seleccionadas. Normalmente, esta es una regla predeterminada que ya existe en el firewall Barracuda NextGen, pero en estado deshabilitado. Haga clic el botón derecho de esta regla para acceder al comando Activate Rule (Activar regla). La regla que se muestra se ha modificado para agregar las dos subredes locales que se crearon como referencias en la sección de requisitos previos de este documento al atributo Source de esta regla.

	![Regla de salida de firewall][14]

- **Regla DNS**: esta regla de paso permite que pase solo el tráfico DNS (puerto 53) al servidor DNS. En este entorno, se bloquea la mayor parte del tráfico desde la subred front-end a la subred back-end. Esta regla permite específicamente DNS.

	![Regla de DNS de firewall][15]

	**Nota**: en esta captura de pantalla se incluye el método Connection. Dado que esta regla es para el tráfico de dirección IP interna a dirección IP interna, no se necesita traducción. El método Connection se establece en "No SNAT" para esta regla de paso.

- **Regla de subred a subred**: esta es una regla de paso predeterminada que se ha activado y modificado para que permita que cualquier servidor en la subred back-end conecte con cualquier servidor en la subred front-end. Esta regla es toda para tráfico interno por lo que el método Connection se puede establecer en No SNAT.

	![Regla entre redes virtuales de firewall][16]

	**Nota**: la casilla Bi-directional (Bidireccional) no está activada (ni lo está en la mayoría de las reglas). Esto es significativo para esta regla porque hace que sea una regla unidireccional, es decir, se puede iniciar una conexión de la subred back-end a la red front-end, pero no a la inversa. Si esa casilla se activa, esta regla permitiría el tráfico bidireccional lo que, desde la perspectiva de nuestro diagrama lógico, no es deseable.

- **Denegar todas las reglas de tráfico**: esta debe ser siempre la última regla (en términos de prioridad) y, como tal, si un flujo de tráfico no coincide con ninguna de las reglas anteriores, esta regla lo descartará. Esta es una regla predeterminada y normalmente está activada; normalmente no se necesitan modificaciones.

	![Regla de denegación de firewall][17]

>[AZURE.IMPORTANT] Una vez creadas todas las reglas anteriores, es importante revisar la prioridad de cada una para asegurarse de que el tráfico se permitirá o se denegará según se desee. En este ejemplo, las reglas están en el orden con que deben aparecer en la cuadrícula principal de reglas de reenvío en el cliente de administración de Barracuda.

## Activación de reglas
Con el conjunto de reglas modificado según la especificación del diagrama lógico, el conjunto de reglas se debe cargar al firewall y después activarse.

![Activación de reglas de firewall][18]
 
En la esquina superior derecha del cliente de administración hay un grupo de botones. Haga clic en el botón "Send Changes" (Enviar cambios) para enviar las reglas modificadas al firewall y, después, haga clic en el botón "Activate" (Activar).
 
Con la activación del conjunto de reglas de firewall finaliza la compilación del entorno de ejemplo.

## Escenarios de tráfico
>[AZURE.IMPORTANT] Es importante recordar que **todo** el tráfico vendrá a través del firewall. Por lo tanto, para conectarse mediante Escritorio remoto al servidor IIS01, aunque esté en el servicio front-end en la nube y en la subred front-end, será necesario enviar RDP al firewall en el puerto 8014 y, a continuación, permitir que el firewall enrute la solicitud RDP internamente al puerto RDP de IIS01. El botón “Conectar” del Portal de Azure no funcionará porque no hay una ruta RDP directa a IIS01 (no una que el portal pueda ver). Esto significa que todas las conexiones desde Internet serán el servicio de seguridad y un puerto, por ejemplo, secscv001.cloudapp.net:xxxx.

En estos escenarios, las siguientes reglas de firewall deben estar en su lugar:

1.	Administración del firewall
2.	RDP a IIS01
3.	RDP a DNS01
4.	RDP a AppVM01
5.	RDP a AppVM02
6.	Tráfico de aplicación a web
7.	Tráfico de aplicación a AppVM01
8.	Saliente a Internet.
9.	Front-end a DNS01
10.	Tráfico entre subredes (solo back-end a front-end)
11.	Denegar todo

El conjunto de reglas de firewall actuales probablemente tendrá muchas otras reglas además de estas. Las reglas en cualquier firewall determinado también tendrán números de prioridad diferentes de los mencionados aquí. Esta lista y números asociados se usan para proporcionar la relevancia entre estas once reglas y la prioridad relativa entre ellas. En otras palabras, en el firewall real, la regla "RDP a IIS01" podría ser la número 5, pero si está por debajo de la regla de "Administración de firewall" y por encima de la regla "RDP a DNS01" estaría en consonancia con la intención de esta lista. La lista también será de ayuda en los escenarios siguientes porque permite brevedad; por ejemplo, “Regla de reenvío 9 (DNS)”. También por motivos de brevedad, las cuatro reglas RDP se llaman en conjunto "reglas RDP" cuando el escenario de tráfico no está relacionado con RDP.

Recuerde también que los grupos de seguridad de red están vigentes para el tráfico entrante de Internet en las subredes front-end y back-end.

#### (Permitido) Internet a servidor web
1.	El usuario de Internet solicita la página HTTP desde SecSvc001.CloudApp.Net (servicio en la nube accesible desde Internet).
2.	El servicio en la nube pasa el tráfico por un extremo abierto en el puerto 80 hacia la interfaz del firewall en 10.0.0.4:80.
3.	Ningún grupo de seguridad de red asignado a la subred de seguridad, las reglas de grupo de seguridad de red del sistema permiten el tráfico al firewall.
4.	El tráfico llega a dirección IP interna del firewall (10.0.1.4).
5.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplica la regla de reenvío 6 (aplicación: web), se permite el tráfico, traducción NAT del firewall a 10.0.1.4 (IIS01).
6.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red (el firewall no aplicó traducción NAT a este tráfico y, por lo tanto, la dirección de origen ahora es el firewall que se encuentra en la subred de seguridad. El grupo de seguridad de red de la subred front-end lo considera tráfico "local" y, por tanto, lo permite); pasar a la siguiente regla.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
7.	IIS01 escucha el tráfico web, recibe esta solicitud y comienza a procesarla.
8.	IIS01 intenta inicia una sesión FTP a AppVM01 en la subred back-end.
9.	La ruta UDR en la subred front-end hace que el firewall sea el próximo salto.
10.	No hay reglas de salida en la subred front-end, se permite el tráfico.
11.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplica la regla de reenvío 6 (reglas de RDP), pasar a la regla siguiente.
  4.	Se aplica la regla de reenvío 7 (aplicación: back-end), se permite el tráfico, el firewall reenvía el tráfico a 10.0.2.5 (AppVM01).
12.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
13.	 AppVM01 recibe la solicitud, inicia la sesión y responde.
14.	La ruta UDR en la subred back-end hace que el firewall sea el próximo salto.
15.	Como no hay ninguna regla de grupos de seguridad de red de salida en la subred back-end, se permite la respuesta.
16.	Como esto devuelve el tráfico en una sesión establecida, el firewall devuelve la respuesta al servidor web (IIS01).
17.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
18.	El servidor IIS recibe la respuesta, completa la transacción con AppVM01 y, a continuación, completa la compilación de la respuesta HTTP; esta respuesta HTTP se envía al solicitante.
19.	Como no hay ninguna regla de grupos de seguridad de red de salida en la subred front-end, se permite la respuesta.
20.	La respuesta HTTP llega al firewall y, como es la respuesta a una sesión NAT establecida, es aceptada por el firewall.
21.	El firewall redirige la respuesta de nuevo al usuario de Internet.
22.	Puesto que no hay reglas de grupos de seguridad de red de salida ni saltos de enrutamiento definido por el usuario en la subred front-end, se permite la respuesta y el usuario de Internet recibe la página web solicitada.

#### (Permitido) RDP de Internet a back-end
1.	El administrador del servidor en Internet solicita una sesión RDP para AppVM01 mediante SecSvc001.CloudApp.Net:8025, donde 8025 es el número de puerto asignado por el usuario para la regla de firewall "RDP a AppVM01".
2.	El servicio en la nube pasa el tráfico por un extremo abierto en el puerto 8025 hacia la interfaz del firewall en 10.0.0.4:8025.
3.	Ningún grupo de seguridad de red asignado a la subred de seguridad, las reglas de grupo de seguridad de red del sistema permiten el tráfico al firewall.
4.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplica la regla de reenvío 2 (RDP IIS), pasar a la regla siguiente.
  3.	No se aplica la regla de reenvío 3 (RDP DNS01), pasar a la regla siguiente.
  4.	Se aplica la regla de reenvío 4 (RDP AppVM01), se permite el tráfico, el firewall lo traduce a 10.0.2.5:3386 (puerto RDP en AppVM01)
5.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red (el firewall no aplicó traducción NAT a este tráfico y, por lo tanto, la dirección de origen ahora es el firewall que se encuentra en la subred de seguridad. El grupo de seguridad de red de la subred back-end lo considera tráfico "local" y, por tanto, lo permite); pasar a la siguiente regla.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
6.	AppVM01 escucha el tráfico RDP y responde.
7.	No hay reglas de grupo de seguridad de red de salida, se aplican las reglas predeterminadas y se permite el tráfico de retorno.
8.	El enrutamiento definido por el usuario enruta el tráfico saliente al firewall como próximo salto
9.	Como esto devuelve el tráfico en una sesión establecida, el firewall devuelve la respuesta al usuario de Internet.
10.	La sesión RDP está habilitada.
11.	AppVM01 solicita la contraseña del nombre de usuario.

#### (Permitido) Búsqueda de DNS del servidor web en el servidor DNS
1.	El servidor web, IIS01, necesita una fuente de datos en www.data.gov, pero debe resolver la dirección.
2.	La configuración de red de la red virtual incluye DNS01 (10.0.2.4 en la subred back-end) como servidor DNS principal, IIS01 envía la solicitud DNS a DNS01.
3.	El enrutamiento definido por el usuario enruta el tráfico saliente al firewall como próximo salto
4.	No hay reglas de grupo de seguridad de red de salida enlazadas a la subred front-end, se permite el tráfico.
5.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplican las reglas de reenvío 6 y 7 (reglas de aplicación), pasar a la regla siguiente.
  4.	No se aplica la regla de reenvío 8 (a Internet), pasar a la regla siguiente.
  5.	Se aplica la regla de reenvío 9, se permite el tráfico, el firewall reenvía el tráfico a 10.0.2.4 (DNS01).
6.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
7.	El servidor DNS recibe la solicitud.
8.	El servidor DNS no tiene la dirección en caché y solicita un servidor DNS raíz en Internet.
9.	El enrutamiento definido por el usuario enruta el tráfico saliente al firewall como próximo salto
10.	No hay reglas de grupo de seguridad de red de salida en la subred back-end, se permite el tráfico.
11.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplican las reglas de reenvío 6 y 7 (reglas de aplicación), pasar a la regla siguiente.
  4.	 Se aplica la regla de reenvío 8 (a Internet), se permite el tráfico, se traducen las direcciones de la sesión al servidor DNS raíz en Internet.
12.	El servidor DNS de Internet responde; puesto que esta sesión se inició desde el firewall, este acepta la respuesta.
13.	Como se trata de una sesión establecida, el firewall reenvía la respuesta al servidor de inicio, DNS01.
14.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
15.	El servidor DNS recibe y almacena en caché la respuesta y responde a la solicitud inicial a IIS01.
16.	La ruta UDR en la subred back-end hace que el firewall sea el próximo salto.
17.	No hay reglas de grupo de seguridad de red de salida en la subred back-end, se permite el tráfico.
18.	Se trata de una sesión establecida en el firewall, el firewall reenvía la respuesta al servidor IIS.
19.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
  2.	La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
20.	IIS01 recibe la respuesta de DNS01.

#### (Denegado) Servidor back-end a servidor front-end
1.	Un administrador cono sesión iniciada en AppVM02 mediante RDP solicita un archivo directamente al servidor IIS01 mediante el Explorador de Windows.
2.	La ruta UDR en la subred back-end hace que el firewall sea el próximo salto.
3.	Como no hay ninguna regla de grupos de seguridad de red de salida en la subred back-end, se permite la respuesta.
4.	El firewall comienza el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplican las reglas de reenvío 6 y 7 (reglas de aplicación), pasar a la regla siguiente.
  4.	No se aplica la regla de reenvío 8 (a Internet), pasar a la regla siguiente.
  5.	No se aplica la regla de reenvío 9 (DNS), pasar a la regla siguiente.
  6.	Se aplica la regla de reenvío 10 (entre subredes), se permite el tráfico, el firewall pasa el tráfico a 10.0.1.4 (IIS01)
5.	La subred front-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
6.	Si la autorización y la autenticación son correctas, IIS01 acepta la solicitud y responde.
7.	La ruta UDR en la subred front-end hace que el firewall sea el próximo salto.
8.	Como no hay ninguna regla de grupos de seguridad de red de salida en la subred front-end, se permite la respuesta.
9.	Como se trata de una sesión existente en el firewall,, se permite esta respuesta y el firewall devuelve la respuesta al AppVM02.
10.	La subred back-end comienza el procesamiento de las reglas de entrada:
  1.	No se aplica la regla 1 (bloquear Internet) de grupo de seguridad de red, pasar a la regla siguiente.
  2.	Las reglas de grupo de seguridad de red predeterminadas permiten el tráfico de subred, se permite el tráfico, detener el procesamiento de las reglas de grupo de seguridad de red.
11.	AppVM02 recibe la respuesta.

#### (Denegado) Internet directo a servidor web
1.	El usuario de Internet intenta acceder al servidor web IIS01 mediante el servicio FrontEnd001.CloudApp.Net.
2.	Puesto que no hay ningún extremo abierto para el tráfico HTTP, no pasaría por el servicio en la nube y no llegaría al servidor.
3.	Si hubiera extremos abiertos por alguna razón, el grupo de seguridad de red (bloquear Internet) en la subred front-end bloquearía este tráfico.
4.	Por último, la ruta UDR de la subred front-end enviaría el tráfico saliente desde IIS01 al firewall como próximo salto. El firewall consideraría este tráfico asimétrico y descartaría la respuesta saliente. Por lo tanto, hay al menos tres capas independientes de defensa entre Internet e IIS01 gracias al servicio en la nube que impide el acceso no autorizado o inadecuado.

#### (Denegado) Internet a servidor back-end
1.	El usuario de Internet intenta acceder a un archivo en AppVM01 a través del servicio BackEnd001.CloudApp.Net.
2.	Puesto que no hay ningún extremo abierto para el uso compartido de archivos, no pasaría por el servicio en la nube y no llegaría al servidor.
3.	Si hubiera extremos abiertos por alguna razón, el grupo de seguridad de red (bloquear Internet) bloquearía este tráfico.
4.	Por último, la ruta UDR enviaría el tráfico saliente desde AppVM01 al firewall como próximo salto. El firewall consideraría este tráfico asimétrico y descartaría la respuesta saliente. Por lo tanto, hay al menos tres capas independientes de defensa entre Internet y AppVM01 gracias al servicio en la nube que impide el acceso no autorizado o inadecuado.

#### (Denegado) Servidor front-end a servidor back-end
1.	Suponga que IIS01 está en riesgo y está ejecutado código malintencionado que intenta analizar los servidores de la subred back-end.
2.	La ruta UDR de la subred front-end enviaría el tráfico saliente de IIS01 al firewall como el próximo salto. Esto no es algo que la máquina virtual en riesgo pueda modificar.
3.	El firewall procesaría el tráfico, si la solicitud era para AppVM01, o al servidor DNS para las búsquedas de DNS; el firewall podría permitir ese tráfico (debido a las reglas de reenvío 7 y 9). La regla de reenvío 11 bloquearía todo el tráfico restante (denegar todo).
4.	Si se habilitó la detección avanzada de amenazas en el firewall (este aspecto no se describe en este documento; consulte la documentación del proveedor de su dispositivo de red para ver las funcionalidades avanzadas para amenazas), podría impedirse incluso el tráfico permitido por las reglas de reenvío básicas descritas en este documento si el tráfico contiene signaturas o patrones que activen una regla de amenaza avanzada.

#### (Denegado) Búsqueda de DNS de Internet en el servidor DNS
1.	El usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio BackEnd001.CloudApp.Net. 
2.	Puesto que no hay ningún extremo abierto para el tráfico DNS, no pasaría por el servicio en la nube y no llegaría al servidor.
3.	Si hubiera extremos abiertos por alguna razón, la regla de grupo de seguridad de red (bloquear Internet) en la subred front-end bloquearía este tráfico.
4.	Por último, la ruta UDR de la subred back-end enviaría el tráfico saliente desde DNS01 al firewall como próximo salto. El firewall consideraría este tráfico asimétrico y descartaría la respuesta saliente. Por lo tanto, hay al menos tres capas independientes de defensa entre Internet e DNS01 gracias al servicio en la nube que impide el acceso no autorizado o inadecuado.

#### (Denegado) Internet a acceso SQL a través de firewall
1.	El usuario de Internet solicita datos SQL desde SecSvc001.CloudApp.Net (servicio en la nube accesible desde Internet).
2.	Puesto que no hay ningún extremo abierto para SQL, no pasaría por el servicio en la nube y no llegaría al firewall.
3.	Si hubiera extremos SQL abiertos por alguna razón, el firewall comenzaría el procesamiento de las reglas:
  1.	No se aplica la regla de reenvío 1 (administración de reenvío), pasar a la regla siguiente.
  2.	No se aplican las reglas de reenvío 2 a 5 (reglas de RDP), pasar a la regla siguiente.
  3.	No se aplican las reglas de reenvío 6 y 7 (reglas de aplicación), pasar a la regla siguiente.
  4.	No se aplica la regla de reenvío 8 (a Internet), pasar a la regla siguiente.
  5.	No se aplica la regla de reenvío 9 (DNS), pasar a la regla siguiente.
  6.	No se aplica la regla de reenvío 10 (entre subredes), pasar a la regla siguiente.
  7.	Se aplica la regla de reenvío 11 (denegar todo), se permite el tráfico, detener el procesamiento de las reglas.


## Referencias
### Script principal y configuración de red
Guarde el script completo en un archivo de script de PowerShell. Guarde la configuración de red en un archivo llamado "NetworkConf2.xml". Modifique las variables definidas por el usuario que sean necesarias. Ejecute el script y siga las instrucciones de configuración de reglas de firewall anteriores.

#### Script completo
En función de las variables definidas por el usuario, este script realizará las siguientes acciones:

1.	Conexión a una suscripción de Azure
2.	Creación de una cuenta de almacenamiento nueva
3.	Creación de una red virtual nueva y tres subredes, como se define en el archivo de configuración de red
4.	Creación de cinco máquinas virtuales, un firewall y cuatro máquinas virtuales de servidor Windows
5.	La configuración del enrutamiento definido por el usuario incluye:
  1.	Crear dos nuevas tablas de enrutamiento
  2.	Agregar rutas a las tablas
  3.	Enlazar las tablas a las subredes correspondientes
6.	Habilitar el reenvío IP en el dispositivo virtual de red
7.	Configuración del grupo de seguridad de red, incluido lo siguiente:
  1.	Creación de un grupo de seguridad de red
  2.	Adición de una regla
  3.	Enlace del grupo de seguridad de red a las subredes adecuadas

Este script de PowerShell debe ejecutarse localmente en un equipo o servidor conectado a Internet.

>[AZURE.IMPORTANT] Cuando se ejecuta este script, puede haber advertencias u otros mensajes informativos que se muestran en PowerShell. Solo los mensajes de error indicados en rojo son motivo de preocupación.

	<# 
	 .SYNOPSIS
	  Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Three new cloud services
	   - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
	   - One server on the FrontEnd Subnet
	   - Three Servers on the BackEnd Subnet
	   - IP Forwading from the FireWall out to the internet
	   - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
	
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Everyone's security requirements are different and can be addressed in a myriad of ways.
	  Please be sure that any sensitive data or applications are behind the appropriate
	  layer(s) of protection. This script serves as an example of some of the techniques
	  that can be used, but should not be used for all scenarios. You are responsible to
	  assess your security needs and the appropriate protections needed, and then effectively
	  implement those protections.
	
	  Security Service (SecNet subnet 10.0.0.0/24)
	   myFirewall - 10.0.0.4
	 
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   IIS01      - 10.0.1.4
	 
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
	    $SecureService = "SecSvc001"
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $VNetPrefix = "10.0.0.0/16"
	    $SecNet = "SecNet"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # UDR Details
	    $FERouteTableName = "FrontEndSubnetRouteTable"
	    $BERouteTableName = "BackEndSubnetRouteTable"
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure UDR and IP forwarding is setup
	    # properly this script requires VM 0 be the NVA.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $SecureService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $SecNet
	      $VMIP += "10.0.0.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
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
	
	If (Test-AzureName -Service -Name $SecureService) { 
	    Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
	
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
	    New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
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
	            # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
	            #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
	            #       rule, so all of these endpoint have the same public and local port and the firewall
	            #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "RemoteDesktop" | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            }
	        $i++
	    }
	
	# Configure UDR and IP Forwarding
	    Write-Host "Configuring UDR" -ForegroundColor Cyan
	
	  # Create the Route Tables
	    Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
	    New-AzureRouteTable -Name $BERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $BESubnet subnet"
	    New-AzureRouteTable -Name $FERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $FESubnet subnet"
	
	  # Add Routes to Route Tables
	    Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
	        -NextHopType VNETLocal
	
	  # Assoicate the Route Tables with the Subnets
	    Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $BESubnet `
	        -RouteTableName $BERouteTableName
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $FESubnet `
	        -RouteTableName $FERouteTableName
	
	 # Enable IP Forwarding on the Virtual Appliance
	    Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
	        |Set-AzureIPForwarding -Enable
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rule
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	  # Assign the NSG to two Subnets
	    # The NSG is *not* bound to the Security Subnet. The result
	    # is that internet traffic flows only to the Security subnet
	    # since the NSG bound to the Frontend and Backback subnets
	    # will Deny internet traffic to those subnets.
	    Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
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
	          <Subnet name="SecNet">
	            <AddressPrefix>10.0.0.0/24</AddressPrefix>
	          </Subnet>
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
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Red perimetral bidireccional con un dispositivo de red virtual, grupos de seguridad de red y enrutamiento definido por el usuario"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vista lógica de las reglas de firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Creación de un objeto de red front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Creación de un objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copia de la regla predeterminada de RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regla de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icono de redirección de aplicación"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icono de NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icono de Paso"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regla de administración del firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regla de RDP de firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regla web de firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regla de AppVM01 de firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regla de salida de firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regla de DNS de firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regla entre redes virtuales de firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regla de denegación de firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activación de reglas de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

<!---HONumber=AcomDC_0204_2016-->