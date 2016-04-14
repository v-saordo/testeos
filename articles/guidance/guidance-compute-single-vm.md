<properties
   pageTitle="Ejecución de VM (Windows) | Plano técnico | Microsoft Azure"
   description="Cómo ejecutar una única VM en Azure, teniendo en cuenta la escalabilidad, resistencia, manejabilidad y seguridad."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Ejecución de una única VM de Windows en Azure

En este artículo se describe un conjunto de procedimientos probados para ejecutar una única VM de Windows en Azure, teniendo en cuenta la escalabilidad, resistencia, manejabilidad y seguridad.

> [AZURE.WARNING] No hay ningún contrato de nivel de servicio de tiempo de actividad para VM únicas en Azure. Use esta configuración para desarrollo y prueba pero no como implementación de producción.

Azure tiene dos modelos de implementación diferentes: el [Administrador de recursos][resource-manager-overview] y el clásico. En este artículo se utiliza el Administrador de recursos, que Microsoft recomienda para las implementaciones nuevas. Hay varias maneras de usar el Administrador de recursos, incluido el [Portal de Azure][azure-portal], [Azure PowerShell][azure-powershell], comandos de la [CLI de Azure][azure-cli] o [plantillas del Administrador de recursos][arm-templates]. En este artículo se incluye un ejemplo en el que se usa la CLI de Azure.

![IaaS: una única VM](media/guidance-compute-single-vm.png)

El aprovisionamiento de una única VM en Azure implica más piezas móviles que la propia VM de núcleo en sí. Existen elementos de proceso, red y almacenamiento.

- **Grupo de recursos**. Cree un [grupo de recursos][resource-manager-overview] para mantener los recursos de esta VM. Un_ grupo de recursos_ es un contenedor que incluye recursos relacionados.

- **VM**. Puede aprovisionar una VM desde una lista de imágenes publicadas o desde un archivo de disco duro virtual cargado en Almacenamiento de blobs de Azure.

- **Disco del sistema operativo.** El disco del sistema operativo es un disco duro virtual almacenado en [Almacenamiento de Azure][azure-storage]. Esto significa que persiste incluso si el equipo host deja de funcionar.

- **Disco temporal.** La VM se crea con un disco temporal (la unidad `D:` de Windows). Este disco se almacena en una unidad física del equipo host. _No_ se guarda en Almacenamiento de Azure y podría desaparecer durante los reinicios y otros eventos del ciclo de vida de la VM. Use este disco solo para datos temporales, como archivos de paginación o de intercambio.

- **Discos de datos.** Un [disco de datos][data-disk] es un disco duro virtual persistente para los datos de la aplicación. Los discos de datos se almacenan en Almacenamiento de Azure, como el disco del sistema operativo.

- **Red virtual y subred.** Cada VM en Azure se implementa en una red virtual, que se divide en subredes.

- **Dirección IP pública.** Es necesaria una dirección IP pública para comunicarse con la VM, por ejemplo, a través de Escritorio remoto (RDP).

- **Grupo de seguridad de red (NSG).** El [NSG][nsg] se utiliza para permitir o denegar el tráfico de red a la VM. Las reglas de NSG predeterminadas deniegan todo el tráfico entrante de Internet.

- **Tarjeta de interfaz de red (NIC).** La NIC permite que la VM se comunique con la red virtual.

- **Diagnóstico.** El registro de diagnóstico es fundamental para administrar y solucionar problemas de la VM.

## Recomendaciones de VM

- Al mover una carga de trabajo existente a Azure, seleccione el [tamaño de VM][virtual-machine-sizes] que mejor coincida con los servidores locales. Se recomiendan las series DS y GS, que pueden usar Almacenamiento premium para cargas de trabajo intensivas de E/S.

    - Si la carga de trabajo no requiere acceso a disco de alto rendimiento y baja latencia, considere la posibilidad de elegir otros tamaños de VM de nivel estándar, como una serie A o D.

- Cuando aprovisiona la VM y otros recursos, debe especificar una ubicación. Por lo general, se recomienda elegir una ubicación más cercana a los usuarios internos o clientes. Sin embargo, no todas las SKU de VM pueden estar disponibles en todas las ubicaciones. Para más información, consulte [Servicios por región][services-by-region].

