<properties 
	pageTitle="Control del tráfico de aplicaciones web de Azure con el Administrador de tráfico de Azure" 
	description="Este artículo proporciona información resumida acerca del Administrador de tráfico de Azure en su relación con aplicaciones web de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="cephalin"/>

# Control del tráfico de aplicaciones web de Azure con el Administrador de tráfico de Azure

> [AZURE.NOTE] Este artículo proporciona información resumida acerca del Administrador de tráfico de Microsoft Azure en su relación con Aplicaciones web del Servicio de aplicaciones de Azure. Puede encontrar más información sobre el Administrador de tráfico de Azure en los vínculos que aparecen al final de este artículo.

## Introducción
Puede utilizar el Administrador de tráfico de Azure para controlar la manera en que se distribuyen solicitudes de clientes web a aplicaciones web del Servicio de aplicaciones de Azure. Cuando se agregan extremos de una aplicación web a un perfil del Administrador de tráfico de Azure, este hace un seguimiento del estado de sus aplicaciones web (en ejecución, detenidas o eliminadas) para decidir cuáles de esos extremos debe recibir tráfico.

## Métodos de equilibrio de carga
El Administrador de tráfico de Azure utiliza tres métodos de equilibrio de carga distintos. Estos métodos se describen en la siguiente lista, porque pertenecen a aplicaciones web de Azure.

* **Conmutación por error**: si tiene clones de aplicación web en distintas regiones, puede utilizar este método a fin de configurar una aplicación web para atender todo el tráfico del cliente web y configurar otra aplicación web en una región distinta para ocuparse de ese tráfico en caso de que la primera aplicación web deje de estar disponible. 
	
* **Round Robin**: si tiene clones de aplicación web en distintas regiones, puede utilizar este método para distribuir el tráfico de manera equitativa entre las aplicaciones web en distintas regiones.
	
* **Rendimiento**: el método Rendimiento distribuye el tráfico según el tiempo de ida y vuelta más breve para los clientes. El método Rendimiento también se puede utilizar para aplicaciones web dentro de la misma región o en regiones distintas.

Para obtener información detallada acerca del equilibrio de carga en el Administrador de tráfico de Azure, consulte [Métodos de equilibrio de carga del Administrador de tráfico](../traffic-manager/traffic-manager-load-balancing-methods.md).

##Aplicaciones web y perfiles del Administrador de tráfico 
Para configurar el control del tráfico de aplicación web, puede crear un perfil en el Administrador de tráfico de Azure que utilice uno de los tres métodos de equilibrio de carga anteriormente descritos y, a continuación, agregar los extremos (en este caso, las aplicaciones web) para los que desea controlar el tráfico al perfil. El estado de la aplicación web (en ejecución, detenido o eliminado) se comunica de manera regular al perfil para que el Administrador de tráfico de Azure pueda dirigir el tráfico como corresponda.

Cuando utilice el Administrador de tráfico de Azure con Azure, tenga en cuenta los siguientes puntos:

* Para las implementaciones de solo aplicación web dentro de la misma región, Aplicaciones web de Azure ya proporciona la funcionalidad de conmutación por error y de round-robin independientemente del modo de la aplicación web.

* En el caso de las implementaciones en la misma región que utilizan aplicaciones web en conjunto con otro servicio en la nube de Azure, puede combinar ambos tipos de extremos para habilitar escenarios híbridos.

* Solo puede especificar un extremo de aplicación web por región en un perfil. Cuando selecciona una aplicación web como un extremo para una región, las aplicaciones web restantes de esa región dejan de estar disponibles para su selección para ese perfil.

* Los extremos de aplicación web que especifica en un perfil del Administrador de tráfico de Azure aparecerán en la sección **Nombres de dominio** de la página de configuración de la aplicación web del perfil, pero no se configurarán ahí.

* Después de agregar una aplicación web a un perfil, la **Dirección URL** del sitio del panel de la página del portal de la aplicación web mostrará la dirección URL del dominio personalizado de la aplicación web si ha configurado alguna. De lo contrario, mostrará la dirección URL del perfil del Administrador de tráfico (por ejemplo, `contoso.trafficmgr.com`). Tanto el nombre de dominio directo de la aplicación web como la dirección URL del Administrador de tráfico serán visibles en la página de configuración de la aplicación web en la sección **Nombres de dominio**.

* Los nombres de dominio personalizado funcionarán tal como se espera, pero además de agregarlos a las aplicaciones web, también deberá configurar la asignación de DNS para que apunte a la dirección URL del Administrador de tráfico. Para obtener información sobre cómo configurar un dominio personalizado para un sitio web de Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](web-sites-custom-domain-name.md).

* Solo podrá agregar aplicaciones web que estén en modo estándar a un perfil del Administrador de tráfico de Azure.

## Pasos siguientes

Si desea obtener información general de carácter técnico y conceptual del Administrador de tráfico de Azure, consulte [Información general sobre el Administrador de tráfico](../traffic-manager/traffic-manager-overview.md).

Para obtener información detallada acerca del equilibrio de carga en el Administrador de tráfico de Azure, consulte [Métodos de equilibrio de carga del Administrador de tráfico](../traffic-manager/traffic-manager-load-balancing-methods.md).

Para obtener más información sobre el uso de Traffic Manager con Sitios web de Azure, consulte las publicaciones del blog [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) y [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
 

<!---HONumber=AcomDC_0302_2016-->