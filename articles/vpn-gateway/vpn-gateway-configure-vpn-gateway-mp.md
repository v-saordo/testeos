<properties 
   pageTitle="Configuración de una puerta de enlace de VPN en el Portal de Azure clásico | Microsoft Azure"
   description="Este artículo le guiará a través de la configuración de la puerta de enlace VPN de red virtual y el cambio de un tipo de enrutamiento de la puerta de enlace VPN de estático a dinámico o de dinámico a estático."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="cherylmc" />

# Configuración de una puerta de enlace VPN para el modelo de implementación clásico


Si desea crear una conexión segura entre entornos Azure y la ubicación local, necesita configurar una puerta de enlace de red virtual. Hay diferentes tipos de puertas de enlace de VPN y el tipo de puerta de enlace de VPN que se va a crear depende tanto del plan de diseño de la red y el dispositivo VPN local que desee usar.

Por ejemplo, algunas opciones de conectividad, como una conexión punto a sitio, requieren una puerta de enlace de enrutamiento dinámico. Si desea configurar la puerta de enlace para admitir conexiones punto a sitio (P2S) y una conexión de sitio a sitio (S2S), tendrá que configurar una puerta de enlace de enrutamiento dinámico, aunque se puede configurar el sitio a sitio con cualquier tipo de enrutamiento de puerta de enlace.

Además, tendrá que asegurarse de que el dispositivo que desea usar para la conexión será compatible con el tipo de VPN que se va a crear. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md).


**Información acerca de este artículo**

