<properties
   pageTitle="Creación de una imagen de máquina virtual para Azure Marketplace | Microsoft Azure"
   description="Instrucciones detalladas sobre cómo crear una imagen de máquina virtual para Azure Marketplace para que otros usuarios la compren."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="hascipio; v-divte"/>

# Guía para la creación de una imagen de máquina virtual para Azure Marketplace

En este artículo, **paso 2**, se explica cómo puede preparar los discos duros virtuales (VHD) que va a implementar en Azure Marketplace. Los VHD constituyen el fundamento de la SKU. El proceso difiere en función de si la SKU que va a proporcionar está basada en Linux o en Windows. En este artículo se tratan ambos escenarios. Este proceso puede realizarse en paralelo con la [creación y registro de cuentas][link-acct-creation].

## 1\. Definición de ofertas y SKU

En esta sección aprenderá a definir las ofertas y sus SKU.

Una oferta es un "elemento primario" de todas sus SKU. Puede tener varias ofertas. Usted elige cómo desea estructurarlas. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus SKU. Sea cauteloso a la hora de elegir los identificadores de sus SKU, ya que serán visibles en la dirección URL.

- Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Portal de vista previa de Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

Una SKU es el nombre comercial de una imagen de máquina virtual. Una imagen de máquina virtual contiene un disco del sistema operativo y cero o más discos de datos. Se trata, básicamente, del perfil de almacenamiento completo de una máquina virtual. Es necesario un VHD por disco. Incluso los discos de datos vacíos requieren la creación de un VHD.

Independientemente del sistema operativo que use, agregue solo el número mínimo de discos de datos necesarios para la SKU. Los usuarios no pueden quitar discos que formen parte de una imagen durante la implementación pero siempre pueden agregar discos durante o después de la implementación si los necesitan.

### 1\.1 Agregar una oferta

1. Inicie sesión en el [Portal de publicación][link-pubportal] con su cuenta de vendedor.
2. Seleccione la pestaña **Máquinas virtuales** del Portal de publicación. En el campo de entrada, escriba el nombre de la oferta. El nombre de la oferta es normalmente el nombre del producto o servicio que vaya a vender en Azure Marketplace.
3. Seleccione **Crear**.

### 1\.2 Definir una SKU
Una vez agregada una oferta, tiene que definir e identificar sus SKU. Puede tener varias ofertas y cada oferta puede tener varias SKU. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus SKU.

1. **Agregue una SKU.** La SKU requiere un identificador que se usa en la dirección URL. El identificador tiene que ser único dentro de su perfil de publicación, pero no existe riesgo de colisión de identificadores con otros anunciantes.

> [AZURE.NOTE] La oferta y los identificadores de SKU se muestran en la dirección URL de la oferta en Marketplace.

2. **Agregue una descripción resumida de la SKU.** Las descripciones resumidas serán visibles para los clientes, así que es recomendable que sean fáciles de leer. No es necesario que esta información esté bloqueada hasta que se inserte en la fase de entorno de ensayo. Hasta entonces, puede editarla como desee.
3. Si usa SKU basadas en Windows, siga los vínculos sugeridos para adquirir las versiones aprobadas de Windows Server.

## 2\. Creación de un VHD compatible con Azure (basado en Linux)
Esta sección se centra en los procedimientos recomendados para crear una imagen de máquina virtual basada en Linux para Azure Marketplace. Para ver un tutorial detallado, consulte la siguiente documentación: [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux.][link-azure-vm-1]

> [AZURE.TIP] Muchos de los pasos siguientes (p. ej. instalación de agentes, parámetros de arranque de kernel) ya están controlados para las imágenes de Linux disponibles en la Galería de imágenes de Microsoft Azure. Por lo tanto, empezar con una de estas imágenes como base puede suponer un ahorro de tiempo frente a la configuración de una imagen de Linux desconocida para Azure.

### 2\.1 Elegir el tamaño de VHD correcto
Las SKU publicadas (imágenes de máquina virtual) deben estar diseñadas para funcionar con todos los tamaños de máquina virtual que admitan el número de discos de la SKU. Puede proporcionar información sobre tamaños recomendados, pero se tratarán como recomendaciones y no serán obligatorios:

