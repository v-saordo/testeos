
<properties
	pageTitle="Creación de una máquina virtual de base de datos de Oracle con el Portal de Azure clásico | Microsoft Azure"
	description="Aprenda a crear una máquina virtual con una base de datos de Oracle en ella con el modelo de implementación clásico y el Portal de Azure."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Creación de una máquina virtual de base de datos Oracle en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Modelo del Administrador de recursos.


En el ejemplo siguiente se muestra cómo puede crear una máquina virtual basada en una imagen de Base de datos de Oracle proporcionada por Microsoft que se ejecuta en Windows Server 2012 en Azure. Hay dos pasos. En primer lugar, cree la máquina virtual y luego cree la base de datos de Oracle dentro de la máquina virtual. El ejemplo mostrado es la versión de la Base de datos de Oracle 12c, pero los pasos son prácticamente idénticos para la versión 11g.

##Para crear una máquina virtual de base de datos de Oracle

1.	Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2.	Haga clic en **Marketplace**, en **Proceso** y, luego, escriba **Oracle** en el cuadro de búsqueda.

3.	Seleccione una de las imágenes de base de datos Oracle disponibles, por ejemplo, la **versión 11g, versión 12c, Standard Edition, Enterprise Edition o una de las opciones conocidas o agrupaciones de opciones avanzadas.** Revise la información sobre la imagen que seleccione (por ejemplo, el tamaño mínimo recomendado) y, luego, haga clic en **Siguiente**.

4.	Especifique un **Nombre de host** para la máquina virtual.

5.	Especifique un **Nombre de usuario** para la máquina virtual. Tenga en cuenta que este usuario permite iniciar sesión de manera remota en la máquina virtual; no es el nombre de usuario de la base de datos de Oracle.

6.	Especifique y confirme una contraseña para la máquina virtual o proporcione una clave pública SSH.

7.	Elija un **Plan de tarifas**. Tenga en cuenta que los niveles de precios recomendados se muestran de forma predeterminada. Para ver todas las opciones de configuración, haga clic en **Ver todo** en la parte superior derecha.

8.	Establezca la configuración opcional según sea necesario, con estas consideraciones:

	a. Deje **Cuenta de almacenamiento** tal y como se encuentra para crear una nueva cuenta de almacenamiento con el nombre de la máquina virtual.

	b. Deje **Conjunto de disponibilidad** como "No configurado".

	c. No agregue ningún **extremo** en este momento.

9.	Elija o cree un grupo de recursos.

10. Elija una **suscripción**.

11. Elija una **ubicación**.

12. Haga clic en **Crear** y se iniciará el proceso de creación de una máquina virtual. En cuanto la máquina virtual tenga el estado **En ejecución**, continúe con el siguiente procedimiento.


##Para crear su base de datos mediante la máquina virtual de Base de datos de Oracle en Azure

1.	Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2.	Haga clic en **Máquinas virtuales**.

3.	Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.

4.	Haga clic en **Conectar**.

5.	Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

6.	Cree una variable de entorno denominada **ORACLE\_HOSTNAME** con su valor establecido en el nombre del equipo de la máquina virtual. Puede crear una variable de entorno mediante los siguientes pasos:

	a. En Windows, haga clic en **Inicio**, escriba **Panel de control**, haga clic en el icono de **Panel de control**, en **Sistema y seguridad**, en **Sistema** y luego en **Configuración avanzada del sistema**.

	b. Haga clic en la pestaña **Avanzado** y, luego, en **Variables de entorno**.

	c. En la sección **Variables del sistema**, haga clic en **Nuevo** para crear la variable.

	d. En el cuadro de diálogo **Nueva variable del sistema**, escriba **ORACLE\_HOSTNAME** para el nombre de la variable y, luego, el nombre del equipo de la máquina virtual como valor. Para determinar el nombre del equipo, abra un símbolo del sistema y ejecute **SET COMPUTERNAME** (el resultado de ese comando contendrá el nombre del equipo).

	e. Haga clic en **Aceptar** para guardar la nueva variable de entorno y cierre el cuadro de diálogo **Nueva variable del sistema**.

	f. Cierre los otros cuadros de diálogo que se abrieron mediante el Panel de control.

7.	En Windows, haga clic en **Inicio** y luego escriba **Asistente de configuración de base de datos**. Haga clic en el icono **Asistente de configuración de base de datos**.

8.	Dentro del **Asistente de configuración de base de datos**, proporcione los valores necesarios para cada paso del cuadro de diálogo:

	a. **Paso 1:** haga clic en **Crear base de datos** y, luego, en **Siguiente**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **Paso 2:** escriba un valor para **Nombre de base de datos global**. Escriba y confirme un valor para **Contraseña administrativa**. Esta contraseña es para el usuario **SYSTEM** de base de datos Oracle. Borre **Crear como base de datos del contenedor**. Haga clic en **Siguiente**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **Paso 3:** la comprobación de los requisitos previos debe continuar automáticamente, avanzando al **paso 4**.

	d. **Paso 4:** revise las opciones **Crear base de datos: resumen** y, luego, haga clic en **Finalizar**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **Paso 5:** en la **Página de progreso** se informará del estado de la creación de la base de datos.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. Después de crear la base de datos, tendrá la opción de usar el cuadro de diálogo **Administración de contraseñas**. Modifique la configuración de la contraseña si es necesario para sus requisitos y, luego, cierre los cuadros de diálogo para salir del **Asistente para configuración de bases de datos**.

##Para confirmar que la base de datos está instalada

