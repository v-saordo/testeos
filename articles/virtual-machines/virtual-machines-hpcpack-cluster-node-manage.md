<properties
 pageTitle="Administrar los nodos de ejecución del clúster de HPC Pack | Microsoft Azure"
 description="Obtenga información sobre las herramientas de script de PowerShell para agregar, quitar, iniciar y detener los nodos de cálculo del clúster de HPC Pack en Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="01/08/2016"
 ms.author="danlep"/>

# Administrar el número y la disponibilidad de los nodos de ejecución en un clúster de HPC Pack en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Si ha creado un clúster de HPC Pack en las máquinas virtuales de Azure, puede que desee maneras de agregar, quitar iniciar (aprovisionar) o detener (desaprovisionar) con facilidad un número de máquinas virtuales de nodo de ejecución en el clúster. Para realizar estas tareas, ejecute los scripts de Azure PowerShell que están instalados en el nodo principal de la máquina virtual (comenzando por HPC Pack 2012 R2 Update 1). Estos scripts le ayudan a controlar el número y la disponibilidad de los recursos de clúster de HPC Pack para que pueda controlar los costos.

>[AZURE.NOTE]Los scripts se encuentra en la carpeta %CCP\_HOME%bin en el nodo principal. Debe ejecutar cada uno de los scripts como administrador.

## Requisitos previos

* **Clúster de HPC Pack en máquinas virtuales de Azure**: cree un clúster de HPC Pack en el modelo de implementación clásico (Administración de servicios) con al menos HPC Pack 2012 R2 Update 1. Por ejemplo, puede automatizar la implementación mediante la imagen de la máquina virtual de HPC Pack en Azure Marketplace y un script de Azure PowerShell. Para obtener información y ver los requisitos previos, vea [Crear un clúster de HPC con el script de implementación de HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

* **Archivo de configuración de publicación o certificado de administración de Azure**: debe llevar a cabo una de las siguientes acciones en el nodo principal:

    * **Importar el archivo de configuración de publicación de Azure**. Para ello, ejecute los siguientes cmdlets de Azure PowerShell en el nodo principal:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configure el certificado de administración de Azure en el nodo principal**. Si tiene el archivo .cer, impórtelo en el almacén CurrentUser\\My certificate y luego ejecute el siguiente cmdlet de Azure PowerShell para su entorno de Azure (AzureCloud o AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## Agregar máquinas virtuales de nodo de ejecución

Agregue nodos de ejecución con el script **Add-HpcIaaSNode.ps1**.

### Sintaxis
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### Parámetros

* **ServiceName**: nombre del servicio en la nube al que se agregarán las nuevas máquinas virtuales de nodos de ejecución.

* **ImageName**: nombre de la imagen de máquina virtual de Azure, que puede obtenerse mediante el Portal de Azure clásico o el cmdlet de Azure PowerShell **Get-AzureVMImage**. La imagen debe cumplir los siguientes requisitos:

    1. Debe haber instalado un sistema operativo Windows.

    2. HPC Pack debe estar instalado en el rol del nodo de ejecución.

    3. La imagen debe ser una imagen privada en la categoría de usuario, no una imagen de máquina virtual de Azure pública.

* **Quantity**: número de máquinas virtuales de nodo de ejecución que se van a agregar.

* **InstanceSize**: tamaño de las máquinas virtuales de nodos de ejecución.

* **DomainUserName**: nombre de usuario de dominio, que se usará para unir las nuevas máquinas virtuales al dominio.

* **DomainUserPassword**: contraseña del usuario de dominio.

* **NodeNameSeries** (opcional): modelo de nomenclatura para los nodos de ejecución. El formato debe ser & lt;*Nombre\_raíz*& gt; & lt;*Número\_inicio*& gt; %. Por ejemplo, MyCN%10% se refiere a una serie de nombres de nodos de ejecución a partir de MyCN11. Si no se especifica, el script usa la serie de nomenclatura de nodos configurados clúster de HPC.

### Ejemplo

En el ejemplo siguiente se agregan 20 máquinas virtuales grandes de nodos de ejecución de gran tamaño en el servicio en la nube *hpcservice1*, según la imagen de máquina virtual *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## Quitar máquinas virtuales de nodo de ejecución

Quite nodos de ejecución con el script **Remove-HpcIaaSNode.ps1**.

### Sintaxis

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### Parámetros

* **Name**: nombres de los nodos de clúster que se van a quitar. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los parámetros **Name** y **Node**.

* **Node**: objeto HpcNode para los nodos que se van quitar, que se puede obtener a través del cmdlet de HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.

* **DeleteVHD** (opcional): configuración para eliminar los discos asociados para las máquinas virtuales que se quitan.

* **Force** (opcional): configuración para forzar nodos HPC sin conexión antes de quitarlos.

* **Confirm** (opcional): solicitud de confirmación antes de ejecutar el comando.

* **WhatIf**: configuración para describir lo que sucedería si ejecutara el comando sin ejecutarlo realmente.

### Ejemplo

En el ejemplo siguiente se fuerzan nodos sin conexión con nombres que comienzan por *HPCNode-CN -* y luego quita los nodos y sus discos asociados.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## Iniciar máquinas virtuales de nodos de ejecución

Inicie nodos de ejecución con el script **Start-HpcIaaSNode.ps1**.

### Sintaxis

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### Parámetros

* **Name**: nombres de los nodos de clúster que se van a iniciar. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los dos parámetros, **Name** y **Node**.

* **Node**: objeto HpcNode para los nodos que se van a iniciar, que se puede obtener mediante el cmdlet de HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.

### Ejemplo

En el ejemplo siguiente se inician nodos con nombres que comienzan por *HPCNode-CN-*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## Detener máquinas virtuales de nodo de ejecución

Detenga nodos de ejecución con el script **Stop-HpcIaaSNode.ps1**.

### Sintaxis

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### Parámetros


* **Name**: nombres de los nodos de clúster que se van a detener. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los dos parámetros, **Name** y **Node**.

* **Node**: objeto HpcNode para los nodos que se van a detener, que se puede obtener mediante el cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) de HPC PowerShell. El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.

* **Force** (opcional): configuración para forzar los nodos HPC sin conexión antes de detenerlos.

### Ejemplo

En el ejemplo siguiente se fuerzan nodos sin conexión con nombres que comienzan por *HPCNode-CN-* y luego se detienen los nodos.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## Pasos siguientes

* Si busca una manera de aumentar o reducir automáticamente los nodos de clúster según la carga de trabajo actual de los trabajos y las tareas en el clúster, consulte [Escalar automáticamente los recursos de proceso de Azure hacia arriba y abajo en un clúster de HPC Pack según la carga de trabajo del clúster](virtual-machines-hpcpack-cluster-node-autogrowshrink.md).

<!---HONumber=AcomDC_0114_2016-->