1. VHD para el sistema operativo Linux: el VHD para el sistema operativo Linux de su imagen de máquina virtual debe crearse como VHD con formato fijo de 30-50 GB. No puede tener menos de 30 GB. Si el tamaño físico es inferior al tamaño del VHD, el VHD debe ser disperso. Los VHD para Linux con un tamaño superior a 50 GB se considerarán caso por caso. Si ya tiene un VHD con un formato diferente, puede usar el [cmdlet Convert-VHD de PowerShell para cambiar el formato][link-technet-1].
2. VHD para disco de datos: los discos de datos pueden tener un tamaño de hasta 1 TB. Los VHD para discos de datos deben crearse como VHD de formato fijo. También deben ser dispersos. A la hora de decidir el tamaño del disco, tenga en cuenta que los clientes no pueden cambiar el tamaño de los VHD de una imagen.

### 2\.2 Asegurarse de que está instalado el Agente de Linux de Azure más reciente
Cuando prepare el VHD del sistema operativo, asegúrese de que está instalada la última versión del [Agente de Linux de Azure][link-azure-vm-2], con los paquetes RPM o Deb. El paquete a menudo se denomina walinuxagent o WALinuxAgent pero compruébelo con su distribuidor para estar seguro. El agente proporciona funciones clave para implementar IaaS Linux en Azure, como el aprovisionamiento de máquina virtual y las funciones de red.

Aunque el agente se puede configurar de diversas formas, se recomienda usar una configuración de agente genérica para maximizar la compatibilidad. Es posible instalar el agente manualmente pero se recomienda encarecidamente usar los paquetes preconfigurados de la distribución, si están disponibles.

Si elige instalar el agente manualmente desde el [repositorio de GitHub][link-github-waagent], copie primero el archivo Waagent en /usr/sbin y ejecute los siguientes comandos en el directorio raíz:

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

El archivo de configuración del agente se coloca en /etc/waagent.conf.

### 2\.3 Comprobar que están incluidas las bibliotecas necesarias
Además del Agente de Linux de Azure, deben incluirse las siguientes bibliotecas:

1. [Servicios de integración de Linux][link-intsvc] versión 3.0 o posterior debe estar habilitado en el kernel. Consulte [Requisitos para el kernel de Linux](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)
2. [Revisión de kernel](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) para la estabilidad de E/S de Azure (probablemente no es necesario para ningún kernel reciente, pero debe comprobarse).
3. [Python][link-python] 2.6 o versiones superiores
4. Paquete pyasn1 de Python, si no está ya instalado.
5. [OpenSSL][link-openssl] (versión 1.0 o superior recomendado)

### 2\.4 Configurar particiones de disco
Se recomienda no usar el administrador de volúmenes lógicos. Cree una sola partición raíz para el disco del sistema operativo. No use una partición de intercambio en el disco del sistema operativo o de datos. Se recomienda quitar las particiones de intercambio, incluso si no están montadas en /etc/fstab. Si es necesario, se puede crear una partición de intercambio en el disco de recursos locales (/dev/sdb) con el Agente de Linux.

### 2\.5 Agregar los parámetros de línea de arranque de kernel necesarios
Los siguientes parámetros también tienen que agregarse a la línea de arranque de kernel.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

Esto garantiza que el servicio de soporte de Azure pueda proporcionar a los clientes salida de consola serie cuando sea necesario. También proporciona un tiempo de espera adecuado para el montaje de discos del sistema operativo desde el almacenamiento en la nube. Incluso si la SKU impide que los clientes usen SSH directamente en la máquina virtual, debe habilitarse la salida de consola serie.

### 2\.6 Incluir SSH Server de forma predeterminada
Se recomienda encarecidamente habilitar SSH para el cliente. Si el servidor SSH está habilitado, agregue SSH persistente a la configuración sshd con la siguiente opción: **ClientAliveInterval 180**. Aunque se recomienda 180, el intervalo aceptable es de 30 a 235. No todas las aplicaciones desean ofrecer a los clientes acceso directo de SSH a la máquina virtual. Si SSH está bloqueado de forma explícita, no es necesario establecer la opción **ClientAliveInterval**.

### 2\.7 Cumplir los requisitos de red
A continuación se indican los requisitos de red para una imagen de máquina virtual Linux compatible con Azure.

- En muchos casos es mejor deshabilitar NetworkManager. Una excepción son los sistemas basados en CentOS 7.x (y derivados) que tienen que mantener NetworkManager habilitado.
- La configuración de red debe poder controlarse mediante los scripts **ifup** y **ifdown**. El Agente de Linux puede usar estos comandos para reiniciar las redes durante el aprovisionamiento.
- No debe haber configuración de red personalizada. Como paso final, se debe eliminar el archivo Resolv.conf. Esto se hace normalmente como parte del desaprovisionamiento (consulte la [Guía del usuario del Agente de Linux de Azure](../virtual-machines/virtual-machines-linux-agent-user-guide/)). También puede realizar este paso manualmente con el siguiente comando.

        rm /etc/resolv.conf

