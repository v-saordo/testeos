<properties
	pageTitle="Ampliación de los grupos de disponibilidad AlwaysOn locales a Azure | Microsoft Azure"
	description="Este tutorial utiliza los recursos creados con el modelo de implementación clásica y describe cómo utilizar el Asistente para agregar réplica en SQL Server Management Studio (SSMS) para agregar una réplica del grupo de disponibilidad AlwaysOn en Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Ampliación de los grupos de disponibilidad AlwaysOn locales a Azure

Los grupos de disponibilidad AlwaysOn proporcionan alta disponibilidad para los grupos de la base de datos mediante la adición de las réplicas secundarias. Estas réplicas permiten la conmutación por error en bases de datos en caso de error. Además puede usarse para descargar las cargas de trabajo de lectura o tareas de copia de seguridad.

Puede ampliar los grupos de disponibilidad locales a Microsoft Azure al aprovisionar una o más máquinas virtuales de Azure con SQL Server y, a continuación, agregarlas a los grupos de disponibilidad locales como réplicas.

Este tutorial supone que tiene lo siguiente:

- Una suscripción de Azure activa. Puede [suscribirse a una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Un grupo de disponibilidad AlwaysOn local existente. Para obtener más información sobre los grupos de disponibilidad, consulte [Grupos de disponibilidad AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx).

- Conectividad entre la red local y la red virtual de Azure. Para obtener más información sobre la creación de esta red virtual, consulte [Configurar una VPN de sitio a sitio en el Portal de Azure clásico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

## Asistente para agregar una réplica de Azure

En esta sección se muestra cómo usar el **Asistente para agregar una réplica de Azure** para ampliar su solución de grupos de disponibilidad AlwaysOn a fin de incluir réplicas de Azure.

1. En SQL Server Management Studio, expanda **Alta disponibilidad AlwaysOn** > **Grupos de disponibilidad** > **[Nombre del grupo de disponibilidad]**.

1. Haga clic con el botón derecho en **Réplicas de disponibilidad** y luego en **Agregar réplica**.

1. De forma predeterminada, aparece el **Asistente para agregar réplica al grupo de disponibilidad**. Haga clic en **Siguiente**. Si ha seleccionado la opción **No volver a mostrar esta página** en la parte inferior de la página durante un inicio anterior de este asistente, esta pantalla no aparecerá.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742861.png)

1. Deberá conectarse a todas las réplicas secundarias existentes. Puede hacer clic en **Conectar...** junto a cada réplica o hacer clic en **Conectar todos...** en la parte inferior de la pantalla. Después de la autenticación, haga clic en **Siguiente** para pasar a la siguiente pantalla.

1. En la página **Especificar réplicas** , aparecen varias pestañas en la parte superior: **Réplicas**, **Extremos**, **Preferencias de copia de seguridad** y **Agente de escucha**. En la pestaña **Réplicas**, haga clic en **Agregar réplica de Azure...** para iniciar el Asistente para agregar una réplica de Azure.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742863.png)

1. Seleccione un certificado de administración de Azure existente en el almacén de certificados local de Windows si ha instalado uno antes. Seleccione o escriba el identificador de una suscripción de Azure si ha usado uno antes. Puede hacer clic en Descargar para descargar e instalar un certificado de administración de Azure y descargar la lista de suscripciones con una cuenta de Azure.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742864.png)

1. El usuario rellenará cada campo de la página con los valores que se usarán para crear la máquina virtual de Azure que hospedará la réplica.

	|Configuración|Descripción|
