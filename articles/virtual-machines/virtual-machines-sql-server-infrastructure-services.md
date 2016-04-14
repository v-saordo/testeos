<properties
	pageTitle="Información general de SQL Server en máquinas virtuales | Microsoft Azure"
	description="Este artículo proporciona una descripción general de SQL Server hospedado en máquinas virtuales de Azure. Esto incluye vínculos a contenido en profundidad."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth"/>

# Información general sobre SQL Server en máquinas virtuales de Azure

## Introducción
Puede hospedar [SQL Server en Máquinas virtuales Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) en una variedad de configuraciones, que van desde un servidor de base de datos única para una configuración de varios equipo mediante grupos de disponibilidad AlwaysOn y red Virtual de Azure.

>[AZURE.NOTE] La ejecución de SQL Server en una máquina virtual de Azure es una opción para el almacenamiento de datos relacionales en Azure. También puede utilizar el servicio Base de datos SQL de Azure. Para obtener más información, consulte [Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Para crear una máquina virtual de SQL Server en Azure, primero debe obtener una suscripción a la plataforma de Azure. Puede adquirir una suscripción de Azure en [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/). Para probarla de manera gratuita, visite la [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

### Implementar una instancia de SQL Server en una única máquina virtual

Cuando se haya registrado para una suscripción, la manera más sencilla de implementar una máquina virtual de SQL Server en Azure es [aprovisionar una imagen de la galería de máquinas de SQL Server en Azure](virtual-machines-sql-server-provision-resource-manager.md). Esas imágenes incluyen licencias de SQL Server en el precio de la máquina virtual.

Es importante tener en cuenta que hay dos modelos para crear y administrar máquinas virtuales de Azure: clásica y Administrador de recursos. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Parte de la documentación de SQL Server para máquinas virtuales de Azure aún se refiere exclusivamente al modelo clásico. Estos temas se actualizan con el tiempo para usar el nuevo portal de Azure y el modelo del Administrador de recursos. Para obtener más información, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md).

>[AZURE.NOTE] Cuando sea posible, use el último [Portal de Azure](https://portal.azure.com/) para aprovisionar y administrar máquinas virtuales de SQL Server. De forma predeterminada, usa Almacenamiento premium y ofrece revisiones automatizadas, copias de seguridad automatizadas y configuraciones AlwaysOn.

En la tabla siguiente se ofrece una matriz de imágenes de SQL Server disponibles en la galería de máquinas virtuales.

|Versión de SQL Server|Sistema operativos|Edición de SQL Server|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|Evaluación|

Además de estas imágenes preconfiguradas, también puede [crear una máquina virtual de Azure](virtual-machines-windows-tutorial.md) sin que SQL Server esté preinstalado. Puede instalar cualquier instancia de SQL Server de la que tenga licencia. Migre su licencia a Azure para ejecutar SQL Server en una máquina virtual de Azure mediante la [Movilidad de licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). En este escenario, solo se paga por los [costes](https://azure.microsoft.com/pricing/details/virtual-machines/) de almacenamiento y proceso de Azure asociados a la máquina virtual.

Para determinar la mejor configuración de la máquina virtual para la imagen de SQL Server, revise [Prácticas recomendadas para mejorar el rendimiento de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md). Para cargas de trabajo de producción, **DS3** y **DS2** son los tamaños de máquina virtual mínimos recomendados para las ediciones SQL Server Enterprise y Standard, respectivamente.

Además de revisar las prácticas recomendadas para mejorar el rendimiento, otras tareas iniciales incluyen lo siguiente:

- [Revisar los procedimientos recomendados para mejorar la seguridad de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-security-considerations.md)
- [Configurar la conectividad.](virtual-machines-sql-server-connectivity-resource-manager.md)

### Migración de los datos

Una vez puesta en marcha la máquina virtual de SQL Server, podrá migrar bases de datos existentes al equipo. Existen varias técnicas, pero el asistente para implementación en SQL Server Management Studio funciona bien en la mayoría de los escenarios. Para obtener una explicación de los escenarios y un tutorial del asistente, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

## Alta disponibilidad

Si necesita alta disponibilidad, considere la posibilidad de configurar los grupos de disponibilidad AlwaysOn de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El Portal de Azure dispone de una plantilla que define esta configuración. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la Galería del Portal de Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Si desea configurar manualmente un grupo de disponibilidad y el agente de escucha asociado, consulte los artículos siguientes basados en el modelo de implementación clásica:

- [Configuración de los grupos de disponibilidad AlwaysOn en Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Ampliación de los grupos de disponibilidad AlwaysOn locales a Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Para otras consideraciones sobre alta disponibilidad, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Copia de seguridad y restauración
Con las bases de datos locales, Azure puede actuar como un centro de datos secundario para almacenar los archivos de copia de seguridad de SQL Server. Para obtener información general de las opciones de copia de seguridad y restauración, consulte [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md).

[La copia de seguridad de SQL Server a URL](https://msdn.microsoft.com/library/dn435916.aspx) almacena los archivos de copia de seguridad de Azure en el almacenamiento de blobs de Azure. [La copia de seguridad administrada de SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) le permite programar la copia de seguridad y la retención en Azure. Estos servicios pueden utilizarse con instancias locales de SQL Server o con SQL Server que se ejecuta en máquinas virtuales de Azure. Las máquinas virtuales de Azure también pueden sacar partido de la [copia de seguridad automatizada](virtual-machines-sql-server-automated-backup.md) y de las [revisiones automatizadas](virtual-machines-sql-server-automated-patching.md) para SQL Server.

## Detalles de configuración de imagen de máquina virtual de SQL Server

En las tablas siguientes se describe la configuración de las imágenes de máquina virtual de SQL Server ofrecidas por la plataforma.

### Windows Server

La instalación de Windows Server en la imagen de plataforma contiene los siguientes componentes y valores de configuración:

|Característica|Configuración
|---|---|
|Escritorio remoto|Habilitada para la cuenta de administrador|
|Windows Update|Enabled|
|Cuentas de usuario|De forma predeterminada, la cuenta de usuario especificada durante el aprovisionamiento es miembro del grupo Administradores local. Esta cuenta de administrador también es miembro del rol de servidor sysadmin de SQL Server.|
|Grupos de trabajo|La máquina virtual es miembro de un grupo de trabajo denominado GRUPO DE TRABAJO|
|Cuenta de invitado|Disabled|
|Firewall de Windows con seguridad avanzada|Por|
|.NET Framework|Versión 4|
|Discos|El tamaño seleccionado limita el número de discos de datos que se puede configurar. Consulte [Tamaños de máquina virtual de Azure](virtual-machines-size-specs.md).|

### SQL Server

La instalación de SQL Server en la imagen de plataforma contiene los siguientes componentes y valores de configuración:

|Característica|Configuración|
|---|---|
|Motor de base de datos|Instalado|
|Analysis Services|Instalado|
|Servicios de integración|Instalado|
|Reporting Services|Configurado en modo nativo|
|Grupos de disponibilidad AlwaysOn|Disponible en SQL Server 2012 o versiones posteriores. Requiere [Configuración adicional](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).
|Replicación|Instalado|
|Extracciones de texto completo y semánticas para búsqueda|Instalado (solo extracciones semánticas en SQL Server 2012 o versiones posteriores)|
|Servicios de calidad de datos|Instalado (solo SQL Server 2012 o versiones posteriores)|
|Master Data Services|Instalado (solo SQL Server 2012 o versiones posteriores) Requiere [Componentes y configuración adicionales](https://msdn.microsoft.com/library/ee633752.aspx).
|PowerPivot para SharePoint|Disponible (solo SQL Server 2012 o versiones posteriores) Requiere componentes y configuración adicionales (incluido SharePoint).|
|Distributed Replay Client|Disponible (solo SQL Server 2012 o versiones posteriores), pero no instalado. Consulte [Ejecución del programa de instalación de SQL Server desde la imagen de SQL Server ofrecida por la plataforma](#run-sql-server-setup-from-the-platform-provided-sql-server-image).|
|Herramientas|Todas las herramientas, incluidas SQL Server Management Studio, el Administrador de configuración de SQL Server, Business Intelligence Development Studio, el programa de instalación de SQL Server, Conectividad con las herramientas de cliente, el SDK de las herramientas de cliente y el SDK de conectividad de cliente SQL, y herramientas de migración y actualización, como las aplicaciones de nivel de datos (DAC), copias de seguridad, restauración, agregar y desasociar.|
|Libros en pantalla de SQL Server|Instalada pero requiere configuración mediante el Visor de Ayuda.|

### Configuración del motor de base de datos

Se configuran los siguientes valores del motor de base de datos. Para obtener más opciones, examine la instancia de SQL Server.

|Característica|Configuración|
|---|---|
|Instance|Contiene una instancia (sin nombre) predeterminada del Motor de base de datos de SQL Server, que escucha solo en el protocolo de memoria compartida.|
|Autenticación|De forma predeterminada, Azure selecciona la Autenticación de Windows durante la instalación de la máquina virtual de SQL Server. Si quiere usar el inicio de sesión de sa o crear una nueva cuenta de SQL Server, deberá cambiar el modo de autenticación. Para obtener más información, consulte [Consideraciones de seguridad para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-security-considerations.md).|
|sysadmin|El usuario que instaló la máquina virtual es inicialmente el único miembro del rol de servidor fijo de sysadmin de SQL Server|
|Memoria|La memoria del motor de base de datos se establece en la configuración de memoria dinámica.|
|Autenticación de base de datos independiente|Off|
|Idioma predeterminado|English|
|Encadenamiento de propiedad entre bases de datos|Off|

### Programa para la mejora de la experiencia del cliente (CEIP)

El [Programa para la mejora de la experiencia del cliente (CEIP)](https://technet.microsoft.com/library/cc730757.aspx) está habilitado. Puede deshabilitar el CEIP mediante la utilidad Informes de uso y errores de SQL Server. Para iniciar la utilidad Informes de uso y errores de SQL Server, en el menú de inicio, haga clic en Todos los programas, versión de Microsoft SQL Server, Herramientas de configuración y luego en Informes de uso y errores de SQL Server. Si no quiere usar una instancia de SQL Server con el CEIP habilitado, también puede implementar su propia imagen de máquina virtual en Azure. Para obtener más información, vea [Creación y carga de un disco duro virtual que contiene el sistema operativo Windows Server](virtual-machines-create-upload-vhd-windows-server.md).

## Ejecutar el programa de instalación de SQL Server desde la imagen de SQL Server ofrecida por la plataforma

Si crea una máquina virtual mediante una imagen de SQL Server ofrecida por la plataforma, puede encontrar los medios de instalación de SQL Server guardados en la máquina virtual en el directorio **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full**. Puede ejecutar el programa de instalación desde este directorio para llevar a cabo cualquier acción de instalación como agregar o quitar características, agregar una nueva instancia o reparar la instancia si lo permite el espacio en disco.

>[AZURE.NOTE] Azure ofrece varias versiones de las imágenes de SQL Server. Si la fecha de lanzamiento de la versión de la imagen ofrecida por la plataforma de SQL Server es el 15 de mayo de 2014 o posterior, contiene la clave de producto de forma predeterminada. Si aprovisiona una máquina virtual mediante una imagen de SQL Server ofrecida por la plataforma que se publica antes de esta fecha, esa máquina virtual no contiene la clave de producto. Como práctica recomendada, se recomienda seleccionar siempre la versión de la imagen más reciente al aprovisionar una nueva máquina virtual.

## Recursos

- [Aprovisionamiento de una máquina virtual de SQL Server en Azure (Administrador de recursos)](virtual-machines-sql-server-provision-resource-manager.md)
- [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md)
- [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Máquinas virtuales de Azure](virtual-machines-about.md)

<!---HONumber=AcomDC_0204_2016-->