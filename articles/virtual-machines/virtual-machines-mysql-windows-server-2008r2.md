<properties
	pageTitle="Creación de una máquina virtual con MySQL | Microsoft Azure"
	description="Cree una máquina virtual de Azure creada con el modelo de implementación clásica que ejecute Windows Server 2012 R2 y, a continuación, instale y configure la base de datos MySQL en ella."
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
	ms.date="11/09/2015"
	ms.author="cynthn"/>


# Instalación de MySQL en una máquina virtual creada con el modelo de implementación clásico que disponga de Windows Server 2012 R2

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


[MySQL](http://www.mysql.com)L es una conocida base de datos SQL de código abierto. Con el [Portal de Azure clásico](http://manage.windowsazure.com), puede crear una máquina virtual que ejecute Windows Server 2012 R2 desde la galería de imágenes. A continuación, puede instalar y configurarla como un MySQL Server.

Para obtener instrucciones acerca de cómo instalar MySQL en Linux, consulte: [Instalación de MySQL en Azure](virtual-machines-linux-install-mysql.md).

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar el Portal de Azure clásico para crear una máquina virtual que ejecute Windows Server 2012 R2.

- Instale y ejecute la versión 5.6.23 de MySQL de la comunidad como un MySQL Server en la máquina virtual.


## Creación de una máquina virtual que ejecuta Windows Server

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Acoplamiento de un disco de datos

Después de crear la máquina virtual, puede adjuntar un disco de datos adicional de forma opcional. Esto se recomienda para las cargas de trabajo de producción y para evitar quedarse sin espacio en la unidad de sistema operativo (C:), que incluye el sistema operativo.

Consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](storage-windows-attach-disk.md) y siga las instrucciones para conectar un disco vacío. Establezca la configuración de la caché de host en **Ninguna** o **Solo lectura**.

## Iniciar sesión en la nueva máquina virtual

A continuación, inicie sesión en la máquina virtual para poder instalar MySQL.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

##Instalación y ejecución de MySQL Community Server en la máquina virtual

Siga estos pasos para instalar, configurar y ejecutar la versión de la comunidad de MySQL Server:

> [AZURE.NOTE]Estos pasos son para la versión 5.6.23.0 de la comunidad de MySQL y Windows Server 2012 R2. Su experiencia podría ser diferente con versiones distintas de Windows Server o de MySQL.

1.	Una vez conectado a la máquina virtual mediante el Escritorio remoto, haga clic en **Internet Explorer** desde la pantalla de inicio.
2.	Seleccione el botón **Herramientas** de la esquina superior derecha (el icono de la rueda dentada) y después haga clic en **Opciones de Internet**. Haga clic en la pestaña **Seguridad**, haga clic en el icono **Sitios de confianza** y, a continuación, haga clic en el botón **Sitios**. Agregue http://*.mysql.com a la lista de sitios de confianza. Haga clic en **Cerrar** y después en **Aceptar**.
3.	En la barra de direcciones de Internet Explorer, escriba http://dev.mysql.com/downloads/mysql/.
4.	Utilice el sitio de MySQL para buscar y descargar la versión más reciente del instalador de MySQL para Windows. Al elegir el instalador de MySQL, descargue la versión que tenga el conjunto completo de archivos (por ejemplo, mysql-installer-community-5.6.23.0.msi con un tamaño de archivo de 282,4 MB) y guarde el instalador.
5.	Cuando el instalador haya terminado de descargarse, haga clic en **Ejecutar** para iniciar el programa de instalación.
6.	En la página **Contrato de licencia**, acepte el contrato de licencia y haga clic en **Siguiente**.
7.	En la página **Elección de un tipo de instalación**, haga clic en el tipo de instalación que desee y, a continuación, haga clic en **Siguiente**. Los pasos siguientes presuponen que se ha seleccionado el tipo de instalación **Solo servidor**.
8.	En la página **Instalación**, haga clic en **Ejecutar**. Cuando la instalación se haya completado, haga clic en **Siguiente**.
9.	En la página **Configuración del producto**, haga clic en **Siguiente**.
10.	En la página **Tipo y redes**, especifique las opciones de conectividad y de tipo de configuración que desea, incluido el puerto TCP si es necesario. Seleccione **Mostrar opciones avanzadas** y después haga clic en **Siguiente**.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)

11.	En la página **Cuentas y roles**, especifique una contraseña de raíz segura de MySQL. Agregue cuentas de usuario de MySQL adicionales según sea necesario y haga clic en **Siguiente**.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)

