<properties
	pageTitle="Inyección de datos personalizados en máquinas virtuales | Microsoft Azure"
	description="Este tema describe cómo inyectar datos personalizados en una máquina virtual de Azure cuando se crea la instancia y cómo localizar dichos datos en Windows o Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="rasquill"/>


#Inyección de datos personalizados en una máquina virtual de Azure

La inyección de un script o de otros datos en una máquina virtual de Azure cuando se está aprovisionando es un escenario muy común, independientemente de si el sistema operativo es Windows o una distribución de Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este tema se describe cómo:

- Inyectar datos en una máquina virtual de Azure cuando se está aprovisionando.

- Recuperarlos tanto para Windows como para Linux.

- Usar herramientas especiales disponibles en algunos sistemas para detectar y administrar datos personalizados automáticamente.

> [AZURE.NOTE]En este artículo se describe cómo se pueden insertar datos personalizados mediante una máquina virtual creada con la API de Administración de servicios de Azure. Para ver cómo se usa la API de Administración de recursos de Azure, consulte [la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Inyección de datos personalizados en su máquina virtual de Azure

Esta característica solamente se admite actualmente en la [interfaz de la línea de comandos de Azure](https://github.com/Azure/azure-xplat-cli). Aunque puede usar cualquiera de las opciones para el comando `azure vm create`, a continuación se muestra una perspectiva muy básica.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Uso de datos personalizados en la máquina virtual

+ Si la máquina virtual de Azure es una máquina virtual Windows, el archivo de datos personalizado se guarda en `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Aunque se codificara con base64 para transferirla del equipo local a la nueva máquina virtual, se descodifica automáticamente y se puede abrir o usar de inmediato.

   >[AZURE.NOTE]Si el archivo existe se sobrescribe. La seguridad en el directorio se establece en **Sistema: Control total** y **Administradores: Control total**.

+ Si la máquina virtual de Azure es una máquina virtual Linux, el archivo de datos personalizados se ubicará en los dos lugares siguientes. Los datos estarán codificados con base64, por lo que deberá descifrarlos en primer lugar.

    + En `/var/lib/waagent/ovf-env.xml`
    + En `/var/lib/waagent/CustomData`



## Cloud-init en Azure

Si la máquina virtual de Azure procede de una imagen de Ubuntu o CoreOS, puede usar CustomData para enviar una cloud-config a cloud-init. O bien, si el archivo de datos personalizados es un script, cloud-init puede simplemente ejecutarlo.

### Ubuntu Cloud Images

En la mayoría de las imágenes de Linux de Azure, debe modificar "/etc/waagent.conf" para configurar el disco de recursos temporal y el archivo de intercambio. Consulte la [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md) para obtener más información.

Sin embargo, en Ubuntu Cloud Images, debe usar cloud-init para configurar el disco de recursos (es decir, el disco "efímero") y la partición de intercambio. Consulte la siguiente página en la wiki de Ubuntu para obtener más detalles: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes: uso de cloud-init

Para obtener más información, consulte la [documentación de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referencia de la API de REST de administración de servicios: Agregar rol](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaz de línea de comandos de Azure](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_1217_2015-->