<properties 
	pageTitle="Uso del Portal de Azure para administrar los recursos de Azure | Microsoft Azure" 
	description="Agrupe varios recursos como grupo lógico que se convierte en el límite del ciclo de vida de los recursos que contiene." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/11/2016" 
	ms.author="tomfitz"/>


# Uso del Portal de Azure para administrar los recursos de Azure

## Introducción

El Administrador de recursos de Azure le permite implementar y administrar las soluciones a través de grupos de recursos. Este tema proporciona información general sobre cómo usar grupos de recursos en el Portal de Azure. Normalmente, un grupo de recursos contiene recursos relacionados con una aplicación específica. Por ejemplo, un grupo puede contener una aplicación web que hospeda el sitio web público, una Base de datos SQL que almacena datos relacionales que usa el sitio y una cuenta de almacenamiento que conserva recursos no relacionales. Todos los recursos de un grupo de recursos deben compartir el mismo ciclo de vida. Para obtener más información sobre el Administrador de recursos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

Actualmente, no todos los servicios son compatibles con el portal o con el Administrador de recursos. Para esos servicios, deberá usar el [portal clásico](https://manage.windowsazure.com). Para más información sobre el estado de cada servicio, consulte [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

También puede administrar los recursos mediante la CLI de Azure y Azure PowerShell. Para más información acerca del uso de estas interfaces, consulte [Uso de Azure PowerShell con Administrador de recursos de Azure](../powershell-azure-resource-manager.md) y [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md).


## Creación de recursos y de un grupo de recursos

Si tiene que crear un grupo de recursos vacío, seleccione **Nuevo**, **Administración** y **Grupo de recursos**.

![crear un grupo de recursos vacío](./media/resource-group-portal/create-empty-group.png)

Asígnele un nombre y una ubicación y, si es necesario, seleccione una suscripción.

![establecer valores de grupo](./media/resource-group-portal/set-group-properties.png)

Al implementar los recursos, puede elegir implementarlos en el grupo de recursos que ha creado. La imagen siguiente muestra cómo crear una nueva aplicación web en un grupo de recursos existente.

![crear grupo de recursos](./media/resource-group-portal/select-existing-group.png)

De forma alternativa, puede decidir crear un nuevo grupo de recursos al implementar sus recursos. En lugar de seleccionar uno de los grupos de recursos existente en su suscripción, seleccione **Nuevo** y asígnele un nombre al grupo de recursos.

![crear nuevo grupo de recursos.](./media/resource-group-portal/select-new-group.png)

## buscar grupos de recursos

Puede buscar en todos los grupos de recursos haciendo clic en **Grupos de recursos**.

![buscar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Al seleccionar un grupo de recursos determinado, verá una hoja del grupo de recursos que ofrece información acerca de dicho grupo de recursos, incluida una lista de todos los recursos del grupo.

![resumen del grupo de recursos](./media/resource-group-portal/group-summary.png)

La hoja del grupo de recursos también proporciona una vista unificada de la información de supervisión y facturación para todos los recursos del grupo de recursos.

![supervisión y facturación](./media/resource-group-portal/monitoring-billing.png)

## Consulta de sus datos de suscripción y costos

Puede ver información acerca de la suscripción y de los costos acumulados de todos los recursos. Seleccione **Suscripciones** y la suscripción que desea ver. Es posible que solo tenga una suscripción para seleccionar.

![subscription](./media/resource-group-portal/select-subscription.png)

En la hoja de suscripciones, verá una tasa de evolución.

![tasa de evolución](./media/resource-group-portal/burn-rate.png)

Y un desglose de costos por tipo de recurso.

![costo de recursos](./media/resource-group-portal/cost-by-resource.png)

## Personalización de la interfaz

Para obtener un acceso rápido al resumen del grupo de recursos, puede anclar la hoja en el Panel de inicio.

![anclar un grupo de recursos](./media/resource-group-portal/pin-group.png)

O bien, puede anclar una sección de la hoja en el Panel de inicio seleccionando el botón de puntos suspensivos (...) que se encuentra sobre la sección. Asimismo puede personalizar el tamaño de la sección en la hoja o quitarlo. La siguiente imagen muestra cómo anclar, personalizar o quitar la sección Eventos.

![anclar sección](./media/resource-group-portal/pin-section.png)

Después de anclar la sección Eventos en el Panel de inicio, verá un resumen de eventos en el Panel de inicio.

![Panel de inicio de eventos](./media/resource-group-portal/events-startboard.png)

Y, si lo selecciona inmediatamente, obtendrá más detalles de los eventos.

## Visualización de implementaciones anteriores

Desde la hoja del grupo de recursos, puede ver la fecha y el estado de la última implementación para este grupo de recursos. Si selecciona el vínculo, se mostrará un historial de implementaciones del grupo.

![última implementación](./media/resource-group-portal/last-deployment.png)

Al seleccionar cualquier implementación del historial, se mostrarán los detalles de dicha implementación.

![resumen de implementación](./media/resource-group-portal/deployment-summary.png)

Puede ver las operaciones individuales que se ejecutaron durante la implementación. La imagen siguiente muestra una operación que se realizó correctamente y otra que produjo un error.

![detalles de la operación](./media/resource-group-portal/operation-details.png)

Si se selecciona cualquiera de las operaciones, se mostrarán más detalles sobre la operación. Lo que puede resultar especialmente útil cuando se produce un error en una operación, tal como se muestra a continuación. Puede ayudarle a averiguar por qué no se pudo realizar la implementación. En la siguiente imagen, puede ver que el sitio web no se implementó porque el nombre no era único.

![mensaje de la operación](./media/resource-group-portal/operation-message.png)

## Visualización de registros de auditoría

El registro de auditoría incluye no solo las operaciones de implementación sino todas las operaciones de administración realizadas en los recursos de la suscripción. Por ejemplo, en los registros de auditoría puede ver cuando alguien de su organización detiene una aplicación. Para ver los registros de auditoría, seleccione **Examinar todos** y **Registros de auditoría**.

![examinar registros de auditoría](./media/resource-group-portal/browse-audit-logs.png)

En la sección de operaciones, puede ver las operaciones individuales que se han realizado a través de su suscripción.

![ver registro de auditoría](./media/resource-group-portal/view-audit-log.png)

Al seleccionar cualquiera de las operaciones, verá más detalles, como el usuario que ejecutó la operación.

Puede filtrar lo que se muestra en el registro de auditoría seleccionando **Filtro**.

![filtrar registro](./media/resource-group-portal/filter-logs.png)

Puede seleccionar qué tipo de operaciones se van a mostrar, como las que pertenecen a un recurso o a un grupo de recursos, las que se encuentran dentro de un intervalo de tiempo especificado, las iniciadas por un autor de la llamada concreto o los niveles de la operación.

![opciones de filtro](./media/resource-group-portal/filter-options.png)

## Incorporación de recursos a grupos de recursos

Puede agregar recursos a un grupo de recursos con el comando **Add** en el cuadro de grupo de recursos.

![agregar recurso](./media/resource-group-portal/add-resource.png)

Puede seleccionar el recurso que quiera en la lista disponible.

## Eliminación de grupos de recursos

Puesto que los grupos de recursos le permiten administrar el ciclo de vida de todos los recursos contenidos, la eliminación de un grupo de recursos provocará que se eliminen todos los recursos que alberga. También puede eliminar recursos individuales de un grupo de recursos. Debe prestar atención cuando elimine un grupo de recursos, ya que puede haber otros recursos vinculados a él. Puede ver los recursos vinculados en la asignación de recursos y seguir los pasos necesarios para evitar consecuencias no deseadas cuando elimine los grupos de recursos. No se eliminarán los recursos vinculados aunque no funcionen según lo esperado.

![eliminar grupo](./media/resource-group-portal/delete-group.png)

## Etiquetado de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información sobre cómo trabajar con etiquetas a través del portal, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

## Implementación de una plantilla personalizada

Si desea ejecutar una implementación sin usar las plantillas de Marketplace, puede crear una plantilla personalizada que defina la infraestructura para la solución. Para obtener más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para implementar una plantilla personalizada a través del portal, seleccione **Nuevo** y busque **Implementación de plantillas** hasta que pueda seleccionarla entre las opciones.

![buscar implementación de plantilla](./media/resource-group-portal/search-template.png)

Seleccione **Implementación de plantillas** de entre los recursos disponibles.

![seleccionar implementación de plantillas](./media/resource-group-portal/select-template.png)

Después de iniciar la implementación de la plantilla, puede crear la plantilla personalizada y establecer los valores para la implementación.

![crear plantilla](./media/resource-group-portal/show-custom-template.png)

## Pasos siguientes
Introducción

- Para obtener información sobre los conceptos del Administrador de recursos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).
- Para obtener información sobre cómo usar Azure PowerShell al implementar recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
- Para obtener información sobre cómo usar la interfaz de la línea de comandos (CLI) de Azure al implementar recursos, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0218_2016-->