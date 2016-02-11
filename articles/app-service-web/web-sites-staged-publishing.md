<properties
	pageTitle="Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure"
	description="Aprenda a utilizar la publicación de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>

# Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure
<a name="Overview"></a>

Al implementar la aplicación web en el [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714), puede implementar en una ranura de implementación independiente en lugar de en la ranura de producción predeterminada al ejecutar en el modo del plan del Servicio de aplicaciones **Estándar** o **Premium**. Los espacios de implementación son realmente aplicaciones web activas realmente con sus propios nombres de host. Los elementos de contenido y configuraciones de aplicaciones web se pueden intercambiar entre dos espacios de implementación, incluido el espacio de producción. La implementación de la aplicación en un espacio de implementación ofrece las ventajas siguientes:

- Puede validar los cambios en la aplicación web en un espacio de implementación de ensayo antes de intercambiarlo con el espacio de producción.

- La implementación de una aplicación web en un espacio en primer lugar y su intercambio con el de la producción garantiza que todas las instancias del espacio estén correctas antes de colocarse en producción. Esto elimina tiempos de inactividad a la hora de implementar la aplicación web. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de intercambio. Este flujo de trabajo completo puede automatizarse mediante la configuración de [Intercambio automático](#configure-auto-swap-for-your-web-app).

- Después de un intercambio, el espacio con la aplicación web de ensayo anterior tiene ahora la aplicación web de producción anterior. Si los cambios intercambiados en el espacio de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a obtener el "último sitio en buen estado".

Cada modo del plan del Servicio de aplicaciones admite un número distinto de espacios de implementación. Para averiguar el número de ranuras compatibles con el modo de la aplicación web, consulte [Detalles de precios de Servicio de aplicaciones](/pricing/details/app-service/).

- Cuando la aplicación web tiene varios espacios, no puede cambiar el modo.

- El escalado no está disponible para los espacios que no son de producción.

- No se admite la administración de recursos vinculados en los espacios que no sean de producción. Solo en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) puede evitar este impacto potencial en un espacio de producción si mueve temporalmente el espacio de no producción a un modo del plan del Servicio de aplicaciones diferente. Tenga en cuenta que el espacio de no producción debe compartir una vez más el mismo modo con el espacio de producción antes de que pueda intercambiar los dos espacios.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## Incorporación de una ranura de implementación a una aplicación web ##

La aplicación web debe estar ejecutándose en el modo **Estándar** o **Premium** para que pueda habilitar varias ranuras de implementación.

