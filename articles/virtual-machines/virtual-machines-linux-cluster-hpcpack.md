<properties
 pageTitle="Uso de máquinas virtuales de proceso de Linux en un clúster de HPC Pack | Microsoft Azure"
 description="Aprenda a generar scripts de implementación de un clúster de HPC Pack en Azure que contiene un nodo principal que ejecuta Windows Server con nodos de proceso de Linux."
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
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure

En este artículo se muestra cómo usar un script de Azure PowerShell para configurar un clúster de Microsoft HPC Pack en Azure que contenga un nodo principal en el que se ejecuta Windows Server y varios nodos de proceso en los que se ejecuta una distribución de Linux. También le mostramos varias maneras de mover archivos de datos a los nodos de cálculo de Linux. Puede usar este clúster para ejecutar cargas de trabajo de Linux HPC en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En un nivel alto el diagrama siguiente muestra el clúster de HPC Pack que creará.

![Clúster HPC con nodos de Linux][scenario]

## Implementación de un clúster de HPC Pack con nodos de proceso de Linux

Usará el script de implementación de Microsoft HPC Pack IaaS (**New-HpcIaaSCluster.ps1**) para automatizar la implementación del clúster en Servicios de infraestructura de Azure (IaaS). Este script de PowerShell de Azure usa una imagen de VM de HPC Pack en Azure Marketplace para una implementación rápida y proporciona un conjunto completo de parámetros de configuración para que la implementación sea sencilla y flexible. El script implementa la red virtual de Azure, las cuentas de almacenamiento, los servicios en la nube, el controlador de dominio, el servidor de base de datos de SQL Server independiente opcional, el nodo principal del clúster, los nodos de proceso, los nodos de agente, los nodos de PaaS de Azure ("ráfaga") y los nodos de proceso de Linux (compatibilidad con Linux introducida en [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx)).

Para obtener información general sobre las opciones de implementación de clústeres de HPC Pack, consulte [Getting started guide for HPC Pack 2012 R2 and HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx) y [Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) en Azure con Microsoft HPC Pack](virtual-machines-hpcpack-cluster-options.md).

### Requisitos previos

* **Equipo cliente**: necesitará un equipo cliente basado en Windows para ejecutar el script de implementación del clúster.

* **Azure PowerShell**: [instale y configure Azure PowerShell](../powershell-install-configure.md) (versión 0.8.10 o posterior) en el equipo cliente.

