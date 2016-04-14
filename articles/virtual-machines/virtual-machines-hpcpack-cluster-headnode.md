<properties
 pageTitle="Creación de un nodo principal de HPC Pack en una máquina virtual de Azure | Microsoft Azure"
 description="Aprenda a usar el Portal de Azure y el modelo de implementación del Administrador de recursos para crear un nodo principal de HPC Pack en una máquina virtual de Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


En este artículo, se muestra cómo usar una [imagen de máquina virtual de Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) en Azure Marketplace para crear el nodo principal de un clúster HPC usando el portal de Azure. Esta imagen de máquina virtual de HPC Pack se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstalado. Utilice este nodo principal para una implementación de prueba de concepto de HPC Pack en Azure. A continuación, puede agregar recursos de proceso al clúster para ejecutar cargas de trabajo HPC.


![Nodo principal de HPC Pack][headnode]

>[AZURE.TIP]Para una implementación de producción de un clúster HPC Pack completo en Azure, se recomienda utilizar un método automatizado, como el [script de implementación de HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md) o una plantilla del Administrador de recursos, como la plantilla del [clúster de HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Consulte [Opciones de clúster de HPC Pack en Azure](virtual-machines-hpcpack-cluster-options.md) para ver más plantillas.

## Consideraciones de planeación

* **Dominio de Active Directory**: El nodo principal de HPC Pack debe estar unido a un dominio de Active Directory en Azure antes de iniciar los servicios HPC en la máquina virtual. Como se muestra en este artículo, para realizar una prueba de implementación del concepto, puede promover la máquina virtual creada para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC. Otra opción es implementar un controlador de dominio independiente y el bosque en Azure al que se une la máquina virtual.

* **Red virtual**: Como se muestra en este artículo, al implementar el nodo principal de HPC Pack utilizando el modelo de implementación del Administrador de recursos en el portal de Azure, hay que especificar o crear una red virtual (VNet) de Azure. Necesitará usar la red virtual más adelante para agregar máquinas virtuales de nodos de proceso de clúster al clúster de HPC, así como para unir el nodo principal a un dominio de Active Directory existente.

    
## Pasos para crear el nodo principal

Estos son los pasos generales para crear una máquina virtual de Azure para el nodo principal de HPC Pack mediante el modelo de implementación del Administrador de recursos en el portal de Azure.


1. Si desea crear un nuevo bosque de Active Directory en Azure con máquinas virtuales de controlador de dominio independientes, una opción es usar una plantilla del [Administrador de recursos](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para realizar simplemente una implementación de prueba de concepto, se puede omitir esto y configurar la propia máquina virtual del nodo principal como un controlador de dominio, tal y como se describe en un paso posterior.
    
2. En el [Portal de Azure](https://portal.azure.com), elija la imagen de HPC Pack 2012 R2 en Azure Marketplace. Para ello, haga clic en **Nuevo** y busque **HPC Pack** en Marketplace. Elija **HPC Pack 2012 R2 en Windows Server 2012 R2**.

3. En la página **HPC Pack 2012 R2 en Windows Server 2012 R2**, elija el modelo de implementación **Administrador de recursos** y haga clic en **Crear**.

    ![Imagen de HPC Pack][marketplace]

4. Use el portal para definir la configuración y crear la máquina virtual. Para conocer los pasos detallados, siga el tutorial [Creación de una máquina virtual de Windows en el Portal de Azure](virtual-machines-windows-tutorial.md). Normalmente, en la primera implementación se pueden aceptar muchos de los valores predeterminados o recomendados.

    **Consideraciones sobre las redes virtuales**

   * Si crea una nueva red virtual en **Configuración**, especifique un intervalo de direcciones de red privada como 10.0.0.0/16 y un intervalo de direcciones de subred como 10.0.0.0/24.
    
4. Una vez que la máquina virtual esté creada y en ejecución, podrá [conectarla](virtual-machines-log-on-windows-server-preview.md). 

5. Una la máquina virtual a un bosque de dominio existente o cree un nuevo bosque de dominio en la propia máquina virtual.

    **Consideraciones sobre los dominios de Active Directory**

    * Si ha creado la máquina virtual en una red virtual de Azure con un bosque de dominio existente, utilice las herramientas estándar del Administrador de servidores o de Windows PowerShell para unirla al bosque de dominio. Después, reinicie.

    * Si la máquina virtual se creó en una red virtual sin un bosque de dominio existente, configúrela como un controlador de dominio. Para ello, use las herramientas estándar de Administrador de servidores o Windows PowerShell para instalar y configurar el rol de Servicios de dominio de Active Directory. Para ver los pasos detallados, consulte [Instalar un nuevo bosque de Active Directory de Windows Server 2012 (nivel 200)](https://technet.microsoft.com/library/jj574166.aspx).

5. Cuando la máquina virtual se esté ejecutando y esté unida a un bosque de Active Directory, inicie los servicios de HPC Pack en el nodo principal. Para ello, siga estos pasos:

    a. Conéctese a la máquina virtual con una cuenta de dominio que sea miembro del grupo Administradores local. Por ejemplo, puede usar la cuenta de administrador que se configura al crear la máquina virtual del nodo principal.

    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Los servicios de HPC Pack pueden tardar algunos minutos en iniciarse.

    Para las opciones adicionales de configuración del nodo principal, escriba `get-help HPCHNPrepare.ps1`.


## Pasos siguientes

* Ahora puede trabajar con el nodo principal del clúster HPC Pack. Por ejemplo, inicie el Administrador de clústeres de HPC y complete la [lista de tareas pendientes de implementación](https://technet.microsoft.com/library/jj884141.aspx).
* Agregue [nodos de ráfaga de Azure](virtual-machines-hpcpack-cluster-node-burst.md) en un servicio en la nube para aumentar la capacidad de proceso del clúster a petición. 

* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144) de HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0211_2016-->