<properties
	pageTitle="Configuración de Grupos de disponibilidad AlwaysOn (GUI) | Microsoft Azure"
	description="Creación de un grupo de disponibilidad AlwaysOn con máquinas virtuales de Azure. Este tutorial usa principalmente la interfaz de usuario y herramientas en lugar de scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/04/2015"
	ms.author="jroth" />

# Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)

> [AZURE.SELECTOR]
- [Portal - Resource Manager](virtual-machines-sql-server-alwayson-availability-groups-gui-arm.md)
- [Portal - Classic](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell - Classic](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Este tutorial integral muestra cómo implementar grupos de disponibilidad mediante SQL Server AlwaysOn ejecutándose en máquinas virtuales de Azure.

>[AZURE.NOTE] El Portal de administración de Azure incluye una nueva configuración de la Galería para Grupos de disponibilidad AlwaysOn con un agente de escucha. Con esto se configura todo lo que necesita para Grupos de disponibilidad AlwaysOn automáticamente. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la galería del Portal de Microsoft Azure clásico](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Para usar PowerShell, vea el tutorial del mismo escenario en [Configurar grupos de disponibilidad AlwaysOn en Azure con PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md).

Al final del tutorial, la solución SQL Server AlwaysOn en Azure constará de los siguientes elementos:

- Una red virtual que contiene varias subredes, incluida una subred front-end y back-end

- Un controlador de dominio con un dominio de Active Directory (AD)

- Dos máquinas virtuales de SQL Server implementadas en la subred back-end y unidas al dominio de AD

- Un clúster WSFC de tres nodos con el modelo de cuórum Mayoría de nodo

- Un grupo de disponibilidad con dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

La siguiente ilustración es una representación gráfica de la solución.

![Arquitectura de pruebas para AG en Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

Tenga en cuenta que se trata de una de las posibles configuraciones. Por ejemplo, puede minimizar el número de máquinas virtuales para un grupo de disponibilidad de dos réplicas para ahorrar horas de cálculo en Azure con el controlador de dominio como el testigo de recurso compartido de archivos de cuórum en un clúster WSFC de dos nodos. Este método reduce el recuento de máquinas virtuales en uno respecto a la configuración anterior.

En este tutorial se da por hecho lo siguiente:

- Ya tiene una cuenta de Azure.

- Ya sabe cómo aprovisionar una máquina virtual de SQL Server desde la galería de máquina virtual mediante la interfaz gráfica de usuario (GUI). Para obtener más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

- Ya tiene un conocimiento sólido de los Grupos de disponibilidad AlwaysOn. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si tiene interés en el uso de los Grupos de disponibilidad AlwaysOn con SharePoint, consulte también [Configuración de Grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Creación de la red virtual y el servidor del controlador de dominio

Comience con una nueva cuenta de prueba de Azure. Cuando haya terminado de configurar la cuenta, debería ver en la pantalla de inicio del Portal de Azure clásico.

1. Haga clic en el botón **Nuevo** en la esquina inferior izquierda de la página, como se muestra a continuación.

	![Haga clic en Nuevo, en el Portal](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. Haga clic en **Servicios de red**, después, haga clic en **Red Virtual,** y finalmente en **Creación personalizada**, tal como se muestra a continuación.

	![Cree una red virtual:](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. En el cuadro de diálogo **CREAR UNA RED VIRTUAL**, cree una nueva red virtual recorriendo las páginas con las opciones siguientes.

	|Page|Settings|
|---|---|
|Detalles de red virtual|**NOMBRE = ContosoNET**<br/>**REGIÓN = West US**|
|Servidores DNS y conectividad VPN|None|
|Espacios de direcciones de la red virtual|La configuración se muestra en la captura de pantalla siguiente: ![Cree una red virtual:](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. A continuación, cree la máquina virtual que se usará como controlador de dominio (DC). Vuelva a hacer clic en **Nuevo**, luego en **Proceso**, en **Máquina Virtual** y en **De la galería**, tal y como se muestra a continuación.

	![Crear una VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. En el cuadro de diálogo **CREAR UNA RED VIRTUAL**, cree una nueva máquina virtual recorriendo las páginas con las opciones siguientes.

	|Page|Settings|
|---|---|
|Seleccionar el sistema operativo de la máquina virtual|Windows Server 2012 R2 Datacenter|
|Configuración de la máquina virtual|**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (la más reciente)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoDC<br/>** NIVEL** = ESTÁNDAR<br/>**TAMAÑO** = A2 (2 núcleos)<br/>**NUEVO NOMBRE DE USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000|
|Configuración de la máquina virtual|**SERVICIO EN LA NUBE** = crear un nuevo servicio de nube<br/>**NOMBRE DNS DE SERVICIO EN LA NUBE** = un nombre único de servicio en la nube<br/>**NOMBRE DNS** = un nombre único (por ej: ContosoDC123)<br/>**REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE RED VIRTUAL** = Back-end(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = (ninguno)|
|Opciones de la máquina virtual|Usar predeterminados|

Una vez que termine de configurar la nueva máquina virtual, espere a que esta se aprovisione. Este proceso tarda algún tiempo en completarse y, si hace clic en la pestaña **Máquina Virtual** en el Portal de Azure clásico, puede ver que ContosoDC recorre los estados de **Inicio (aprovisionamiento)** a **Detenido**, **Inicio**, **Ejecución (aprovisionamiento)** y, finalmente, **Ejecución**.

El servidor DC ya está aprovisionado correctamente. A continuación, configurará el dominio de Active Directory en este servidor DC.

## Configuración del controlador de dominio

En los pasos siguientes configurará la máquina ContosoDC como controlador de dominio para corp.contoso.com.

1. En el Portal, seleccione la máquina **ContosoDC**. En la pestaña **Panel** haga clic en **Conectar** para abrir un archivo RDP para el acceso de escritorio remoto.

	![Conectarse a máquinas virtuales](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. Inicie sesión con su cuenta de administrador configurada (**\\AzureAdmin**) y la contraseña (**Contoso!000**).

1. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** .

1. Haga clic en el vínculo **Agregar roles y características** en el panel.

	![Explorador de servidores Agregar roles](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.

1. Seleccione los roles **Servicios de dominio de Active Directory** y **Servidor DNS**. Cuando se le pida, agregue las características adicionales que requieran estos roles.

	>[AZURE.NOTE] Recibirá una advertencia de validación que le indicará que no hay ninguna dirección IP estática. Si está probando la configuración, haga clic en Continuar En los escenarios de producción [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Diálogo Agregar roles](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. Haga clic en **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario**.

1. Haga clic en **Instalar**.

1. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor**.

1. Seleccione la nueva opción **AD DS** en el panel izquierdo.

1. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.

	![Cuadro de diálogo AD DS en la máquina virtual del servidor de DNS](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.

1. En el **Asistente de configuración de Servicios de dominio de Active Directory**, use los siguientes valores:

	|Page|Configuración|
|---|---|
|Configuración de la implementación|**Agregar un nuevo bosque** = seleccionado<br/>**Nombre del dominio raíz** = corp.contoso.com|
|Opciones del controlador de dominio|**Contraseña** = Contoso!000<br/>**Confirmar contraseña** = Contoso!000|

1. Haga clic en **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Tenga en cuenta que debe revisar todos los mensajes de advertencia aplicables, pero puede continuar con la instalación.

1. Haga clic en **Instalar**. La máquina virtual **ContosoDC** se reiniciará automáticamente.

## Configuración de cuentas de dominio

Los pasos siguientes configuran las cuentas de Active Directory (AD) para su uso posterior.

1. Vuelva a iniciar sesión en la máquina **ContosoDC**.

1. En **Administrador del servidor**, seleccione **Herramientas** y, después, haga clic en **Centro de administración de Active Directory**.

	![Centro de administración de Active Directory](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. En el **Centro de administración de Active Directory** seleccione **corp (local)** en el panel izquierdo.

1. En el panel **Tareas** de la derecha, seleccione **Nuevo** y, después, haga clic en **Usuario**. Use la configuración siguiente:

	|Configuración|Valor|
|---|---|
|**Nombre**|Instalación|
|**Usuario NombreDeCuentaSam**|Instalación|
|**Password**|Contoso!000|
|**Confirmar contraseña**|Contoso!000|
|**Otras opciones de contraseña**|Seleccionado|
|**La contraseña nunca expira**|Activado|

1. Haga clic en **Aceptar** para crear el usuario **Install**. Esta cuenta se usará para configurar el clúster de conmutación por error y el grupo de disponibilidad.

1. Cree dos usuarios adicionales con los mismos pasos: **CORP\\SQLSvc1** y **CORP\\SQLSvc2**. Estas cuentas se usarán para las instancias de SQL Server. A continuación, tiene que conceder a **CORP\\Install** los permisos necesarios para configurar Clústeres de conmutación por error de Windows Service (WSFC).

1. En el **Centro de administración de Active Directory**, seleccione **corp (local)** en el panel izquierdo. A continuación, en el panel **Tareas** a la derecha, haga clic en **Propiedades**.

	![Propiedades de usuario CORP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. Seleccione **Extensiones** y luego haga clic en el botón **Avanzadas** situado en la pestaña **Seguridad**.

1. En el cuadro de diálogo **Configuración de seguridad avanzada** para corp. Haga clic en **Agregar**.

1. Haga clic en **Seleccionar una entidad de seguridad**. Después busque **CORP\\Install**. Haga clic en **Aceptar**.

1. Seleccione los permisos **Leer todas las propiedades** y **Crear objetos de equipo**.

	![Permisos de usuario Corp](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. Haga clic en **Aceptar** y luego vuelva a hacer clic en **Aceptar**. Cierre la ventana de propiedades de corp.

Ahora que ha terminado de configurar Active Directory y los objetos de usuario, creará tres máquinas virtuales de SQL Server y las unirá a este dominio.

## Creación de las máquinas virtuales de SQL Server

A continuación, cree tres máquinas virtuales, incluido un nodo de clúster WSFC y dos máquinas virtuales de SQL Server. Para crear cada una de las máquinas virtuales, vuelva al Portal de Azure clásico, haga clic en **Nuevo**, **Proceso**, **Máquina virtual** y, finalmente, en **De la galería**. Después use las plantillas de la tabla siguiente para ayudarle a crear las máquinas virtuales.

|Page|VM1|VM2|VM3|
|---|---|---|---|
|Seleccionar el sistema operativo de la máquina virtual|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuración de la máquina virtual|**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (la más reciente)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoWSFCNode<br/>**NIVEL** = ESTÁNDAR<br/>**TAMAÑO** = A2 (2 núcleos)<br/>**NUEVO NOMBRE DE USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000|**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (la más reciente)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoSQL1<br/>**NIVEL** = ESTÁNDAR<br/>**TAMAÑO** = A3 (4 núcleos)<br/>**NUEVO NOMBRE DE USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000|**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (la más reciente)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoSQL2<br/>**NIVEL** = ESTÁNDAR<br/>**TAMAÑO** = A3 (4 núcleos)<br/>**NUEVO NOMBRE DE USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000|
|Configuración de la máquina virtual|**SERVICIO EN LA NUBE** = Nombre DNS de servicio en la nube único creado con anterioridad (por ej.: ContosoDC123)<br/>**REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = Crear un conjunto de disponibilidad<br/>**NOMBRE DE CONJUNTO DE DISPONIBILIDAD** = SQLHADR|**SERVICIO EN LA NUBE** = Nombre DNS de servicio en la nube único creado con anterioridad (por ej.: ContosoDC123)<br/>**REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = SQLHADR (También puede configurar el conjunto de disponibilidad una vez creada la máquina. Las tres máquinas deben asignarse al conjunto de disponibilidad SQLHADR).|**SERVICIO EN LA NUBE** = Nombre DNS de servicio en la nube único creado con anterioridad (por ej.: ContosoDC123)<br/>**REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = SQLHADR (También puede configurar el conjunto de disponibilidad una vez creada la máquina. Las tres máquinas deben asignarse al conjunto de disponibilidad SQLHADR).|
|Opciones de la máquina virtual|Usar predeterminados|Usar predeterminados|Usar predeterminados|

<br/>

>[AZURE.NOTE] La configuración anterior le sugiere usar máquinas virtuales de nivel ESTÁNDAR, porque las máquinas de nivel BÁSICO no admiten puntos de conexión con equilibrio de carga, y estos son necesarios para crear más adelante los agentes de escucha de un grupo de disponibilidad. Asimismo, los tamaños de máquina que se sugieren aquí están diseñados para probar los grupos de disponibilidad en máquinas virtuales de Azure. Para optimizar el rendimiento de las cargas de trabajo de producción, consulte las recomendaciones de tamaños de máquina y la configuración de SQL Server en [Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).

Una vez que las tres máquinas virtuales estén totalmente aprovisionadas, tendrá que unirlas al dominio **corp.contoso.com** y conceder a CORP\\Install los derechos administrativos sobre las máquinas. Para ello, siga estos pasos para cada una de las tres máquinas virtuales.

1. En primer lugar, cambie la dirección del servidor DNS preferido. Comience por descargar el archivo de escritorio remoto (RDP) de cada máquina virtual en el directorio local; para ello, seleccione la máquina virtual en la lista y haga clic en el botón **Conectar** . Para seleccionar una máquina virtual, haga clic en algún lugar de la primera celda de la fila, como se muestra a continuación.

	![Descarga del archivo RDP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. Inicie el archivo RDP que descargó e inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\\AzureAdmin**) y la contraseña (**Contoso!000**).

1. Una vez iniciada la sesión, debería ver el panel **Administrador del servidor**. En el panel izquierdo, haga clic en **Servidor local**.

1. Seleccione el vínculo **Dirección IPv4 asignada por DHCP, IPv6 habilitado** .

1. En la ventana **Conexiones de red**, seleccione el icono de red.

	![Cambio del servidor DNS preferido de máquina virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. En la barra de comandos, haga clic en **Cambiar la configuración de esta conexión** (según el tamaño de la ventana, tendrá que hacer clic en la flecha doble que está mirando hacia la derecha para ver este comando).

1. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en Propiedades.

1. Seleccione “Usar las siguientes direcciones de servidor DNS” y especifique **10.10.2.4** en **Servidor DNS preferido**.

1. La dirección **10.10.2.4** es la que se asigna a una máquina virtual en la subred 10.10.2.0/24 de una red virtual de Azure y esa máquina virtual es **ContosoDC**. Para comprobar la dirección IP de **ContosoDC**, use la opción **nslookup contosodc** en el símbolo del sistema, según se muestra a continuación.

	![Uso de NSLOOKUP para buscar la dirección IP para DC](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. Haga clic en **Aceptar** y, a continuación, en **Cerrar** para confirmar los cambios. Ahora ya puede unir la máquina virtual a **corp.contoso.com**.

1. De vuelta en la ventana **Servidor Local**, haga clic en el vínculo **GRUPO\_DE\_TRABAJO**.

1. En la sección **Nombre de equipo**, haga clic en **Cambiar**.

1. Active la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto. Haga clic en **Aceptar**.

1. En el cuadro de diálogo emergente **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\\AzureAdmin**) y la contraseña (**Contoso!000**).

1. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", haga clic en **Aceptar**.

1. Haga clic en **Cerrar** y, a continuación, en **Reiniciar ahora** en el cuadro de diálogo emergente.

### Agregue el usuario Corp\\Install como administrador en cada máquina virtual:

1. Espere hasta que se reinicie la máquina virtual e inicie el archivo RDP de nuevo para iniciar sesión en la máquina virtual con la cuenta **BUILTIN\\AzureAdmin**.

1. En **Administrador del servidor** seleccione **Herramientas** y, a continuación, haga clic en **Administración de equipos**.

	![Administración de equipos](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. En la ventana **Administración de equipos**, expanda **Usuarios y grupos locales** y, a continuación, seleccione **Grupos**.

1. Haga doble clic en el grupo **Administradores**.

1. En el cuadro de diálogo **Propiedades de administradores**, haga clic en el botón **Agregar**.

1. Especifique el usuario **CORP\\Install** y haga clic en **Aceptar**. Cuando se le pidan las credenciales, use la cuenta **AzureAdmin** con la contraseña **Contoso!000**.

1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de administradores**.

### Agregue la característica **Clústeres de conmutación por error** a cada máquina virtual.

1. En el panel **Administrador del servidor** haga clic en **Agregar roles y características**.

1. En el **Asistente para agregar roles y características**, haga clic en **Siguiente** hasta llegar a la página **Características**.

1. Seleccione **Clústeres de conmutación por error**. Cuando se le pida, agregue todas las características dependientes.

	![Agregar la característica Clústeres de conmutación por error a máquina virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. Haga clic en **Siguiente** y después en **Instalar** en la página **Confirmación**.

1. Cuando la instalación de la característica **Clústeres de conmutación por error** esté completada, haga clic en **Cerrar**.

1. Cierre sesión en la máquina virtual.

1. Repita los pasos de esta sección para los tres servidores: **ContosoWSFCNode**, **ContosoSQL1** y **ContosoSQL2**.

Las máquinas virtuales de SQL Server están aprovisionadas y en ejecución, pero se instalan con SQL Server con las opciones predeterminadas.

## Creación del clúster WSFC

En esta sección, cree el clúster de WSFC que hospedará el grupo de disponibilidad que creará más adelante. Por ahora, debería haber hecho lo siguiente para cada una de las tres máquinas virtuales que se usarán en el clúster de WSFC:

- Están totalmente aprovisionadas en Azure

- La máquina virtual está unida al dominio

- Se agregó **CORP\\Install** al grupo de administradores local.

- Se agregó la característica Clústeres de conmutación por error.

Todos estos son los requisitos previos en cada máquina virtual para que pueda unirse al clúster de WSFC.

Además, tenga en cuenta que la red virtual de Azure no se comporta de la misma manera que una red local. Tendrá que crear el clúster en el orden siguiente:

1. Cree un clúster de nodo único en uno de los nodos (**ContosoSQL1**).

1. Modifique la dirección IP del clúster en una dirección IP sin usar (**10.10.2.101**).

1. Ponga el nombre de clúster en línea.

1. Agregue los demás nodos (**ContosoSQL2** y **ContosoWSFCNode**).

Siga los pasos a continuación para realizar estas tareas y configurar totalmente el clúster.

1. Inicie el archivo RDP de **ContosoSQL1** e inicie sesión con la cuenta de dominio **CORP\\Install**.

1. En el panel **Administrador del servidor**, seleccione **Herramientas** y, a continuación, haga clic en **Administrador de clústeres de conmutación por error**.

1. En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, después, haga clic en **Crear un clúster**.

	![Crear clúster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. En el Asistente para crear clúster, cree un clúster de un solo nodo avanzando por las páginas con la siguiente configuración:

	|Page|Settings|
|---|---|
|Antes de empezar|Usar predeterminados|
|Seleccionar servidores|Escriba **ContosoSQL1** en **Especifique el nombre del servidor** y haga clic en **Agregar**|
|Advertencia de validación|Seleccione **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continuar con la creación del clúster**.|
|Punto de acceso para administrar el clúster|Escriba **Cluster1** en **Nombre de clúster**|
|Confirmación|Use los valores predeterminados a menos que use Espacios de almacenamiento. Consulte la nota que sigue a esta tabla.|

	>[AZURE.WARNING] Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster** en la página **Confirmación**. Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.

1. En el panel izquierdo, expanda **Administrador de clústeres de conmutación por error** y, a continuación, haga clic en **Cluster1.corp.contoso.com**.

1. En el panel central, desplácese hacia abajo hasta la sección **Recursos principales de clúster** y expanda los detalles de **Nombre: Cluster1**. Debería de ver los recursos **Nombre** y **Dirección IP** en el estado **Con error**. El recurso de dirección IP no se puede poner en línea porque al clúster se le asigna la misma dirección IP que la de la propia máquina, que es una dirección duplicada.

1. Haga clic con el botón secundario en el recurso **dirección IP** erróneo y, a continuación, haga clic en **Propiedades**.

	![Propiedades de clúster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. Seleccione **Dirección IP estática** y especifique **10.10.2.101** en el cuadro de texto Dirección. A continuación, haga clic en **Aceptar**.

1. En la sección **Recursos principales de clúster**, haga clic con el botón derecho en **Nombre: Cluster1** y haga clic en **Poner en línea**. Después espere hasta que ambos recursos estén en línea. Cuando el recurso de nombre de clúster está en línea, actualiza el servidor DC con una nueva cuenta de equipo de AD. Esta cuenta de AD se usará para ejecutar más tarde el servicio de clúster del grupo de disponibilidad.

1. Finalmente, agregue los nodos restantes al clúster. En el árbol del explorador, haga clic con el botón derecho en **Cluster1.corp.contoso.com** y haga clic en **Agregar nodo**, tal como se muestra a continuación.

	![Agregar nodo al clúster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. En el **Asistente para agregar nodo**, haga clic en **Siguiente**. En la página **Seleccionar servidores** agregue **ContosoSQL2** y **ContosoWSFCNode** a la lista escribiendo el nombre del servidor en **Especifique el nombre de servidor** y, a continuación, haga clic en **Agregar**. Cuando haya terminado, haga clic en **Siguiente**.

1. En la página **Advertencia de validación**, haga clic en **No** (en un escenario de producción debe realizar las pruebas de validación). A continuación, haga clic en **Siguiente**.

1. En la página **Confirmación**, haga clic en **Siguiente** para agregar los nodos.

	>[AZURE.WARNING] Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster**. Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.

1. Una vez que se agregan los nodos al clúster, haga clic en **Finalizar**. Ahora el Administrador de clústeres de conmutación por error tiene que indicar que el clúster tiene tres nodos y mostrarlos en una lista en el contenedor **Nodos**.

1. Cierre sesión en la sesión de escritorio remoto.

## Preparación de las instancias de SQL Server para el grupo de disponibilidad

En esta sección, hará lo siguiente tanto en **ContosoSQL1** como en **contosoSQL2**:

- Agregar un inicio de sesión para **NT AUTHORITY\\System** con un conjunto de permisos necesarios preestablecido para la instancia de SQL Server

- Agregar **CORP\\Install** como un rol sysadmin a la instancia de SQL Server predeterminada

- Abrir el firewall para el acceso remoto de SQL Server

- Habilitar la característica Grupos de disponibilidad AlwaysOn

- Cambiar la cuenta de servicio de SQL Server a **CORP\\SQLSvc1** y **CORP\\SQLSvc2**, respectivamente

Estas acciones pueden realizarse en cualquier orden. No obstante, los pasos siguientes se seguirán en orden. Siga los pasos para **ContosoSQL1** y **ContosoSQL2**:

1. Si no ha cerrado la sesión de escritorio remoto para la máquina virtual, hágalo ahora.

1. Inicie los archivos RDP para **ContosoSQL1** y **ContosoSQL2** e inicie sesión como **BUILTIN\\AzureAdmin**.

1. En primer lugar, agregue **NT AUTHORITY\\System** a los inicios de sesión de SQL Server con los permisos necesarios. Inicie **SQL Server Management Studio**.

1. Haga clic en **Conectar** para conectarse a la instancia predeterminada de SQL Server.

1. En el **Explorador de objetos**, expanda **Seguridad** y luego expanda **Inicios de sesión**.

1. Haga clic con el botón derecho en el inicio de sesión **NT AUTHORITY\\System** y haga clic en **Propiedades**.

1. En la página **Elementos protegibles** para el servidor local, seleccione **Conceder** para los siguientes permisos y haga clic en **Aceptar**.

	- Modificar cualquier grupo de disponibilidad

	- Conectar SQL

	- Ver estado del servidor

1. A continuación, agregue **CORP\\Install** como un rol **sysadmin** a la instancia de SQL Server predeterminada. En el **Explorador de objetos**, haga clic con el botón derecho en **Inicios de sesión** y en **Nuevo inicio de sesión**.

1. Escriba **CORP\\Install** en **Nombre de inicio de sesión**.

1. En la página **Roles de servidor**, seleccione **sysadmin**. A continuación, haga clic en **Aceptar**. Una vez creado el inicio de sesión, puede verlo expandiendo **Inicios de sesión** en **Explorador de objetos**.

1. Después cree una regla de firewall para SQL Server. Desde la pantalla **Inicio**, abra **Firewall de Windows con seguridad avanzada**.

1. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, haga clic en **Nueva regla**.

1. En la página **Tipo de regla**, seleccione **Programa** y, a continuación, haga clic en **Siguiente**.

1. En la página **Programa**, seleccione **Esta ruta de acceso del programa** y escriba **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** en el cuadro de texto (si sigue estas instrucciones, pero usa SQL Server 2012, el directorio de SQL Server es **MSSQL11. MSSQLSERVER**). A continuación, haga clic en **Siguiente**.

1. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y haga clic en **Siguiente**.

1. En la página **Perfiles**, acepte la configuración predeterminada y haga clic en **Siguiente**.

1. En la página **Nombre**, especifique el nombre de una regla, como **SQL Server (regla de programa)** en el cuadro de texto **Nombre** y haga clic en **Finalizar**.

1. Después, habilite la característica **Grupos de disponibilidad AlwaysOn**. Desde la pantalla **Inicio**, abra el **Administrador de configuración de SQL Server**.

1. En el árbol del explorador, haga clic en **Servicios SQL Server**, luego haga clic con el botón derecho en el servicio **SQL Server (MSSQLSERVER)** y, a continuación, haga clic en **Propiedades**.

1. Haga clic en la pestaña **Alta disponibilidad de AlwaysOn** y seleccione **Habilitar los Grupos de disponibilidad AlwaysOn**, tal como se muestra a continuación, y haga clic en **Aplicar**. Haga clic en **Aceptar** en el cuadro de diálogo emergente y no cierre la ventana de propiedades todavía. Reiniciará el servicio SQL Server tras cambiar la cuenta de servicio.

	![Habilitación de grupos de disponibilidad AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. A continuación, cambie la cuenta del servicio de SQL Server. Haga clic en la pestaña **Iniciar sesión** y, seguidamente, escriba **CORP\\SQLSvc1** (para **ContosoSQL1**) o **CORP\\SQLSvc2** (para **ContosoSQL2**) en **Nombre de cuenta**; escriba y confirme la contraseña y, finalmente, haga clic en **Aceptar**.

1. En la ventana emergente, haga clic en **Sí** para reiniciar el servicio de SQL Server. Después de reiniciar el servicio SQL Server, entran en vigor los cambios que realizó en la ventana Propiedades.

1. Cierre sesión en las máquinas virtuales.

## Creación del grupo de disponibilidad

Ahora está en disposición de configurar un grupo de disponibilidad. A continuación se resume lo que debe hacer:

- Crear una nueva base de datos (**MyDB1**) en **ContosoSQL1**

- Realizar una copia de seguridad completa y una copia de seguridad del registro de transacciones de la base de datos

- Restaurar las copias de seguridad completas y de registro en **ContosoSQL2** con la opción **NORECOVERY**

- Crear el grupo de disponibilidad (**AG1**) con confirmación sincrónica, conmutación automática por error y réplicas secundarias legibles

### Cree la base de datos de MyDB1 en ContosoSQL1:

1. Si no cerró aún las sesiones de escritorio remoto para **ContosoSQL1** y **ContosoSQL2**, hágalo ahora.

1. Inicie el archivo RDP para **ContosoSQL1** e inicie sesión como **CORP\\Install**.

1. En el **Explorador de archivos**, en **C:**, cree un directorio denominado **copia de seguridad**. Usará este directorio para la copia de seguridad y restauración de la base de datos.

1. Haga clic con el botón derecho en el nuevo directorio, seleccione **Compartir con**, y, a continuación, haga clic en **Personas específicas**, tal como se muestra a continuación.

	![Creación de una carpeta de copia de seguridad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. Agregue **CORP\\SQLSvc1** y asígnele el permiso **Lectura/escritura**; a continuación, agregue **CORP\\SQLSvc2** y asígnele el permiso **Lectura**, tal como se muestra a continuación y, finalmente, haga clic en **Compartir**. Una vez que se complete el proceso de uso compartido de archivos, haga clic en **Listo**.

	![Conceder permisos para la carpeta de copia de seguridad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. A continuación, cree la base de datos. Desde el menú **Inicio**, abra **SQL Server Management Studio** y haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.

1. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Nueva base de datos**.

1. En **Nombre de base de datos**, escriba **MyDB1** y, a continuación, haga clic en **Aceptar**.

### Realice copia de seguridad completa de MyDB1 y restáurela en ContosoSQL2:

1. A continuación, haga una copia de seguridad completa de la base de datos. En el **Explorador de objetos**, expanda **Bases de datos**, haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.

1. En la sección **Origen**, mantenga el **Tipo de copia de seguridad** establecido en **Completa**. En la sección **Destino**, haga clic en **Quitar** para quitar la ruta de acceso del archivo predeterminado para el archivo de copia de seguridad.

1. En la sección **Destino**, haga clic en **Agregar**.

1. En el cuadro de texto **Nombre de archivo**, escriba **\\ContosoSQL1\\backup\\MyDB1.bak**. Seguidamente, haga clic en **Aceptar** y, finalmente, haga clic en **Aceptar** de nuevo para realizar una copia de seguridad de la base de datos. Cuando finalice la operación de copia de seguridad, vuelva a hacer clic en **Aceptar** para cerrar el cuadro de diálogo.

1. A continuación, haga una copia de seguridad del registro de transacciones de la base de datos. En el **Explorador de objetos**, expanda **Bases de datos**, haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.

1. En **Tipo de copia de seguridad**, seleccione **Registro de transacciones**. Mantenga la ruta de acceso al archivo de **Destino** establecida en la que especificó anteriormente y haga clic en **Aceptar**. Una vez se complete la operación de copia de seguridad, haga clic de nuevo en **Aceptar**.

1. A continuación, restaure las copias de seguridad completas y de registros de transacción en **ContosoSQL2**. Inicie el archivo RDP para **ContosoSQL2** e inicie sesión como **CORP\\Install**. Deje abierta la sesión de escritorio remoto para **ContosoSQL1**.

1. Desde el menú **Inicio**, abra **SQL Server Management Studio** y haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.

1. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Restaurar base de datos**.

1. En la sección **Origen**, seleccione **Dispositivo** y haga clic en el botón **...**.

1. En **Seleccionar dispositivos de copia de seguridad**, haga clic en **Agregar**.

1. En la ubicación del archivo de copia de seguridad, escriba \\ContosoSQL1\\backup, haga clic en actualizar, a continuación, seleccione MyDB1.bak y haga clic en Aceptar, luego haga clic de nuevo en Aceptar. Ahora debería ver la copia de seguridad completa y la copia de seguridad del registro en el panel Conjuntos de copia de seguridad para restaurar.

1. Vaya a la página Opciones, seleccione RESTAURAR CON NORECOVERY en Estado de recuperación y, a continuación, haga clic en Aceptar para restaurar la base de datos. Una vez se complete la operación de copia de seguridad, haga clic en Aceptar.

### Cree el conjunto de disponibilidad:

1. Vuelva a la sesión de escritorio remoto para **ContosoSQL1**. En el **Explorador de objetos** de SSMS, haga clic en **Alta disponibilidad AlwaysOn** y en **Asistente para nuevo grupo de disponibilidad**, tal como se muestra a continuación.

	![Iniciar el Asistente para nuevo grupo de disponibilidad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. En la página **Introducción**, haga clic en **Siguiente**. En la página **Especificar nombre de grupo de disponibilidad**, escriba **AG1** en **Nombre del grupo de disponibilidad** y, a continuación, vuelva a hacer clic en **Siguiente**.

	![Asistente para nuevo grupo de disponibilidad, especificar el nombre del grupo](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. En la página **Seleccionar bases de datos**, seleccione **MyDB1** y haga clic en **Siguiente**. La base de datos cumple los requisitos previos para un grupo de disponibilidad porque ha realizado al menos una copia de seguridad completa en la réplica principal pretendida.

	![Asistente para nuevo grupo de disponibilidad, seleccionar bases de datos](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. En la página **Especificar réplicas**, haga clic en **Agregar réplica**.

	![Asistente para nuevo grupo de disponibilidad, especificar réplicas](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. Se abre el cuadro de diálogo **Conectar con el servidor**. Escriba **ContosoSQL2** en **Nombre del servidor** y, después, haga clic en **Conectar**.

	![Asistente para nuevo de AG, conectar a servidor](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. De vuelta en la página **Especificar réplicas**, ahora debería ver **ContosoSQL2** en **Réplicas de disponibilidad**. Configure las réplicas como se muestra a continuación. Cuando haya terminado, haga clic en **Siguiente**.

	![Asistente para nuevo grupo de disponibilidad, especificar réplicas (completo)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. En la página **Seleccionar sincronización de datos iniciales**, seleccione **Solo unión** y después haga clic en **Siguiente**. Ya realizó la sincronización de datos de forma manual cuando efectuó las copias de seguridad completas y de transacciones en **ContosoSQL1** y las restauró en **ContosoSQL2**. En lugar de esto, puede elegir no realizar las operaciones de copia de seguridad y restauración en la base de datos y seleccionar **Total** para permitir que el Asistente del nuevo grupo de disponibilidad realice la sincronización de datos en su lugar. Sin embargo, esto no se recomienda para bases de datos de gran tamaño como las que se encuentran en algunas empresas.

	![Asistente para nuevo grupo de disponibilidad, seleccionar sincronización de datos iniciales](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. En la página **Validación**, haga clic en **Siguiente**. Esta página debe ser similar a la siguiente. Hay una advertencia para la configuración del agente de escucha porque no ha configurado un agente de escucha de grupo de disponibilidad. Puede omitir esta advertencia, ya que este tutorial no configura un agente de escucha. Para configurar el agente de escucha después de completar este tutorial, consulte [Configuración del agente de escucha ILB de los Grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

	![Asistente para nuevo grupo de disponibilidad, validación](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. En la página **Resumen**, haga clic en **Finalizar** y espere hasta que el asistente configure el nuevo grupo de disponibilidad. En la página **Progreso**, puede hacer clic en **Más detalles** para ver el progreso detallado. Cuando el asistente termine, inspeccione la página **Resultados** para comprobar que se creó correctamente el grupo de disponibilidad y, a continuación, haga clic en **Cerrar** para salir del asistente.

	![Asistente para nuevo grupo de disponibilidad, resultados](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. En el **Explorador de objetos**, expanda **Alta disponibilidad AlwaysOn** y después, expanda **Grupos de disponibilidad**. Ahora debería de ver el nuevo grupo de disponibilidad en este contenedor. Haga clic con el botón derecho en **AG1 (principal)** y luego en **Mostrar panel**.

	![Mostrar panel de grupo de disponibilidad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. El **panel AlwaysOn** debería ser similar al que se muestra a continuación. Puede ver las réplicas, el modo de conmutación por error de cada réplica y el estado de sincronización.

	![Panel de grupo de disponibilidad](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. Vuelva a **Administrador del servidor**, seleccione **Herramientas** y, después, inicie **Administrador de clústeres de conmutación por error**.

1. Expanda **Cluster1.corp.contoso.com** y expanda **Servicios y aplicaciones**. Elija **Roles** y tenga en cuenta que se ha creado el rol del grupo de disponibilidad **AG1**. Tenga en cuenta que AG1 no tiene ninguna dirección IP por la que los clientes de la base de datos puedan conectarse al grupo de disponibilidad, porque no configuró ningún agente de escucha. Puede conectarse directamente al nodo principal para las operaciones de lectura y escritura y al nodo secundario para las consultas de sólo lectura.

	![Grupo de disponibilidad en el administrador de clústeres de conmutación por error.](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING] No trate de realizar una conmutación por error del grupo de disponibilidad desde el Administrador de clústeres de conmutación por error. Todas las operaciones de conmutación por error deben realizarse desde el **Panel AlwaysOn** de SSMS. Para obtener más información, consulte [Restricciones en el uso del Administrador de clústeres de conmutación por error de WSFC con Grupos de disponibilidad](https://msdn.microsoft.com/library/ff929171.aspx).

## Pasos siguientes
Ha implementado correctamente SQL Server AlwaysOn creando un grupo de disponibilidad en Azure. Para configurar un agente de escucha para este grupo de disponibilidad, consulte [Configuración del agente de escucha ILB de los grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Para obtener más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Máquinas virtuales de Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0211_2016-->