- El dispositivo de red tiene que aparecer en el arranque y usar DHCP.
- Azure no admite IPv6 6. Si está habilitada esta propiedad, no funcionará.

### 2\.8 Asegurarse de que se implementan los procedimientos recomendados de seguridad
En el caso de las SKU de Azure Marketplace , es crucial seguir las prácticas recomendados de seguridad. Entre ellas se incluyen las siguientes:

- Instalar todas las revisiones de seguridad para la distribución.
- Seguir las instrucciones de seguridad para la distribución.
- Evitar la creación de cuentas predeterminadas, que permanecen igual, entre instancias de aprovisionamiento.
- Borrar las entradas del historial de Bash
- Incluir el software IPtables (firewall), pero sin habilitar ninguna regla. Esto proporciona a los clientes una experiencia predeterminada sin problemas. Los clientes que deseen usar un firewall de máquina virtual como configuración adicional pueden configurar reglas de IPtables para satisfacer sus necesidades específicas.

### 2\.9 Generalizar la imagen
Todas las imágenes de Azure Marketplace deben ser reutilizables de forma genérica, lo que requiere quitarles algunas opciones de configuración específicas. Para lograr esto en Linux, debe desaprovisionarse el VHD del sistema operativo.

El comando de Linux para el desaprovisionamiento es el siguiente.

        # waagent -deprovision

Este comando automáticamente:

- Quita la configuración de Nameserver de /etc/resolv.conf.
- Quita concesiones del cliente de DHCP en caché.
- Restablece el nombre de host a localhost.localdomain.

Se recomienda establecer el archivo de configuración (/etc/waagent.conf) para asegurarse de que se realizan las acciones siguientes:

- Establecer Provisioning.RegenerateSshHostKeyPair en "y" en el archivo de configuración para quitar todas las claves de host SSH.
- Establecer Provisioning.DeleteRootPassword en "y" en el archivo de configuración para quitar la contraseña raíz de /etc/shadow. Para obtener documentación sobre el contenido del archivo de configuración, consulte la sección "Configuración" del archivo Léame en la página del repositorio del agente de Github ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) y desplácese hacia abajo).  

En este momento, ha completado la generalización de la máquina virtual de Linux. Desconecte la máquina virtual desde el Portal de Azure, la línea de comandos o desde la máquina virtual. Cuando la máquina virtual esté desconectada, continúe en el paso 3.4.

## 3\. Creación de un VHD compatible con Azure (basado en Windows)
En esta sección se indican los pasos necesarios para crear una SKU basada en Windows Server para Azure Marketplace.

### 3\.1 Asegurarse de que se usan los VHD base correctos
Los VHD del sistema operativo para su imagen de máquina virtual deben basarse en una imagen aprobada para Azure que contenga Windows Server o SQL Server.

Para comenzar, cree una máquina virtual a partir de alguna de las siguientes imágenes, que encontrará en el [Portal de Microsoft Azure][link-azure-portal]\:

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Estos vínculos se encuentran también en el portal de publicación, en la página de la SKU.

> [AZURE.TIP] Si usa el Portal de Azure actual o PowerShell, las imágenes de Windows Server publicadas a partir del 8 de septiembre de 2014 están aprobadas.


### 3\.2 Crear su propia máquina virtual basada en Windows
Desde el Portal de Microsoft Azure, puede crear su máquina virtual basada en una imagen base aprobada con solo algunos pasos sencillos. A continuación se ofrece información general del proceso:

1. En la página de la imagen base, seleccione **Crear máquina virtual** para ir al nuevo [Portal de Microsoft Azure][link-azure-portal].

    ![dibujo][img-acom-1]

2. Inicie sesión en el portal con la cuenta Microsoft y la contraseña de la suscripción de Azure que desee usar.
3. Siga las indicaciones para crear una máquina virtual con la imagen base que seleccionó. Tendrá que proporcionar un nombre de host (nombre del equipo), un nombre de usuario (registrado como administrador) y una contraseña para la máquina virtual.

    ![dibujo][img-portal-vm-create]