Este artículo se escribió pensando en el modelo de implementación clásico mediante el [Portal clásico](https://manage.windowsazure.com) (no el Portal de Azure). Cuando tengamos un artículo disponible para el modelo de implementación del Administrador de recursos, nos vincularemos a este desde aquí.

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Información general sobre la configuración

El siguiente procedimiento le guiará a través de la configuración de una puerta de enlace de VPN en el Portal de Azure clásico. Estos pasos se aplican a las puertas de enlace de redes virtuales que se crearon usando el modo de administración de servicios y están visibles en el Portal de Azure clásico. No son los pasos para usar el portal de vista previa o para redes virtuales configuradas mediante el modo del Administrador de recursos. Puede encontrar información acerca de cómo crear puertas de enlace de red virtual para las redes virtuales creadas mediante el modo del Administrador de recursos en [Crear una red virtual con una conexión de sitio a sitio mediante el Administrador de recursos de Azure y PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).


1. [Creación de una puerta de enlace de VPN para la red virtual](#create-a-vpn-gateway)

1. [Recopilación de información para la configuración del dispositivo VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configuración del dispositivo VPN](#configure-your-vpn-device)

1. [Verificación de los intervalos de red local y la dirección IP de puerta de enlace de VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### Antes de empezar

Antes de configurar la puerta de enlace, primero deberá crear la red virtual. Para ver los pasos para crear una red virtual para conectividad entre entornos, vea [Configuración de una red virtual con una conexión VPN de sitio a sitio](vpn-gateway-site-to-site-create.md) o [Configuración de una red virtual con una conexión VPN de punto a sitio](vpn-gateway-point-to-site-create.md). A continuación, utilice los pasos siguientes para configurar la puerta de enlace de VPN y recopilar la información necesaria para configurar el dispositivo VPN.

Si ya tiene una puerta de enlace de VPN y desea cambiar el tipo de enrutamiento, consulte [Cambio del tipo de enrutamiento de puerta de enlace de VPN](#how-to-change-your-vpn-gateway-type).

## Creación de una puerta de enlace de VPN

1. En la página **Redes** del [Portal de Azure clásico](https://manage.windowsazure.com), compruebe que la columna de estado de la red virtual muestra **Creado**.

1. En la columna **Nombre**, haga clic en el nombre de la red virtual.

1. En la página **Panel**, observe que esta red virtual aún no tiene configurada ninguna puerta de enlace. Verá que este estado cambia a medida que avance por los pasos para configurar la puerta de enlace.

![Puerta de enlace no creada](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


A continuación, en la parte inferior de la página, haga clic en **Crear puerta de enlace**. Puede seleccionar *Enrutamiento estático* o *Enrutamiento dinámico*. El tipo de enrutamiento que seleccione depende de una serie de factores. Por ejemplo, lo que será compatible con el dispositivo VPN y si necesita admitir conexiones punto a sitio. Consulte [Acerca de los dispositivos VPN para la conectividad de redes virtuales](vpn-gateway-about-vpn-devices.md) para comprobar el tipo de enrutamiento que necesita. Una vez creada la puerta de enlace, no se puede cambiar entre los tipos de enrutamiento VPN de puerta de enlace sin eliminar y volver a crear la puerta de enlace. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en **SÍ**.

![Tipo de enrutamiento VPN de puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Una vez creada la puerta de enlace, verá que el gráfico de la puerta de enlace de la página cambia a amarillo e indica *Creando puerta de enlace*. La puerta de enlace puede tardar hasta 25 minutos en crearse. Tendrá que esperar hasta que la puerta de enlace se haya completado antes de poder avanzar con otras opciones de configuración.

![Creación de una puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Cuando la puerta de enlace cambia a *Conectando*, puede recopilar la información que necesitará para el dispositivo VPN.

![Conexión de una puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## Recopilación de información para la configuración del dispositivo VPN

Una vez creada la puerta de enlace, recopile la información de la configuración del dispositivo VPN. Esta información se encuentra en la página **Panel** de la red virtual:

1. **Dirección IP de puerta de enlace:** la dirección IP se encuentra en la página **Panel**. No podrá verla hasta después de que ha terminado de crear la puerta de enlace.

1. **Clave compartida:** haga clic en el botón **Administrar claves**, en la parte inferior de la pantalla. Haga clic en el icono situado junto a la clave para copiarlo en el Portapapeles y, a continuación, pegar y guardar la clave. Tenga en cuenta que este botón solo funciona cuando hay un solo túnel VPN S2S. Si tiene varios túneles de VPN S2S, utilice la API Obtener clave compartida de puerta de enlace de red virtual o el cmdlet de PowerShell.

![Administración de una clave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## Configurar el dispositivo VPN

Después de completar los pasos anteriores, usted o su administrador de red se debe configurar el dispositivo VPN para crear la conexión. [Para obtener más información acerca de los dispositivos VPN, consulte Acerca de los dispositivos VPN para la conectividad de redes virtuales](vpn-gateway-about-vpn-devices.md).

Una vez configurado el dispositivo VPN, puede ver la información de conexión actualizada en la página Panel para la red virtual.

También puede ejecutar uno de los comandos siguientes para probar la conexión:

| | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **SA de comprobación de modo principal** | show crypto isakmp sa | show crypto isakmp sa | get ike cookie | show security ike security-association |
| **SA de comprobación de modo rápido** | show crypto ipsec sa | show crypto ipsec sa | get sa | show security ipsec security-association |


## Verificación de los intervalos de red local y la dirección IP de puerta de enlace de VPN

### Verificación de la dirección IP de la puerta de enlace de VPN

Para que la puerta de enlace se conecte correctamente, la dirección IP del dispositivo VPN debe configurarse correctamente para la red local que especificó para la configuración entre entornos. Normalmente, esto se configura durante el proceso de configuración de sitio a sitio. Sin embargo, si anteriormente usó esta red local con un dispositivo diferente o ha cambiado la dirección IP para esta red local, deberá modificar la configuración para especificar la dirección IP de la puerta de enlace correcta.

1. Para comprobar la dirección IP de la puerta de enlace, haga clic en **Redes** en el panel izquierdo del portal y seleccione **Redes locales** en la parte superior de la página. Verá la dirección de la puerta de enlace de VPN para cada red local que ha creado. Para modificar la dirección IP, seleccione la red virtual y haga clic en **Editar** en la parte inferior de la página.

1. En la página **Especificación de detalles de la red local** , modifique la dirección IP y, a continuación, haga clic en la flecha siguiente en la parte inferior de la página.

1. En la página **Especificación del espacio de direcciones**, haga clic en la marca de verificación de la parte inferior derecha para guardar la configuración.

### Verificación de los intervalos de direcciones de sus redes locales

Para que el tráfico correcto pase a través de la puerta de enlace a la ubicación local, deberá comprobar que ha enumerado cada intervalo de direcciones IP que desea incluir en la configuración de la red local. Según la configuración de red de su ubicación local, puede ser una tarea bastante pesada porque cada intervalo debe estar incluido en su configuración de **Redes locales** de Azure. A continuación, el tráfico vinculado a una dirección IP que está dentro de los intervalos especificados se enviará a través de la puerta de enlace de VPN de red virtual. Los intervalos de direcciones IP que indique no debe ser intervalos privados, aunque deberá comprobar que la configuración local puede recibir el tráfico entrante.

Para agregar o editar los intervalos de una red local, siga este procedimiento.

1. Para editar los intervalos de direcciones IP de una red local, haga clic en **Redes** en el panel izquierdo del portal y seleccione **Redes locales** en la parte superior de la página. En el portal, la manera más fácil de ver los intervalos que ha enumerado es en la página **Editar**. Para ver los intervalos, seleccione la red virtual y haga clic en **Editar** en la parte inferior de la página.

1. No realice ningún cambio en la página **Especificación de detalles de la red local**. Haga clic en la flecha siguiente de la parte inferior de la página.

1. En la página **Especificación del espacio de direcciones**, realice los cambios del espacio de direcciones de red. A continuación, haga clic en la marca de verificación para guardar la configuración.

## Visualización del tráfico de la puerta de enlace

Puede ver la puerta de enlace y el tráfico de la puerta de enlace desde la página **Panel** de la red virtual.

En la página **Panel** puede ver lo siguiente:

- La cantidad de datos que fluyen a través de la puerta de enlace, tanto de entrada como de salida.

- Los nombres de los servidores DNS especificados para la red virtual.

- La conexión entre la puerta de enlace y el dispositivo VPN.

- La clave compartida que se utiliza para configurar la conexión de la puerta de enlace para el dispositivo VPN.


## Cómo cambiar el tipo de enrutamiento de VPN para la puerta de enlace

Puesto que algunas configuraciones de conectividad solo están disponibles para ciertos tipos de enrutamiento de puerta de enlace, puede que necesite cambiar el tipo de enrutamiento VPN de puerta de enlace de una puerta de enlace VPN existente. Por ejemplo, puede desear agregar conectividad punto a sitio a una conexión de sitio a sitio ya existente que tiene una puerta de enlace estática. Punto a sitio requiere una puerta de enlace dinámica, lo que significa que para configurarla, tendrá que cambiar el tipo de enrutamiento VPN de puerta de enlace de estática a dinámica.

Si necesita cambiar un tipo de enrutamiento VPN de puerta de enlace, deberá eliminar la puerta de enlace existente y, a continuación, volver a crearla con el nuevo tipo de enrutamiento. No es necesario eliminar toda la red virtual para cambiar el tipo de enrutamiento de la puerta de enlace.

Antes de cambiar el tipo VPN de puerta de enlace, asegúrese de comprobar que el dispositivo VPN será compatible con el tipo de enrutamiento que desea usar. Para descargar nuevos ejemplos de configuración de enrutamiento y comprobar los requisitos del dispositivo VPN, consulte [Acerca de los dispositivos VPN para la conectividad de redes virtuales](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Cuando se elimina una puerta de enlace de VPN de red virtual, se libera la VIP asignada a la puerta de enlace. Al volver a crear la puerta de enlace, se la asignará una nueva VIP.

1. **Elimine la puerta de enlace VPN existente.**

	En la página **Panel** de la red virtual, desplácese a la parte inferior de la página y haga clic en **Eliminar puerta de enlace**. Espere la notificación de que se ha eliminado la puerta de enlace. Cuando reciba una notificación en la pantalla de que se ha eliminado la puerta de enlace, podrá crear una nueva puerta de enlace.

1. **Cree una puerta de enlace de VPN.**

	Utilice el procedimiento que aparece en la parte superior de la página para crear una nueva puerta de enlace: [Creación de una puerta de enlace de VPN](#create-a-vpn-gateway).


## Pasos siguientes

Puede obtener más información sobre conectividad entre entornos de red virtual en este artículo: [Información acerca de la conectividad entre entornos de red virtual](vpn-gateway-cross-premises-options.md).

Puede agregar máquinas virtuales a la red virtual. Vea [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Si quiere configurar una conexión VPN de punto a sitio, vea [Configuración de una conexión VPN de punto a sitio](vpn-gateway-point-to-site-create.md).

 

<!---HONumber=AcomDC_0224_2016-->