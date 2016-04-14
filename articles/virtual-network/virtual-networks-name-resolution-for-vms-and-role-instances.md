<properties 
   pageTitle="Resolución de máquinas virtuales e instancias de rol"
   description="Escenarios de resolución de nombres para IaaS de Azure, soluciones híbridas, entre servicios en la nube diferentes, Active Directory y con su propio servidor DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="telmos" />

# Resolución de nombres para las máquinas virtuales e instancias de rol

Dependiendo de cómo utilice Azure para hospedar la IaaS, la PaaS y las soluciones híbridas, puede que necesite permitir que las máquinas virtuales y las instancias de rol creadas se comuniquen entre sí. Aunque este tipo de comunicación puede realizarse mediante las direcciones IP, es mucho más sencillo usar nombres ya que podrá recordarlos con más facilidad y no cambiarlos.

Cuando las instancias de rol y las máquinas virtuales hospedadas en Azure necesitan resolver nombres de dominio en direcciones IP internas, pueden usar uno de dos métodos:

- [Resolución de nombres de Azure](#azure-provided-name-resolution)

- [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) (que puede reenviar consultas a los servidores DNS proporcionados por Azure)

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol entre sí.

**La siguiente tabla muestra los escenarios y las soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Solución** | **Sufijo** |
|--------------|--------------|----------|
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en el mismo servicio en la nube o red virtual. | [Resolución de nombres de Azure](#azure-provided-name-resolution)| Nombre de host o FQDN |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en diferentes redes virtuales | Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).| Solo FQDN |
| Resolución de nombres de servicios y de equipos locales desde instancias de rol o máquinas virtuales en Azure | Servidores DNS administrados por el cliente (por ejemplo, controlador de dominio local, controlador de dominio de solo lectura local o un DNS secundario sincronizado usando transferencias de zona). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).|Solo FQDN |
| Resolución de nombres de host de Azure desde equipos locales | Reenvío de consultas a un servidor proxy DNS administrado por el cliente en la red virtual correspondiente: el servidor proxy reenvía consultas a Azure para su resolución. Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).| Solo FQDN |
| DNS inverso para direcciones IP internas | [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) | N/D |
| Resolución de nombres entre máquinas virtuales o instancias de rol ubicadas en diferentes servicios en la nube y no en una red virtual| No aplicable. La conectividad entre máquinas virtuales e instancias de rol de servicios en la nube diferentes no es compatible fuera de una red virtual.| N/D |



## Resolución de nombres de Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona una resolución de nombres interna para máquinas virtuales e instancias de rol que residen dentro de la misma red virtual o servicio en la nube. Las máquinas virtuales y las instancias de un servicio en la nube comparten el mismo sufijo DNS (por lo que es suficiente con el nombre de host solo) pero, en las redes virtuales clásicas, los distintos servicios en la nube tienen sufijos DNS diferentes, por lo que se necesita el FQDN para resolver nombres entre diferentes servicios en la nube. En las redes virtuales basadas en ARM, el sufijo DNS es coherente en toda la red virtual (por lo que no es necesario el FQDN), y los nombres DNS pueden asignarse a la NIC y a la máquina virtual. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