4. Seleccione el tamaño de la máquina virtual que desea implementar:

    a. Si planea desarrollar el VHD en modo local, el tamaño no importa. Considere usar una de las máquinas virtuales de menor tamaño.

    b. Si planea desarrollar la imagen en Azure, considere usar uno de los tamaños de máquina virtual recomendados para la imagen seleccionada.

    c. Para obtener información de precios, consulte el selector de **plan de tarifa recomendada** que se muestra en el portal. Mostrará los tres precios recomendados proporcionados por el anunciante. (En este caso, el anunciante es Microsoft.)

    ![dibujo][img-portal-vm-size]

5. Establezca las propiedades:

    a. Para una rápida implementación, puede dejar los valores predeterminados de las propiedades en **Configuración opcional** y **Grupo de recursos**.

    b. En **Cuenta de almacenamiento**, puede seleccionar opcionalmente la cuenta de almacenamiento donde se almacenará el VHD del sistema operativo.

    c. En **Grupo de recursos**, puede seleccionar opcionalmente el grupo lógico donde ubicar la máquina virtual.
6. Seleccione la **Ubicación** para la implementación:

    a. Si planea desarrollar el VHD en modo local, la ubicación no importa, ya que cargará la imagen más tarde en Azure.

    b. Si planea desarrollar la imagen en Azure, considere usar una de las regiones de Microsoft Azure en Estados Unidos desde el principio. Esto agiliza el proceso de copia del VHD que Microsoft realiza por usted cuando envía la imagen para certificarla.

    ![dibujo][img-portal-vm-location]

7. Haga clic en **Crear**. La máquina virtual empieza a implementarse. En cuestión de minutos, dispondrá de una implementación correcta y podrá comenzar a crear la imagen para su SKU.

### 3\.3 Desarrollar el VHD en la nube
Se recomienda encarecidamente desarrollar el VHD en la nube mediante el Protocolo de escritorio remoto (RDP). La conexión a RDP se realiza con el nombre de usuario y la contraseña que especificó durante el aprovisionamiento.

> [AZURE.IMPORTANT] Si está desarrollando su VHD de forma local (aunque no se recomienda), consulte [Creación de una imagen de máquina virtual de forma local](marketplace-publishing-vm-image-creation-on-premise.md). Si desarrolla el VHD en la nube, no es necesario descargarlo.


**Conectarse mediante RDP usando el [Portal de Microsoft Azure][link-azure-portal]**

1. Seleccione **Examinar** > **Máquinas virtuales**.
2. Se abre la hoja Máquinas virtuales. Compruebe que la máquina virtual con la que desea conectarse está ejecutándose y selecciónela en la lista de máquinas virtuales implementadas.
3. Se abre una hoja en la que se describe la máquina virtual seleccionada. En la parte superior, haga clic en **Conectar**.
4. Se le pide que proporcione el nombre de usuario y la contraseña que especificó durante el aprovisionamiento.

**Conectarse mediante RDP con PowerShell**

