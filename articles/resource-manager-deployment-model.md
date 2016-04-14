<properties
   pageTitle="Comprender las diferencias entre los modelos de implementación del Administrador de recursos y clásica"
   description="Describe las diferencias entre el modelo de implementación del Administrador de recursos y el modelo de implementación clásica (o de administración del servicio)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# Descripción de la implementación del Administrador de recursos y la implementación clásica

El modelo de implementación del Administrador de recursos proporciona una nueva manera de implementar y administrar los servicios que conforman su aplicación. Este nuevo modelo contiene importantes diferencias con el modelo de implementación clásica, y los dos modelos no son totalmente compatibles entre sí. Para simplificar la implementación y administración de recursos, Microsoft recomienda que utilice el Administrador de recursos para los nuevos recursos y, si es posible, que vuelva a implementar los recursos existentes a través del Administrador de recursos.

También puede conocer el modelo de implementación clásica como modelo de administración de servicios.

En este tema se describen las diferencias entre los dos modelos y algunos de los problemas que pueden surgir al realizar la transición desde el modelo clásico al Administrador de recursos. Ofrece información general de los modelos, pero no cubre en detalle las diferencias en los servicios individuales.

Muchos recursos funcionan sin problema tanto en el modelo clásico como en el Administrador de recursos. Estos recursos son totalmente compatibles con el Administrador de recursos incluso si se crean en el modelo clásico. Puede realizar la transición al Administrador de recursos sin ningún problema ni esfuerzo adicional.

Sin embargo, algunos proveedores de recursos ofrecen dos versiones del recurso (una para el modelo clásico y otra para el Administrador de recursos) debido a las diferencias arquitectónicas entre los modelos. Los proveedores de recursos que distinguen entre los dos modelos son:

- **Proceso**: admite instancias de máquinas virtuales y conjuntos de disponibilidad opcional.
- **Almacenamiento**: admite cuentas de almacenamiento requeridas que almacenan los discos duros virtuales para máquinas virtuales, incluido su sistema operativo y discos de datos adicionales.
- **Red**: admite NIC requeridos, direcciones IP de máquinas virtuales y subredes de redes virtuales y equilibradores de carga opcionales, direcciones IP de equilibradores de carga y grupos de seguridad de red.

