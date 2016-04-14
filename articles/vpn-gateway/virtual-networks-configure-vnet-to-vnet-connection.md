<properties
   pageTitle="Configuración de una conexión de red virtual a red virtual | Microsoft Azure"
   description="Cómo conectar redes virtuales de Azure simultáneamente en las mismas o en diferentes suscripciones o regiones mediante PowerShell y el Portal de Azure clásico. Puede aplicar la información de este artículo para crear redes virtuales mediante el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/18/2015"
   ms.author="cherylmc"/>


# Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico

> [AZURE.SELECTOR]
- [Azure Classic Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


Este artículo le guiará por los pasos necesarios para crear y conectar redes virtuales mediante el modelo de implementación clásica (también conocido como administración de servicios). Si quiere conectar redes virtuales creadas con el modelo de implementación del Administrador de recursos, consulte  
[Configuración de una conexión de red virtual a red virtual para redes virtuales en la misma suscripción mediante el Administrador de recursos de Azure y PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Si quiere conectar una red virtual creada en el modelo de implementación clásica en una red virtual creada con el modelo del Administrador de recursos. Vea [Conexión de redes virtuales clásicas a nuevas redes virtuales](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Acerca de conexiones de red virtual a red virtual

La conexión de una red virtual a otra (de red virtual a red virtual) es muy parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar pueden estar en suscripciones y regiones distintas. Incluso es posible combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Diagrama de conectividad VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

### ¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

- **Presencia geográfica y redundancia geográfica entre regiones**
	- Puede configurar su propia replicación geográfica o la sincronización con conectividad segura sin recurrir a los extremos con conexión a Internet.
	- Con el equilibrador de carga de Azure y Microsoft, o con una tecnología de agrupación en clústeres de otros fabricantes, puede configurar cargas de trabajo de alta disponibilidad y redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar Always On de SQL con grupos de disponibilidad en varias regiones de Azure.

- **Aplicaciones regionales de varios niveles con límite de aislamiento sólido**
	- En la misma región se pueden configurar aplicaciones de varios niveles con múltiples redes virtuales conectadas entre sí, con un aislamiento sólido y una comunicación entre niveles segura.

- **Comunicación entre suscripciones y entre organizaciones en Azure**
	- Si tiene varias suscripciones a Azure, puede conectar cargas de trabajo de distintas suscripciones simultáneamente entre redes virtuales de forma segura.
	- Asimismo, tanto las empresas como los proveedores de servicios pueden habilitar la comunicación entre organizaciones con tecnología VPN segura en Azure.

### P+F sobre conexiones de red virtual a red virtual

- Las redes virtuales pueden estar en la misma suscripción o en suscripciones distintas.

- Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).

- Un servicio en la nube o un extremo de equilibrio de carga NO PUEDE abarcar varias redes virtuales aunque estas estén conectadas entre sí.

- La conexión simultánea de varias redes virtuales no requiere puertas de enlace de VPN locales, a menos que sea necesaria la conectividad entre entornos.

- VNet a VNet admite la conexión a redes virtuales de Azure. No admite la conexión de máquinas virtuales o servicios en la nube que NO estén en una red virtual.

- VNet a VNet necesita puertas de enlace VPN de Azure con VPN de enrutamiento dinámico. No se admiten puertas de enlace VPN de enrutamiento estático de Azure.

- La conectividad de red virtual se puede usar de forma simultánea con VPN de varios sitios, con un máximo de 10 túneles de VPN para una puerta de enlace de VPN de red virtual conectada a otras redes virtuales o sitios locales.

- Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear redes virtuales o al cargar archivos de configuración netcfg.

- No se admiten los túneles redundantes entre un par de redes virtuales.

- Todos los túneles de VPN de la red virtual, incluidas las VPN de punto a sitio (P2S) comparten el ancho de banda disponible en la puerta de enlace de VPN de Azure y en el mismo SLA de tiempo de actividad de puerta de enlace de VPN en Azure.

- El tráfico VNet a VNet viaja a través de la red troncal de Azure.

## Configuración de una conexión de red virtual a red virtual

En este procedimiento, le guiaremos en la conexión de dos redes virtuales: VNet1 y VNet2. Tendrá que familiarizarse con la conexión de redes para sustituir los intervalos de direcciones IP compatibles con sus requisitos de diseño de red. En una red virtual de Azure, conectarse a otra red virtual de Azure es igual que conectarse a una red local con una VPN de sitio a sitio (S2S).

Este procedimiento usa principalmente el Portal de Azure clásico, pero hay que usar cmdlets de Microsoft Azure PowerShell para conectar las puertas de enlace de VPN.

![Conexión VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)


## Paso 1: Planear los intervalos de direcciones IP

Es importante decidir los intervalos que usará para configurar el archivo de configuración de red (netcfg). Desde la perspectiva de VNet1, VNet2 solo es otra conexión VPN definida en la plataforma de Azure. Y desde la perspectiva de VNet2, VNet1 solo es otra conexión VPN. Las dos se identificarán mutuamente como sitio de red local. Tenga en cuenta que hay que asegurarse de que ninguno de los intervalos de VNet o intervalos de red local se solapen.

En la Tabla 1 se muestra un ejemplo de cómo definir las redes virtuales. Use los intervalos siguientes solo como directriz. Escriba los intervalos que usará para las redes virtuales. Necesitará esta información en pasos posteriores.

**Tabla 1**

|Red virtual |Definición de sitio de red virtual |Definición de sitio de red local|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |VNet2 (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |VNet1 (10.1.0.0/16) |

## Paso 2: Crear redes virtuales

A los efectos de este tutorial, se crearan dos redes virtuales: VNet1 y VNet2. Al crear las redes virtuales, sustituya los valores propios correspondientes. Para este tutorial, se usaran los siguientes valores para las redes virtuales:

VNet1: Espacio de direcciones = 10.1.0.0/16; Región=Oeste de EE. UU.

VNet2: Espacio de direcciones = 10.2.0.0/16; Región=Este de Japón

1. Inicie sesión en el **Portal de Azure clásico** (no en el Portal de Azure).

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

En la página **Detalles de la red virtual** escriba la siguiente información.

  ![Detalles de red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nombre**: nombre de la red virtual. Por ejemplo, VNet1.
  - **Ubicación**: al crear una red virtual, la debe asociar a una ubicación de Azure (región). Por ejemplo, Por ejemplo, si desea que las máquinas virtuales que implemente en la red virtual se encuentren físicamente en Oeste de EE.UU., seleccione esa ubicación. No se puede cambiar la ubicación asociada a la red virtual después de crearla.


En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y luego haga clic en la flecha siguiente situada en la parte inferior derecha.

  ![Servidores DNS y conectividad VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)


- **Servidores DNS**: escriba el nombre y la dirección IP del servidor DNS o seleccione un servidor DNS previamente registrado de la lista desplegable. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual. Si desea tener resolución de nombres entre las redes virtuales, deberá configurar su propio servidor DNS, en vez de usar la resolución de nombres que proporciona Azure.

  - No seleccione ninguna casilla. Tan solo haga clic en la flecha de la parte inferior derecha para pasar a la pantalla siguiente.

**En la página Espacios de direcciones de la red virtual**, especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual. Es especialmente importante seleccionar un intervalo que no se superponga con ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para el uso en la red virtual.


  ![Página Espacios de direcciones de la red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  **Escriba la información siguiente** y luego haga clic en la marca de verificación de la esquina inferior derecha para configurar la red.

  - **Espacio de direcciones**: incluidas Dirección IP de inicio y Recuento de direcciones. Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local. En este ejemplo, usaremos 10.1.0.0/16 para VNet1.
  - **Agregar subred**: incluidas Dirección IP de inicio y Recuento de direcciones. No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tendrán DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.

**Haga clic en la marca de verificación** de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando termine, verá el mensaje *Creado* indicado en el *Estado* de la página *Redes* del Portal de Azure clásico.

## Paso 3: Crear otra red virtual

A continuación, repita los pasos anteriores para crear otra red virtual. Más adelante en este ejercicio, deberá conectar esas dos redes virtuales. Tenga en cuenta que es muy importante no tener espacios de direcciones duplicados o superpuestos. Para realizar este tutorial, use estos valores:

- **VNet2**
- **Espacio de direcciones** = 10.2.0.0/16
- **Región** = Este de Japón

## Paso 4: Agregar redes locales

Cuando crea una configuración de red virtual a red virtual, debe configurar cada red virtual para que se identifiquen entre sí como un sitio de red local. En este procedimiento, configurará cada red virtual como una red local. Si ya ha configurado redes virtuales antes, así es como debería agregarlas como redes locales en el Portal de Azure clásico.

1. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Agregar una red local**.

2. En la página **Especificar los detalles de la red local**, en **Nombre**, escriba el nombre de la red virtual que quiera usar en la configuración de red virtual a red virtual. En este ejemplo, se usará VNet 1, ya que en la configuración se apuntara a esta red virtual con VNet2.

  Para Dirección IP del dispositivo VPN, utilice cualquier dirección IP. Normalmente, se usaría la dirección IP externa real para un dispositivo VPN. Para las configuraciones de red virtual a red virtual, se usará la dirección IP de puerta de enlace. Pero como aún no ha creado la puerta de enlace, se usará la dirección IP indicada aquí como marcador de posición. Después volverá a estos parámetros y los configurará con las direcciones IP de puerta de enlace correspondientes que genera Azure.

3. En la **página Especificar la dirección**, escribirá el intervalo de direcciones IP real y el recuento de direcciones para VNet1. Se debe corresponder exactamente con el intervalo que especificó antes para VNet1.

4. Después de configurar VNet1 como red local, vuelva atrás y configure VNet2 con los valores correspondientes a dicha red virtual.

5. Ahora apuntará con una VNet a la otra como red local. En el Portal de Azure clásico, vaya a la página **Configurar** de VNet1. En **Conectividad de sitio a sitio**, seleccione **Conectar a la red local** y elija **VNET2** como la red local.

  ![Conectarse a una red local](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736058.jpg)

6. En la sección **Espacios de direcciones de red virtual** de la misma página, haga clic en **Agregar subred de puerta de enlace** y, a continuación, en el icono **Guardar** de la parte inferior de la página para guardar la configuración.

7. Repita el paso para que VNet2 especifique VNet1 como red local.

## Paso 5: Crear las puertas de enlace de enrutamiento dinámico para cada red virtual

Ahora que tiene configuradas las redes virtuales, configurará las puertas de enlace de red virtual.

1. En la página **Redes**, compruebe que la columna de estado de la red virtual sea **Creada**.

2. En la columna **Nombre**, haga clic en el nombre de la red virtual.

3. En la página **Panel**, observe que esta red virtual aún no tiene configurada una puerta de enlace. Verá que este estado cambia a medida que avance por los pasos para configurar la puerta de enlace.

4. En la parte inferior de la página, haga clic en **Crear puerta de enlace**.

  Debe seleccionar **Enrutamiento dinámico**. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en Sí.

  ![Tipo de puerta de enlace](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Una vez creada la puerta de enlace, verá que el gráfico de la puerta de enlace de la página cambia a amarillo e indica Creando puerta de enlace. La puerta de enlace suele tardar unos 15 minutos en crearse..

6. Repita los mismos pasos para la otra red virtual y asegúrese de seleccionar **Puerta de enlace dinámica**. No es necesario completar la primera puerta de enlace de red virtual para empezar a crear la puerta de enlace de la otra.

7. Cuando el estado de la puerta de enlace cambia a Conectando, la dirección IP para cada puerta de enlace estará visible en el panel. Anote la dirección IP que corresponde a cada red virtual, teniendo cuidado para no mezclarlas. Estas son las direcciones IP que se utilizarán al editar las direcciones IP de marcador de posición para el dispositivo VPN en **Redes locales**.

## Paso 6 : Editar la red local

1. En la página **Redes locales**, haga clic en el nombre de la red local que quiere editar y, a continuación, elija **Editar** en la parte inferior de la página. En **Dirección IP del dispositivo VPN**, escriba la dirección IP de la puerta de enlace correspondiente a la red virtual. Por ejemplo, para VNet1, escriba la dirección IP de puerta de enlace asignada a VNet1. A continuación, haga clic en la flecha de la parte inferior de la página.

2. En la página **Especifique el espacio de direcciones**, haga clic en la marca de verificación de la parte inferior derecha sin hacer cambios.

## Paso 7: Conectar las puertas de enlace de VPN

Una vez completados los pasos anteriores, definirá las claves compartidas previamente de IPsec/IKE para que sean iguales. Puede hacerlo con una API de REST o un cmdlet de PowerShell. Si usa PowerShell, compruebe que tiene la [versión más reciente](https://azure.microsoft.com/downloads/) de los cmdlets de Microsoft Azure PowerShell. Los ejemplos siguientes usan cmdlets de PowerShell para establecer el valor de clave en A1b2C3D4. Observe que las dos usan el mismo valor de clave. Edite los ejemplos siguientes para especificar sus propios valores.

Para VNet1

	Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4

Para VNet2

	Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4

Espere a que se inicialicen las conexiones. Una vez inicializada la puerta de enlace, esta tendrá un aspecto similar al gráfico siguiente y las redes virtuales estarán conectadas.

![Estado de la puerta de enlace: conectada](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Vea [Cómo crear una máquina virtual](../virtual-machines/virtual-machines-windows-tutorial-classic-portal.md) para ver los pasos.


[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=AcomDC_0211_2016-->