* **Script de implementación de IaaS de HPC Pac **: descargue y desempaquete la versión más reciente del script desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Puede comprobar la versión del script ejecutando `New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.4.0 o posterior del script.

* **Suscripción de Azure**: puede usar una suscripción en el servicio Azure Global o Azure China. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cuota de núcleos**: tal vez tenga que aumentar la cuota de núcleos, especialmente si decide implementar varios nodos de clúster con tamaños de máquina virtual de múltiples núcleos. Por ejemplo, en este artículo, necesitará al menos 12 núcleos. Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

### Creación del archivo de configuración

El script de implementación de HPC Pack IaaS usa un archivo de configuración XML como entrada que describe la infraestructura del clúster HPC. Para implementar un clúster pequeño que consta de un nodo principal y dos nodos de proceso de Linux, sustituya los valores para el entorno en el siguiente archivo de configuración de ejemplo. Para obtener más información sobre el archivo de configuración, vea el archivo Manual.rtf en la carpeta de script y [Creación de un clúster de HPC de Windows con el script de implementación de IaaS de HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

A continuación se incluyen descripciones breves de los elementos en el archivo de configuración.

* **IaaSClusterConfig**: elemento raíz del archivo de configuración.

* **Suscripción**: suscripción de Azure que se usa para implementar el clúster de HPC Pack. Use el comando siguiente para asegurarse de que el nombre de la suscripción de Azure está configurado y es único en el equipo cliente. En este ejemplo, se usa la suscripción de Azure "Suscripción 1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]O bien, use el identificador de suscripción para especificar la suscripción que quiere usar. Vea el archivo Manual.rtf en la carpeta de script.

* **StorageAccount**: todos los datos persistentes para el clúster de HPC Pack se almacenarán en la cuenta de almacenamiento especificada (allvhdsje en este ejemplo). Si no existe la cuenta de almacenamiento, el script la creará en la región especificada en **Ubicación**.

* **Ubicación**: región de Azure donde implementará el clúster de HPC Pack (este de Japón en este ejemplo).

* **VNet**: configuración de la red virtual y la subred donde se creará el clúster HPC. Puede crear la red virtual y subred antes de ejecutar este script, o el script crea una red virtual con espacio de direcciones 192.168.0.0/20 y una subred con espacio de direcciones 192.168.0.0/23. En este ejemplo, el script crea la red virtual centos7rdmavnetje y la subred CentOS7RDMACluster.

* **Domain**: configuración de dominio de Active Directory para el clúster de HPC Pack. Todas las máquinas virtuales de Windows creadas por el script se unirán al dominio. Actualmente, el script admite tres opciones de dominio: ExistingDC, NewDC y HeadNodeAsDC. En este ejemplo, se configura el nodo principal como el controlador de dominio con un nombre de dominio completo de hpc.local.

* **Database**: base de datos de configuración para el clúster de HPC Pack. Actualmente, el script admite tres opciones de base de datos: ExistingDB, NewRemoteDB y LocalDB. En este ejemplo, se crea una base de datos local en el nodo principal.

* **HeadNode**: configuración del nodo principal de HPC Pack. En este ejemplo, se crea un nodo principal de tamaño A7 denominado CentOS7RDMA-HN en el servicio en la nube centos7rdma-je. Para admitir el envío de trabajos de HPC desde equipos cliente remotos (no unidos a un dominio), el script habilita la API de REST del programador de trabajos y el portal web de HPC.

* **LinuxComputeNodes**: configuración de los nodos de proceso de HPC Pack Linux. En este ejemplo, se crean dos nodos de proceso de tamaño A7 CentOS 7 Linux (CentOS7RDMA-LN1 y CentOS7RDMA-LN2) en el servicio en la nube centos7rdma-je.

### Consideraciones adicionales sobre los nodos de proceso de Linux

* Actualmente, HPC Pack admite las siguientes distribuciones de Linux para nodos de proceso: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 y SUSE Linux Enterprise Server 12.

* En el ejemplo de este artículo, se usa una versión específica de CentOS que está disponible en Azure Marketplace para crear el clúster. Si desea usar otras imágenes disponibles, use el cmdlet **get-azurevmimage** de Azure PowerShell para encontrar la que necesita. Por ejemplo, para ver una lista de todas las imágenes de CentOS 7.0, ejecute el comando siguiente:

    ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Busque la que necesite y reemplace el valor **ImageName** en el archivo de configuración.

* Hay disponibles imágenes de Linux que admiten conectividad de RDMA para VM de tamaño A8 y A9. Si especifica una imagen con controladores de Linux RDMA instalados y habilitados, el script de implementación de HPC Pack IaaS los implementará. Por ejemplo, especifique el nombre de imagen `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` para el SUSE Linux Enterprise Server 12 actual: optimizado para la imagen de proceso de alto rendimiento en el Marketplace.

* Para habilitar Linux RDMA en las máquinas virtuales Linux creadas a partir de imágenes admitidas para ejecutar trabajos de MPI, instale y configure una biblioteca específica de MPI en los nodos de Linux después de implementar el clúster según las necesidades de su aplicación. Para ver un ejemplo, consulte [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Asegúrese de implementar todos los nodos de Linux RDMA dentro de un servicio para que la conexión a la red de RDMA funcione entre los nodos.


## Ejecución del script de implementación de HPC Pack IaaS

1. Abra la consola de PowerShell en el equipo cliente como administrador.

2. Cambie el directorio a la carpeta de script (E:\\IaaSClusterScript en este ejemplo).

    ```
    cd E:\IaaSClusterScript
    ```

3. Ejecute el comando siguiente para implementar el clúster de HPC Pack. En este ejemplo se supone que el archivo de configuración se encuentra en E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    El script genera un archivo de registro automáticamente porque el parámetro **-LogFile** no está especificado. Los registros no se escriben en tiempo real, pero se recopilan al final de la validación y la implementación, por lo que si se detiene el proceso de PowerShell mientras se ejecuta el script, se perderán algunos registros.

    a. Como **AdminPassword** no se ha especificado en el comando anterior, se le pedirá que escriba la contraseña del usuario *MyAdminName*.

    b. A continuación, el script empieza a validar el archivo de configuración. Tarda desde decenas de segundos hasta varios minutos según la conexión de red.

    ![Validación][validate]

    c. Una vez pasadas las validaciones, el script enumera los recursos que se crearán para el clúster HPC. Escriba *Y* para continuar.

    ![Recursos][resources]

    d. El script empieza a implementar el clúster de HPC Pack y completa la configuración sin más pasos manuales. Esto puede tardar varios minutos.

    ![Implementación][deploy]

4. Una vez finalizada correctamente la configuración, inicie el Escritorio remoto para el nodo principal y abra el Administrador de clústeres HPC para comprobar el estado del clúster de HPC Pack. Puede administrar y supervisar los nodos de proceso de Linux del mismo modo que trabaja con nodos de proceso de Windows. Por ejemplo, verá los nodos de Linux enumerados en Administración de nodos.

    ![Administración de nodos][management]

    También verá los nodos de Linux en la vista Mapa térmico.

    ![Mapa térmico][heatmap]

## Cómo mover los datos en un clúster con nodos de Linux

Hay varias opciones para mover datos entre los nodos de Linux y el nodo principal de Windows del clúster. A continuación se muestran tres métodos comunes.

* **Archivo de Azure**: expone un recurso compartido de archivos para almacenar archivos de datos en Almacenamiento de Azure. Los nodos de Windows y los nodos de Linux pueden montar un recurso compartido de archivo de Azure como una unidad o carpeta al mismo tiempo incluso si se implementan en diferentes redes virtuales.

* **Recurso compartido de SMB del nodo principal**: monta una carpeta compartida del nodo principal en los nodos de Linux.

* **Servidor NFS del nodo principal**: proporciona una solución de uso compartido de archivos para un entorno mixto de Windows y Linux.

### Almacenamiento de archivos de Azure

El servicio [Archivo de Azure](https://azure.microsoft.com/services/storage/files/) expone recursos compartidos de archivos mediante el protocolo SMB 2.1 estándar. Las VM y los servicios en la nube de Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos. Para obtener más información, consulte [Uso de Almacenamiento de archivos de Azure con PowerShell y .NET](../storage/storage-dotnet-how-to-use-files.md).

Para crear un recurso compartido de Archivo de Azure, consulte los pasos detallados en [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Para establecer conexiones persistentes, consulte [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

En este ejemplo, creamos un recurso compartido de Archivo de Azure denominado rdma en nuestra cuenta de almacenamiento allvhdsje. Para montar el recurso compartido en el nodo principal, abra una ventana de comandos y escriba los siguientes comandos:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

En este ejemplo, allvhdsje es el nombre de la cuenta de almacenamiento, storageaccountkey es la clave de la cuenta de almacenamiento y rdma es el nombre del recurso compartido de Archivo de Azure. El recurso compartido de Archivo de Azure se montará en Z: en el nodo principal.

Para montar el recurso compartido de Archivo de Azure en los nodos de Linux, ejecute un comando **clusrun** en el nodo principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** es una herramienta de HPC Pack útil para llevar a cabo tareas administrativas en varios nodos (vea también [CLusrun para los nodos de Linux](#CLusrun-for-Linux-nodes)en este artículo).

Abra una ventana de Windows PowerShell y escriba los comandos siguientes.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

El primer comando crea una carpeta denominada /rdma en todos los nodos en el grupo LinuxNodes. El segundo comando monta el recurso compartido de Archivo de Azure allvhdsjw.file.core.windows.net/rdma en la carpeta /rdma con dir y bits de modo de archivo establecidos en 777. En el segundo comando, allvhdsje es el nombre de la cuenta de almacenamiento y storageaccountkey es la clave de la cuenta de almacenamiento.

>[AZURE.NOTE]El símbolo “`” del segundo comando es un símbolo de escape de PowerShell. “`,” significa que “,” (coma) forma parte del comando.

### Recurso compartido del nodo principal

Si lo prefiere, puede montar una carpeta compartida del nodo principal en los nodos de Linux. Esta es la manera más sencilla de compartir archivos, pero el nodo principal y todos los nodos de Linux deben implementarse en la misma red virtual. A continuación se muestran los pasos que se deben seguir.

1. Cree una carpeta en el nodo principal y compártala con Todos con permisos de lectura y escritura. Por ejemplo, comparta D:\\OpenFOAM en el nodo principal como \\CentOS7RDMA-HN\\OpenFOAM. En el ejemplo, CentOS7RDMA-HN es el nombre de host del nodo principal.

    ![Permisos del recurso compartido de archivos][fileshareperms]

    ![Uso compartido de archivos][filesharing]

2. Abra una ventana de Windows PowerShell y ejecute los siguientes comandos para montar la carpeta compartida.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

El primer comando crea una carpeta denominada /openfoam en todos los nodos del grupo LinuxNodes. El segundo comando monta la carpeta compartida //CentOS7RDMA-HN/OpenFOAM en la carpeta con dir y bits de modo de archivo establecidos en 777. El nombre de usuario y la contraseña del comando deben ser el nombre de usuario y la contraseña de un usuario de clúster en el nodo principal.

>[AZURE.NOTE]El símbolo “`” del segundo comando es un símbolo de escape de PowerShell. “`,” significa que “,” (coma) forma parte del comando.


### Servidor NFS

El servicio NFS permite a los usuarios compartir y migrar archivos entre equipos que ejecutan el sistema operativo Windows Server 2012 con el protocolo SMB y equipos basados en Linux con el protocolo NFS. El servidor NFS y todos los demás nodos tienen que implementarse en la misma red virtual. Proporciona mayor compatibilidad con los nodos de Linux en comparación con un recurso compartido de SMB; por ejemplo, es compatible con el vínculo de archivos.

1. Para instalar y configurar un servidor NFS, siga los pasos indicados en [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    Por ejemplo, cree un recurso compartido de NFS denominado nfs con las siguientes propiedades.

    ![Autorización de NFS][nfsauth]

    ![Permisos del recurso compartido de NFS][nfsshare]

    ![Permisos de NTFS de NFS][nfsperm]

    ![Propiedades de administración de NFS][nfsmanage]

2. Abra una ventana de Windows PowerShell y ejecute el siguiente comando para montar el recurso compartido de NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  El primer comando crea una carpeta denominada /nfsshared en todos los nodos del grupo LinuxNodes. El segundo comando monta el recurso compartido de NFS CentOS7RDMA-HN:/nfs en la carpeta. Aquí CentOS7RDMA-HN:/nfs es la ruta de acceso remoto al recurso compartido de NFS.

## Envío de trabajos
Hay varias formas de enviar trabajos al clúster de HPC Pack.

* Administrador de clústeres HPC o GUI del Administrador de trabajos de HPC

* Portal web de HPC

* API de REST

El envío de trabajos al clúster en Azure a través de herramientas de GUI de HPC Pack y el portal web de HPC es igual que para los nodos de proceso de Windows. Consulte [Administrador de trabajos de HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) y [Envío de trabajos desde un cliente local](virtual-machines-hpcpack-cluster-submit-jobs.md).

Para enviar trabajos mediante la API de REST, consulte [Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Consulte también el ejemplo de Python en el [SDK de HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) para enviar trabajos desde un cliente Linux.

## ClusRun para nodos de Linux

La herramienta **clusrun** de HPC Pack se puede usar para ejecutar comandos en los nodos de Linux mediante una ventana de comandos o el Administrador de clústeres HPC. A continuación se muestran algunos ejemplos básicos.

* Mostrar los nombres de usuario actuales de todos los nodos del clúster.

    ```
    > clusrun whoami
    ```

* Instale la herramienta de depuración **gdb** con **yum** en todos los nodos del grupo linuxnodes y reinícielos después de 10 minutos.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Cree un script de shell que muestre cada número de 1 a 10 durante un segundo en cada nodo del clúster, ejecútelo y muestre la salida de los nodos inmediatamente.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] Tal vez tenga que usar ciertos caracteres de escape en los comandos de **clusrun**. Como se muestra en este ejemplo, utilice ^ en una ventana de comandos para anular el símbolo ">".

## Pasos siguientes

* Pruebe a escalar verticalmente el clúster a un mayor número de nodos o ejecute una carga de trabajo de Linux en el clúster. Para ver un ejemplo, consulte [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-cluster-hpcpack-namd.md).

* Pruebe un clúster con nodos de proceso de tamaño [A8 o A9](virtual-machines-a8-a9-a10-a11-specs.md) para ejecutar cargas de trabajo MPI. Para ver un ejemplo, consulte [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Pruebe una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) con el Administrador de recursos de Azure para acelerar implementaciones de HPC Pack con un número mayor de nodos de proceso de Linux.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=AcomDC_0128_2016-->