Para descargar un archivo de escritorio remoto en una máquina local, use el [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Para usar este cmdlet, tiene que conocer el nombre del servicio y el de la máquina virtual. Si creó la máquina virtual desde el [Portal de Microsoft Azure][link-azure-portal], puede ver esta información en las propiedades de la máquina virtual:

1. En el Portal de Microsoft Azure, seleccione **Examinar** y después **Máquinas virtuales**.
2. Se abre la hoja Máquinas virtuales. Seleccione la máquina virtual que ha implementado.
3. Se abre una hoja en la que se describe la máquina virtual seleccionada.
4. Haga clic en **Propiedades**.
5. La primera parte del nombre del dominio es el nombre del servicio. El nombre de host es el nombre de la máquina virtual.

    ![dibujo][img-portal-vm-rdp]

6. El cmdlet para descargar el archivo RDP de la máquina virtual creada en el escritorio local del administrador es el siguiente.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Encontrará más información acerca de RDP en el artículo de MSDN [Conectarse a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Configurar una máquina virtual y crear la SKU**

Una vez descargado el VHD del sistema operativo, use Hyper-V y configure una máquina virtual para comenzar a crear la SKU. Encontrará los pasos detallados en el siguiente vínculo de TechNet: [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

### 3\.4 Elegir el tamaño de VHD correcto
El VHD del sistema operativo Windows en la imagen de máquina virtual debe crearse como VHD de 128 GB con formato fijo.

Si el tamaño físico es inferior a 128 GB, el VHD debe ser disperso. Las imágenes base de Windows y SQL Server proporcionadas cumplen ya estos requisitos, por lo que no es necesario que cambie el formato ni el tamaño del VHD obtenido.

Los discos de datos pueden tener un tamaño de hasta 1 TB. A la hora de decidir el tamaño de disco, tenga en cuenta que los clientes no pueden cambiar el tamaño de los VHD de una imagen en el momento de la implementación. Los VHD para discos de datos deben crearse como VHD de formato fijo. También deben ser dispersos. Los discos de datos pueden estar vacíos o contener datos.


### 3\.5 Instalar las últimas revisiones de Windows
Las imágenes base contienen las últimas revisiones hasta su fecha de publicación. Antes de publicar el VHD del sistema operativo que ha creado, asegúrese de que se ejecutó Windows Update y se instalaron todas las revisiones de seguridad Críticas e Importantes.

### 3\.6 Configurar opciones adicionales y programar tareas según sea necesario
Si es necesaria alguna configuración adicional, considere la posibilidad de usar una tarea programada que se ejecute al inicio para realizar posibles cambios finales en la máquina virtual una vez implementada.

- Se recomienda que la propia tarea se elimine después de ejecutarse correctamente.
- Ninguna configuración debe depender de unidades que no sean C o D, ya que estas son las dos únicas unidades cuya existencia está garantizada. La unidad C es el disco del sistema operativo y unidad D es el disco local temporal.

### 3\.7 Generalizar la imagen
Todas las imágenes de Azure Marketplace deben ser reutilizables de forma genérica. Dicho de otro modo, el VHD del sistema operativo debe generalizarse:

- Para Windows, la imagen debe estar preparada con sysprep y no deben realizarse configuraciones que no admitan el comando **sysprep**.
- Puede ejecutar el siguiente comando desde el directorio % windir%\\System32\\Sysprep.

        sysprep.exe /generalize /oobe /sshutdown

  En uno de los pasos del artículo de MSDN [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/), se explica cómo preparar el sistema operativo con sysprep.

## 4\. Implementación de una máquina virtual a partir de VHD
Una vez cargados en una cuenta de almacenamiento de Azure sus VHD (el VHD del sistema operativo generalizado y cero o más VHD de discos de datos), puede registrarlos como imagen de máquina virtual de usuario. A continuación, puede probar esa imagen. Tenga en cuenta que, como el VHD del sistema operativo está generalizado, no puede implementar la máquina virtual directamente proporcionando la dirección URL del VHD.

Para obtener más información sobre las imágenes de máquina virtual consulte las siguientes entradas de blog:

- [Imagen de máquina virtual](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Procedimientos para trabajar con imágenes de máquina virtual en PowerShell](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [Acerca de las imágenes de máquina virtual en Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 Crear una imagen de máquina virtual de usuario
Para crear una imagen de máquina virtual de usuario desde su SKU con el fin de comenzar a implementar varias máquinas virtuales, tiene que usar la [API de Rest Create VM Image](http://msdn.microsoft.com/library/azure/dn775054.aspx) para registrar VHD como una imagen de máquina virtual.

Use el cmdlet **Invoke-WebRequest** para crear una imagen de máquina virtual desde PowerShell. El siguiente script de PowerShell muestra cómo crear una imagen de máquina virtual con un disco del sistema operativo y un disco de datos. Tenga en cuenta que una suscripción y la sesión de PowerShell deben estar ya configuradas.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


Al ejecutar este script, crea una imagen de máquina virtual de usuario con el nombre que proporcionó en el parámetro ImageName, myVMImage. Consta de un disco del sistema operativo y un disco de datos.

Esta API es una operación asincrónica y responde con el código "202 - Aceptado". Para ver si se creó la imagen de máquina virtual, tiene que hacer una consulta del estado de la operación. El valor x-ms-request-id de la respuesta devuelta es el identificador de la operación. Debe especificar este identificador en $opId dentro del siguiente código.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

Para crear una imagen de máquina virtual a partir de un VHD del sistema operativo y otros discos de datos vacíos (no tiene el VHD creado para este disco) con la API Create VM Image, use el siguiente script:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

Al ejecutar este script, crea una imagen de máquina virtual de usuario con el nombre que proporcionó en el parámetro ImageName, myVMImage. Consta de un disco del sistema operativo y un disco de datos.

Esta API es una operación asincrónica y responde con el código "202 - Aceptado". Para ver si se creó la imagen de máquina virtual, tiene que hacer una consulta del estado de la operación. El valor x-ms-request-id de la respuesta devuelta es el identificador de la operación. Debe especificar este identificador en $opId dentro del siguiente código.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

Para crear una imagen de máquina virtual a partir de un VHD del sistema operativo y otros discos de datos vacíos (no tiene el VHD creado para este disco) con la API Create VM Image, use el siguiente script:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

Al ejecutar este script, crea una imagen de máquina virtual de usuario con el nombre que proporcionó en el parámetro ImageName, myVMImage. Consta de un disco del sistema operativo, basado en el VHD que pasó, y un disco de datos de 32 GB vacío.

### 4\.2 Implementar una máquina virtual a partir de una imagen de máquina virtual de usuario
Para implementar una máquina virtual a partir de una imagen de máquina virtual de usuario, puede usar el [Portal de Azure](https://manage.windowsazure.com) actual o PowerShell.

**Implementar una máquina virtual desde el Portal de Azure actual**

1. Haga clic en **Nuevo** > **Proceso** > **Máquina virtual** > **Desde la galería**.

    ![dibujo][img-manage-vm-new]

2. Vaya a **Mis imágenes** y seleccione la imagen de máquina virtual con la que quiere implementar una máquina virtual:
  1. Preste mucha atención a la imagen que selecciona, porque la vista **Mis imágenes** enumera tanto imágenes del sistema operativo como imágenes de máquina virtual.
  2. Comprobar el número de discos puede ayudar a determinar el tipo de imagen que va a implementar, ya que la mayoría de las imágenes de máquina virtual tienen más de un disco. No obstante, es posible tener una imagen de máquina virtual con un solo disco del sistema operativo, es decir, tendría **Número de discos** establecido en un 1.

    ![dibujo][img-manage-vm-select]

3. Siga el asistente para crear máquinas virtuales y especifique el nombre de la máquina virtual, el tamaño, la ubicación, el nombre de usuario y la contraseña.

**Implementación de una máquina virtual a partir de PowerShell**

Para implementar una máquina virtual de gran tamaño, puede usar los siguientes cmdlets desde la imagen de máquina virtual generalizada que acaba de crear.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. Obtención de la certificación para su imagen de máquina virtual
El siguiente paso para preparar su imagen de máquina virtual para Azure Marketplace es certificarla.

Este proceso incluye ejecutar una herramienta de certificación especial, cargar los resultados de la comprobación en el contenedor de Azure donde residen sus VHD, agregar una oferta, definir su SKU y enviar su imagen de máquina virtual para que la certifiquen.

### 5\.1 Descargar e instalar “Certification Test Tool for Azure Certified”
La herramienta de certificación se ejecuta en una máquina virtual en ejecución, aprovisionada desde su imagen de máquina virtual de usuario, para comprobar que la imagen de máquina virtual es compatible con Microsoft Azure. Comprueba que se han cumplido las instrucciones y los requisitos para preparar su VHD. El resultado de la herramienta es un informe de compatibilidad que se debe cargar en el Portal de publicación al solicitar la certificación.

La herramienta de certificación se puede usar con máquinas virtuales basadas tanto en Windows como en Linux. Se conecta con las máquinas virtuales basadas en Windows a través de PowerShell y, con las máquinas virtuales basadas en Linux, mediante SSH.Net:

1. En primer lugar, descargue la herramienta de certificación en el [sitio de descargas de Microsoft][link-msft-download].
2. Abra la herramienta de certificación y haga clic en el botón **Iniciar nueva prueba**.
3. En la pantalla **Información de la prueba**, escriba un nombre para la serie de pruebas.
4. Especifique si la máquina virtual está basada en Linux o Windows. En función de lo que especifique, seleccione las demás opciones.

### **Conexión de la herramienta de certificación a una imagen de máquina virtual basada en Linux**

1. Seleccione el modo de autenticación con SSH: contraseña o archivo de clave.
2. Si usa autenticación por contraseña, escriba el Sistema de nombres de dominio (DNS), el nombre de usuario y la contraseña.
3. Si usa autenticación con archivo de clave, escriba el nombre DNS, el nombre de usuario y la ubicación de la clave privada.

  ![Autenticación de contraseña de imagen de máquina virtual de Linux][img-cert-vm-pswd-lnx]

  ![Autenticación de archivo de clave de máquina virtual de Linux][img-cert-vm-key-lnx]

### **Conexión de la herramienta de certificación a una imagen de máquina virtual basada en Windows**

1. Escriba el nombre DNS completo de la máquina virtual (por ejemplo, MyVMName.ClOudapp.net).
2. Escriba el nombre de usuario y la contraseña.

  ![Autenticación de contraseña de imagen de máquina virtual de Windows][img-cert-vm-pswd-win]

Después de seleccionar las opciones correctas para la imagen de máquina virtual basada en Linux o Windows, elija **Probar conexión** para comprobar que SSH.Net o PowerShell tienen una conexión válida con fines de prueba. Una vez establecida la conexión, seleccione **Siguiente** para iniciar la prueba.

Cuando termine la prueba, recibirá los resultados (sin errores/error/con advertencia) para cada elemento de la prueba.

![Casos de prueba para la imagen de máquina virtual de Linux][img-cert-vm-test-lnx]

![Casos de prueba para la imagen de máquina virtual de Windows][img-cert-vm-test-win]

Si se produce un error en alguna de las pruebas, la imagen no se certificará. Si ocurre esto, revise los requisitos y realice los cambios necesarios.

Después de la prueba automatizada, se le pedirá que proporcione información adicional en la imagen de máquina virtual a través de una pantalla de cuestionario. Complete las preguntas y seleccione **Siguiente**

![Cuestionario de la herramienta de certificación][img-cert-vm-questionnaire]

![Cuestionario de la herramienta de certificación][img-cert-vm-questionnaire-2]

Una vez haya completado el cuestionario, puede proporcionar información adicional, como información de acceso SSH para la imagen de máquina virtual de Linux y una explicación sobre las evaluaciones que han dado errores. Puede descargar los resultados de la prueba y los archivos de registro de las pruebas ejecutadas, además de sus respuestas al cuestionario. Guarde los resultados en el mismo contenedor que los VHD.

![Guardar resultados de la prueba de certificación][img-cert-vm-results]

### 5\.2 Obtener el identificador URI de firma de acceso compartido para sus imágenes de máquina virtual

Durante el proceso de publicación, tiene que especificar los identificadores uniformes de recursos (URI) que llevan a cada uno de los VHD que creó para su SKU. Microsoft necesita tener acceso a estos VHD durante el proceso de certificación. Por lo tanto, debe crear un URI de firma de acceso compartido para cada VHD. Este es el URI que debe proporcionar en la pestaña **Imágenes** del portal de publicación.

El URI de firma de acceso compartido creado debe cumplir los siguientes requisitos:

- Para generar los URI de firma de acceso compartido para sus VHD, son suficientes los permisos de lista y lectura. No proporcione acceso de escritura o eliminación.
- La duración del acceso debe ser un mínimo de siete días laborables a partir de la creación del URI de firma de acceso compartido.
- Para evitar errores inmediatos debido al desfase temporal, especifique una hora que sea 15 minutos anterior a la hora actual.

Para crear un URI de firma de acceso compartido, siga las instrucciones que se proporcionan en [Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido][link-azure-1] y [Firmas de acceso compartido, parte 2: Creación y uso de una firma de acceso compartido con el servicio BLOB][link-azure-2].

En lugar de generar una clave de acceso compartido mediante código, también puede usar herramientas de almacenamiento, como [Explorador de almacenamiento de Azure][link-azure-codeplex].

**Usar Explorador de almacenamiento de Azure para generar una clave de acceso compartido**

1. Descargue [Explorador de almacenamiento de Azure][link-azure-codeplex] versión 6 o superior desde CodePlex.
2. Una vez instalado, abra la aplicación.
3. Haga clic en **Agregar cuenta**.

    ![dibujo][img-azstg-add]

4. Especifique el nombre de cuenta de almacenamiento, la clave de la cuenta de almacenamiento y el dominio de los puntos de conexión de almacenamiento. No seleccione **Usar HTTPS**.

    ![dibujo][img-azstg-setup-1]

5. El Explorador de almacenamiento de Azure está conectado a su cuenta de almacenamiento específica. Empezará a mostrar todos los contenedores que se encuentran en la cuenta de almacenamiento. Seleccione el contenedor en el que haya copiado el archivo VHD del disco del sistema operativo (también los discos de datos si son aplicables en su caso).

    ![dibujo][img-azstg-setup-2]

6. Después de seleccionar el contenedor de blobs, se iniciará la aplicación Explorador de almacenamiento de Azure con los archivos dentro del contenedor. Seleccione el archivo de imagen (.vhd) que tiene que enviarse.

    ![dibujo][img-azstg-setup-3]

7. Después de seleccionar el archivo (.vhd) en el contenedor, haga clic en la pestaña **Seguridad**.

    ![dibujo][img-azstg-setup-4]

8. En el cuadro de diálogo **Seguridad del contenedor de blobs**, deje los valores predeterminados en la pestaña **Nivel de acceso** y, a continuación, haga clic en la pestaña **Firmas de acceso compartido**.

    ![dibujo][img-azstg-setup-5]

9. Siga estos pasos a fin de generar un URI de firma de acceso compartido para la imagen .vhd:

    ![dibujo][img-azstg-setup-6]

    a. **Acceso permitido desde**: para proteger la hora UTC, seleccione el día anterior a la fecha actual. Por ejemplo, si la fecha actual es el 6 de octubre de 2014, seleccione 5/10/2014.

    b. **Acceso permitido hasta**: seleccione una fecha que sea al menos siete u ocho días después de la fecha de **Acceso permitido hasta**.

    c. **Acciones permitidas**: seleccione los permisos **Lista** y **Lectura**.

    d. Si seleccionó correctamente el archivo .vhd, en **Nombre de blob para acceder** aparece el archivo con la extensión .vhd

    e. Haga clic en **Generar firma**.

    f. En **URI de firma de acceso compartido generado de este contenedor**, compruebe lo siguiente tal como se ha resaltado anteriormente:

    - 	Asegúrese de que la dirección URL no empieza por "https".
    - 	Compruebe que el nombre de archivo de imagen y ".vhd" están en el URI.
    - 	Asegúrese de que "=rl" aparece al final de la firma. Esto demuestra que el acceso de lectura y lista se ha proporcionado correctamente.

    g. Para asegurarse de que el URI de firma de acceso compartido generado funciona correctamente, haga clic en **Probar en el explorador**. El proceso de descarga debería comenzar.
10. Copie el URI de firma de acceso compartido. Este es el URI que debe pegar en el portal de publicación.
11. Repita estos pasos para cada VHD de la SKU.

### 5\.3 Proporcionar información acerca de la imagen de máquina virtual y solicitar su certificación en el portal de publicación
Una vez que haya creado su oferta y SKU, debe proporcionar los datos de la imagen asociados con esa SKU.

1. Vaya al [Portal de publicación][link-pubportal] e inicie sesión con su cuenta de vendedor.
2. Seleccione la pestaña **Imágenes de VM**.
3. El identificador que aparece en la parte superior de la página es realmente el identificador de la oferta, no de la SKU.
4. Rellene las propiedades de la sección **SKUs**.
5. En **Familia del sistema operativo**, haga clic en el tipo de sistema operativo asociado con el VHD del sistema operativo.
6. En el cuadro **Sistema operativo**, describa el sistema operativo. Considere un formato como familia de sistemas operativos, tipo, versión y actualizaciones. Por ejemplo, "Windows Server Datacenter 2014 R2".
7. Seleccione hasta seis tamaños de máquina virtual recomendados. Estas recomendaciones se muestran al cliente en la hoja del plan de tarifa del Portal de Azure cuando este decide comprar e implementar su imagen.

  > [AZURE.NOTE] Estas son solo recomendaciones. El cliente puede seleccionar cualquier tamaño de máquina virtual que admita los discos especificados en su imagen.

8. Especifique la versión. El campo de versión encapsula una versión semántica para identificar el producto y sus actualizaciones:
  -	Las versiones deben ser del tipo X.Y.Z, donde X, Y y Z son números enteros.
  -	Imágenes de SKU diferentes pueden tener distintas versiones principales y secundarias.
  -	Las versiones dentro de una SKU solo deberían ser los cambios incrementales que aumenten la versión de revisión (Z de X.Y.Z).
9. En el cuadro **Dirección URL del VHD del sistema operativo**, escriba el URI de firma de acceso compartido creado para el VHD del sistema operativo.
10. Si hay discos de datos asociados con esta SKU, seleccione el número de unidad lógica (LUN) donde desea que se monte este disco de datos durante la implementación.
11. En el cuadro **Dirección URL del VHD de LUN X**, escriba el URI de firma de acceso compartido creado para el primer VHD de datos.

    ![dibujo][img-pubportal-vm-skus-2]

## Paso siguiente
Cuando haya terminado con los detalles de SKU, puede avanzar con la [Guía de contenido de marketing de Azure Marketplace][link-pushstaging]. En este paso del proceso de publicación, proporciona el contenido de marketing, precios y demás información necesaria antes del **Paso 3: Prueba de la oferta de máquina virtual en ensayo**, donde prueba varios escenarios de caso de uso antes de implementar la oferta en Azure Marketplace para la compra y visibilidad pública.

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: http://storageexplorer.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=AcomDC_0204_2016-->