12.	En la página **Servicio de Windows**, especifique los cambios en la configuración predeterminada para ejecutar MySQL Server como un servicio de Windows según sea necesario y, a continuación, haga clic en **Siguiente**.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)

13.	En la página **Opciones avanzadas**, especifique los cambios en las opciones de registro según sea necesario y, a continuación, haga clic en **Siguiente**.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)

14.	En la página **Aplicar la configuración del servidor**, haga clic en **Ejecutar**. Cuando haya completado los pasos de configuración, haga clic en **Finalizar**.
15.	En la página **Configuración del producto**, haga clic en **Siguiente**.
16.	En la página **Instalación completada**, haga clic en **Copiar el registro al Portapapeles** si desea examinarlo más tarde y, a continuación, haga clic en **Finalizar**.
17.	Desde la pantalla de inicio, escriba **mysql** y, a continuación, haga clic en **Cliente de línea de comandos de MySQL 5.6**.
18.	Escriba la contraseña raíz que especificó en el paso 11 y se mostrará un símbolo del sistema donde puede emitir comandos para configurar MySQL. Para obtener detalles de los comandos y la sintaxis, consulte [Manuales de referencia de MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)

19.	También puede configurar los ajustes predeterminados de configuración del servidor, como los directorios y unidades de datos y de base, con las entradas en el archivo C:\\Program Files (x86)\\MySQL\\MySQL Server 5.6\\my-default.ini. Para obtener más información, consulte [Valores predeterminados de configuración de Server 5.1.2](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## Configuración de extremos

Si desea que el servicio de MySQL Server esté disponible para los equipos de cliente de MySQL en Internet, debe configurar un extremo del puerto TCP en el que escucha el servicio MySQL Server y crear una regla adicional de Firewall de Windows. Es el puerto TCP 3306 a menos que se especifique uno diferente en la página **Tipos y redes** (paso 10 del procedimiento anterior).


> [AZURE.NOTE]Debe considerar detenidamente las implicaciones de seguridad de hacer esto, ya que haría que el servicio de MySQL Server esté disponible para todos los equipos en Internet. Puede definir el conjunto de direcciones IP de origen que tienen permiso para usar el extremo con una lista de control de acceso (ACL). Para obtener más información, consulte [Cómo establecer extremos en una máquina virtual](virtual-machines-set-up-endpoints.md).


Para configurar un extremo del servicio de MySQL Server:

1.	En el Portal de Azure clásico, haga clic en **Máquinas virtuales**, en el nombre de la máquina virtual de MySQL y, después, en **Puntos de conexión**.
2.	En la barra de comandos, haga clic en **Agregar**.
3.	En la página **Agregar un extremo a una máquina virtual**, haga clic en la flecha derecha.
4.	Si usa el puerto TCP 3306 predeterminado de MySQL, haga clic en **MySQL** en **Nombre** y, a continuación, haga clic en la marca de verificación.
5.	Si utiliza un puerto TCP diferente, escriba un nombre único en **Nombre**. En protocolo, seleccione **TCP**, escriba el número de puerto tanto en **Puerto público** y **Puerto privado** y, a continuación, haga clic en la marca de verificación.

## Agregar una regla de Firewall de Windows para permitir el tráfico de MySQL

Para agregar una regla de Firewall de Windows que permita tráfico de MySQL desde Internet, ejecute el siguiente comando en un símbolo del sistema de Windows PowerShell con privilegios elevados en la máquina virtual del servidor de MySQL.

	New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


	
## Probar la conexión remota


Para probar la conexión remota con el servicio de MySQL Server que se ejecuta en la máquina virtual de Azure, primero debe determinar el nombre DNS que corresponde al servicio en la nube que contenga la máquina virtual que ejecuta MySQL Server.

1.	En el Portal de Azure clásico, haga clic en **Máquinas virtuales**, en el nombre de la máquina virtual del servidor de MySQL y, después, en **Panel**.
2.	En el panel de la máquina virtual, tenga en cuenta el valor de **Nombre DNS** en la sección **Vista rápida**. Aquí tiene un ejemplo:

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)

3.	Desde un equipo local que tenga MySQL o el cliente de MySQL en ejecución, ejecute el siguiente comando para iniciar sesión como un usuario de MySQL.

		mysql -u <yourMysqlUsername> -p -h <yourDNSname>

	Por ejemplo, con el nombre de usuario de MySQL dbadmin3 y el nombre DNS testmysql.cloudapp.net de la máquina virtual, use el siguiente comando.

		mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## Recursos adicionales

Para obtener más información sobre MySQL, consulte la [Documentación de MySQL](http://dev.mysql.com/doc/).

<!---HONumber=AcomDC_1203_2015-->