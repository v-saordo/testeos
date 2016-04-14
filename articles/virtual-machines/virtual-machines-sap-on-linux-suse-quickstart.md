<properties
   pageTitle="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure | Microsoft Azure"
   description="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="hermannd"/>

# Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure


A continuación, se muestra una lista de elementos que hay que considerar cuando se prueba SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure. En este momento, no hay ninguna declaración de compatibilidad oficial de SAP para SAP-Linux-Azure. No obstante, los clientes pueden realizar algunas pruebas, demostraciones o prototipos siempre y cuando no dependan de ningún soporte técnico SAP oficial.

La siguiente información debería ayudarle a evitar algunos obstáculos potenciales.



## Imágenes SUSE en Microsoft Azure para realizar pruebas de SAP

Para realizar pruebas de SAP en Azure, utilice solo SUSE Linux Enterprise Server (SLES) 11 SP4 y SLES 12. Se puede encontrar una imagen SUSE especial en Azure Marketplace ("SLES 11 SP3 para SAP CAL"), pero no está pensado para uso general. Está reservado para la solución de [biblioteca de aplicaciones en la nube de SAP](https://cal.sap.com/). No había ninguna opción para ocultar esta imagen del público. Así que no la utilice.

Todas las nuevas pruebas en Azure deben realizarse con el Administrador de recursos de Azure. Para buscar imágenes y versiones de SUSE SLES mediante Azure PowerShell o la interfaz de la línea de comandos de Azure (CLI), utilice los siguientes comandos. El resultado puede utilizarse, por ejemplo, para definir la imagen del sistema operativo en una plantilla JSON para implementar una nueva máquina virtual de SUSE Linux. Los siguientes comandos de PowerShell son válidos para la versión de Azure PowerShell 1.0.1 o posterior.

* Busque publicadores existentes que incluyan SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Busque ofertas existentes de SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Busque ofertas de SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Busque una versión específica de una SKU de SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Instalación de WALinuxAgent en una máquina virtual de SUSE

El agente denominado WALinuxAgent forma parte de las imágenes de SLES en Azure Marketplace. Estos son los lugares donde puede encontrar información sobre cómo instalarlo manualmente (por ejemplo, al cargar un disco duro virtual del sistema operativo de SLES desde una ubicación local):

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Las tablas de Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Conexión de discos de datos de Azure a una máquina virtual Linux de Azure

No monte nunca discos de datos de Azure en una máquina virtual Linux de Azure mediante el identificador de dispositivo. En su lugar, utilice el UUID. Tenga cuidado al utilizar, por ejemplo, herramientas gráficas para montar discos de datos de Azure. Compruebe las entradas de /etc/fstab.

El problema con el identificador del dispositivo es que puede cambiar y la máquina virtual de Azure podría bloquearse en el proceso de arranque. Puede agregar el parámetro nofail en /etc/fstab para mitigar el problema. Pero tenga en cuenta que con nofail las aplicaciones pueden usar el punto de montaje como antes y quizá escribir en el sistema de archivos raíz en caso de que no se haya montado un disco de datos externos de Azure durante el arranque.

La única excepción relacionada con el montaje a través de UUID está relacionada con conectar un disco del SO para solucionar problemas, tal como se describe en la sección siguiente.

## Solución de problemas con una máquina virtual de SUSE a la que ya no es posible tener acceso

Puede haber situaciones en las que una máquina virtual de SUSE en Azure se bloquea en el proceso de arranque (por ejemplo, un error relacionado con el montaje de los discos). El problema se puede comprobar, por ejemplo, a través de la característica de diagnóstico de arranque en el portal para las máquinas virtuales v2 ( [ver este blog](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)).

Una opción para solucionar el problema es conectar el disco del SO de la máquina virtual dañada a otra máquina virtual de SUSE en Azure y, luego, realizar los cambios correspondientes, como editar /etc/fstab o quitar reglas udev de la red, tal como se describe en la siguiente sección.

Sin embargo, hay un aspecto importante que debe considerar. La implementación de varias máquinas virtuales de SUSE desde la misma galería de imágenes de Azure (por ejemplo, SLES 11 SP4) muestra que el mismo UUID siempre montará el disco del SO. Por lo tanto, si conecta un disco del SO desde una máquina virtual distinta a través de un UUID que se implementó mediante la misma imagen de la galería de Azure generará dos UUID idénticos. Esto provoca problemas y podría significar que la máquina virtual destinada a la solución de problemas arrancará, de hecho, desde el disco del SO conectado y dañado, en lugar de hacerlo desde el original.

Existen dos posibilidades de evitar esto:

* Use una imagen de la galería de Azure distinta para la máquina virtual de solución de problemas (por ejemplo, SLES 12 en lugar de SLES 11 SP4).
* No conecte el disco del SO dañado desde otra máquina virtual mediante UUID; hágalo de una manera distinta.

## Carga de una máquina virtual de SUSE desde una instalación local a Azure

[Este artículo](virtual-machines-linux-create-upload-vhd-suse.md) describe los pasos para cargar una máquina virtual SUSE desde local a Azure.

Si desea cargar una máquina virtual sin el paso de desaprovisionamiento al final para mantener, por ejemplo, una instalación de SAP existente, así como el nombre de host, compruebe los elementos siguientes:

* Asegúrese de que el disco de sistema operativo se monte a través de UUID y no del identificador de dispositivo. Cambiar a UUID solo en /etc/fstab no es suficiente para el disco de sistema operativo. No olvide adaptar el cargador de arranque a través de YaST o mediante la edición de /boot/grub/menu.lst.
* Si utilizó el formato VHDX para el disco de sistema operativo de SUSE y lo convirtió en VHD para cargarlo en Azure, es muy probable que el dispositivo de red cambie de eth0 a eth1. Para evitar problemas al arrancar después en Azure, hay que devolverlo a eth0, tal como se describe en [este artículo](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Además de lo que se describe en el artículo le recomendamos que quite también lo siguiente:

   /lib/udev/rules.d/75-persistent-net-generator.rules

La instalación del Agente de Linux de Azure (waagent) también debería evitar cualquier posible problema siempre y cuando no haya varias tarjetas NIC.

## Implementación de una máquina virtual de SUSE en Azure

Las nuevas máquinas virtuales de SUSE se deben crear a través de archivos de plantilla JSON en el nuevo modelo del Administrador de recursos de Azure. Una vez creado el archivo de plantilla JSON, puede implementar la máquina virtual mediante el siguiente comando CLI como alternativa a PowerShell:

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` Puede encontrar más detalles acerca de los archivos de plantilla JSON en [este artículo](resource-group-authoring-templates.md) y [esta página web](https://azure.microsoft.com/documentation/templates/).

Puede encontrar más detalles acerca de la CLI y el Administrador de recursos de Azure en [este artículo](xplat-cli-azure-resource-manager.md).

## Clave de licencia y hardware de SAP

Para la certificación oficial SAP-Windows-Azure se introdujo un nuevo mecanismo para calcular la clave de hardware de SAP, que se utiliza para la licencia SAP. Había que adaptar el núcleo SAP para usarlo. Las versiones actuales de kernel de SAP para Linux no incluyen este cambio en el código. Por lo tanto, puede ocurrir que, en determinadas situaciones (por ejemplo, el cambio de tamaño de la máquina virtual de Azure), los cambios en la clave de hardware de SAP y en la licencia de SAP ya no sean válidos.

## Paquete sapconf de SUSE

SUSE ofrece un paquete denominado "sapconf" que se ocupa de un conjunto de opciones específicas de SAP. Puede encontrar más detalles sobre este paquete, lo que hace y cómo instalarlo y utilizarlo en [esta entrada de blog de SUSE](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) y en [esta entrada de blog de SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Recursos compartidos de NFS en instalaciones de SAP distribuidas

En el caso de una instalación distribuida donde desea instalar, por ejemplo, la base de datos y los servidores de aplicaciones SAP en máquinas virtuales independientes, puede compartir el directorio /sapmnt a través de Network File System (NFS). Si se produjeron problemas con los pasos de instalación después de crear el recurso compartido NFS para /sapmnt, compruebe si se ha establecido "no\_root\_squash" para el recurso compartido. Esta fue la solución en un caso de prueba interna.


## Volúmenes lógicos

El administrador de volúmenes lógicos (LVM) no está totalmente validado en Azure. Si necesita un gran volumen lógico en varios discos de datos de Azure (por ejemplo, para la base de datos SAP), debe utilizar mdadm. [Este artículo](virtual-machines-linux-configure-raid.md) describe cómo configurar RAID de Linux en Azure mediante mdadm.


## Repositorio SUSE de Azure

Si hay un problema con el acceso al repositorio SUSE de Azure estándar, hay un comando simple para restablecerlo. Esto podría suceder al crear una imagen de sistema operativo privada en una región de Azure y, después, copiar la imagen en una región diferente donde desea implementar nuevas máquinas virtuales basadas en esta imagen de sistema operativo privada. Solo ejecute los siguientes comandos dentro de la máquina virtual:

   ```
   service guestregister restart
   ```

## Escritorio Gnome

Si desea utilizar el escritorio Gnome para instalar un sistema completo de demostración SAP dentro de una máquina virtual única, incluidos la GUI de SAP, el explorador, la consola de administración de SAP, etc, esta es una sugerencia para su instalación en las imágenes de SLES Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## Compatibilidad de SAP-Oracle en Linux en la nube

Hay una restricción de soporte técnico de Oracle en Linux en entornos virtualizados. Este es un tema general, no uno específico de Azure. No obstante, es importante entenderlo. SAP no admite Oracle en SUSE ni Red Hat en una nube pública como Azure. Los clientes deben ponerse en contacto con Oracle directamente para tratar este tema.

<!---HONumber=AcomDC_0218_2016-->