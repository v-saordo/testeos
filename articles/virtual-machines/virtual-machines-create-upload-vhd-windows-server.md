<properties
	pageTitle="Crear y cargar un VHD de Windows Server mediante Powershell | Microsoft Azure"
	description="Obtenga información acerca de cómo crear y cargar un disco duro virtual (VHD) basado en Windows Server mediante el modelo de implementación clásica y Azure Powershell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Crear y cargar un VHD de Windows Server a Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo que podrá utilizar como imagen para crear máquinas virtuales basadas en dicha imagen. Para obtener más detalles sobre discos y VHD en Microsoft Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md).



## Requisitos previos

En este artículo se supone que ya dispones de:

1. **Suscripción de Azure** - Si no tiene una, puede [abrir una cuenta de Azure de forma gratuita](/pricing/free-trial/?WT.mc_id=A261C142F): obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá conservar la cuenta y usar los servicios gratuitos de Azure, como Sitios Web. Nunca se va a hacer ningún cargo a tu tarjeta de crédito, a menos que cambies explícitamente la configuración y pidas que se haga algún cargo. También puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): la suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.

2. **Microsoft Azure PowerShell** - Tiene el módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción. Para descargar el módulo, consulte [Descargas de Microsoft Azure](https://azure.microsoft.com/downloads/). Hay disponible un tutorial para instalar y configurar el módulo [aquí](../powershell-install-configure.md). Usará el cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) para cargar el VHD.

3. **Un sistema operativo Windows compatible almacenado en un archivo .vhd y conectado a una máquina virtual** - Existen varias herramientas para crear archivos .vhd. Por ejemplo, puedes usar Hyper-V para crear una máquina virtual e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx). Para obtener más información sobre los sistemas operativos, consulte [Soporte de software del servidor de Microsoft para máquinas virtuales de Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550).

