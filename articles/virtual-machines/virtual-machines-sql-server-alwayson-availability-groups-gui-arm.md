<properties
	pageTitle="Configuración del Administrador de recursos de Azure para Grupos de disponibilidad AlwaysOn | Microsoft Azure"
	description="Cree un grupo de disponibilidad AlwaysOn con máquinas virtuales de Azure en el modo Administrador de recursos de Azure. Este tutorial usa principalmente la interfaz de usuario para crear automáticamente toda la solución."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/04/2016"
	ms.author="MikeRayMSFT" />

# Configuración de un grupo de disponibilidad AlwaysOn en máquinas virtuales del Administrador de recursos de Azure (GUI).

> [AZURE.SELECTOR]
- [Portal - Resource Manager](virtual-machines-sql-server-alwayson-availability-groups-gui-arm.md)
- [Portal - Classic](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell - Classic](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modelo clásico.


Este tutorial completo muestra cómo crear un grupo de disponibilidad de SQL Server con máquinas virtuales del Administrador de recursos de Azure. El tutorial utiliza hojas de Azure para configurar una plantilla. Revisará la configuración predeterminada, escribirá la configuración requerida y actualizará las hojas en el portal a medida que recorra este tutorial.

>[AZURE.NOTE] El Portal de administración de Azure incluye una nueva configuración de la galería para Grupos de disponibilidad AlwaysOn con un agente de escucha. Con esto se configura todo lo que necesita para Grupos de disponibilidad AlwaysOn automáticamente. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la galería del Portal de Microsoft Azure clásico](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Al final del tutorial, la solución SQL Server AlwaysOn en Azure constará de los siguientes elementos:

- Una red virtual que contiene varias subredes, incluida una subred front-end y back-end

- Dos controladores de dominio con un dominio de Active Directory (AD)

- Dos máquinas virtuales de SQL Server implementadas en la subred back-end y unidas al dominio de AD

- Un clúster WSFC de tres nodos con el modelo de cuórum Mayoría de nodo

- Un grupo de disponibilidad con dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

La siguiente ilustración es una representación gráfica de la solución.

![Arquitectura de pruebas para AG en Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/0-EndstateSample.png)

Todos los recursos de esta solución pertenecen a un único grupo de recursos.

En este tutorial se da por hecho lo siguiente:

- Ya tiene una cuenta de Azure. Si no tiene una, [suscríbase para una cuenta de prueba](http://azure.microsoft.com/pricing/free-trial/).

- Ya sabe cómo aprovisionar una máquina virtual de SQL Server desde la galería de máquina virtual mediante la interfaz gráfica de usuario (GUI). Para obtener más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

- Ya tiene un conocimiento sólido de los Grupos de disponibilidad AlwaysOn. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si tiene interés en el uso de los Grupos de disponibilidad AlwaysOn con SharePoint, consulte también [Configuración de grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

En este tutorial se usará el Portal de Azure para:

- seleccionar la nueva plantilla de un grupo de disponibilidad AlwaysOn desde el portal,

- revisar la configuración de la plantilla y actualizar algunas configuraciones para el entorno,

- supervisar Azure mientras crea todo el entorno, y

- conectarse a uno de los controladores de dominio y después a uno de los servidores SQL Server.

## Aprovisionamiento de un grupo de disponibilidad AlwaysOn desde la galería con el modelo de implementación del Administrador de recursos

Azure ofrece una galería de imágenes para toda la solución. Para encontrar la plantilla:

1. 	Inicie sesión en el Portal de Azure con su cuenta.
1.	En el Portal de Azure, haga clic en **+Nuevo**. El portal abrirá la hoja Nuevo. 
1.	En la hoja Nuevo, busque **AlwaysOn**. ![Buscar la plantilla de AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/16-findalwayson.png)
1.	En los resultados de búsqueda localice **Clúster AlwaysOn de SQL Server**. ![Plantilla de AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/17-alwaysontemplate.png)
1.	En **Seleccionar un modelo de implementación**, elija **Administrador de recursos**.

### Aspectos básicos

Haga clic en **Básico** y configure lo siguiente:

- **Nombre de usuario del administrador**: es una cuenta de usuario con permisos de administrador de dominio y un miembro del rol de servidor fijo sysadmin de SQL Server en ambas instancias de SQL Server. En este tutorial use **DomainAdmin**. 

- **Contraseña**: es la contraseña para la cuenta de administrador de dominio. Utilice una contraseña compleja. Confirme la contraseña.

- **Suscripción**: es la suscripción que Azure facturará para ejecutar todos los recursos implementados para el grupo de disponibilidad AlwaysOn. Puede especificar otra suscripción si la cuenta tiene varias suscripciones.
 
- **Grupo de recursos**: es el nombre del grupo al que pertenecerán todos los recursos de Azure creados por este tutorial. En este tutorial use **SQL-HA-RG**. Para obtener más información, consulte (información general del Administrador de recursos de Azure)[resource-group-overview.md/#resource-groups].

- **Ubicación**: es la región de Azure donde se crearán los recursos para este tutorial. Seleccione una región de Azure para hospedar la infraestructura.

A continuación se muestra el aspecto de la hoja **Básico**:

![Aspectos básicos](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/1-basics.png)

- Haga clic en **Aceptar**. 

### Configuración de red y dominio

Esta plantilla de la galería de Azure crea un nuevo dominio con nuevos controladores de dominio. También crea una nueva red y dos subredes. La plantilla no permite la creación de los servidores en una red virtual o dominio existente. El siguiente paso es configurar el dominio y la red.

En la hoja **Configuración de dominio y red** revise los valores predefinidos para la configuración del dominio y de la red:

- **Nombre de dominio raíz del bosque**: es el nombre de dominio que se usará para el dominio de AD que va a hospedar el clúster. En este tutorial use **contoso.com**. 

- **Nombre de red virtual**: es el nombre de red para la red virtual de Azure. En este tutorial use **autohaVNET**.

- **Nombre de subred del controlador de dominio**: es el nombre de una parte de la red virtual que hospeda el controlador de dominio. En este tutorial use **subnet-1**. Esta subred usará el prefijo de dirección **10.0.0.0/24**.

- **Nombre de subred de SQL Server**: es el nombre de una parte de la red virtual que hospeda servidores SQL Server y el testigo del recurso compartido de archivos. En este tutorial use **subnet-2**. Esta subred usará el prefijo de dirección **10.0.1.0/26**.

Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales](virtual-networks-overview.md).

**Configuración de red y dominio** debe tener este aspecto:

![Configuración de red y dominio](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/2-domain.png)
 
Si es necesario, puede cambiar estos valores. En este tutorial usamos los valores preestablecidos.
 
- Revise la configuración y haga clic en **Aceptar**. 

###Configuración de grupos de disponibilidad

En **Configuración de grupo de disponibilidad** revise los valores preestablecidos para el grupo de disponibilidad y el agente de escucha.

- **Nombre del grupo de disponibilidad**: es el nombre de recurso de clúster para el grupo de disponibilidad. En este tutorial use **Contoso-ag**. 

- **Nombre de agente de escucha de grupo de disponibilidad**: lo usa el clúster y el equilibrador de carga interno. Los clientes que se conectan a SQL Server pueden usar este nombre para conectarse a la réplica de la base de datos adecuada. En este tutorial use **Contoso-listener**.

-  **Puerto de escucha de grupo de disponibilidad**: especifica el puerto TCP que utilizará el agente de escucha de SQL Server. En este tutorial use el puerto predeterminado **1433**.

Si es necesario, puede cambiar estos valores. En este tutorial use los valores preestablecidos.

![Configuración de grupos de disponibilidad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/3-availabilitygroup.png)

- Haga clic en **Aceptar**. 

###Tamaño de VM y configuración de almacenamiento

En **Tamaño de VM y configuración de almacenamiento** elija un tamaño de máquina virtual de SQL Server y revise otras configuraciones.

- **Tamaño de máquina virtual de SQL Server**: es el tamaño de la máquina virtual de Azure para ambos servidores SQL Server. Elija un tamaño de máquina virtual adecuado para la carga de trabajo. Si va a crear este entorno para el tutorial, use **DS2**. Para cargas de trabajo de producción elija un tamaño de máquina virtual que pueda admitir la carga de trabajo. Muchas cargas de trabajo de producción requerirán **DS4** o superior. La plantilla creará dos máquinas virtuales de este tamaño e instalará SQL Server en cada uno de ellas. Para más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

>[AZURE.NOTE]Azure instalará Enterprise Edition de SQL Server. El costo depende de la edición y el tamaño de la máquina virtual. Para obtener información detallada acerca de los costos actuales, consulte [Precios de Máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Tamaño de la máquina virtual del controlador de dominio**: es el tamaño de la máquina virtual para los controladores de dominio. En este tutorial use **D2**.

- **Tamaño de la máquina virtual del testigo del recurso compartido de archivos**: es el tamaño de la máquina virtual para el testigo del recurso compartido de archivos. En este tutorial use **A1**.

- **Cuenta de almacenamiento SQL**: es el nombre de la cuenta de almacenamiento para contener los datos de SQL Server y los discos del sistema operativo. En este tutorial use **alwaysonsql01**.

- **Cuenta de almacenamiento de controladores de dominio**: es el nombre de la cuenta de almacenamiento para los controladores de dominio. En este tutorial use **alwaysondc01**.

- **Tamaño de disco de datos de SQL Server**: es el tamaño (en TB) del disco de datos de SQL Server en TB. Especifique un número entre 1 y 4 Este es el tamaño del disco de datos que se adjuntará a cada servidor SQL Server. En este tutorial use **1**.

- **Optimización de almacenamiento**: establece una configuración de almacenamiento específica para las máquinas virtuales de SQL Server según el tipo de carga de trabajo. Todos los servidores SQL Server en este escenario usan almacenamiento premium con caché de host de disco de Azure establecida en solo lectura. Además, puede optimizar la configuración de SQL Server para la carga de trabajo eligiendo una de estas tres configuraciones:

    - **Carga de trabajo general**: no establece una configuración específica. 

    - **Procesamiento transaccional**: establece la marca de seguimiento 1117 y 1118.

    - **Almacenamiento de datos**: establece la marca de seguimiento 1117 y 610.

En este tutorial use **Carga de trabajo general**.

![Configuración de almacenamiento del tamaño de VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/4-vm.png)

- Revise la configuración y haga clic en **Aceptar**. 

####Una nota acerca de almacenamiento

Las optimizaciones adicionales dependen del tamaño de los discos de datos de SQL Server. Por cada terabyte de disco de datos, Azure agrega un almacenamiento premium adicional de 1 TB (SSD). Cuando un servidor requiere de 2 TB o más, la plantilla crea un bloque de almacenamiento en cada servidor SQL Server. Un bloque de almacenamiento es una forma de virtualización del almacenamiento donde varios discos se configuran para proporcionar mayor capacidad, resistencia y rendimiento. Después, la plantilla crea un espacio de almacenamiento en el bloque de almacenamiento y presenta esto como datos únicos en el sistema operativo. La plantilla designa este disco como disco de datos para SQL Server. La plantilla optimiza el bloque de almacenamiento para SQL Server con la siguiente configuración:

- El tamaño de franja es la configuración de intercalación para el disco virtual. Para cargas de trabajo transaccionales se establece en 64 KB. Para cargas de trabajo de almacenamiento de datos, el valor es 256 KB. 

- La resistencia es simple (sin resistencia).

>[AZURE.NOTE] El almacenamiento premium de Azure tiene redundancia local y mantiene tres copias de los datos dentro de una sola región, por lo que no se necesita resistencia adicional en el bloque de almacenamiento.

- El número de columnas es igual al número de discos del bloque de almacenamiento. 

Para obtener información adicional sobre el espacio de almacenamiento y los bloques de almacenamiento, consulte:

- [Introducción a los espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx). 

- [Copias de seguridad de Windows Server y bloques de almacenamiento](http://technet.microsoft.com/library/dn390929.aspx)

Para obtener más información sobre prácticas recomendadas para la configuración de SQL Server, consulte [Prácticas recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).


###Configuración de SQL Server

En **Configuración de SQL Server** revise y modifique el prefijo del nombre de VM de SQL Server, la versión de SQL Server, la contraseña y la cuenta de servicio de SQL Server, y la programación de mantenimiento de aplicación de revisión automática de SQL.

- **Prefijo de nombre de SQL Server**: se utiliza para crear un nombre para cada servidor SQL Server. En este tutorial use **Contoso-ag**. Los nombres de SQL Server serán *Contoso-ag-0* y *Contoso-ag-1*. 

- **Versión de SQL Server**: es la versión de SQL Server. En este tutorial use **SQL Server 2014**. También puede elegir **SQL Server 2012** o **SQL Server 2016**.

- **Nombre de usuario de la cuenta del servicio SQL Server**: es el nombre de cuenta de dominio para el servicio SQL Server. En este tutorial use **sqlservice**.

- **Contraseña**: es la contraseña de la cuenta del servicio SQL Server. Utilice una contraseña compleja. Confirme la contraseña.

- **Programación de mantenimiento para aplicar revisiones automáticas de SQL**: identifica el día de la semana que Azure aplicará revisiones automáticamente en los servidores SQL Server. En este tutorial escriba **Domingo**.

- **Hora de inicio del mantenimiento de aplicación de revisiones automáticas de SQL**: es la hora del día de la región de Azure a la que se iniciará la aplicación de revisiones automática.

>[AZURE.NOTE]La ventana de aplicación de revisiones para cada VM se escalona en una hora. Solo se aplican revisiones a una máquina virtual en cada momento para evitar la interrupción de los servicios.

![Configuración de SQL Server](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/5-sql.png)

Revise la configuración y haga clic en **Aceptar**.

###Resumen

Azure valida la configuración en la página de resumen. También puede descargar la plantilla. Revise el resumen. Haga clic en **Aceptar**.

###Comprar

Esta hoja final contiene **términos de uso** y la **política de privacidad**. Revise esta información. Cuando esté listo para que Azure comience a crear las máquinas virtuales y todos los recursos necesarios para el grupo de disponibilidad AlwaysOn, haga clic en **Crear**.
 
El Portal de Azure creará el grupo de recursos y todos los recursos.

##Supervisión de la implementación

Supervise el progreso de la implementación desde el portal de Azure. Un icono que representa la implementación se ancla automáticamente al panel del Portal de Azure.

![Panel de Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/11-deploydashboard.png)

##Conexión con SQL Server

Las nuevas instancias de SQL Server se ejecutan en máquinas virtuales que no tienen conexiones a Internet. Sin embargo, los controladores de dominio tienen una conexión a Internet. Para conectarse a los servidores SQL Server con Escritorio remoto, abra primero la RDP a uno de los controladores de dominio. Desde el controlador de dominio, abra una segunda RDP al servidor SQL Server.

Para la RDP al controlador de dominio principal, siga estos pasos:

1.	Desde el panel del Portal de Azure compruebe que la implementación se ha realizado correctamente. 

1.	Haga clic en **Recursos**.

1.	En la hoja **Recursos**, haga clic en **ad-primary-dc** que es el nombre del equipo de la máquina virtual del controlador de dominio principal.

1.	En la hoja de **ad-primary-dc**, haga clic en **Conectar**. El explorador le preguntará si desea abrir o guardar el objeto de conexión remota. Haga clic en **Abrir**. ![Conectarse al controlador de dominio](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/13-ad-primary-dc-connect.png)
1.	**Conexión a Escritorio remoto** puede advertirle de que el publicador de esta conexión remota no se puede identificar. Haga clic en **Conectar**.

1.	Seguridad de Windows le pide que escriba sus credenciales para conectarse a la dirección IP del controlador de dominio principal. Haga clic en **Usar otra cuenta**. Para **Nombre de usuario**, escriba **contoso\\DomainAdmin**. Esta es la cuenta que eligió para el nombre de usuario del administrador. Utilice la contraseña compleja que eligió al configurar la plantilla.

1.	**Escritorio remoto** puede advertirle de que no se pudo autenticar el equipo remoto debido a problemas con su certificado de seguridad. Le mostrará el nombre del certificado de seguridad. Si ha seguido el tutorial, el nombre será **ad-principal-dc.contoso.com**. Haga clic en **Sí**.

Ahora está conectado al controlador de dominio principal. Para la RDP a SQL Server, siga estos pasos:

1.	En el controlador de dominio, abra **Conexión a Escritorio remoto**. 

1.	Para **Equipo**, escriba el nombre de uno de los servidores SQL Server. En este tutorial escriba **sqlserver-0**.

1.	Utilice la misma cuenta de usuario y contraseña que usó para RDP para el controlador de dominio.

Ahora está conectado con RDP a SQL Server. Puede abrir SQL Server Management Studio, conectarse a la instancia predeterminada de SQL Server y comprobar que el grupo de disponibilidad AlwaysOn está configurado.

<!---HONumber=AcomDC_0211_2016-->