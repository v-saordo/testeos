<properties
    pageTitle="Migrar a Almacenamiento premium de Azure | Microsoft Azure"
    description="Migre las máquinas virtuales existentes a Almacenamiento premium de Azure. El Almacenamiento premium le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure."
    services="storage"
    documentationCenter="na"
    authors="ms-prkhad"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="prkhad"/>


# Migrar a Almacenamiento premium de Azure

## Información general

El Almacenamiento premium de Azure le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Discos de máquinas virtuales que usan datos de almacén de Almacenamiento premium en unidades de estado sólido (SSD). Puede migrar los discos de máquina virtual de su aplicación al Almacenamiento premium de Azure para aprovechar la velocidad y rendimiento de estos discos.

Una máquina virtual de Azure admite la conexión de varios discos de Almacenamiento premium para que las aplicaciones puedan disponer de hasta 64 TB de almacenamiento por máquina virtual. Con el Almacenamiento premium, las aplicaciones pueden tener hasta 80.000 E/S por segundo (operaciones de entrada/salida por segundo) por máquina virtual, así como un rendimiento del disco de 2.000 MB por segundo por máquina virtual, con latencias extremadamente bajas para operaciones de lectura.

>[AZURE.NOTE] Se recomienda migrar cualquier disco de máquina virtual que requiera un número elevado de operaciones de entrada/salida por segundo al Almacenamiento premium de Azure para mejorar el rendimiento de la aplicación. Si el disco no requiere un número elevado de operaciones de entrada/salida por segundo, puede limitar los costos mediante el mantenimiento del almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (HDD) en lugar de SSD.

La finalidad de esta guía es ayudar a los nuevos usuarios del Almacenamiento premium de Azure a prepararse mejor para realizar una transición sin problemas desde su sistema actual a Almacenamiento premium. La guía trata tres de los componentes clave de este proceso: la planeación de la migración a Almacenamiento premium, la migración de discos duros virtuales (VHD) existentes a Almacenamiento premium y la creación de instancias de máquina virtual de Azure en Almacenamiento premium.

Para completar el proceso de migración en su totalidad puede ser necesario realizar acciones adicionales antes y después de los pasos proporcionados en esta guía, por ejemplo, configurar redes virtuales o extremos, o realizar cambios de código dentro de la propia aplicación. Estas acciones son únicas para cada aplicación y deben completarse junto con los pasos proporcionados en esta guía para realizar la transición completa a Almacenamiento premium lo más fácilmente posible.

Encontrará una introducción a las características de Almacenamiento premium en [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](storage-premium-storage.md).

Esta guía se divide en dos secciones donde se tratan los dos escenarios de migración siguientes:

- [Migrar máquinas virtuales desde otras plataformas al Almacenamiento premium de Azure](#migrating-vms-from-other-platforms-to-azure-premium-storage).
- [Migrar máquinas virtuales de Azure al Almacenamiento premium de Azure](#migrating-existing-azure-vms-to-azure-premium-storage).

Siga los pasos especificados en la sección correspondiente en función de su escenario.

## Migrar máquinas virtuales desde otras plataformas al Almacenamiento premium de Azure

### Requisitos previos
- Necesitará una suscripción de Azure. Si no tiene una, puede crear una suscripción de [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) de un mes o visitar la página [Precios de Azure](https://azure.microsoft.com/pricing/) para conocer más opciones.
- Para ejecutar los cmdlets de PowerShell, necesitará un módulo de PowerShell de Microsoft Azure. Vaya a [Descargas de Microsoft Azure](https://azure.microsoft.com/downloads/) para descargar el módulo.
- Si tiene previsto ejecutar máquinas virtuales de Azure en Almacenamiento premium, deberá usar las máquinas virtuales de las series DS o GS. Con la serie DS de máquinas virtuales se pueden usar discos de almacenamiento tanto estándar como premium. Pronto habrá disponibles discos de Almacenamiento premium con más tipos de VM. Para más información sobre todos los tamaños y tipos de disco de máquina virtual de Azure disponibles, vea [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md) y [Tamaños de servicios en la nube](../cloud-services/cloud-services-sizes-specs.md).

### Consideraciones

#### Tamaños de VM
Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md). Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.


#### Tamaños de disco
Hay tres tipos de discos que se pueden usar con las máquinas virtuales, cada uno de ellos con sus límites particulares de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco para la máquina virtual según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

|Tipo de disco de Almacenamiento premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamaño del disco|128 GB|512 GB|1\.024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Rendimiento de disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

#### Objetivos de escalabilidad de la cuenta de almacenamiento

Aparte de los [objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md), las cuentas de Almacenamiento premium tienen estos otros objetivos de escalabilidad. Si los requisitos de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento.

|Capacidad total de la cuenta|Ancho de banda total de una cuenta de almacenamiento con redundancia local|
|:--|:---|
|Capacidad de disco: 35 TB<br />Capacidad de instantánea: 10 TB|Hasta 50 gigabits por segundo de entrada y salida|

Para más información sobre las especificaciones del Almacenamiento premium, consulte [Objetivos de escalabilidad y rendimiento al usar el Almacenamiento premium](storage-premium-storage.md#scalability-and-performance-targets-whes-ESing-premium-storage).

#### Discos de datos adicionales
Dependiendo de la carga de trabajo, decida si son necesarios más discos de datos para la máquina virtual. Puede conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. Si secciona discos de datos Almacenamiento premium usando [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), será necesario configurarlo con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. En las máquinas virtuales de Linux, esto se logra con la utilidad *mdadm*. Vea el artículo [Configuración del software RAID en Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) para más información.

#### Directiva de almacenamiento en caché de disco
De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco del sistema operativo Premium vinculado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación. La configuración de caché de los discos de datos existentes se puede actualizar con el *Portal de Azure* o con el parámetro [- HostCaching](https://portal.azure.com) del cmdlet *Set-AzureDataDisk*.

#### Ubicación
Elija una ubicación donde el Almacenamiento premium de Azure esté disponible. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para obtener información actualizada sobre las ubicaciones disponibles. El rendimiento de las máquinas virtuales será superior si están en la misma región que la cuenta de almacenamiento donde se almacenan los discos de máquina virtual, y no en regiones separadas.

#### Otras opciones de configuración de máquina virtual de Azure

Al crear una máquina virtual de Azure, hay que establecer ciertas configuraciones de máquina virtual. Recuerde que hay algunas configuraciones que son fijas durante la vigencia de la máquina virtual, además de que puede modificar o agregar otras más adelante. Revise estos valores de configuración de máquina virtual de Azure y asegúrese de que están configurados correctamente para que coincidan con sus requisitos de carga de trabajo.

## Preparar los discos duros virtuales para la migración

En la siguiente sección encontrará las instrucciones necesarias para preparar los discos duros virtuales de la máquina virtual de forma que estén preparados para la migración. El disco duro virtual puede ser:

- Una imagen de sistema operativo generalizada que se puede usar para crear varias máquinas virtuales de Azure.
- Un disco del sistema operativo que se puede usar con una sola instancia de máquina virtual de Azure.
- Un disco de datos que se puede conectar a una máquina virtual de Azure con fines de almacenamiento persistente.

### Requisitos previos

Para migrar las máquinas virtuales, necesitará:

- Una suscripción de Azure, una cuenta de almacenamiento y un contenedor en dicha cuenta en el que copiar el disco duro virtual. Conviene saber que, según cuáles sean sus necesidades, la cuenta de almacenamiento de destino puede ser una cuenta de almacenamiento estándar o premium.
- Una herramienta para generalizar el disco duro virtual, en caso de que tenga intención de crear varias instancias de máquina virtual a partir de dicho disco. Por ejemplo, sysprep para Windows o virt sysprep para Ubuntu.
- Una herramienta para cargar el archivo VHD en la cuenta de almacenamiento. Consulte [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md) o use un [Explorador de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Esta guía describe el procedimiento de copiar su VDN con la herramienta AzCopy.

> [AZURE.NOTE] Para obtener un rendimiento óptimo, copie el disco duro virtual ejecutando una de estas herramientas desde una máquina virtual de Azure que está en la misma región que la cuenta de almacenamiento de destino. Si está copiando un VHD de una máquina virtual de Azure en una región distinta, el rendimiento puede ser más lento.
>
> En caso de copiar grandes cantidades de datos sobre un ancho de banda limitado, piense en recurrir al [servicio Importación/Exportación de Azure para transferir los datos al Almacenamiento de blobs](storage-import-export-service.md); esto permite transferir los datos enviando las unidades de disco duro a un centro de datos de Azure. Puede usar el servicio de importación y exportación de Azure para copiar los datos solo en una cuenta de almacenamiento estándar. Una vez que los datos estén en la cuenta de almacenamiento estándar, puede usar la [API Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy para transferirlos a la cuenta de almacenamiento premium.
>
> Tenga en cuenta que Microsoft Azure solo admite archivos VHD de tamaño fijo. No se admiten archivos VHDX ni discos duros virtuales dinámicos. Si tiene un disco duro virtual dinámico, puede convertirlo a tamaño fijo con el cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Escenarios para preparar los discos duros virtuales

A continuación se describen algunos escenarios para preparar los discos duros virtuales.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM

Si está cargando un disco duro virtual que se va usar para crear varias instancias de máquina virtual de Azure genéricas, antes habrá que generalizarlo usando una utilidad sysprep. Esto es válido para discos duros virtuales tanto locales como en la nube. Sysprep quita cualquier información específica del equipo del disco duro virtual.

>[AZURE.IMPORTANT] Realice una instantánea o copia de seguridad de la máquina virtual antes de generalizarla. Al ejecutar sysprep se eliminará la instancia de máquina virtual. Siga los pasos que se muestran a continuación para un sysprep de un disco duro virtual del sistema operativo Windows. Tenga en cuenta que la ejecución del comando Sysprep requerirá que apague la máquina virtual. Para obtener más información sobre Sysprep, consulte [Introducción a Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Referencia técnica de Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Abra una ventana de símbolo del sistema como administrador.
2. Escriba el siguiente comando para abrir Sysprep:

		%windir%\system32\sysprep\sysprep.exe

4. En la herramienta de preparación del sistema, seleccione Iniciar la Configuración rápida (OOBE) del sistema, active la casilla Generalizar, seleccione **Apagado** y, a continuación, haga clic en **Aceptar**, tal y como se muestra en la imagen siguiente. Esto generalizará el sistema operativo y apagará el sistema.

	![][1]

Para una máquina virtual de Ubuntu, use virt-sysprep para lograr lo mismo. Vea [sysprep virt](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para más información. Vea también algunos de los [software de aprovisionamiento del servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de código abierto correspondientes a otros sistemas operativos Linux.

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM

Si una aplicación que se ejecuta en la máquina virtual requiere los datos específicos de la máquina, no generalice el disco duro virtual. Un disco duro virtual no generalizado se puede usar para crear una única instancia de la máquina virtual de Azure. Por ejemplo, si tiene el controlador de dominio en el disco duro virtual, ejecutar sysprep lo anulará como controlador de dominio. Antes de generalizar el disco duro virtual, revise las aplicaciones que se ejecutan en la máquina virtual y el impacto que sysprep puede tener en ellos.

#### Discos duros virtuales de discos de datos que se van a conectar a instancias de máquina virtual

Si tiene discos de datos en un almacenamiento en la nube y va a migrarlos, asegúrese de que las máquinas virtuales que usan esos discos de datos estén apagadas. En el caso de los discos de datos locales, cree un disco duro virtual coherente.

## Copiar discos duros virtuales en Almacenamiento de Azure

Ya tenemos listo el disco duro virtual, así que vamos a realizar los pasos descritos a continuación para cargar el disco duro virtual en Almacenamiento de Azure y registrarlo como una imagen de sistema operativo, como un disco de sistema de operativo aprovisionado o como un disco de datos aprovisionado.

### Crear el destino para el disco duro virtual

Cree una cuenta de almacenamiento para mantener los discos duros virtuales. Tenga en cuenta los siguientes puntos cuando planee dónde almacenar los discos duros virtuales.

- La cuenta de almacenamiento de destino puede ser un almacenamiento estándar o premium, según cuáles sean los requisitos de la aplicación.
- La ubicación de la cuenta de almacenamiento debe ser la misma que la de las máquinas virtuales de Azure de las series DS o GS que crearemos en el paso final. Puede copiar en una nueva cuenta de almacenamiento o decidir usar la misma cuenta de almacenamiento, según sus necesidades.
- Copie y guarde la clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino para el siguiente paso.
- En relación con los discos de datos, puede optar por mantener algunos en una cuenta de almacenamiento estándar (por ejemplo, los discos que disponen de almacenamiento más frío) y mover discos con una gran cantidad de E/S por segundo a una cuenta de almacenamiento premium.

### Copiar el disco duro virtual desde el origen

A continuación se describen algunos escenarios para copiar el disco duro virtual.

#### Copiar el disco duro virtual desde el Almacenamiento de Azure

Si va a migrar un disco duro virtual desde una cuenta de almacenamiento estándar de Azure y una cuenta de Almacenamiento premium de Azure, copie la ruta de acceso de origen y el nombre del contenedor de disco duro virtual y la clave de la cuenta de almacenamiento de origen.

1. Vaya a **Portal de Azure > Máquinas virtuales > Discos**.
2. Copie y guarde la dirección URL del contenedor de los discos duros virtuales de la columna de la ubicación. La dirección URL del contenedor será similar a `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Copiar el disco duro virtual desde una nube que no es de Azure

Si va a migrar un disco duro virtual desde un almacenamiento en la nube que no es de Azure a Azure, exporte antes el disco duro virtual a un directorio local. Copie la ruta de acceso de origen completa del directorio local donde está el disco duro virtual.

1. Si usa AWS, exporte la instancia de EC2 a un disco duro virtual en un depósito de Amazon S3. Siga los pasos descritos en la documentación de Azure para [Exportar instancias de Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) para instalar la herramienta de interfaz de la línea de comandos (CLI) de Amazon EC2 y ejecutar el comando para exportar la instancia de EC2 a un archivo VHD.

	Procure usar **VHD** para la variable DISK&#95;IMAGE&#95;FORMAT cuando ejecute el comando. El archivo VHD exportado se guarda en el depósito de Amazon S3 que haya especificado durante el proceso.

	![][2]

2. Descargue el archivo VHD desde el depósito de S3. Seleccione el archivo VHD y después **Acciones** > **Descargar**.

	![][3]|

#### Copiar un disco duro virtual desde un entorno local

Si va a migrar un disco duro virtual desde un entorno local, necesitará la ruta de acceso de origen completa en la que se almacena el disco duro virtual. Puede ser una ubicación de servidor o un recurso compartido de archivo.

### Copiar un disco duro virtual con AzCopy

Con AzCopy, puede cargar el disco duro virtual fácilmente a través de Internet. Dependiendo del tamaño de los discos duros virtuales, esto puede tardar tiempo. Recuerde comprobar los límites de entrada/salida de la cuenta de almacenamiento cuando use esta opción. Vea [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](storage-scalability-targets.md) para obtener detalles.

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)
2. Abra PowerShell de Azure y vaya a la carpeta donde AzCopy está instalado.
3. Use el siguiente comando para copiar el archivo VHD de "Origen" a "Destino".

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

	Estas son las descripciones de los parámetros utilizados en el comando AzCopy:

 - **/Source: *&lt;source&gt;:*** ubicación de la carpeta o dirección URL del contenedor de almacenamiento que contiene el disco duro virtual.
 - **/SourceKey: *&lt;source-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.
 - **/Dest: *&lt;destination&gt;:*** dirección URL del contenedor de almacenamiento donde se va a copiar el disco duro virtual.
 - **/DestKey: *&lt;dest-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
 - **/BlobType: page:** indica que el destino es un blob en páginas.
 - **/Pattern: *&lt;file-name&gt;:*** escriba el nombre de archivo del disco duro virtual que va a copiar.

Para obtener más información sobre cómo usar la herramienta AzCopy, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

### Copiar un disco duro virtual con PowerShell
También puede copiar el archivo VHD con el cmdlet de PowerShell Start-AzureStorageBlobCopy. Use el siguiente comando de Azure PowerShell para copiar el VHD: Reemplace los valores de <> por los valores correspondientes de la cuenta de almacenamiento de origen y de destino. Para usar este comando, debe tener un contenedor llamado vhds en la cuenta de almacenamiento de destino. Si el contenedor no existe, créelo antes de ejecutar el comando.

    $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"
    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>
    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>
    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### Otras opciones para cargar un disco duro virtual

También puede cargar un VHD a la cuenta de almacenamiento mediante uno de los siguientes medios:

- [API de tipo Copy Blob de Almacenamiento Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Referencia de la API de REST de servicios de importación y exportación de almacenamiento](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] El servicio de importación y exportación solamente se puede usar para copiar en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento premium, use una herramienta como AzCopy.

## Crear máquinas virtuales de Azure mediante Almacenamiento Premium

Tras cargar el disco duro virtual en la cuenta de almacenamiento de su elección, siga las instrucciones de esta sección para registrar el disco duro virtual como una imagen del sistema operativo o como un disco del sistema operativo, según el escenario y, tras ello, cree una instancia de máquina virtual a partir de este. El disco duro virtual de disco de datos se puede conectar a la máquina virtual una vez creado.

### Registrar el disco duro virtual

Para crear una máquina virtual a partir de un disco duro virtual de sistema operativo o conectar un disco de datos a una máquina virtual nueva, antes hay que registrarlos. Siga estos pasos, dependiendo de sus circunstancias.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure

Después cargar el disco duro virtual de imagen de sistema operativo generalizado en la cuenta de almacenamiento, regístrelo como una **imagen de máquina virtual de Azure** para poder crear una o más instancias de VM a partir de él. Use los siguientes cmdlets de PowerShell para registrar el disco duro virtual como una imagen de sistema operativo de máquina virtual de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copie y guarde el nombre de esta nueva imagen de máquina virtual de Azure. En el ejemplo anterior, es *OSImageName*.

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure

Después de cargar el disco duro virtual de sistema operativo único en la cuenta de almacenamiento, regístrelo como un **disco de sistema operativo de Azure** para poder crear una instancia de VM a partir de él. Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de sistema operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copie y guarde el nombre de este nuevo disco de sistema operativo de Azure. En el ejemplo anterior, es *OSDisk*.

#### Disco duro virtual de disco de datos que se va a conectar a nuevas instancias de máquina virtual de Azure

Después de cargar el disco duro virtual de disco de datos en la cuenta de almacenamiento, regístrelo como un disco de datos de Azure para poder conectarlo a la nueva instancia de máquina virtual de Azure de las series DS o GS.

Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de datos operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copie y guarde el nombre de este nuevo disco de datos de Azure. En el ejemplo anterior, es *DataDisk*.

### Crear una máquina virtual de serie DS o GS

Tras registrar la imagen del sistema operativo o el disco del sistema operativo, cree una máquina virtual de la serie DS o GS. Para ello, usaremos el nombre de la imagen de sistema operativo o del disco de sistema operativo que haya registrado. Seleccione el tipo de máquina virtual en el nivel de Almacenamiento premium. En el siguiente ejemplo, usamos el tamaño de máquina virtual *Standard\_DS2*.

>[AZURE.NOTE] Actualice el tamaño del disco para procurar que coincida con sus requisitos de capacidad y rendimiento y los tamaños de disco de Azure disponibles.

Siga los siguientes cmdlets de PowerShell detallados para crear la nueva máquina virtual. En primer lugar, establezca los parámetros comunes:

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US)
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

En primer lugar, cree un servicio en la nube en el que hospedar las máquinas virtuales nuevas.

	New-AzureService -ServiceName $serviceName -Location $location

A continuación, cree la instancia de máquina virtual de Azure a partir de la imagen de sistema operativo o del disco de sistema operativo que haya registrado, según cuál sea su caso.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure

Cree una o varias instancias de máquina virtual de Azure de la serie DS usando la **imagen de sistema operativo de Azure** registrada. Especifique este nombre de imagen de SO en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

	New-AzureVM -ServiceName $serviceName -VM $vm

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure

Cree una instancia de máquina virtual de Azure de la serie DS usando el **disco de sistema operativo de Azure** registrado. Especifique este nombre de disco de sistema operativo en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"

	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

	New-AzureVM -ServiceName $serviceName –VM $vm

Especifique más datos sobre la máquina virtual de Azure, como un servicio en la nube, la región, la cuenta de almacenamiento, el conjunto de disponibilidad y la directiva de almacenamiento en caché. La instancia de máquina virtual debe colocarse con discos de datos o de sistema operativo asociados, por lo que los datos seleccionados de servicio en la nube, región y cuenta de almacenamiento deben estar en la misma ubicación que los discos duros virtuales subyacentes de esos discos.

### Conectar un disco de datos

Por último, si tiene registrados VHD de discos de datos, conéctelos a la nueva máquina virtual de Azure de las series DS o GS.

Use los siguientes cmdlets de PowerShell para conectar un disco de datos a la nueva máquina virtual y especificar la directiva de almacenamiento en caché. En el siguiente ejemplo, la directiva de almacenamiento en caché se establece en *ReadOnly*.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

	Update-AzureVM  -VM $vm

>[AZURE.NOTE] Puede que en esta guía no se contemplen otros pasos adicionales necesarios para admitir sus aplicaciones.

## Migración de máquinas virtuales de Azure existentes a Almacenamiento premium de Azure

Si ya tiene una máquina virtual de Azure que usa discos de Almacenamiento estándar, siga el proceso siguiente para migrar a Almacenamiento premium. A grandes rasgos, la migración consta de dos etapas: migración de los discos de la cuenta de Almacenamiento estándar a una cuenta de Almacenamiento premium y conversión del tamaño de máquina virtual de A/D/G a DS o GS necesarios para el uso de discos de Almacenamiento premium.

Además, consulte en la sección anterior las consideraciones para comprender las diversas optimizaciones que puede realizar para Almacenamiento premium. Según las optimizaciones aplicables a sus aplicaciones, el proceso de migración puede estar en uno de los siguientes escenarios de migración.

### Migración simple
En este escenario simple, desea conservar la configuración tal cual durante la migración de Almacenamiento estándar a Almacenamiento premium. Aquí podrá mover cada uno de los discos tal cual y, después, convertir la máquina virtual también. La ventaja de esto es la facilidad de migración. La desventaja es que la configuración resultante no se puede optimizar para lograr un costo menor.

#### Preparación
1. Asegúrese de que Almacenamiento premium está disponible en la región a la que se va a migrar.
2. Decida la nueva serie de máquinas virtuales que se va a usar. Debe ser la serie DS o la serie GS según la disponibilidad en la región y según sus necesidades.
3. Decida el tamaño exacto de máquina virtual que usará. El tamaño de la máquina virtual debe ser suficientemente grande para admitir el número de discos de datos que tiene. Por ejemplo, si tiene 4 discos de datos, la máquina virtual debe tener 2 o más núcleos. Tenga en cuenta también las necesidades de potencia de procesamiento, memoria y ancho de banda de red.
4. Cree una cuenta de Almacenamiento premium en la región de destino. Esta es la cuenta que se va a usar para la nueva máquina virtual.
5. Tenga a mano los detalles de la máquina virtual, incluida la lista de discos y los blobs de disco duro virtual correspondientes.
6. Prepare la aplicación para el tiempo de inactividad. Para realizar una migración limpia, tendrá que detener todo el procesamiento en el sistema actual. Solo entonces se puede obtener un estado coherente que se puede migrar a la nueva plataforma. La duración del tiempo de inactividad dependerá de la cantidad de datos en los discos para migrar.

#### Pasos de ejecución
1.	Pare la VM. Como se explicó anteriormente, la máquina virtual debe estar totalmente apagada para migrar un estado limpio. Habrá un tiempo de inactividad hasta que se complete la migración.

2.	Una vez que detenida la máquina virtual, copie cada uno de los discos duros virtuales de esa máquina virtual a la nueva cuenta de Almacenamiento premium. Tiene que copiar el blob VHD de disco de sistema operativo, así como todos los blobs VHD de disco de datos. Para la migración, se recomienda usar AzCopy o CopyBlob. Si lo prefiere, puede usar otras herramientas de terceros.

  Consulte las secciones anteriores de [Copiar un disco duro virtual con AzCopy](#copy-a-vhd-with-azcopy) o [Copiar un disco duro virtual con PowerShell](#copy-a-vhd-with-powershell) para ver los comandos.

3.	Compruebe si la copia se completó. Espere hasta que se copien todos los discos. Una vez copiados todos los discos, estará listo para continuar con los pasos siguientes para crear la nueva máquina virtual.
4.	Cree un nuevo disco de sistema operativo usando el blob VHD de disco de sistema operativo que copió en la cuenta de Almacenamiento premium. Para ello, puede usar el cmdlet de PowerShell "Add-AzureDisk".

    Script de ejemplo: Add-AzureDisk -DiskName "NewOSDisk1" -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/MyOSDisk.vhd" -OS "Windows"
5. A continuación, cree la máquina virtual de la serie DS (o serie GS) usando el disco de sistema operativo anterior y los discos de datos.

    Script de ejemplo para crear un nuevo servicio en la nube y una nueva máquina virtual dentro de ese servicio: New-AzureService -ServiceName “NewServiceName” -Location “East US 2"

        New-AzureVMConfig -Name "NewDSVMName" -InstanceSize "Standard_DS2" -DiskName "NewOSDisk1" | Add-AzureProvisioningConfig -Windows | Add-AzureDataDisk -LUN 0 -DiskLabel "DataDisk1" -ImportFrom -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk1.vhd" | Add-AzureDataDisk -LUN 1 -DiskLabel "DataDisk2" -ImportFrom -MediaLocation https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk2.vhd | New-AzureVM -ServiceName "NewServiceName" –Location “East US 2”

6.	Una vez que la nueva máquina virtual está en funcionamiento, acceda a ella con el mismo identificador de inicio de sesión y contraseña que la máquina virtual original y compruebe que todo funciona según lo previsto. Toda la configuración, incluidos los volúmenes seccionados, deberían estar presentes en la nueva máquina virtual.

7.	El último paso es planear la copia de seguridad y la programación de mantenimiento de la nueva máquina virtual según las necesidades de la aplicación.

### Automatización
Si tiene varias máquinas virtuales para migrar, la automatización mediante scripts de PowerShell le resultará útil. El siguiente es un script de ejemplo que automatiza la migración de una máquina virtual. Tenga en cuenta que el siguiente script es solo un ejemplo y se dieron por hechas algunas suposiciones sobre los discos de máquina virtual actuales. Puede que necesite actualizar el script para que coincida con su escenario específico.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a vm from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source vm to the new storage account.
    And then it will create a new vm from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [String] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the vm mannually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires to delete the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`tMoving VM requires to remove the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
    	    Start-Sleep -Seconds 10
    	    $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a vm from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

### Optimización
La configuración actual de la máquina virtual se puede personalizar específicamente para que funcione bien con discos estándar. Por ejemplo, para aumentar el rendimiento, puede usar muchos discos en un volumen seccionado. Dado que los discos de Almacenamiento Premium proporcionan un mejor rendimiento, podrá optimizar el costo reduciendo el número de discos. Por ejemplo, la aplicación puede necesitar un volumen con 2000 IOPS y puede que esté usando un conjunto seccionado de 4 discos de Almacenamiento estándar para obtener 4 x 500 = 2000 IOPS. Con el disco de Almacenamiento premium, un único disco de 512 GB podrá proporcionar 2300 IOPS. Por lo tanto, en lugar de usar 4 discos por separado en Almacenamiento premium, puede tener un único disco para optimizar el costo. Las optimizaciones como estas deben tratarse individualmente y requieren uns pasos de personalización después de la migración. Tenga en cuenta también que este proceso podría no funcionar bien para las bases de datos y las aplicaciones que dependen del diseño de discos definido en el programa de instalación.

#### Preparación
1.	Complete la migración simple, tal y como se describe en la sección anterior. Las optimizaciones se realizarán en la nueva máquina virtual después de la migración.
2.	Define los nuevos tamaños de disco necesarios para la configuración optimizada.
3.	Determine las asignaciones de los discos o volúmenes actuales a las especificaciones de los nuevos discos.

#### Pasos de ejecución:
1.	Cree los discos nuevos con los tamaños correctos en la máquina virtual de Almacenamiento premium.
2.	Inicie sesión en la máquina virtual y copie los datos del volumen actual en el nuevo disco asignado a ese volumen. Haga esto para todos los volúmenes actuales que se deben asignar a un nuevo disco.
3.	A continuación, cambie la configuración de la aplicación para cambiar a los nuevos discos, y desasocie los volúmenes antiguos.

###  Migraciones de aplicaciones
Las bases de datos y otras aplicaciones complejas pueden requerir pasos especiales tal y como defina el proveedor de la aplicación para la migración. Consulte la documentación de la aplicación correspondiente. Por ejemplo, las bases de datos normalmente se pueden migrar mediante copia de seguridad y restauración.

## Pasos siguientes

Vea los siguientes recursos para conocer otros escenarios específicos de migración de máquinas virtuales.

- [Migrar máquinas virtuales de Azure entre cuentas de almacenamiento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server.md)
- [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../virtual-machines/virtual-machines-linux-create-upload-vhd.md)
- [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Vea también los siguientes recursos para obtener más información sobre Almacenamiento de Azure y Máquinas virtuales de Azure:

- [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=AcomDC_0224_2016-->