1.	Todavía conectado a la máquina virtual, inicie un símbolo del sistema de SQL Plus. En Windows, haga clic en *Inicio** y luego escriba **SQL Plus**. Haga clic en el icono **SQL Plus**.

2.	Cuando se le solicite, inicie sesión con el nombre de usuario de **SYSTEM** y la contraseña que especificó cuando creó la base de datos Oracle.

3.	Ejecute el comando siguiente en el símbolo del sistema SQL Plus.

		**select * from GLOBAL\_NAME;**

	El resultado debe ser el nombre global de la base de datos que ha creado.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Permitir el acceso remoto a la base de datos
Para permitir el acceso remoto a la base de datos (por ejemplo, desde un equipo cliente que ejecute código Java), se necesitará iniciar el agente de escucha de la base de datos, abrir el puerto 1521 en el firewall de su máquina virtual y crear un extremo público para el puerto 1521.

### Iniciar el agente de escucha de la base de datos
1.	Inicie sesión en la máquina virtual.

2.	Abra el símbolo del sistema.

3.	En el símbolo del sistema, ejecute el siguiente comando.

		**lsnrctl start**

> [AZURE.NOTE] Puede ejecutar **lsnrctl status** para comprobar el estado del agente de escucha. Si desea detener el agente de escucha, puede ejecutar **lsnrctl stop**.

### Abrir el puerto 1521 en el firewall de su máquina virtual

1.	Mientras sigue conectado a la máquina virtual, en Windows, haga clic en **Inicio**, escriba **Firewall de Windows con seguridad avanzada,** y, luego, haga clic en el icono **Firewall de Windows con seguridad avanzada**. De este modo se abrirá la consola de administración **Firewall de Windows con seguridad avanzada**.

2.	En la consola de administración de firewall, haga clic en **Reglas de entrada** en el panel izquierdo (si no ve **Reglas de entrada**, expanda el nodo superior en el panel izquierdo) y, luego, haga clic en **Nueva regla** en el panel derecho.

3.	En **Tipo de regla**, seleccione **Puerto** y luego haga clic en **Siguiente**.

4.	Para **Protocolo y puerto**, seleccione **TCP** y **Puertos locales específicos**, escriba **1521** para el puerto y, luego, haga clic en **Siguiente**.

5.	Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

6.	Acepte los valores predeterminados para los perfiles para los que se aplica la regla y luego haga clic en **Siguiente**.

7.	Especifique un nombre para la regla y, opcionalmente, una descripción y, luego, haga clic en **Finalizar**.

### Crear un extremo público para el puerto 1521

1.	Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2.	Haga clic en **Examinar**.

3.  Haga clic en **Máquinas virtuales**.

4.	Seleccione la máquina virtual.

5.	Haga clic en **Configuración**.

6.	Haga clic en **Extremos**.

7.	Haga clic en **Agregar**.

8.	Escriba un nombre para el extremo:

	a. Use **TCP** para el protocolo

	b. Use **1521** para el puerto público

	c. Use **1521** para el puerto privado.

9.	No cambie el resto de las opciones.

10. Haga clic en **Aceptar**.

##Habilitar el acceso remoto al Administrador corporativo de la Base de datos de Oracle
Si desea habilitar el acceso remoto al Administrador corporativo de la Base de datos de Oracle, abra el puerto 5500 de del firewall y cree un punto de conexión de máquina virtual para 5500 en el Portal de Azure clásico (mediante los pasos indicados anteriormente para abrir el puerto 1521 y crear un punto de conexión para 1521). A continuación, para ejecutar el Administrador corporativo de Oracle desde el equipo remoto, abra un explorador en la dirección URL en forma de `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE] Puede determinar el valor de *\<\<unique\_domain\_name\>\>* dentro del [Portal de Azure clásico](https://ms.portal.azure.com/) haciendo clic en **Máquinas virtuales** y, luego, seleccionando la máquina virtual que está usando para ejecutar la base de datos de Oracle.

##Configuración de opciones conocidas y paquetes de opciones avanzadas
Si eligió la **base de datos Oracle con opciones conocidas** o la **base de datos Oracle con paquetes de opciones avanzadas**, el paso siguiente consiste en configurar las características de complemento en su instalación de Oracle. Consulte la documentación de Oracle para obtener instrucciones acerca de cómo configurarlas en Windows, ya que las configuraciones pueden variar ampliamente en función de sus necesidades para cada componente individual.

La **base de datos Oracle con el paquete de opciones conocidas** incluye Oracle Database Enterprise Edition e instancias con licencia incluida de [Creación de particiones](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Protección de datos activa](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack para base de datos](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack para base de datos](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) y [Oracle Lifecycle Management Pack para base de datos](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

La **base de datos Oracle con paquetes de opciones avanzadas** incluye instancias con licencia incluida de todas las opciones del paquete de opciones conocidas, además de [Compresión avanzada](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Seguridad avanzada](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Seguridad de etiquetas](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Almacén de base de datos](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Análisis avanzado](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Espacial y gráfico](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Memoria caché de base de datos en la memoria](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Paquete de enmascaramiento de datos](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) y el paquete de administración de datos de prueba de Oracle (como parte del paquete de enmascaramiento de datos).

##Recursos adicionales
Ahora que ha configurado la máquina virtual y que ha creado su base de datos, consulte los temas siguientes para obtener información adicional.

-	[Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Biblioteca de documentación de Base de datos de Oracle 12c](http://www.oracle.com/pls/db1211/homepage)

-	[Conexión a la Base de datos de Oracle desde una aplicación de Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Imágenes de máquina virtual de Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database DBA 12c versión 1 de dos días](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1203_2015-->