Para estos tipos de recursos, debe ser consciente de la versión que utiliza, ya que variarán las operaciones admitidas. Para obtener más detalles acerca de la transición de recursos de procesos, almacenamiento y redes, consulte [Proveedores de procesos, redes y almacenamiento de Azure en el Administrador de recursos de Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

## Características del Administrador de recursos

Los recursos creados a través del Administrador de recursos comparten las siguientes características:

- Creadas a través de uno de los métodos siguientes:

  - El [Portal de Azure](https://portal.azure.com/).

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Para recursos de proceso, almacenamiento y red, puede usar el Administrador de recursos o la implementación clásica. Select **Administrador de recursos**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Para las versiones de Azure PowerShell anteriores a la versión 1.0, los comandos se ejecutan en el modo **AzureResourceManager**.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - Para Azure PowerShell 1.0, use la versión del Administrador de recursos de comandos. Estos comandos tienen el formato *verb-AzureRm*, tal como se muestra a continuación.

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operaciones REST.
  - Los comandos de la CLI de Azure se ejecutan en el modo **arm**.

            azure config mode arm

- El tipo de recurso no incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento**.

    ![aplicación web](./media/resource-manager-deployment-model/resource-manager-type.png)

La aplicación que se muestra en el diagrama siguiente muestra cómo los recursos implementados a través del Administrador de recursos están incluidos en un único grupo de recursos.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Además, hay relaciones entre los recursos de los proveedores de recursos:

- Una máquina virtual depende de una cuenta de almacenamiento específica definida en el SRP para almacenar sus discos en el almacenamiento de blobs (obligatorio).
- Una máquina virtual hace referencia a una NIC específica definida en el NRP (obligatorio) y un conjunto de disponibilidad definido en el CRP (opcional).
- Una NIC hace referencia a la dirección IP asignada a la máquina virtual (necesaria), la subred de la red virtual para la máquina virtual (necesaria) y a un grupo de seguridad de red (opcional).
- Una subred dentro de una red virtual hace referencia a un grupo de seguridad de red (opcional).
- Una instancia de equilibrador de carga hace referencia al grupo de backend de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una dirección IP pública o privada del equilibrador de carga (opcional).

## Características de implementación clásica

En Administración de servicios de Azure, los recurss de cálculo, almacenamiento o para hospedar máquinas virtuales son proporcionados por:

- Un servicio de nube requerido que actúa como contenedor para hospedar máquinas virtuales (cálculo). Las máquinas virtuales se proporcionan automáticamente con una tarjeta de interfaz de red (NIC) y una dirección IP asignada por Azure. Además, el servicio de nube contiene una instancia de equilibrador de carga externa, una dirección IP pública y extremos predeterminados para permitir un escritorio remoto y tráfico de PowerShell remoto para máquinas virtuales basadas en Windows y tráfico de Secure Shell (SSH) para máquinas virtuales basadas en Linux.
- Una cuenta de almacenamiento necesaria que almacena discos duros virtuales para una máquina virtual, incluido el sistema operativo, los discos de datos temporales y adicionales (almacenamiento).
- Una red virtual opcional que actúa como un contenedor adicional, en el que se puede crear una estructura de subredes y designar la subred en la que se encuentra la máquina virtual (red).

Los recursos creados en el modelo de implementación clásica comparten las siguientes características:

- Creadas a través de uno de los métodos siguientes:

  - [Portal clásico](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        O bien, el portal de vista previa y el usuario deben especificar la implementación **clásica** (para cálculo, almacenamiento y redes).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Para las versiones de Azure PowerShell anteriores a la versión 1.0, los comandos se ejecutan en el modo **AzureServiceManagement** (que es el modo predeterminado, por lo que si no cambia de forma específica a AzureResourceManager, ejecuta el modo AzureServiceManagement).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - Para Azure PowerShell 1.0, use la versión de Administración de servicios de comandos. Estos nombres de comandos **no** tienen el formato *verb-AzureRm*, tal como se muestra a continuación.

            PS C:\> Get-AzureDeployment

  - [API de REST de Administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operaciones REST.
  - Los comandos de la CLI de Azure se ejecutan en modo **asm** o predeterminado.
- El tipo de recurso incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento (clásica)**.

    ![tipo clásico](./media/resource-manager-deployment-model/classic-type.png)

Todavía puede usar el portal para administrar los recursos creados a través de la implementación clásica.

Aquí se encuentran los componentes y sus relaciones para la administración de servicios de Azure.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Ventajas de usar el Administrador de recursos y grupos de recursos

El Administrador de recursos agregó el concepto del grupo de recursos. Cada recurso creado a través del Administrador de recursos existe dentro de un grupo de recursos. El modelo de implementación del Administrador de recursos ofrece varias ventajas:

- Puede implementar, administrar y supervisar todos los servicios para su solución como grupo, en lugar de controlar estos servicios individualmente.
- Puede implementar la aplicación repetidamente a lo largo del ciclo de vida de esta y tener la seguridad de que los recursos se implementan de forma coherente.
- Puede utilizar plantillas declarativas para definir la implementación.
- Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.
- Puede aplicar control de acceso a todos los servicios del grupo de recursos al integrarse de forma nativa Control de acceso basado en rol (RBAC) en la plataforma de administración.
- Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos en su suscripción.


Antes del Administrador de recursos, cada recurso creado a través de la implementación clásica no existía dentro de un grupo de recursos. Al agregarse el Administrador de recursos, todos los recursos se agregaron retroactivamente a los grupos de recursos predeterminados. Si ahora crea un recurso a través de la implementación clásica, el recurso se crea automáticamente dentro de un grupo de recursos predeterminado para el servicio, aunque no se especifique dicho grupo de recursos durante la implementación. Sin embargo, solo el hecho de existir dentro de un grupo de recursos no significa que el recurso se haya convertido al modelo del Administrador de recursos. En el caso de las máquinas virtuales, el almacenamiento y las redes virtuales, si el recurso se creó a través de la implementación clásica, debe continuar trabajando en él a través de operaciones clásicas.

Puede mover recursos a un grupo de recursos distinto y agregar nuevos recursos a un grupo de recursos existente. Por tanto, su grupo de recursos puede contener una combinación de recursos creados a través del Administrador de recursos y la implementación clásica. Esta combinación de recursos puede crear resultados inesperados porque los recursos no son compatibles con las mismas operaciones.

Mediante plantillas declarativas, puede simplificar sus scripts para la implementación. En lugar de intentar convertir los scripts existentes de la administración del servicio al Administrador de recursos, considere la posibilidad de volver a trabajar en su estrategia de implementación para aprovechar las ventajas de definir su infraestructura y configuración en la plantilla.

## Uso de etiquetas

Las etiquetas le permiten organizar de forma lógica sus recursos. Solo los recursos creados a través del Administrador de recursos son compatibles con las etiquetas. No puede aplicar etiquetas a los recursos clásicos.

Para obtener más información sobre el uso de las etiquetas en el Administrador de recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

## Operaciones admitidas para los modelos de implementación

Los recursos creados en el modelo de implementación clásica no son compatibles con las operaciones del Administrador de recursos. En algunos casos, un comando del Administrador de recursos puede recuperar información sobre un recurso creado a través de la implementación clásica, o puede realizar tareas administrativas como mover un recurso clásico a otro grupo de recursos, pero estos casos no deben dar la impresión de que el tipo es compatible con las operaciones del Administrador de recursos. Por ejemplo, suponga que tiene un grupo de recursos que contiene las máquinas virtuales que se crearon con el Administrador de recursos y el modelo clásico. Si ejecuta el siguiente comando PowerShell, verá todas las máquinas virtuales:

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

Sin embargo, si ejecuta el comando Get-AzureVM, solo conseguirá las máquinas virtuales que se crearon con el Administrador de recursos.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

En general, no debe esperar que los recursos creados a través de la implementación clásica funcionen con los comandos del Administrador de recursos.

Al trabajar con los recursos creados a través del Administrador de recursos, debe usar las operaciones del Administrador de recursos y no volver a las operaciones de administración del servicio.

## Consideraciones para las máquinas virtuales

Hay algunas consideraciones importantes al trabajar en máquinas virtuales.

- Las máquinas virtuales implementadas con el modelo de implementación clásica no pueden incluirse en una red virtual implementada con el Administrador de recursos.
- Las máquinas virtuales implementadas con el modelo de implementación del Administrador de recursos deben incluirse en una red virtual.
- Las máquinas virtuales implementadas con el modelo de implementación clásica no deben incluirse en una red virtual.

Si puede permitirse disponer de tiempo de inactividad para las máquinas virtuales, puede realizar la transición desde la implementación clásica hasta el administrador de recursos con los [scripts PowerShell de ASM2ARM](https://github.com/fullscale180/asm2arm).

Para obtener una lista de los comandos de la CLI de Azure equivalentes al realizar la transición desde la implementación clásica al Administrador de recursos, consulte [Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de VM](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md).

Para obtener más detalles acerca de la transición de recursos de procesos, almacenamiento y redes, consulte [Proveedores de procesos, redes y almacenamiento de Azure en el Administrador de recursos de Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Para obtener información sobre cómo conectar redes virtuales de diferentes modelos de implementación, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Pasos siguientes

- Para obtener información sobre cómo crear plantillas de implementación declarativas, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para ver los comandos para implementar una plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0128_2016-->