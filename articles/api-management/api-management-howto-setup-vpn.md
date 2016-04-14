<properties
	pageTitle="Configuración de conexiones VPN en Administración de API de Azure"
	description="Aprenda a configurar una conexión VPN en Administración de API de Azure y obtenga acceso a los servicios web a través de ella."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="antonba"/>

# Configuración de conexiones VPN en Administración de API de Azure

La compatibilidad con VPN de Administración de API le permite conectar la puerta de enlace de Administración de API a una red virtual de Azure (clásica). Esto permite a los clientes de Administración de API conectarse de forma segura a sus servicios web back-end del entorno local o que, por el contrario, no son inaccesibles a la Internet pública.

>[AZURE.NOTE] Administración de API de Azure funciona con redes virtuales clásicas. Para información sobre la creación de una red virtual clásica, consulte [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obtener información sobre cómo conectar redes virtuales clásicas a redes virtuales ARM, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).

## <a name="enable-vpn"> </a>Habilitación de conexiones VPN

>La conectividad VPN solo está disponible en el nivel **Premium**. Para cambiar, abra el servicio Administración de API en el [Portal de Azure clásico ][] y luego abra la pestaña **Escala**. En la sección **General** seleccione el nivel Premium y haga clic en Guardar.

Para habilitar la conectividad VPN, abra su servicio Administración de API en el [Portal de Azure clásico][] y cambie a la pestaña **Configurar**.

En la sección VPN, cambie **Conexión VPN** a **Activar**.

![Pestaña Configurar de la instancia de Administración de API][api-management-setup-vpn-configure]

Ahora verá una lista de todas las regiones donde se aprovisiona el servicio Administración de API.

Seleccione una VPN y la subred para cada región. La lista de VPN se rellena en función de las redes virtuales disponibles en su suscripción de Azure que están configuradas en la región que se va a configurar.

![Selección de una VPN][api-management-setup-vpn-select]

Haga clic en **Guardar** en la parte inferior de la pantalla. No podrá realizar otras operaciones en el servicio Administración de API desde el Portal de Azure clásico mientras este se esté actualizando. La puerta de enlace de servicio seguirá estando disponible y las llamadas en tiempo de ejecución no se debería ver afectadas.

Tenga en cuenta que la dirección VIP de la puerta de enlace cambiará cada vez que se habilite o deshabilite VPN.

## <a name="connect-vpn"> </a>Conexión a un servicio web detrás de VPN

Después de que el servicio Administración de API se conecta a la VPN, el acceso a los servicios web dentro de la red virtual no es diferente al acceso a los servicios públicos. Solo tiene que escribir la dirección local o el nombre de host (si se ha configurado un servidor DNS para la red virtual de Azure) del servicio web en el campo **Dirección URL de servicio web** al crear una nueva API o editar una existente.

![Agregar una API desde VPN][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>Contenido relacionado


* [Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio][]
* [Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Portal de Azure clásico]: https://manage.windowsazure.com/
[Portal de Azure clásico ]: https://manage.windowsazure.com/

[Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_0211_2016-->