<properties
	pageTitle="Aprovisionamiento de una máquina virtual de SQL Server en el Administrador de recursos de Azure (GUI) | Microsoft Azure"
	description="Creación de una máquina virtual de SQL Server en el modo de Administrador de recursos de Azure. Este tutorial usa principalmente la interfaz de usuario y herramientas en lugar de scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Aprovisionamiento de una máquina virtual de SQL Server en el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Este tutorial de extremo a extremo muestra cómo aprovisionar una máquina virtual de Azure en el portal mediante el modelo del Administrador de recursos de Azure y cómo configurar SQL Server desde una plantilla en la galería de Azure.

La galería de máquinas virtuales (VM) de Azure incluye varias imágenes que contienen Microsoft SQL Server. Puede seleccionar una de las imágenes de máquina virtual en la galería y, con unos pocos clics, puede aprovisionar la máquina virtual a su entorno de Azure.

En este tutorial, aprenderá lo siguiente:

- [Conectarse al portal de Azure y aprovisionar una imagen de la máquina virtual de SQL desde la galería con el modelo de implementación del Administrador de recursos](#Provision)

- [Configurar la máquina virtual y SQL Server](#ConfigureVM)

- [Abrir la máquina virtual mediante el Escritorio remoto](#Open)

- [Conectarse a la instancia de SQL Server con SQL Server Management Studio en otro equipo](#Connect)

- [Pasos siguientes](#Next)

En este tutorial se supone que ya tiene una cuenta de Azure. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a id="Provision">Aprovisionar una imagen de la máquina virtual de SQL desde la galería con el modelo de implementación del Administrador de recursos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) con su cuenta.
1. En el Portal de Azure, haga clic en **Nuevo**. El Portal abrirá la hoja **Nuevo**. Las plantillas de máquina virtual de SQL Server están en el grupo **Proceso** de Marketplace.

1. En la hoja **Nuevo**, haga clic en **Proceso**.
1. Para ver todos los tipos de recursos en la hoja **Proceso**, haga clic en **Ver todos**. <br/> ![Hoja de proceso de Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-blade.png) <br/>
1. En **Servidores de bases de datos**, haga clic en **SQL Server** para ver todas las plantillas disponibles para SQL Server. Es posible que deba desplazarse hacia abajo para buscar en **Servidores de bases de datos**.
1. 	Cada plantilla identifica una versión de SQL Server y un sistema operativo. Seleccione una de estas imágenes de la lista para abrir una hoja que contiene los detalles.
1.	La hoja de detalles ofrece una descripción de esta imagen de máquina virtual y le permite seleccionar un modelo de implementación. En **Seleccionar un modelo de implementación**, seleccione **Administrador de recursos** y haga clic en **Crear**. <br/> ![Hoja de proceso de Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> Configuración de la máquina virtual
En el Portal de Azure, hay cinco hojas para configurar una máquina virtual de SQL Server.

1.	Configuración básica
1.	Elección del tamaño de la máquina virtual
1.	Configuración de la máquina virtual
1.	Configuración de SQL Server
1.	Revisión del resumen

## 1\. Configuración básica
En la hoja **Crear máquina virtual** en **Datos básicos** proporcione la siguiente información:

* Un **nombre** exclusivo para la máquina virtual.
* En el cuadro **Nombre de usuario**, un nombre de usuario exclusivo para la cuenta de administrador local de la máquina. Esta cuenta también será miembro del rol fijo de servidor de sysadmin de SQL Server.
* En el cuadro **Contraseña**, escriba una contraseña segura.
* Si tiene varias suscripciones, compruebe que la suscripción es correcta para la máquina virtual que se va a compilar.
* En el cuadro **Grupo de recursos**, escriba un nombre para el grupo de recursos. O bien, utilice un grupo de recursos existente haciendo clic en **Seleccionar existente**. Un grupo de recursos es una colección de servicios relacionados de Azure. Para más información sobre los grupos de recursos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md). Compruebe que la **ubicación** es correcta para sus requisitos.
* Haga clic en **Aceptar** para guardar los cambios. <br/>

>![Conceptos básicos de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-basic.png) <br/>

## 2\. Elección del tamaño de la máquina virtual
En la hoja **Crear máquina virtual** en **Tamaño** elija un tamaño de máquina virtual. El Portal de Azure mostrará los tamaños recomendados. Para más información sobre los tamaños de máquina virtual, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md). Los tamaños se basan en la plantilla seleccionada. El tamaño calcula el costo mensual para ejecutar la máquina virtual. Seleccione un tamaño de máquina virtual para el servidor. Para más información acerca de los tamaños de máquinas virtuales de SQL Server, consulte [Prácticas recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).

## 3\. Configuración de la máquina virtual
En la hoja **Crear máquina virtual** en **Configuración**, configure el Almacenamiento de Azure, las redes y la supervisión de la máquina virtual.

- En **Almacenamiento** especifique un tipo de disco. Se recomienda Almacenamiento premium para cargas de trabajo de producción.

>[AZURE.NOTE] Almacenamiento premium está habilitado de forma predeterminada. Esto cambia automáticamente el tamaño del equipo a un tamaño que admita Almacenamiento premium. Si deshabilita el Almacenamiento premium, se utilizará la selección anterior de tamaño de equipo.

- En **Cuenta de almacenamiento**, puede aceptar el nombre aprovisionado automáticamente para la cuenta de almacenamiento, o puede hacer clic en **Cuenta de almacenamiento** para elegir una cuenta existente y configurar el tipo de cuenta de almacenamiento. De forma predeterminada, Azure crea una nueva cuenta de almacenamiento con redundancia local.

- En **Red**, puede aceptar los valores rellenados automáticamente para las características o hacer clic en cada una de ellas para configurar la **red virtual**, la **subred**, la **dirección IP pública** y el **grupo de seguridad de red**. De forma predeterminada, Azure configura automáticamente estos valores.

- Azure habilita la **supervisión** de forma predeterminada con la misma cuenta de almacenamiento designada para la máquina virtual. Puede cambiar estas opciones aquí.

- En **Conjunto de disponibilidad** especifique un conjunto de disponibilidad. Por lo que respecta a este tutorial, puede seleccionar la opción **ninguno**. Si va a configurar grupos de disponibilidad AlwaysOn de SQL, configure la disponibilidad para evitar volver a crear la máquina virtual. Para obtener más información, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

## 4\. Configuración de SQL Server
En la hoja **Crear máquina virtual** en **Configurar SQL Server** configure los valores y optimizaciones específicos de SQL Server. Entre los valores que se pueden configurar para SQL Server se incluyen:

- Conectividad
- Autenticación
- Optimización del almacenamiento
- Aplicación de revisiones
- Copias de seguridad
- Integración del Almacén de claves

### Conectividad
En **Conectividad SQL**, especifique **Público (Internet)** para permitir conexiones a SQL Server desde equipos o servicios de Internet. Con esta opción seleccionada, Azure configurará automáticamente el firewall y el grupo de seguridad de red para permitir el tráfico en el puerto 1433. <br/>![Conectividad de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-connectivity-alt.png) <br/>

Para conectarse a SQL Server a través de Internet, también debe habilitar la autenticación de SQL Server.

>[AZURE.NOTE]Por seguridad, restrinja el puerto de origen mediante el grupo de seguridad de red. Para más información, consulte [¿Qué es un grupo de seguridad de red (NSG)?](virtual-networks-nsg.md)

Si prefiere no habilitar las conexiones con el motor de base de datos a través de Internet automáticamente, elija una de las siguientes opciones:- **Local (solo dentro de la máquina virtual)** para permitir conexiones a SQL Server solo desde dentro de la máquina virtual. - **Privada (dentro de la red virtual)** para permitir conexiones a SQL Server desde equipos o servicios en la misma red virtual.


El **puerto** predeterminado es el 1433. Puede especificar un número de puerto diferente. Para más información, consulte [Conexión a una máquina virtual de SQL Server (Administrador de recursos) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).



### Autenticación
Si requiere autenticación de SQL Server, haga clic en **Habilitar** en **Autenticación de SQL**.

<br/>![Autenticación de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-authentication.png) <br/>


Si habilita la autenticación de SQL Server, especifique un **nombre de inicio de sesión** y una **contraseña**. Este nombre de usuario será un inicio de sesión de autenticación de SQL Server y miembro del rol fijo de servidor sysadmin. Para más información acerca de los modos de autenticación, consulte [Elegir un modo de autenticación](http://msdn.microsoft.com/library/ms144284.aspx). De forma predeterminada, SQL Server no habilita la autenticación de SQL Server. En ese caso, los administradores locales en la máquina virtual pueden conectarse a la instancia de SQL Server.

>[AZURE.NOTE] Si piensa obtener acceso a SQL Server a través de Internet (es decir, con la opción de conectividad pública), debe habilitar aquí la autenticación de SQL. El acceso público a SQL Server requiere la utilización de autenticación de SQL.

### Optimización del almacenamiento
Haga clic en **Configuración de almacenamiento** para especificar los requisitos de almacenamiento. Puede especificar requisitos como operaciones de entrada/salida por segundo (IOPS), el rendimiento en MB/s y el tamaño de almacenamiento total. Configure estas opciones mediante las escalas deslizantes. El portal calcula automáticamente el número de discos según estos requisitos.

De forma predeterminada, Azure optimiza el almacenamiento de 5000 IOPS, 200 MB y 1 TB de espacio de almacenamiento. Puede cambiar estos valores de almacenamiento según la carga de trabajo. En **Almacenamiento optimizado para**, seleccione una de las siguientes opciones

- **General** es la configuración predeterminada y es compatible con la mayoría de las cargas de trabajo.
- El procesamiento **Transaccional** optimiza el almacenamiento para las cargas de trabajo OLTP de bases de datos tradicionales.
- **Almacenamiento de datos** optimiza el almacenamiento para cargas de trabajo de informes y análisis.

La siguiente imagen muestra la hoja de configuración de almacenamiento. <br/>![Almacenamiento de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] Los límites de la configuración de almacenamiento dependen del tamaño de la máquina virtual. Para más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

### Aplicación de revisiones
La opción **Aplicación de revisión automatizada de SQL** está habilitada de forma predeterminada. La aplicación de revisiones automatizada permite a Azure aplicar automáticamente las revisiones de SQL Server y del sistema operativo. Especifique un día de la semana, la hora y la duración de una ventana de mantenimiento. Azure realizará la aplicación de revisión en la ventana de mantenimiento. La programación de la ventana de mantenimiento utiliza la configuración regional de la máquina virtual para la hora. Si no desea que Azure realice la aplicación de revisiones de SQL Server y del sistema operativo automáticamente, haga clic en **Deshabilitar**.

<br/>![Aplicación de revisiones de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-patching.png) <br/>

Para más información, consulte [Aplicación de revisión automatizada para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-automated-patching.md).

### Copias de seguridad
Habilite las copias de seguridad automáticas de la base de datos para todas las bases de datos en **Copia de seguridad automatizada de SQL**. Cuando habilita esta opción puede configurar lo siguiente:

- Período de retención de copia de seguridad en días
- La cuenta de almacenamiento que se utilizará para las copias de seguridad
- Si cifrar o no la copia de seguridad. Para cifrar la copia de seguridad, haga clic en **Habilitar**. Si se cifran las copias de seguridad automatizadas, especifique una contraseña. Azure crea un certificado para cifrar las copias de seguridad y utiliza la contraseña especificada para proteger ese certificado.

<br/>![Copia de seguridad de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-autobackup.png) <br/>

 Para obtener más información, vea [Copia de seguridad automatizada para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-automated-backup.md).

### Integración del Almacén de claves
Para almacenar información confidencial de seguridad en Azure para el cifrado, haga clic en **Integración del Almacén de claves de Azure** y haga clic en **Habilitar**.

<br/>![Integración con el Almacén de claves de Azure de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-akv.png) <br/>

En la tabla siguiente se enumeran los parámetros necesarios para configurar la integración del Almacén de claves de Azure.

|PARÁMETRO|DESCRIPCIÓN|EJEMPLO:|
|----------|----------|-------|
|**Dirección URL del Almacén de claves** | La ubicación del Almacén de claves.|https://contosokeyvault.vault.azure.net/ |
|**Nombre de la entidad de seguridad de AKV** |Nombre de la entidad de servicio de Azure Active Directory Esto se conoce también como Id. de cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Secreto de entidad de seguridad de AKV**|La integración de AKV crea una credencial en SQL Server, permitiendo el acceso de la máquina virtual al Almacén de claves. Elija un nombre para esta credencial. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Nombre de credencial**|Elija un nombre para identificar esta credencial.| mycred1|

Para más información, consulte [Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-azure-key-vault-integration.md).

## 5\. Revisión del resumen
Revise el resumen y haga clic en **Aceptar** crear SQL Server, el grupo de recursos y los recursos especificados para esta máquina virtual. Puede supervisar la implementación desde el portal de Azure. El botón **Notificaciones** situado en la parte superior de la pantalla muestra el estado básico de la implementación.

##<a id="Open"> Apertura de la máquina virtual con Escritorio remoto y finalización de la configuración
Siga estos pasos para usar Escritorio remoto para abrir la máquina virtual:

1.	Después de crear la máquina virtual de Azure, aparecerá un icono de la misma en el panel de Azure. Haga clic en el icono para ver información acerca de la máquina virtual.
1.	En la parte superior de la hoja de máquina virtual, haga clic en **Conectar**. El explorador descargará un archivo .rdp para la máquina virtual. Abra el archivo .rdp
1.	Conexión a Escritorio remoto le notificará que el publicador de esta conexión remota no se puede identificar y le preguntará si desea conectarse de todos modos. Haga clic en **Conectar**.
1.	En el cuadro de diálogo **Seguridad de Windows**, haga clic en **Usar otra cuenta**. Como **Nombre de usuario** escriba el <machine name><nombre de usuario> que especificó al configurar la máquina virtual.

Una vez conectado a la máquina virtual de SQL Server, puede iniciar SQL Server Management Studio y conectarse con la autenticación de Windows mediante sus credenciales de administrador local. Esto también le permite cambiar la configuración del firewall o de SQL Server con posterioridad al aprovisionamiento, si es necesario.

##<a id="Connect">Conexión a SQL Server a través de Internet

Si desea conectarse al motor de base de datos de SQL Server desde Internet, debe completar varios pasos, como configurar el firewall, habilitar la autenticación de SQL Server y configurar el grupo de seguridad de red. Debe tener una regla de grupo de seguridad de red para permitir el tráfico TCP en el puerto 1433.

Si utiliza el portal para aprovisionar una imagen de máquina virtual de SQL Server con el administrador de recursos, estos pasos se realizaron de forma automática cuando seleccionó **Público** como opción de conectividad de SQL y habilitó la autenticación de SQL Server Sin embargo, hay algunos pasos restantes necesarios para tener acceso a la instancia de SQL Server a través de Internet.

>[AZURE.NOTE] Si no seleccionó la opción Público durante el aprovisionamiento, son necesarios pasos adicionales para tener acceso a la instancia de SQL Server a través de Internet. Para más información, consulte [Conexión a una máquina virtual de SQL Server (Administrador de recursos) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).

Los siguientes pasos no son necesarios si solo necesita tener acceso a la máquina virtual de forma local o desde la misma red virtual.

> [AZURE.INCLUDE [Conexión a SQL Server en el Administrador de recursos de una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next"> Pasos siguientes
Para obtener más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Máquinas virtuales de Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->