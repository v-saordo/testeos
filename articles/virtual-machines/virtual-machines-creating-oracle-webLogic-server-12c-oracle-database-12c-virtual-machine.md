<properties
	pageTitle="Creación de una VM de Oracle WebLogic Server y Database | Microsoft Azure"
	description="Cree una imagen de Oracle WebLogic Server 12c y Oracle Database 12c Azure que se ejecute en Windows Server 2012, con el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Creación de una máquina virtual Oracle WebLogic Server 12c y Oracle Database 12c en Azure

En este artículo se muestra cómo crear una máquina virtual basada en una imagen Oracle WebLogic Server 12c proporcionada por Microsoft y una imagen Oracle Database 12c que se ejecuta en Windows Server 2012 en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


##Para crear una máquina virtual Oracle WebLogic Server 12c y Oracle Database 12c en Azure

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2.	Haga clic en **Marketplace**, en **Proceso** y, a continuación, escriba **Oracle** en el cuadro de búsqueda.

3.	Seleccione la imagen de **Oracle Database 12c y WebLogic Server 12c Standard Edition en Windows Server 2012** u **Oracle Database 12c y WebLogic Server 12c Enterprise Edition en Windows Server 2012**. Revise la información sobre esta imagen (por ejemplo, el tamaño mínimo recomendado) y después haga clic en **Siguiente**.

4.	Especifique la información que desee en **Nombre de host** para la máquina virtual.

5.	Especifique la información que desee en **Nombre de usuario** para la máquina virtual. Tenga en cuenta que este nombre de usuario permite iniciar sesión remotamente en la máquina virtual, no es el nombre de usuario de la base de datos de Oracle.

6.	Especifique y confirme una contraseña para la máquina virtual o proporcione una clave pública de Secure Shell (SSH).

7.	Elija un **Plan de tarifa**. Tenga en cuenta que los niveles de precios recomendados se muestran de forma predeterminada. Para ver todas las opciones de configuración, haga clic en **Ver todas** en la parte superior derecha.

8. Establezca las configuraciones opcionales según sea necesario. Siga estas consideraciones:

	a. No modifique **Cuenta de almacenamiento** para crear una nueva cuenta de almacenamiento con el nombre de la máquina virtual.

	b. Deje **Conjunto de disponibilidad** como **No configurado**.

	c. No agregue ningún extremo en este momento.

9.	Elija o cree un grupo de recursos. Para obtener más información, vea [Uso del Portal de Azure para administrar los recursos de Azure](resource-group-portal.md).

10. Elija una **suscripción**.

11. Elija una **ubicación**.


##Para crear la base de datos hospedada en esta máquina virtual

Siga las instrucciones de [Creación de una máquina virtual de Oracle Database 12c en Azure](virtual-machines-creating-oracle-database-virtual-machine.md), empezando por la sección **Para crear la base de datos con la máquina virtual de Oracle Database 12c en Azure**.

##Para configurar su Oracle WebLogic Server 12c hospedado en esta máquina virtual
Siga las instrucciones de [Creación de una máquina virtual de Oracle WebLogic Server 12c en Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md), empezando por la sección **Para configurar la máquina virtual de Oracle WebLogic Server 12c en Azure**. Si desea configurar un clúster de WebLogic Server, vea también [Creación de un clúster Oracle WebLogic Server 12c en Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Recursos adicionales
[Consideraciones variadas sobre las imágenes de máquina virtual de Oracle](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Lista de imágenes de máquinas virtuales de Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Conexión a la Base de datos de Oracle desde una aplicación de Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c con Linux en Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database DBA 12c versión 1 de dos días](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1203_2015-->