1. En el [Portal de Azure](https://portal.azure.com/), abra la hoja de la aplicación web.
2. Haga clic en **Configuración** y luego en **Espacios de implementación**. A continuación, en la hoja **Ranuras de implementación**, haga clic en **Agregar ranura**.

	![Agregar una nueva ranura de implementación][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]Si la aplicación web ya no está en el modo **Estándar** o **Premium**, recibirá un mensaje que indica los modos compatibles para habilitar la publicación de ensayo. Llegados a este punto, tiene la opción de seleccionar **Actualizar** e ir a la pestaña **Escalar** de la aplicación web antes de continuar.

2. En la hoja **Agregar ranura**, asigne un nombre a la ranura y seleccione si la configuración de la aplicación web se debe clonar de otra ranura de implementación existente. Haga clic en la marca de verificación para continuar.

	![Origen de configuración][ConfigurationSource1]

	La primera vez que agrega un espacio solo tendrá dos opciones: clonar la configuración del espacio predeterminado en producción o no.

	Después de haber creado varios espacios, podrá clonar la configuración de un espacio que no sea el de producción:

	![Orígenes de configuración][MultipleConfigurationSources]

5. En la hoja **Ranuras de implementación**, haga clic en la ranura de implementación para abrir una hoja para la ranura, con un conjunto de métricas y una configuración igual a la de cualquier otra aplicación web. **nombre-aplicación-webnombre-ranura-implementación** aparecerá en la parte superior de la hoja para recordarle que está viendo la ranura de implementación.

	![Título de la ranura de implementación][StagingTitle]

5. Haga clic en la dirección URL de la aplicación en la hoja del espacio. Observe que el espacio de implementación tiene su propio nombre de host y que se trata también de una aplicación activa. Para limitar el acceso público a la ranura de implementación, consulte [Aplicación web del Servicio de aplicaciones: bloquear acceso web a ranuras de implementación que no son de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

No hay ningún contenido después de la creación de un espacio de implementación. Puede implementar el espacio desde una rama de repositorio diferente o desde un repositorio completamente diferente. También puede cambiar la configuración del espacio. Utilice el perfil de publicación o las credenciales de implementación asociadas al espacio de implementación para ver las actualizaciones del contenido. Por ejemplo, [puede publicar en esta ranura mediante Git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configuración de ranuras de implementación ##
Cuando crea un clon de la configuración de otro espacio de implementación, la configuración clonada se puede editar. Además, algunos elementos de configuración seguirán el contenido a través de un intercambio (no son específicos del espacio) mientras que otros elementos de configuración permanecerán en el mismo espacio después de un intercambio (son específicos del espacio). Las listas siguientes muestran la configuración que cambiará al intercambiar las ranuras.

**Configuraciones que se intercambian**:

- Configuración general: por ejemplo, versión de framework, 32 o 64 bits, Web Sockets
- Configuración de la aplicación (puede configurarse para ajustarse a un espacio)
- Cadenas de conexión (puede configurarse para ajustarse a un espacio)
- Asignaciones de controlador
- Configuración de supervisión y diagnóstico
- Contenido de WebJobs

**Configuraciones que no se intercambian**:

- Extremos de publicación
- Nombres de dominio personalizados
- Certificados SSL y enlaces
- Configuración de escala
- Programadores de WebJobs

Para establecer una configuración de aplicación o una cadena de conexión que se ajuste a una ranura (no intercambiada), obtenga acceso a la hoja **Configuración de la aplicación** para una ranura específica y seleccione el cuadro **Configuración de ranura** para los elementos de configuración que se deben ajustar la ranura. Tenga en cuenta que marcar un elemento de configuración como espacio específico tiene el efecto de establecer ese elemento como no intercambiable en todos los espacios de implementación asociados con la aplicación web.

![Configuración de ranura][SlotSettings]

<a name="Swap"></a>
## Para intercambiar ranuras de implementación ##

>[AZURE.IMPORTANT]Antes de colocar una aplicación web de una ranura de implementación en producción, asegúrese de que todos los valores específicos que no son de ranura están configurados exactamente como desea que estén en el destino de intercambio.

1. Para intercambiar las ranuras de implementación, haga clic en el botón **Intercambiar** en la barra de comandos de la aplicación web o en la barra de comandos de una ranura de implementación. Asegúrese de que el origen y el destino del intercambio estén correctamente establecidos. Normalmente, el destino de intercambio suele ser el espacio de producción.  

	![Botón de cambio][SwapButtonBar]

3. Haga clic en **Aceptar** para completar la operación. Cuando termine la operación, los espacios de implementación se habrán intercambiado.

## Configuración del intercambio automático para la aplicación web ##

El intercambio automático optimiza los escenarios de DevOps donde desee implementar continuamente la aplicación web sin arranques en frío ni tiempos de inactividad para los clientes finales de la aplicación web. Cuando se configura un espacio de implementación para el intercambio automático en producción, cada vez que inserte una actualización de código para ese espacio, el Servicio de aplicaciones coloca automáticamente la aplicación web en producción, una vez que ya esté correcta en el espacio.

>[AZURE.IMPORTANT]Al habilitar el intercambio automático para una ranura, asegúrese de que la configuración de ranura sea exactamente la configuración prevista para la ranura de destino (normalmente la ranura de producción).

Es fácil configurar el intercambio automático para un espacio. Siga estos pasos:

1. En la hoja **Ranuras de implementación**, elija una ranura que no sea de producción, haga clic en **Todas las configuraciones** para la hoja de esa ranura.  

	![][Autoswap1]

2. Haga clic en **Configuración de la aplicación**. Seleccione **Activado** para **Intercambio automático**, elija la ranura de destino deseada en **Ranura de intercambio automático** y haga clic en **Guardar** en la barra de comandos. Asegúrese de que la configuración del espacio sea exactamente la configuración prevista para el espacio de destino.

	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación.

	![][Autoswap2]

	>[AZURE.NOTE]Para probar el intercambio automático para la aplicación web, puede seleccionar primero una ranura de destino que no sea de producción en **Ranura de intercambio automático** para familiarizarse con la característica.

3. Ejecute una inserción de código para este espacio de implementación. El intercambio automático se realizará después de un breve tiempo y la actualización se reflejará en la dirección URL del espacio de destino.

<a name="Multi-Phase"></a>
## Uso del intercambio de varias fase para la aplicación web ##

El intercambio de varias fase está disponible para simplificar la validación en el contexto de elementos de configuración diseñados para ajustarse a una ranura como cadenas de conexión. En estos casos, puede ser de utilidad aplicar este tipo de elemento de configuración desde el destino de intercambio al origen de intercambio y validar antes de que el intercambio realmente surta efecto. Una vez que los elementos de configuración del destino de intercambio se aplican al origen de intercambio las acciones disponibles completan el intercambio o se revierten a la configuración original del origen de intercambio, lo que también tiene el efecto de cancelar el intercambio. En los cmdlets de Azure PowerShell se incluyen ejemplos para los cmdlets de Azure PowerShell disponibles para el intercambio de varias fases se incluyen para la sección de ranuras de implementación.

<a name="Rollback"></a>
## Para revertir una aplicación de producción después de un intercambio ##
Si se identifican errores en producción después del intercambio de espacios, revierta los espacios a sus estados anteriores. Para ello, intercambie los mismos dos espacios inmediatamente.

<a name="Delete"></a>
## Para eliminar una ranura de implementación##

En la hoja de una ranura de implementación, haga clic en **Eliminar** en la barra de comandos.

![Eliminación de una ranura de implementación][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets de PowerShell de Azure para espacios de implementación

PowerShell de Azure es un módulo que proporciona cmdlets para administrar Azure mediante Windows PowerShell, incluida la compatibilidad para administrar espacios de implementación de aplicaciones web del Servicio de aplicaciones de Azure.

- Para obtener información acerca de cómo instalar y configurar Azure PowerShell y cómo autenticar Azure PowerShell con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md).  

- Para poder utilizar el nuevo modo del Administrador de recursos de Azure para cmdlets de PowerShell, empiece con lo siguiente: `Switch-AzureMode -Name AzureResourceManager`.

----------

### Crear una aplicación web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Creación de una ranura de implementación para una aplicación web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -SlotName [deployment slot name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Inicio del intercambio de varias fases y aplicación de la configuración de la ranura de destino a ranura de origen

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Reversión de la primera fase de intercambio de varias fase y restauración de la configuración de la ranura de origen

`Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01`

----------

### Intercambio de ranuras de implementación

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Eliminación de una ranura de implementación

`Remove-AzureResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01`

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## Comandos de la interfaz de la línea de comandos de Azure (CLI de Azure) para ranuras de implementación

La CLI de Azure proporciona comandos de varias plataformas para trabajar con Azure, incluida la compatibilidad para administrar ranuras de implementación en Aplicaciones web.

- Para obtener instrucciones acerca de cómo instalar y configurar la CLI de Azure, incluyendo la información acerca de cómo conectar la CLI de Azure a su suscripción de Azure, consulte [Instalación y configuración de la CLI de Azure](../xplat-cli-install.md).

-  Para mostrar los comandos disponibles para el Servicio de aplicaciones de Azure en la CLI de Azure, llame a `azure site -h`.

----------
### azure site list
Para obtener información acerca de las aplicaciones web de Azure de la suscripción actual, llame a **azure site list**, como en el ejemplo siguiente.

`azure site list webappslotstest`

----------
### azure site create
Para crear una ranura de implementación, llame a **azure site create** y especifique el nombre de la aplicación web existente y el de la ranque se vaya a crear, como en el ejemplo siguiente.

`azure site create webappslotstest --slot staging`

Para habilitar el control de código fuente en la nueva ranura, use la opción **--git**, como en el ejemplo siguiente.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Para hacer que la ranura de implementación actualizada se convierta en la aplicación de producción, utilice el comando **azure site swap** para realizar una operación de intercambio, como en el ejemplo siguiente. La aplicación de producción no experimentará tiempos de inactividad ni arranques en frío.

`azure site swap webappslotstest`

----------
### azure site delete
Para eliminar una ranura de implementación que ya no sea necesaria, utilice el comando **azure site delete**, como en el ejemplo siguiente.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes ##
[Aplicación web del Servicio de aplicaciones de Azure: bloquear acceso web a ranuras de implementación que no son de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Evaluación gratuita de Microsoft Azure](/pricing/free-trial/)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=AcomDC_0114_2016-->