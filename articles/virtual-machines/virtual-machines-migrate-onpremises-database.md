<properties
	pageTitle="Migración de una Base de datos SQL Server a SQL Server en una máquina virtual | Microsoft Azure"
	description="Obtenga información sobre cómo migrar una base de datos de usuario local a SQL Server en una máquina virtual de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="carlrab"/>


# Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Existen varios métodos para migrar una base de datos de usuario de SQL Server local a SQL Server en una máquina virtual de Azure. En este artículo se describirán brevemente diversos métodos, se recomendará el mejor método para diversos escenarios y se incluirá un [tutorial](#azure-vm-deployment-wizard-tutorial) que le guiará a través del uso del Asistente para **implementación de una base de datos SQL Server en una máquina virtual de Microsoft Azure**.

## ¿Cuáles son los principales métodos de migración?

Los principales métodos de migración son:

- Usar el Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure.
- Realizar copia de seguridad local con compresión y copiar manualmente el archivo de copia de seguridad en la máquina virtual de Azure.
- Realizar una copia de seguridad en una dirección URL y restaurarla en la máquina virtual de Azure desde dicha dirección URL.
- Desasociar y, a continuación, copiar los archivos de datos y de registro en el almacenamiento de blobs de Azure y, seguidamente, asociarlos a SQL Server en la máquina virtual de Azure desde una dirección URL.
- Convertir una máquina física de local a VHD de Hyper-V, cargarla en el almacenamiento de blobs de Azure y, a continuación, implementarla como una máquina virtual nueva con el VHD cargado
- Enviar la unidad de disco duro de envío con el servicio de importación y exportación de Windows
- Si tiene una implementación AlwaysOn local, utilice el [Asistente para agregar una réplica de Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md) para crear una réplica en Azure y, luego, ejecute una conmutación por error para dirigir a los usuarios a la instancia de base de datos de Azure.
- Utilice la [replicación transaccional](https://msdn.microsoft.com/library/ms151176.aspx) de SQL Server para configurar la instancia de Azure SQL Server como suscriptor y, luego, deshabilite la replicación y dirija a los usuarios a la instancia de base de datos de Azure.



## Elección del método de migración

Para obtener un rendimiento óptimo de la transferencia de datos, el mejor método suele ser la migración de los archivos de base de datos a la máquina virtual de Azure con un archivo de copia de seguridad comprimido. Este es el método que usa el [Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure](#azure-vm-deployment-wizard-tutorial). Este asistente es el método recomendado para migrar una base de datos de usuario local que se ejecuta en SQL Server 2005, o cualquier versión superior, a SQL Server 2014, o cualquier versión superior, cuando el archivo de copia de seguridad de base de datos comprimido tiene menos de 1 TB.

Para minimizar el tiempo de inactividad durante el proceso de migración de la base de datos, utilice la opción AlwaysOn o la opción de replicación transaccional.

Si no es posible usar los métodos anteriores, migre la base de datos de forma manual. Por lo general, con este método comenzará con una copia de seguridad de la base de datos y luego la copiará en Azure para, posteriormente, realizar una restauración de la base de datos. También es posible copiar los archivos mismos de la base de datos en Azure y, luego, anexarlos. Existen varios métodos para llevar a cabo este proceso manual de migración de una base de datos a una máquina virtual de Azure.

> [AZURE.NOTE] Al actualizar a SQL Server 2014 o SQL Server 2016 desde versiones anteriores de SQL Server, es preciso considerar si es preciso realizar cambios. Como parte del proyecto de migración es aconsejable solucionar todas las dependencias de características no compatibles con la nueva versión de SQL Server. Para obtener más información sobre los escenarios y las ediciones compatibles, consulte [Actualización a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

En la tabla siguiente se muestran los principales métodos de migración y se explica en qué circunstancias es más apropiado usar cada uno de ellos.

| Método | Versión de base de datos de origen | Versión de base de datos de destino | Restricción del tamaño de copia de seguridad de la base de datos de origen | Notas |
|---|---|---|---|---|
| [Usar el Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 o superior | SQL Server 2014 o superior | > 1 TB | Es el método más rápido y sencillo. Úselo siempre que sea posible para migrar a una instancia de SQL Server nueva o existente en una máquina virtual de Azure. | 
| [Uso del Asistente para agregar una réplica de Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md) | SQL Server 2012 o superior | SQL Server 2012 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Minimiza el tiempo de inactividad; utilícelo cuando tenga una implementación local de AlwaysOn |
| [Uso de la replicación transaccional de SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilícelo cuando necesite minimizar el tiempo de inactividad y no tenga una implementación local de AlwaysOn |
| [Realizar una copia de seguridad local con compresión y copiar manualmente el archivo de copia de seguridad en la máquina virtual de Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Se usa solo cuando no se puede usar al asistente, por ejemplo, cuando la versión de la base de datos de destino es anterior a SQL Server 2012 SP1 CU2 o cuando el tamaño de la copia de seguridad de la base de datos es mayor que 1 TB (12,8 TB con SQL Server 2016). |
| [Realizar una copia de seguridad a una dirección URL y restaurarla en la máquina virtual de Azure desde dicha dirección URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 o superior | SQL Server 2012 SP1 CU2 o superior | > 1 TB (en el caso SQL Server 2016 < 12,8 TB) | Por lo general, el uso de [copia de seguridad en URL](https://msdn.microsoft.com/library/dn435916.aspx) es equivalente, en cuanto a rendimiento, al uso del asistente y no es tan sencillo |
| [Desasociar y, a continuación, copiar los archivos de datos y de registro en el almacenamiento de blobs de Azure y, a continuación, asociarlos a SQL Server en la máquina virtual de Azure desde una URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 o superior | SQL Server 2014 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Este método se usa cuando se pretenden [almacenar estos archivos mediante el servicio de almacenamiento de blobs de Azure](https://msdn.microsoft.com/library/dn385720.aspx) y adjuntarlos a SQL Server en una VM de Azure, especialmente con bases de datos muy grandes. |
| [Convertir máquina local en VHD de Hyper-V, cargar en el almacenamiento de blobs de Azure y, a continuación, implementar una nueva máquina virtual con el VHD cargado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Se usa cuando el usuario [tiene su propia licencia de SQL Server](../data-management-azure-sql-database-and-sql-server-iaas/), cuando se migra una base de datos que se va a ejecutar en una versión anterior de SQL Server o cuando se migran bases de datos de usuario y del sistema conjuntamente como parte de la migración de base de datos dependiente de otras bases de datos de usuario o bases de datos del sistema. |
| [Envío de unidad de disco duro con el servicio de importación y exportación de Windows](#ship-hard-drive) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de máquina virtual de Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Use el [servicio de importación y exportación de Windows](../storage-import-export-service/) cuando el método de copia manual sea demasiado lento, como por ejemplo, en el caso de bases de datos muy grandes. |

## Tutorial del Asistente para implementación de máquina virtual de Azure

Use el Asistente para **implementación de una base de datos SQL Server en una máquina virtual de Microsoft Azure** en Microsoft SQL Server Management Studio para migrar una base de datos de usuario local SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 o SQL Server 2016 (de hasta 1 TB) a SQL Server 2014 o SQL Server 2016 en una máquina virtual de Azure. Este asistente se usa para migrar una base de datos de usuario a una máquina virtual de Azure existente o a una máquina virtual de Azure con SQL Server que crea el asistente en el proceso de migración. Al migrar una base de datos a una versión más reciente de SQL Server, la base de datos se actualiza automáticamente durante el proceso.

### Obtención de última versión del Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure

Use la última versión de Microsoft SQL Server Management Studio para SQL Server para asegurarse de que tiene la versión más reciente del Asistente para **implementación de una base de datos SQL Server en una máquina virtual de Microsoft Azure**. La última versión de este asistente incorpora las últimas actualizaciones al Portal de Azure clásico y admite las imágenes de máquina virtual de Azure más recientes de la galería (en las versiones anteriores del asistente pueden no funcionar). Para obtener la última versión de Microsoft SQL Server Management Studio para SQL Server, [descárguelo](http://go.microsoft.com/fwlink/?LinkId=616025) e instálelo en un equipo cliente con conectividad a la base de datos que va a migrar y a Internet.

### Configuración de la máquina virtual de Azure existente y de una instancia de SQL Server (si procede)

Si va a realizar la migración a una máquina virtual de Azure existente, es preciso llevar a cabo se los siguientes pasos de configuración:

- Configure la máquina virtual de Azure y la instancia de SQL Server para habilitar la conectividad desde otro equipo, para lo que debe seguir los pasos de la sección Conexión a la instancia de máquina virtual de SQL Server desde SSMS en otro equipo de [Aprovisionamiento de una máquina virtual de SQL Server en Azure](../virtual-machines-provision-sql-server/#SSMS). Si la migración se realiza con el asistente, solo se admitirán las imágenes de SQL Server 2014 y SQL Server 2016 de la galería.
- Configure un extremo abierto para el servicio del adaptador para la nube de SQL Server en la puerta de enlace de Microsoft Azure con el puerto privado 11435. Este puerto se crea como parte del aprovisionamiento de SQL Server 2014 o 2016 de SQL Server en una máquina virtual de Microsoft Azure. El adaptador para la nube también crea una regla de Firewall de Windows para permitir las conexiones TCP entrantes en el puerto predeterminado 11435. Este extremo permite al asistente usar el servicio del adaptador para la nube para copiar los archivos de copia de seguridad de la instancia local a la máquina virtual de Azure. Para obtener más información, consulte [Adaptador para la nube de SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

	![Crear extremo de adaptador de nube](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Ejecución del Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure

1. Abra Microsoft SQL Server Management Studio para Microsoft SQL Server 2016 y conéctese a la instancia de SQL Server que contiene la base de datos de usuario que va a migrar a una máquina virtual de Azure.
2. Haga clic con el botón derecho en la base de datos que va a migrar, apunte a Tareas y, a continuación, haga clic en Implementar en máquina virtual de Microsoft Azure.

	![Iniciar el asistente](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. En la página Introducción, haga clic en Siguiente.
4. En la página Configuración de origen, conéctese a la instancia de SQL Server que contiene la base de datos que va a migrar a una máquina virtual de Azure.
5. Especifique una ubicación temporal para los archivos de copia de seguridad. Si se va a conectar a un servidor remoto, debe especificar una unidad de red.

	![Configuración de origen](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. Haga clic en Siguiente.
7. En la página de inicio de sesión de Microsoft Azure, haga clic en Iniciar sesión e inicie sesión en su cuenta de Azure.
8. Seleccione la suscripción que desea usar y haga clic en Siguiente.

	![Inicio de sesión de Azure](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. En la página Configuración de implementación, puede especificar un nombre de servicio en la nube y un nombre de máquina virtual nuevos o existentes:

 - Especifique un nombre de servicio en la nube y un nombre de máquina virtual nuevos para crear un nuevo servicio de nube con una nueva máquina virtual de Azure con una imagen de SQL Server 2014 o SQL Server 2016 de la galería.

     - Si especifica un nuevo nombre de servicio en la nube, especifique la cuenta de almacenamiento que va a usar.

     - Si especifica un nombre de servicio en la nube existente, se recuperará y se introducirá automáticamente la cuenta de almacenamiento.

 - Especifique un nombre de servicio en la nube existente y un nombre de máquina virtual nuevo para crear una máquina virtual de Azure nueva en un servicio de nube existente. Especifique solo una imagen de SQL Server 2014 o 2016 de SQL Server de la galería.
 - Especifique un nombre de servicio en la nube y un nombre de máquina virtual existentes para usar una máquina virtual de Azure existente. Debe ser una imagen generada con una imagen de la galería de SQL Server 2014 o SQL Server 2016.

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. Hacer clic en Configuración
  - Si especificó un nombre de servicio en la nube y un nombre de máquina virtual existentes, se le pedirá que escriba el nombre de usuario y la contraseña.

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- Si especificó un nuevo nombre de máquina virtual, se le pedirá que seleccione una imagen de la lista de la galería y proporcione la siguiente información:
	  - Imagen: seleccione solo SQL Server 2014 o SQL Server 2016
		- Nombre de usuario
		- Contraseña nueva
		- Confirmar contraseña
		- Ubicación
		- Tamaño
 	- Además, haga clic para aceptar el certificado generado automáticamente para esta nueva máquina virtual de Microsoft Azure y, a continuación, haga clic en Aceptar.

	![Configuración de equipo nuevo de Azure](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. Especifique el nombre de la base de datos de destino si es diferente del nombre de la base de datos de origen. Si la base de datos de destino ya existe, el sistema incrementará automáticamente el nombre de la base de datos, en lugar de sobrescribir la base de datos existente.
12. Haga clic en Siguiente y, a continuación, en Finalizar.

	![Resultados](./media/virtual-machines-migrate-onpremises-database/results.png)

13. Cuando finalice el asistente, conéctese a la máquina virtual y compruebe que la base de datos ha migrado.
14. Si creó una nueva máquina virtual, configure la máquina virtual de Azure y la instancia de SQL Server siguiendo los pasos de la sección Conexión a la instancia de máquina virtual de SQL Server desde SSMS en otro de equipo de [Aprovisionamiento de una máquina virtual de SQL Server en Azure](../virtual-machines-provision-sql-server/#SSMS).

## Copia de seguridad en archivo y copia en máquina virtual y restauración

Este método se usa cuando no se puede usar el Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure porque se va a realizar una migración a una versión de SQL Server anterior a SQL Server 2014 o porque el archivo de copia de seguridad es mayor que 1 TB. Si el archivo de copia de seguridad es mayor que 1 TB, debe seccionarlo porque el tamaño máximo de un disco de máquina virtual es 1 TB. Utilice los siguientes pasos generales para migrar una base de datos de usuario con este método manual:

1.	Realice una copia de seguridad completa de la base de datos en una ubicación local.
2.	Cree o cargue una máquina virtual con la versión de SQL Server deseada.
3.	Aprovisione la máquina virtual siguiendo los pasos de [Aprovisionamiento de una máquina virtual de SQL Server en Azure](../virtual-machines-provision-sql-server/#SSMS).
4.	Copie los archivos de copia de seguridad en la máquina virtual con Escritorio remoto, el Explorador de Windows o el comando de copia de un símbolo del sistema.

## Copia de seguridad en una dirección URL y restauración

El método de [copia de seguridad en URL](https://msdn.microsoft.com/library/dn435916.aspx) se usa cuando no se puede usar el Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure porque el archivo de copia de seguridad tiene más de 1 TB y va a migrar a SQL Server 2016, o desde este. En el caso de las bases de datos menores de 1 TB o que se ejecuten en una versión de SQL Server anterior a SQL Server 2016, se recomienda usar el asistente. SQL Server 2016 admite los conjuntos de copia de seguridad seccionados, se recomiendan para mejorar el rendimiento y son necesarios para superar los límites de tamaño por blob. En el caso de bases de datos muy grandes, se recomienda usar el [servicio de importación y exportación de Windows](../storage-import-export-service/).

## Desasociación y copia en dirección URL y asociación desde dirección URL

Este método se usa cuando se planea [almacenar estos archivos mediante el servicio de almacenamiento de blobs de Azure](https://msdn.microsoft.com/library/dn385720.aspx) y adjuntarlos a SQL Server en una máquina virtual de Azure, especialmente con bases de datos muy grandes. Utilice los siguientes pasos generales para migrar una base de datos de usuario con este método manual:

1.	Desasocie los archivos de base de datos de la instancia de base de datos local.
2.	Copie los archivos de base de datos desasociados en un almacenamiento de blobs de Azure con la [utilidad de línea de comandos AZCopy](../storage-use-azcopy/).
3.	Asocie los archivos de base de datos desde la dirección URL de Azure a la instancia de SQL Server en la máquina virtual de Azure.

## Conversión a máquina virtual y carga en la dirección URL e implementación como máquina virtual nueva

Este método se usa para migrar todas las bases de datos de usuario y del sistema de una instancia de SQL Server local a una máquina virtual de Azure. Utilice los siguientes pasos generales para migrar una instancia completa de SQL Server con este método manual:

1.	Convierta las máquinas físicas o virtuales en VHD de Hyper-V mediante [Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx).
2.	Cargue archivos VHD en Almacenamiento de Azure mediante el [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.	Implemente una máquina virtual nueva mediante el VHD cargado.

> [AZURE.NOTE] Para migrar una aplicación completa, considere el uso de [Azure Site Recovery](../services/site-recovery/).

## Envío de unidad de disco duro

Use el [método del servicio de importación y exportación de Azure](../storage-import-export-service/) para transferir grandes cantidades de datos de archivo al almacenamiento en blobs de Azure en aquellas situaciones en que el proceso de carga a través de la red sea demasiado caro o no sea viable. Con este servicio, se envían una o varias unidades de discos duros que contengan esos datos a un centro de datos de Azure, donde los datos se cargarán a su cuenta de almacenamiento.

## Pasos siguientes

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->