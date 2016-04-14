<properties
	pageTitle="Creación de una máquina virtual de Oracle WebLogic Server 12c | Microsoft Azure"
	description="Cree una máquina virtual de Oracle WebLogic Server 12c que ejecute Windows Server 2012 en Microsoft Azure, usando el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Creación de una máquina virtual Oracle WebLogic Server 12c en Azure
En el ejemplo siguiente se muestra cómo puede crear una máquina virtual basada en una imagen Oracle WebLogic Server 12c que se ejecuta en Microsoft con Windows Server 2012 en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.



##Para crear una máquina virtual Oracle WebLogic Server 12c en Azure

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2. Haga clic en **Marketplace**, en **Proceso** y, a continuación, escriba **Oracle** en el cuadro de búsqueda.

3.	Seleccione la imagen **Oracle WebLogic Server 12c Standard Edition en Windows Server 2012** o **Oracle WebLogic Server 12c Enterprise Edition en Windows Server 2012**. Revise la información sobre esta imagen (por ejemplo, el tamaño mínimo recomendado) y, a continuación, haga clic en **Siguiente**.

4.	Especifique un **Nombre de host** para la máquina virtual.

5.	Especifique un **Nombre de usuario** para la máquina virtual. Tenga en cuenta que este usuario permite iniciar sesión de manera remota en la máquina virtual; no es el nombre de usuario de la base de datos de Oracle.

6.	Especifique y confirme una contraseña para la máquina virtual o proporcione una clave pública SSH.

7.	Elija un **Plan de tarifas**. Tenga en cuenta que los planes de tarifas recomendados se muestran de forma predeterminada. Para ver todas las opciones de configuración, haga clic en **Ver todo** en la parte superior derecha.

8.	Establezca la configuración opcional según sea necesario, con estas consideraciones:
	1. Deje **Cuenta de almacenamiento** tal y como se encuentra para crear una nueva cuenta de almacenamiento con el nombre de la máquina virtual.
	2. Deje **Conjunto de disponibilidad** como "No configurado".
	3. No agregue ningún **extremo** en este momento.

9.	Elija o cree un [Grupo de recursos](resource-group-portal.md).

10. Elija una **Suscripción**.

11. Elija una **Ubicación**.


##Para configurar su máquina virtual Oracle WebLogic Server 12c en Azure

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2.	Haga clic en **Máquinas virtuales**.

3.	Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.

4.	Haga clic en **Conectar**.

5.	Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

6.	Dentro del cuadro de diálogo **Inicio rápido de plataforma WebLogic**, haga clic en **Introducción a WebLogic Server**. (Si el cuadro de diálogo **Inicio rápido de plataforma WebLogic** no está abierto, ábralo haciendo clic en **Inicio de Windows**, escribiendo **Iniciar el servidor de administración del dominio de WebLogic Server** y, a continuación, haciendo clic en el icono **Iniciar el servidor de administración del dominio de WebLogic Server**).

7.	En el cuadro de diálogo **Bienvenido**, seleccione **Crear un nuevo dominio de WebLogic** y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image10.png)

8.	En el cuadro de diálogo **Seleccionar origen de dominio**, acepte los valores predeterminados y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image11.png)

9.	En el cuadro de diálogo **Especificar nombre de dominio y ubicación**, acepte los valores predeterminados y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image12.png)

10.	En el cuadro de diálogo **Configurar el nombre de usuario y la contraseña de administrador**:

	1.	[Opcional] Cambie el nombre de usuario de **weblogic** a un valor que desee.

	2.	Especifique y confirme una contraseña para el administrador del WebLogic Server.

	3.	Haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image13.png)

11.	En el cuadro de diálogo **Configurar modo de inicio del servidor y JDK**, seleccione **Modo de producción**, seleccione el JDK disponible (o el explorador para un JDK si lo desea) y haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image14.png)

12.	En el cuadro de diálogo **Seleccionar configuración opcional**, no seleccione ninguna opción y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image15.png)

13.	En el cuadro de diálogo **Resumen de configuración**, haga clic en **Crear**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image16.png)