- Para información acerca de cómo elegir una imagen de VM, consulte [Selección y navegación por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure][select-vm-image].

## Recomendaciones de discos y almacenamiento

Se recomienda el [Almacenamiento premium][premium-storage], para un mejor rendimiento de E/S de discos. No obstante, tenga en cuenta que Almacenamiento premium requiere VM de la serie DS o GS.

- Para Almacenamiento premium, el costo se basa en el tamaño del disco de aprovisionamiento. Las E/S por segundo y el rendimiento (es decir, la velocidad de transferencia de datos) también dependen del tamaño del disco, por lo que al aprovisionar un disco, debería tener en cuenta los tres factores (capacidad, E/S por segundo y rendimiento).

- En el caso de Almacenamiento estándar, el costo se basa en la cantidad de datos escritos en el disco. Por lo tanto, se recomienda aprovisionar el tamaño máximo (1023 GB). Sin embargo, debe asegurarse de utilizar el formato rápido al dar formato a los discos. Un formato de disco completo escribe ceros en el disco, lo que provoca el uso de almacenamiento real. Consulte [Precios de Almacenamiento de Azure][storage-price].

- Si elige Almacenamiento estándar, se recomienda el almacenamiento con redundancia geográfica (GRS) porque se mantiene incluso ante un apagón regional completo o un desastre del cual la región primaria no se puede recuperar.

- Agregue uno o más discos de datos. Cuando se crea un nuevo disco duro virtual, no tiene formato. Inicie sesión en la VM para dar formato al disco.

- Si tiene un gran número de discos de datos, tenga en cuenta los límites de E/S totales de la cuenta de almacenamiento. Para más información, consulte [Límites de discos de máquinas virtuales][vm-disk-limits].

- Para obtener el mejor rendimiento, cree una cuenta de almacenamiento independiente para contener los registros de diagnóstico. Una cuenta de almacenamiento con redundancia local (LRS) estándar es suficiente para este tipo de registros.

## Recomendaciones de red

- Para una única VM, cree una red virtual con una sola subred. Cree igualmente un NSG y una dirección IP pública.

- La dirección IP pública puede ser dinámica o estática. El valor predeterminado es dinámica.

    - Reserve una [dirección IP estática][static-ip] si necesita una dirección IP fija que no cambie, por ejemplo, si tiene que crear un registro D en DNS o necesita que se permita la dirección IP.

    - De forma predeterminada, la dirección IP no tiene un nombre de dominio completo (FQDN). Para más información, consulte [Crear un nombre de dominio completo en el Portal de Azure][fqdn].

- Asigne una NIC y asóciela a la dirección IP, subred y NSG.

- Las reglas NSG predeterminadas no permiten RDP. Para habilitar RDP, agregue una regla al NSG que permita el tráfico entrante al puerto TCP 3389.

## Escalabilidad

Puede escalar una VM vertical u horizontalmente cambiando su tamaño. El siguiente comando de la CLI de Azure ajusta el tamaño de una VM:

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

Cambiar el tamaño de la VM desencadenará un reinicio del sistema y volverá a asignar el sistema operativo existente, así como los discos de datos, después del reinicio. Se perderá todo lo que haya en el disco temporal. La opción `--boot-diagnostics-storage-uri` habilita el [diagnóstico de arranque][boot-diagnostics] para que registre cualquier error relacionado con el inicio de sesión.

Es posible que no pueda escalar desde una familia SKU a otra (por ejemplo, desde una serie A a una serie G). Si desea obtener una lista de los tamaños disponibles para una VM existente, utilice el siguiente comando de la CLI:

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Para escalar a un tamaño que no aparece en la lista, debe eliminar la instancia de VM y crear una nueva. La eliminación de una VM no elimina los discos duros virtuales.

## Disponibilidad

- No hay ningún contrato de nivel de servicio de disponibilidad para VM únicas. Para lograr un contrato de nivel de servicio sobre disponibilidad, debe implementar varias VM en un conjunto de disponibilidad.

- La VM puede verse afectada por un [mantenimiento planeado][planned-maintenance] o un [mantenimiento no planeado][manage-vm-availability]. Puede usar [registros de reinicio de VM][reboot-logs] para determinar si se produjo un reinicio de VM por mantenimiento planeado.

