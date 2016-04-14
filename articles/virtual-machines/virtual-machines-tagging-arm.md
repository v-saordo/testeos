<properties
   pageTitle="Cómo etiquetar una máquina virtual | Microsoft Azure"
   description="Obtenga información acerca del etiquetado de una máquina virtual Azure creada con el modelo de implementación del Administrador de recursos."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Etiquetado de una máquina virtual en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


En este artículo se describen diferentes maneras de etiquetar una máquina virtual en Azure por medio del Administrador de recursos de Azure. Las etiquetas son pares clave-valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Actualmente, Azure admite un máximo de 15 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocar en un recurso en el momento de su creación, o bien se pueden agregar a un recurso existente. Tenga en cuenta que las etiquetas solamente son compatibles para los recursos creados mediante el Administrador de recursos de Azure.

## Etiquetado de una máquina virtual mediante plantillas

En primer lugar, veamos el etiquetado a través de plantillas. [Esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) coloca etiquetas en los siguientes recursos: proceso (máquina virtual), almacenamiento (cuenta de almacenamiento) y red (dirección IP pública, red virtual e interfaz de red).

Haga clic en el botón **Implementar en Azure** del [vínculo de la plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Esto le remitirá al [Portal de Azure](https://portal.azure.com/), donde puede implementar esta plantilla.

![Implementación sencilla con etiquetas](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Esta plantilla incluye las siguientes etiquetas: *Department*, *Application* y *Created By*. Estas etiquetas se pueden agregar o modificar directamente en la plantilla si se desea cambiar sus nombres.

![Etiquetas de Azure en una plantilla](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Como puede ver, las etiquetas se definen como pares de clave-valor, separados por dos puntos (:). Las etiquetas deben definirse con este formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Guarde el archivo de plantilla cuando termine de modificarlo con las etiquetas que prefiera.

A continuación, en la sección **Editar parámetros**, puede rellenar los valores de las etiquetas.

![Editar etiquetas en el Portal de Azure](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Haga clic en **Crear** para implementar esta plantilla con sus valores de etiqueta.


## Etiquetado a través del portal

Después de crear los recursos con etiquetas, puede ver, agregar y eliminar etiquetas en el portal.

Seleccione el icono de etiquetas para ver las etiquetas:

![Icono de etiquetas en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Para agregar una nueva etiqueta desde el portal, defina su propio par de clave-valor y guárdela.

![Agregar etiqueta nueva en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

La nueva etiqueta debería aparecer en la lista de etiquetas del recurso.

![Etiqueta nueva guardada en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Etiquetado con PowerShell

Para crear, agregar y eliminar etiquetas a través de PowerShell, primero es preciso configurar el [entorno de PowerShell con Administrador de recursos de Azure][]. Una vez completada la configuración, puede colocar etiquetas en los recursos de proceso, red y almacenamiento tanto al crear el recurso a través de PowerShell como después de haberlo creado. En este artículo se centrará en la visualización y edición de etiquetas en máquinas virtuales.

En primer lugar, navegue a una máquina virtual a través del cmdlet `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Si la máquina virtual ya contiene etiquetas, verá todas ellas en el recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si desea agregar etiquetas a través de PowerShell, puede usar el comando `Set-AzureResource`. Tenga en cuenta que si actualiza las etiquetas a través de PowerShell, se actualizan todas ellas en conjunto. Por tanto, si va a agregar una etiqueta a un recurso que ya tiene etiquetas, tendrá que incluir todas las etiquetas que desea colocar en el recurso. A continuación se muestra un ejemplo de cómo agregar etiquetas adicionales a un recurso mediante los cmdlets de PowerShell.

Este primer cmdlet establece todas las etiquetas colocadas en *MyWindowsVM* con la variable *tags*, para lo que emplea las funciones `Get-AzureResource` y `Tags`. Tenga en cuenta que el parámetro `ApiVersion` es opcional; si no se especifica, se utilizará la última versión de API del proveedor de recursos.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

El segundo comando muestra las etiquetas de la variable especificada.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

El tercer comando agrega una etiqueta adicional a la variable *tags*. Observe el uso de **+=** para anexar el nuevo par de clave-valor a la lista de *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

El cuarto comando establece todas las etiquetas definidas en la variable *tags* en el recurso especificado. En este caso, es MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

El quinto comando muestra todas las etiquetas en el recurso. Como puede ver, *Location* está definido como una etiqueta, con *MyLocation* como valor.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Para más información sobre el etiquetado a través de PowerShell, consulte los [cmdlets de recursos de Azure][].


## Etiquetado con la CLI de Azure

El etiquetado también es compatible con los recursos creados a través de la CLI de Azure. Para comenzar, configure el [entorno de la CLI de Azure][]. Inicie sesión en su suscripción a través de la CLI de Azure y cambie al modo ARM. Con este comando es posible ver todas las propiedades de una máquina virtual dada, incluidas las etiquetas:

        azure vm show -g MyResourceGroup -n MyVM

A diferencia de PowerShell, si va a agregar etiquetas a un recurso que ya contiene etiquetas, no necesitará especificar todas las etiquetas (antiguas y nuevas) antes de utilizar el comando `azure vm set`. En su lugar, este comando permite anexar una etiqueta al recurso. Para agregar una nueva etiqueta de máquina virtual a través de la CLI de Azure, puede usar el comando `azure vm set` junto con el parámetro de etiqueta **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para quitar todas las etiquetas, puede usar el parámetro **-T** en el comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Ahora que hemos aplicado etiquetas a nuestros recursos a través de PowerShell, la CLI de Azure y el portal, echemos un vistazo a los detalles de uso para ver las etiquetas del portal de facturación.


## Visualización de etiquetas en los detalles de uso

Las etiquetas colocadas en los recursos de proceso, red y almacenamiento a través del Administrador de recursos de Azure se rellenarán en los detalles de uso del [portal de facturación](https://account.windowsazure.com/).

Haga clic en **Descargar detalles de uso** para ver los detalles de uso de la suscripción.

![Detalles de uso en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Seleccione el extracto de facturación y los detalles de uso de la **versión 2**:

![Detalles de uso de la vista previa de la versión 2 en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

En los detalles de uso puede ver todas las etiquetas en la columna de **etiquetas**:

![Columna de etiquetas en el Portal de Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

Mediante el análisis de estas junto con el uso, las organizaciones podrán obtener nuevos puntos de vista en sus datos de consumo.


## Recursos adicionales

* [Información general del Administrador de recursos de Azure][]
* [Uso de etiquetas para organizar los recursos de Azure][]
* [Comprender la factura de Azure][]
* [Obtención de información sobre el consumo de recursos de Microsoft Azure][]




[entorno de PowerShell con Administrador de recursos de Azure]: ../powershell-azure-resource-manager.md
[cmdlets de recursos de Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[entorno de la CLI de Azure]: ./xplat-cli-azure-resource-manager.md
[Información general del Administrador de recursos de Azure]: ../resource-group-overview.md
[Uso de etiquetas para organizar los recursos de Azure]: ../resource-group-using-tags.md
[Comprender la factura de Azure]: ../billing-understand-your-bill.md
[Obtención de información sobre el consumo de recursos de Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0128_2016-->