> [AZURE.NOTE] En el caso de los roles web y de trabajo, puede acceder a las direcciones IP internas de las instancias de rol que estén basadas en el nombre de rol y el número de instancia, mediante la API de REST de la Administración de servicios de Azure. Para obtener más información, consulte la [Referencia de la API de REST de la administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Características y consideraciones

**Características**

- Facilidad de uso: no es necesario realizar configuración alguna para usar la resolución de nombres proporcionada por Azure.

- El servicio de resolución de nombres de Azure es altamente disponible y evita la creación y administración de clústeres de sus propios servidores DNS.

- Se puede utilizar junto con sus propios servidores DNS para resolver nombres de host de Azure y locales.

- Las instancias de rol o las máquinas virtuales que están dentro del mismo servicio en la nube son las que proporcionan la resolución de nombres sin necesidad de un nombre de dominio completo.

- La resolución de nombres se proporciona siempre entre máquinas virtuales en redes virtuales basadas en ARM sin necesidad del nombre de dominio completo, las redes clásicas virtuales requieren el nombre de dominio completo al resolver los nombres de los distintos servicios en la nube.

- Puede usar los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

**Consideraciones:**

- No se puede modificar el sufijo DNS creado por Azure.

- No se pueden registrar manualmente los registros propios.

- No se admiten ni WINS ni NetBIOS. (Las máquinas virtuales no se pueden ver en el Explorador de Windows).

- Los nombres de host deben ser compatibles con el DNS (solamente pueden usar los caracteres 0-9, a-z y “-” y no pueden comenzar ni terminar con un “-”. Consulte la sección 2 de RFC 3696).

- El tráfico de consultas de DNS está limitado por cada máquina virtual. Esto no debería afectar a la mayoría de las aplicaciones. Si se observa una limitación de solicitudes, asegúrese de que está habilitado el almacenamiento en caché del lado cliente. Para obtener más información, consulte [Obtención del máximo partido de la resolución de nombres de Azure](#Getting-the-most-from-Azure-provided-name-resolution).

- Solo se registran las máquinas virtuales de los 180 primeros servicios en la nube para cada red virtual clásica. Esto no se aplica a las redes virtuales basadas en ARM.


### Obtención del máximo partido de la resolución de nombres de Azure
**Almacenamiento en caché del lado cliente:**

No todas las consultas DNS deben enviarse a través de la red. El almacenamiento en caché del lado cliente ayuda a reducir la latencia y mejorar la resistencia a la señalización visual de la red mediante la resolución de las consultas DNS periódicas desde una caché local. Los registros DNS contienen un período de vida (TTL) que permite a la memoria caché almacenar el registro tanto como sea posible sin afectar a la actualización de registros, por lo que el almacenamiento en caché de cliente es adecuado para la mayoría de las situaciones.

El cliente DNS de Windows predeterminado tiene una memoria caché DNS integrada. Algunas distribuciones Linux no incluyen el almacenamiento en caché de forma predeterminada; se recomienda que se agregue una a cada máquina virtual Linux (después de comprobar que aún no hay una memoria caché local).

Hay una serie de distintos paquetes de almacenamiento en caché DNS disponibles, p. ej., dnsmasq; estos son los pasos para instalar dnsmasq en las distribuciones más comunes:

- **Ubuntu (usa resolvconf)**:
	- instalar únicamente el paquete dnsmasq ("sudo apt-get install dnsmasq")
- **SUSE (usa netconf)**:
	- instalar el paquete dnsmasq ("sudo apt-get install dnsmasq") 
	- habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service") 
	- iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service") 
	- editar “/etc/sysconfig/network/config” y cambie NETCONFIG\_DNS\_FORWARDER="" por ”dnsmasq”
	- actualizar resolv.conf ("netconfig update") para establecer la memoria caché como el solucionador DNS local
- **OpenLogic (usa NetworkManager)**:
	- instalar el paquete dnsmasq ("sudo yum install dnsmasq")
	- habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service")
	- iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service")
	- agregar “prepend domain-name-servers 127.0.0.1;” en “/etc/dhclient-eth0.conf”
	- reiniciar el servicio de red ("service network restart") para establecer la memoria caché como el solucionador DNS local

> [AZURE.NOTE]\: El paquete 'dnsmasq' es solo una de las muchas cachés DNS disponibles para Linux. Antes de usarlo, compruebe su idoneidad para sus necesidades concretas y que no se instale ninguna otra memoria caché.

**Reintentos de cliente:**

DNS es principalmente un protocolo UDP. Como el protocolo UDP no garantiza la entrega de mensajes, la lógica de reintento se controla en el mismo protocolo DNS. Cada cliente DNS (sistema operativo) puede presentar una lógica de reintento diferente, dependiendo de la preferencia de los creadores:

 - Los sistemas operativos Windows realizan un intento tras un segundo y después tras otros 2, 4 y otros 4 segundos. 
 - El programa de instalación predeterminado de Linux lo intenta después de 5 segundos. Se recomienda cambiar esta opción para reintentarlo 5 veces a intervalos de 1 segundo.  

Para comprobar la configuración actual en una máquina virtual Linux, 'cat /etc/resolv.conf' y busque la línea 'options', p. ej.:

	options timeout:1 attempts:5

El archivo resolv.conf suele ser autogenerado y no se debe editar. Los pasos específicos para agregar la línea 'options' varían según la distribución:

- **Ubuntu** (usa resolvconf):
	- agregar la línea de opciones a '/ etc/resolveconf/resolv.conf.d/head' 
	- ejecutar 'resolvconf -u' para actualizar
- **SUSE** (usa netconf):
	- agregar 'timeout:1 attempts:5' al parámetro NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" en '/etc/sysconfig/network/config' 
	- ejecutar 'netconfig update' para actualizar
- **OpenLogic** (usa NetworkManager):
	- agregar 'echo "options timeout:1 attempts:5"' a '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- ejecutar 'service network restart' para actualizar

## Resolución de nombres mediante su propio servidor DNS
Hay una serie de situaciones donde sus necesidades de resolución de nombres pueden ir más allá de las características proporcionadas por Azure, por ejemplo, cuando se usan dominios de Active Directory o cuando se requiere la resolución de DNS entre redes virtuales. Para abarcar estos escenarios, Azure le ofrece la posibilidad de que use sus propios servidores DNS.

Los servidores DNS de una red virtual pueden reenviar consultas DNS a resoluciones recursivas de Azure para resolver los nombres de host en la red virtual. Por ejemplo, un controlador de dominio (DC) que se ejecute en Azure puede responder a las consultas DNS referidas a sus dominios y reenviar todas las demás consultas a Azure. Esto permite que las máquinas virtuales vean sus recursos locales (mediante el controlador de dominio) y los nombres de host proporcionados por Azure (mediante el reenviador). El acceso a las resoluciones recursivas de Azure se proporciona a través de la IP virtual 168.63.129.16.

El reenvío de DNS también habilita la resolución de DNS entre redes virtuales y permite a los equipos locales resolver nombres de host proporcionados por Azure. Para resolver el nombre de host de una máquina virtual, la máquina virtual del servidor DNS debe residir en la misma red virtual y debe configurarse para reenviar consultas de nombre de host a Azure. Como el sufijo DNS es diferente en cada red virtual, puede usar las reglas de reenvío condicional para enviar consultas DNS a la red virtual correcta para su resolución. La imagen siguiente muestra dos redes virtuales y una red local realizando la resolución DNS entre redes virtuales con este método:

![DNS entre redes virtuales](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Cuando se utiliza la resolución de nombres proporcionada por Azure, el sufijo DNS interno se proporciona a cada máquina virtual mediante DHCP. Cuando se utiliza su propia solución de resolución de nombres, no se proporciona este sufijo a las máquinas virtuales puesto que interfiere con otras arquitecturas DNS. Para hacer referencia a equipos mediante el FQDN (o para configurar el sufijo en las máquinas virtuales), el sufijo puede determinarse mediante PowerShell o la API:

-  En el caso de las redes virtuales administradas con ARM, el sufijo está disponible a través del recurso de la [tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) o a través del cmdlet [AzureRmNetworkInterface Get](https://msdn.microsoft.com/library/mt619434.aspx).    
-  En las implementaciones clásicas, el sufijo está disponible a través de la llamada a la API de [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) o a través del cmdlet [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).


Si el reenvío de consultas a Azure no satisface sus necesidades, debe proporcionar su propia solución de DNS. La solución de DNS deberá cumplir estos requisitos:

-  Proporcionar la resolución apropiada para el nombre de host, por ejemplo, a través de [DDNS](virtual-networks-name-resolution-ddns.md). Tenga en cuenta que si usa DDNS debe deshabilitar la limpieza de registros DNS, ya que las concesiones DHCP de Azure son muy largas y, al efectuar la limpieza, se pueden eliminar los registros DNS prematuramente. 
-  Proporcionar la resolución recursiva adecuada para permitir la resolución de nombres de dominio externos.
-  Estar accesible (TCP y UDP en el puerto 53) desde los clientes a los que sirve y poder acceder a Internet.
-  Tener protección contra el acceso desde Internet para mitigar las amenazas que suponen los agentes externos.

> [AZURE.NOTE] Para obtener un mejor rendimiento, cuando se usan máquinas virtuales de Azure como servidores DNS, se debe deshabilitar IPv6 y debe asignarse una [IP pública de nivel de instancia](virtual-networks-instance-level-public-ip.mp) a cada máquina virtual del servidor DNS. Si opta por usar Windows Server como el servidor DNS, [este artículo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) proporciona optimizaciones y análisis de rendimiento adicionales.



## Especificar los servidores DNS

Puede especificar varios servidores DNS para que los usen las máquinas virtuales y las instancias de rol. Por cada consulta de DNS el cliente intentará probar primero su servidor DNS preferido y solo probará otros servidores alternativos si su servidor preferido no responde, es decir, las consultas de DNS no tendrán la carga equilibrada en los diferentes servidores DNS. Por este motivo, compruebe que tiene los servidores DNS enumerados en el orden correcto para su entorno.

> [AZURE.NOTE] Si cambia la configuración DNS en un archivo de configuración de red de una red virtual que ya esté implementada, deberá reiniciar cada máquina virtual para que los cambios surtan efecto.

### Especificación de un servidor DNS en el Portal de administración

Al crear la red virtual mediante el Portal de administración, puede especificar la dirección IP y el nombre del servidor DNS (o servidores) que desea usar. Una vez creada la red virtual, las máquinas virtuales y las instancias de rol que implemente en la red virtual se configurarán automáticamente mediante la configuración DNS especificada. Los servidores DNS especificados para un servicio en la nube concreto (Azure clásico) o para una tarjeta de interfaz de red (implementaciones basadas en ARM) tienen prioridad sobre los especificados para la red virtual.

### Especificación de un servidor DNS mediante el uso de archivos de configuración (Azure clásico)

En las redes virtuales clásicas, puede especificar la configuración DNS mediante el uso de dos archivos de configuración diferentes: el archivo de *configuración de red* y el archivo de *configuración de servicio*.

El archivo de configuración de red describe las redes virtuales incluidas en su suscripción. Al agregar instancias de rol o máquinas virtuales a cualquier servicio en la nube en una red virtual, la configuración DNS de su archivo de configuración de red se aplica a la instancia de rol o a la máquina virtual, a menos que se especifiquen servidores DNS específicos del servicio en la nube.

El archivo de configuración de servicio se crea por cada servicio en la nube que agregue a Azure. Al agregar instancias de rol o máquinas virtuales en el servicio en la nube, la configuración DNS de su archivo de configuración de servicio se aplica a la instancia de rol o a la máquina virtual.

> [AZURE.NOTE] Los servidores DNS en el archivo de configuración de servicio sobrescribirán la configuración del archivo de configuración de red.


## Pasos siguientes

[Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Esquema de configuración de Red virtual](https://msdn.microsoft.com/library/azure/jj157100)

[Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0302_2016-->