> [AZURE.IMPORTANT] El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Para obtener más información, consulta esta [publicación de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Paso 1: Preparar el VHD 

Antes de cargar el VHD en Azure, tiene que generalizarse mediante la herramienta Sysprep. Esto prepara el VHD para usarlo como una imagen. Para obtener más información sobre Sysprep, consulta [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Desde la máquina virtual en la que se instaló el sistema operativo, realice el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

	![Abrir una ventana de símbolo del sistema](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema**.

	![Iniciar Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  En la **Herramienta de preparación del sistema**, selecciona **Iniciar la Configuración rápida (OOBE) del sistema** y asegúrate de que la casilla **Generalizar** está seleccionada.

5.  En **Opciones de apagado**, seleccione **Apagar**.

6.  Haga clic en **Aceptar**.

## Paso 2: Crear u obtener información de tu cuenta de almacenamiento de Azure

Necesitas una cuenta de almacenamiento de Azure para tener un sitio para cargar el archivo .vhd. Este paso te muestra cómo crear una cuenta u obtener la información que necesitas de una cuenta existente.

### Opción 1: crear de una cuenta de almacenamiento

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. En la barra de comandos, haga clic en **Nuevo**.

3. Haga clic en **Servicios de datos** > **Almacenamiento** > **Creación rápida**.

	![Crear rápidamente una cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Rellene los campos de la manera siguiente:

 - En **URL**, escriba un nombre de subdominio que vaya a usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host de la URL que usas para acceder a los recursos de la suscripción blob, cola o tabla.
 - Elija **la ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite colocar sus servicios y almacenamiento en la nube en el mismo centro de datos.
 - Decida si va a utilizar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste, por lo que su almacenamiento conmuta por error a esa ubicación si se produce un error importante en la ubicación principal. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o las directivas de su organización, puede desactivar la replicación geográfica. Sin embargo, si más tarde activas la replicación geográfica, se te cobrará una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Para obtener más información, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

      ![Escribir los detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Haga clic en **Crear cuenta de almacenamiento**. La cuenta aparece ahora en **Almacenamiento**.

	![Cuenta de almacenamiento creada correctamente](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Después cree un contenedor para los VHD que ha cargado. Haga clic en el nombre de la cuenta de almacenamiento y, a continuación, en **Contenedores**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Haga clic en **Crear un contenedor**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

	![Nombre del contenedor](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir el acceso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos del contenedor, use la opción **Blob público**. Para permitir el acceso de lectura público completo para el contenedor y los blobs, utilice la opción **Contenedor público**.

### Opción 2: obtener la información de la cuenta de almacenamiento

1.	Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2.	En el panel de navegación, haz clic en **Almacenamiento**.

3.	Haz clic en el nombre de la cuenta de almacenamiento y después haz clic en **Panel**.

4.	En el panel, en **Servicios**, mantén el ratón sobre la dirección URL de Blobs, haz clic en el icono del portapapeles para copiar la dirección URL y después pega y guárdala. La podrás usar al generar el comando para cargar el VHD.

## Paso 3: conéctate a tu suscripción de Azure PowerShell

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

> [AZURE.TIP] Para empezar a usar Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Para obtener información general, consulta [Introducción a los cmdlets de Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Opción 1: usar Microsoft Azure AD

1. Abra la consola de Azure PowerShell.

2. Escriba: `Add-AzureAccount`

3.	En las ventanas de inicio de sesión de Windows, escribe el nombre de usuario y la contraseña de tu cuenta profesional o educativa.

4. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

### Opción 2: usar un certificado

1. Abra la consola de Azure PowerShell.

2.	Escriba: `Get-AzurePublishSettingsFile`.

3. Se abre una ventana del explorador que le solicita que descargue un archivo .publishsettings. Contiene información y un certificado para su suscripción de Microsoft Azure.

	![Página de descarga del explorador](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings.

4. Escriba: `Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

## Paso 4: Carga del archivo .vhd

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs.

1. Desde la ventana de Azure PowerShell que usaste en el paso anterior, escribe un comando parecido a este:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	Donde:- **BlobStorageURL** es la dirección URL de la cuenta de almacenamiento - **YourImagesFolder** es el contenedor del almacenamiento de blobs donde quiere almacenar las imágenes - **VHDName** es el nombre que quiere que muestre el Portal de Azure clásico para identificar el disco duro virtual - **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

Para obtener más información acerca del cmdlet Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Paso 5: incorporación de la imagen a la lista de imágenes personalizadas

> [AZURE.TIP] Para usar Azure PowerShell en lugar del Portal de Azure clásico para agregar la imagen, use el cmdlet **Add-AzureVMImage**. Por ejemplo:

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. En el Portal de Azure clásico, en **Todos los elementos**, haga clic en **Máquinas virtuales**.

2. En Máquinas virtuales, haga clic en **Imágenes**.

3. Haz clic en **Crear una imagen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. En la ventana **Crear una imagen desde un disco duro virtual**:

	- Especifica el **nombre**.

	- Especifica la **descripción**.

	- En **la dirección URL del VHL** haz clic en el icono de carpeta para abrir la ventana **Buscar en el almacenamiento en la nube**. Busca el archivo .vhd y después haz clic en **Abrir**.

    ![Seleccionar VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

5.	En la ventana **Crear una imagen desde un VHD**, en **Familia del sistema operativo**, selecciona tu sistema operativo. Active la casilla **He ejecutado Sysprep en la máquina virtual asociada a este disco duro virtual** para comprobar que generalizó el sistema operativo y después haga clic en **Aceptar**.

    ![Agregar imagen](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

6. Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes**.

	![imagen personalizada](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Esta nueva imagen ahora está disponible en **Mis imágenes** al crear una máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](virtual-machines-create-custom.md).

	![Creación de una máquina virtual a partir de una imagen personalizada](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP] Si se produce un error al intentar crear una máquina virtual, con este mensaje de error: "El VHD https://XXXXX.. tiene un tamaño virtual no compatible de YYYY bytes. El tamaño debe ser un número entero (en MB)", significa que el disco duro virtual no es un número entero de MB y debe ser un disco duro virtual de tamaño fijo. Pruebe a usar el cmdlet de PowerShell **Add-AzureVMImage** en lugar del Portal de Azure clásico para agregar la imagen (consulte el paso 5 anterior). Los cmdlets de Azure se aseguran de que el disco duro virtual cumple los requisitos de Azure.



[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=AcomDC_0128_2016-->