- No coloque una única VM en un conjunto de disponibilidad. Al colocar la VM en un conjunto de disponibilidad, se indica a Azure que trate la VM como parte de un conjunto de instancias múltiples y no recibirá advertencia ni notificación avanzada alguna sobre reinicios de mantenimientos planeados.

- Los discos duros virtuales están respaldados por [Almacenamiento de Azure][azure-storage], que se replica para su disponibilidad y durabilidad.

- Para protegerse de la pérdida accidental de datos durante las operaciones normales (por ejemplo, debido a errores de usuario), debe implementar igualmente copias de seguridad a un momento dado mediante [instantáneas de blobs][blob-snapshot] u otra herramienta.

## Manejabilidad

- Ejecute el siguiente comando de la CLI para habilitar los diagnósticos de VM:
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Este comando habilita las métricas de mantenimiento básicas, los registros de infraestructura de diagnóstico y los diagnósticos de arranque. Para más información, consulte [Habilitación de supervisión y diagnóstico][enable-monitoring].

- Utilice la extensión [Colección de registros de Azure][log-collector] para recopilar registros y cargarlos en Almacenamiento de Azure.

- Azure hace una distinción entre estados "Detenidos" y "Asignación anulada". Se le cobrará cuando el estado de la VM sea "Detenido". No se le cobrará cuando la asignación de la VM esté anulada. (Consulte [Preguntas más frecuentes sobre Máquinas virtuales de Azure con el modelo de implementación clásica][vm-faq].)

    Utilice el siguiente comando de la CLI para anular la asignación de una VM:
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Nota: El botón **Detener** en el Portal de Azure también anula la asignación de la VM. Sin embargo, si cierra desde dentro de Windows (a través de RDP), la VM se detiene pero _no_ se anula su asignación, por lo que se le seguirá cobrando.

- Si elimina una VM, no se eliminarán los discos duros virtuales. Esto significa que puede eliminar de forma segura la VM sin perder datos. Sin embargo, se le seguirá cobrando por el almacenamiento. Para eliminar el disco duro virtual, elimine el archivo desde [Almacenamiento de blobs][blog-storage].

- Para cambiar el tamaño del disco del sistema operativo, descargue el archivo .vhd y utilice una herramienta como [Resize-VHD][Resize-VHD] para cambiar el tamaño del disco duro virtual. Cargue el disco duro virtual con el tamaño cambiado a Almacenamiento de blobs, a continuación, elimine la instancia de VM y aprovisione una nueva instancia que utilice el disco duro virtual con el tamaño cambiado.


## Seguridad

- Use [Azure Security Center][security-center] para obtener una visión central del estado de la seguridad de sus recursos en Azure. El Centro de seguridad supervisa los posibles problemas de seguridad como actualizaciones del sistema, antimalware y las ACL de punto de conexión, a la vez que proporciona una imagen completa del estado de seguridad de su implementación. **Nota:** En el momento de redactar este texto, el Centro de seguridad sigue aún en versión preliminar.

- Utilice [control de acceso basado en rol][rbac] (RBAC) para definir los miembros de su equipo de DevOps que pueden administrar los recursos de Azure (VM, redes, etc.) que implemente.

- Considere la posibilidad de instalar [extensiones de seguridad][security-extensions].

- Utilice [Cifrado de discos de Azure][disk-encryption] para cifrar los discos de datos y del sistema operativo. **Nota:** En el momento de redactar este texto, el Cifrado de discos de Azure sigue aún en versión preliminar.

## Solución de problemas

- Para restablecer la contraseña de administrador local, ejecute el comando `vm reset-access` de la CLI de Azure.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Si la VM entra en un estado que no permite el arranque, use [Diagnósticos de arranque][boot-diagnostics] para diagnosticar errores de arranque.

- Mire los [registros de auditoría][audit-logs] para ver las acciones de aprovisionamiento y otros eventos de VM.

## Comandos de la CLI de Azure (ejemplo)

El siguiente script por lotes de Windows ejecuta los comandos de la [CLI de Azure][azure-cli] para implementar una única instancia de VM, así como los recursos de red y almacenamiento relacionados, tal como se muestra en el diagrama anterior.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/es-ES/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/es-ES/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/es-ES/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/es-ES/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/es-ES/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/es-ES/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/es-ES/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->