14.	En el cuadro de diálogo **Crear dominio**, marque la opción **Iniciar el servidor de administración** y, a continuación, haga clic en **Listo**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image17.png)

15.	Se iniciará un símbolo del sistema para **startWebLogic.cmd**. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

##Para instalar una aplicación en una máquina virtual Oracle WebLogic Server 12c en Azure
1.	Todavía conectado a la máquina virtual, copie el ejemplo de shoppingcart.war disponible en http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war localmente. Por ejemplo, cree una carpeta denominada **c:\\mywar** y guarde el archivo WAR de http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war en **c:\\mywar**.

2.	Abra la **Consola de administración de WebLogic Server**, http://localhost:7001/console. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

3.	Dentro de la **Consola de administración de WebLogic Server**, haga clic en **Bloquear y editar**, haga clic en **Implementaciones** y, a continuación, haga clic en **Instalar**.

4.	En **Ruta de acceso**, escriba **c:\\mywar\\shoppingcart.war.**

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image18.png)

	Haga clic en **Siguiente**.

5.	Seleccione **Instalar esta implementación como una aplicación** y, a continuación, haga clic en **Siguiente**.

6.	Haga clic en **Finalizar**

7.	Dentro de la **Consola de administración de WebLogic Server**, haga clic en **Guardar** y, a continuación, haga clic en **Activar cambios**.

8.	Haga clic en **Implementaciones**, seleccione **shoppingcart**, haga clic en **Inicio** y, a continuación, haga clic en **Atender todas las solicitudes**. Cuando se le solicite confirmar, haga clic en **Sí**.

9.	Para ver la aplicación de carro de la compra que se ejecuta localmente, abra un explorador en <http://localhost:7001/shoppingcart>

10.	Cree un extremo para la máquina virtual:

	1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

	2.	Haga clic en **Examinar**.

	3.	Haga clic en **Máquinas virtuales**.

	4.	Seleccione la máquina virtual

	5.	Haga clic en **Configuración**.

	6.	Haga clic en **Extremos**.

	7.	Haga clic en **Agregar**.

	8.	Escriba un nombre para el extremo

		1. Use **TCP** para el protocolo.

		2. Use **80** para el puerto público.

		3. Use **7001** para el puerto privado.

	9.	No cambie el resto de las opciones

	10. Haga clic en **Aceptar**.

11.	Permita una conexión entrante a través del firewall al puerto 7001.

	1.	Inicie sesión en la máquina virtual.

	2.	Haga clic en **Inicio de Windows**, escriba **Firewall de Windows con seguridad avanzada,** y, a continuación, haga clic en el icono **Firewall de Windows con seguridad avanzada**. De este modo se abrirá la consola de administración **Firewall de Windows con seguridad avanzada**.

	3.	En la consola de administración de firewall, haga clic en **Reglas de entrada** en el panel izquierdo (si no ve **Reglas de entrada**, expanda el nodo superior en el panel izquierdo) y, a continuación, haga clic en Nueva regla en el panel derecho.

	4.	En **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.

	5.	En **Protocolo y puerto**, seleccione **TCP** y **Puertos locales específicos**, escriba **7001** para el puerto y, luego, haga clic en **Siguiente**.

	6.	Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	7.	Acepte los valores predeterminados para los perfiles para los que se aplica la regla y haga clic en **Siguiente**.

	8.	Especifique un nombre para la regla y, opcionalmente, una descripción y, luego, haga clic en **Finalizar**.

12.	Para ver la aplicación de carro de la compra que se ejecuta en Internet, abra un explorador en la dirección URL en forma de `http://<<unique_domain_name>>/shoppingcart`. (Para determinar el valor de <<*unique\_domain\_name*>> dentro del [Portal de Azure](https://ms.portal.azure.com/), haga clic en **Máquinas virtuales** y después seleccione la máquina virtual que está usando para ejecutar Oracle WebLogic Server).


##Recursos adicionales
Ahora que ha configurado la máquina virtual que se está ejecutando en el Oracle WebLogic Server, consulte los temas siguientes para obtener información adicional.

-	[Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Documentación del producto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c con Linux en Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Imágenes de máquina virtual de Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=AcomDC_1203_2015-->