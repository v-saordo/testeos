<properties
	pageTitle="Preguntas más frecuentes sobre máquinas virtuales | Microsoft Azure"
	description="Proporciona respuestas a algunas de las preguntas más comunes sobre las máquinas virtuales de Azure creadas con el modelo de implementación clásica"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="cynthn"/>

	
# Preguntas más frecuentes sobre Máquinas virtuales de Azure con el modelo de implementación clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo encontrará respuestas a algunas de las preguntas más comunes de los usuarios sobre las máquinas virtuales de Azure creadas con el modelo de implementación clásico.

## ¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Puede ejecutar versiones recientes de Windows Server, así como varias distribuciones de Linux. Para obtener más información de soporte técnico, consulte:

• Para máquinas virtuales de Windows: [soporte de software del servidor de Microsoft para Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Para máquinas virtuales de Linux: [Linux en distribuciones aprobadas por Azure](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Para imágenes de cliente de Windows, hay disponibles determinadas versiones de Windows 7 y Windows 8.1 para suscriptores de MSDN Azure y suscriptores de pago por uso de desarrollo y prueba de MSDN, para tareas de desarrollo y prueba. Para obtener más información, como instrucciones y limitaciones, consulte [Imágenes de cliente de Windows para los suscriptores de MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## ¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## ¿Qué tipos de disco duro virtual se puede usar?

Azure solo admite discos duros virtuales fijos con formato VHD. Si tiene un disco con formato VHDX que desea usar en Azure, debe convertirlo mediante el Administrador de Hyper-V o el cmdlet [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656). Una vez hecho esto, use el cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (en modo de administración de servicios) para cargar el VHD en una cuenta de almacenamiento de Azure para que pueda usarlo con máquinas virtuales.

- Para obtener instrucciones para Linux, consulte [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-create-upload-vhd.md).

- Para obtener instrucciones para Windows, consulte [Creación y carga de un VHD de Windows Server en Azure](virtual-machines-create-upload-vhd-windows-server.md).

## ¿Son estas máquinas virtuales las mismas que las máquinas virtuales de Hyper-V?

En muchos aspectos son similares a las máquinas virtuales Hyper-V de "generación 1", pero no son exactamente lo mismo. Ambos tipos proporcionan hardware virtualizado, y los discos duros virtuales de formato VHD son compatibles. Esto significa que puede moverlos entre Hyper-V y Azure. Tres diferencias claves que a veces sorprenden a los usuarios de Hyper-V son:

- Azure no proporciona acceso a consolas a una máquina virtual. No hay ninguna manera de obtener acceso a una máquina virtual hasta que se hace arranque.
- Las máquinas virtuales de Azure de la mayoría de [tamaños](virtual-machines-size-specs.md) solo tienen un adaptador de red virtual, lo que significa que también solo pueden tener una dirección IP externa. (Los tamaños A8 y A9 emplean un segundo adaptador de red para la comunicación de aplicaciones entre las instancias en escenarios limitados).
- Las máquinas virtuales de Azure no admiten funciones de máquina virtual de Hyper-V de generación 2. Para obtener más información sobre estas características, consulte [Especificaciones de máquina virtual de Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) y [Introducción a las máquinas virtuales de 2ª generación](https://technet.microsoft.com/library/dn282285.aspx).

## ¿Pueden estas máquinas virtuales usar mi infraestructura de red local existente?

Para las máquinas virtuales creadas en el modelo de implementación clásico, puede usar la red virtual de Azure para extender su infraestructura existente. El enfoque es como configurar una sucursal. Puede aprovisionar y administrar redes privadas virtuales (VPN) en Azure, así como conectarlas de forma segura con la infraestructura de TI local. Para obtener más información, consulte [Información general de la red virtual](../virtual-network/virtual-networks-overview.md).

Necesitará especificar la red a la que desea que pertenezca la máquina virtual al crear la máquina virtual. No puede unir una máquina virtual existente a una red virtual. Sin embargo, puede solucionar este problema separando el disco duro virtual (VHD) de la máquina virtual existente y, a continuación, usarlo para crear una nueva máquina virtual con la configuración de red que desee.

## ¿Cómo puedo tener acceso a mi máquina virtual?

Deberá establecer una conexión remota para iniciar sesión en la máquina virtual, usando Conexión a Escritorio remoto para una máquina virtual de Windows o un Shell seguro (SSH) para una máquina virtual de Linux. Para obtener instrucciones, consulte:

- [Inicio de sesión en una máquina virtual con Windows Server](virtual-machines-log-on-windows-server.md). Se admite un máximo de 2 conexiones simultáneas, a menos que el servidor está configurado como un host de sesión de servicios de escritorio remoto.  
- [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md) De forma predeterminada, SSH permite un máximo de 10 conexiones simultáneas. Puede aumentar este número editando el archivo de configuración.

Si tiene problemas con el Escritorio remoto o SSH, instale y use la extensión [VMAccess](virtual-machines-extensions-features.md); esta le ayudará a solucionar el problema.

En las máquinas virtuales de Windows, entre las opciones adicionales se incluyen:

- En el Portal de Azure clásico, busque la máquina virtual y, a continuación, haga clic en **Restablecer acceso remoto** desde la barra de comandos.
- Revise [Solución de problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)
- Use Acceso remoto a Windows PowerShell para conectarse a la máquina virtual o crear extremos adicionales para que otros recursos se conecten a la máquina virtual. Para obtener más información, consulte [Configuración de puntos de conexión en una máquina virtual](virtual-machines-set-up-endpoints.md).

Si está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no admite el acceso de la consola a una máquina virtual.

## ¿Puedo usar el disco temporal (la unidad D: de Windows o/dev/sdb1 de Linux) para almacenar los datos?

No debería usar el disco temporal (la unidad D: por defecto de Windows o /dev/sdb1 de Linux) para almacenar los datos. Estos discos solamente proporcionan almacenamiento de forma temporal, por lo que se arriesgaría a perder datos que no se pueden recuperar. Esto puede ocurrir cuando se mueve la máquina virtual a un host diferente. Cambiar el tamaño de una máquina virtual, actualizar el host o un error de hardware en el host son algunas de las razones por las que se puede mover una máquina virtual.

## ¿Cómo puedo cambiar la letra de la unidad del disco temporal?

En una máquina virtual de Windows, puede cambiar la letra de la unidad moviendo el archivo de la página y reasignando letras de unidad, pero deberá asegurarse de realizar los pasos en un orden específico. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-change-drive-letter.md).

## ¿Cómo puedo actualizar el sistema operativo invitado?

Generalmente, el término "Actualizar" significa cambiar a una versión más reciente del sistema operativo sin modificar el hardware. En las máquinas virtuales de Azure, el proceso para cambiar a una versión más reciente es diferente para Linux y Windows:

- Las máquinas virtuales de Linux usan las herramientas de administración de paquetes y procedimientos adecuados para la distribución.
- En una máquina virtual de Windows, debe migrar el servidor con algo parecido a las herramientas de migración de Windows Server. No intente actualizar el sistema operativo invitado cuando resida en Azure. No se admite debido al riesgo de perder el acceso a una máquina virtual. Si se producen problemas durante la actualización, podría perder la capacidad de iniciar una sesión de escritorio remoto y no sería capaz de solucionar los problemas. 

Para obtener información general sobre las herramientas y procesos para migrar a Windows Server, consulte [Migración de roles y características a Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).



## ¿Cuál es el nombre de usuario predeterminado y la contraseña en la máquina virtual?

Las imágenes proporcionadas por Azure no tienen un nombre de usuario configurado previamente y una contraseña. Cuando cree la máquina virtual mediante una de esas imágenes, deberá proporcionar un nombre de usuario y una contraseña, que usará para iniciar sesión en la máquina virtual.

Si ha olvidado el nombre de usuario o contraseña y ha instalado el Agente de máquina virtual, puede instalar y usar la extensión [VMAccess](virtual-machines-extensions-features.md) para corregir el problema.

Detalles adicionales:


- En cuanto a las imágenes de Linux, si usa el Portal de Azure clásico, “azureuser” se proporciona como un nombre de usuario predeterminado, pero puede cambiarlo usando “De la Galería” en lugar de “Creación rápida” como forma de crear la máquina virtual. Utilizar “De la Galería” también le permite decidir si desea usar una contraseña, una clave SSH o ambos para iniciar la sesión. La cuenta de usuario es un usuario sin privilegios que tiene acceso “sudo” para ejecutar comandos privilegiados. La cuenta “root” está deshabilitada.


- Para las imágenes de Windows, deberá proporcionar un nombre de usuario y una contraseña al crear la máquina virtual. La cuenta se agrega al grupo de administradores.

## ¿Puede Azure ejecutar antivirus en las máquinas virtuales?

Azure ofrece varias opciones para soluciones antivirus, pero la administración depende de usted. Por ejemplo, podría necesitar una suscripción independiente para el software antimalware y tendrá que decidir cuándo ejecutar análisis e instalar las actualizaciones. Puede agregar soporte antivirus con una extensión de máquina virtual de Microsoft Antimalware, Symantec Endpoint Protection o TrendMicro Deep Security Agent al crear una máquina virtual de Windows o en un momento posterior. Las extensiones de Symantec y TrendMicro le permiten usar una suscripción de prueba gratuita de tiempo limitado o una suscripción existente de empresa. Microsoft Antimalware es gratuito. Para obtener información, consulte:

- [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Implementación de soluciones antimalware en máquinas virtuales de Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## ¿Qué opciones tengo para la copia de seguridad y la recuperación?

Copia de seguridad de Azure está disponible como una vista previa en determinadas regiones. Para obtener más información, consulte [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md). Hay otras soluciones disponibles de socios certificados. Para averiguar lo que está actualmente disponible, busque Azure Marketplace.

Otra opción es usar las capacidades de instantánea del almacenamiento de blobs. Para ello, deberá apagar la máquina virtual antes de cualquier operación que se base en una instantánea de blob. Esto guarda las escrituras de datos pendientes y coloca el sistema de archivos en un estado coherente.

## ¿Cómo cobra Azure mi máquina virtual?

Azure cobra un precio por hora basado en el tamaño y el sistema operativo de la máquina virtual. En las fracciones de hora, solo se cobra por los minutos de uso. Si crea la máquina virtual con una imagen de máquina virtual que contiene determinado software preinstalado, es posible que se apliquen cargos adicionales de software por hora. Azure cobra por separado el almacenamiento de los discos de datos y de sistema operativo de las máquinas virtuales. El almacenamiento en disco temporal es gratuito.

Se le cobrará cuando el estado de la máquina virtual esté en ejecución o detenido, pero no se le cobrará cuando el estado de la máquina virtual sea Detenido (sin asignar). Para poner una máquina virtual en estado Detenido (sin asignar), realice una de las acciones siguientes:

- Apague o elimine la máquina virtual desde el Portal de Azure clásico.
- Use el cmdlet Stop-AzureVM, disponible en el módulo de Azure PowerShell.
- Use la operación de Cerrar rol en la API de REST de administración de servicios y especifique StoppedDeallocated para el elemento PostShutdownAction.

Para obtener más información, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

## ¿Reiniciará Azure mi máquina virtual para efectuar tareas de mantenimiento?

En ocasiones Azure reinicia la máquina virtual como parte de las actualizaciones de mantenimiento regular planeadas en los centros de datos de Azure.

Es posible que se produzcan eventos de mantenimiento no planeados cuando Azure detecte un problema grave de hardware que afecte a la máquina virtual. Para los eventos no planeados, Azure migra la máquina virtual a un host correcto automáticamente y reinicia la máquina virtual.

Para cualquier máquina virtual independiente (es decir, la máquina virtual no forma parte de un conjunto de disponibilidad), Azure envía una notificación al administrador de servicios de la suscripción por correo electrónico al menos una semana antes de efectuar las tareas de mantenimiento planeadas porque las máquinas virtuales pueden reiniciarse durante la actualización. Las aplicaciones que se ejecutan en las máquinas virtuales podrían experimentar tiempos de inactividad.

Asimismo, también puede usar el Portal de Azure clásico o Azure PowerShell para ver los registros de reinicio cuando este se produjo debido a una tarea de mantenimiento planificada. Para obtener más información, consulte [Visualización de registros de reinicio de máquina virtual](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Para proporcionar redundancia, coloque dos o más máquinas virtuales configuradas de manera similar en el mismo conjunto de disponibilidad. Esto ayuda a asegurarse de que haya al menos una máquina virtual esté disponible durante el mantenimiento, sea este planeado o no. Azure garantiza determinados niveles de disponibilidad de la máquina virtual para esta configuración. Para obtener más detalles, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

## Recursos adicionales

[Acerca de máquinas virtuales de Azure](virtual-machines-about.md)

[Diferentes formas de crear una máquina virtual Linux](virtual-machines-linux-choices-create-vm.md)

[Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_0204_2016-->