|---|---|
|**Imagen**|Seleccione la combinación deseada de sistema operativo y SQL Server|
|**Tamaño de VM**|Seleccione el tamaño de la máquina virtual que mejor se adapte a sus necesidades empresariales|
|**Nombre de VM**|Especifique un nombre único para la nueva máquina virtual. El nombre debe contener entre 3 y 15 caracteres, puede contener solo letras, números y guiones y debe comenzar con una letra y terminar con una letra o un número.|
|**Nombre de usuario de máquina virtual**|Especifique un nombre de usuario que se convertirá en la cuenta de administrador en la máquina virtual|
|**Contraseña de administrador de VM**|Especifique una contraseña para la nueva cuenta|
|**Confirm Password**|Confirme la contraseña de la nueva cuenta|
|**Red virtual**|Especifique la red virtual de Azure que debe usar la nueva máquina virtual. Para obtener más información sobre las redes virtuales, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).|
|**Subred de red virtual**|Especifique la subred de red virtual que debe usar la nueva máquina virtual|
|**Dominio**|Confirme que el valor previamente rellenado para el dominio es correcto|
|**Nombre de usuario del dominio**|Especifique una cuenta que esté en el grupo de administradores local en los nodos de clúster local|
|**Password**|Especifique la contraseña para el nombre de usuario del dominio|

1. Haga clic en **Aceptar** para validar la configuración de la implementación.

1. A continuación se muestran las condiciones legales. Léalas y haga clic en **Aceptar** si acepta estos términos.

1. Volverá a aparecer la página **Especificar réplicas**. Compruebe la configuración de la nueva réplica de Azure en las pestañas **Réplicas**, **Extremos** y **Preferencias de copia de seguridad**. Modifique la configuración para satisfacer sus requisitos empresariales. Para obtener más información sobre los parámetros contenidos en estas pestañas, consulte la [página Especificar réplicas (Asistente para nuevo grupo de disponibilidad/Asistente para agregar una réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Tenga en cuenta que no se pueden crear agentes de escucha mediante la pestaña Agente de escucha para los grupos de disponibilidad que contienen réplicas de Azure. Además, si ya se ha creado un agente de escucha antes de iniciar al asistente, recibirá un mensaje que indica que no se admite en Azure. Veremos cómo crear agentes de escucha en la sección **Creación del agente de escucha del grupo de disponibilidad**.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742865.png)

1. Haga clic en **Siguiente**.

1. Seleccione el método de sincronización de datos que desea usar en la página **Seleccionar sincronización de datos iniciales** y haga clic en **Siguiente**. Para la mayoría de los escenarios, seleccione **Sincronización de datos completos**. Para obtener más información sobre los métodos de sincronización de datos, consulte la página [ Seleccionar sincronización de datos iniciales (Asistente para grupos de disponibilidad AlwaysOn)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Revise los resultados en la página **Validación**. Corrija los problemas pendientes, si es necesario, vuelva a ejecutar la validación. Haga clic en **Siguiente**.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742866.png)

1. Revise la configuración en la página **Resumen** y haga clic en **Finalizar**.

1. Comienza el proceso de aprovisionamiento. Cuando el asistente finalice correctamente, haga clic en **Cerrar** para salir del asistente.

>[AZURE.NOTE] El Asistente para agregar una réplica de Azure crea un archivo de registro en Usuarios\\Nombre de usuario\\AppData\\Local\\SQL Server\\AddReplicaWizard. Este archivo de registro puede usarse para solucionar problemas de las implementaciones de réplicas de Azure incorrectas. Si el asistente no puede ejecutar alguna acción, todas las operaciones anteriores se revierten, incluida la eliminación de la máquina virtual aprovisionada.

## Creación del agente de escucha del grupo de disponibilidad

Después de crear el grupo de disponibilidad, debe crear un agente de escucha para que los clientes se conecten a las réplicas. Los agentes de escucha dirigen las conexiones entrantes a la réplica principal o a una réplica secundaria de solo lectura. Para obtener más información sobre los agentes de escucha, consulte [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

## Pasos siguientes

Además de usar el **Asistente para agregar una réplica de Azure** para ampliar el grupo de disponibilidad AlwaysOn a Azure, también puede mover algunas cargas de trabajo de SQL Server totalmente a Azure. Para obtener más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0204_2016-->