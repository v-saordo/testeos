<properties
	pageTitle="Configurar puntos de conexión en una máquina virtual clásica | Microsoft Azure"
	description="Aprenda a configurar puntos de conexión en el Portal de Azure clásico para permitir la comunicación con una máquina virtual en Azure."
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
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Configurar los puntos de conexión en una máquina virtual de Azure clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Todas las máquinas virtuales que se crean en Azure con el modelo de implementación clásico pueden comunicarse automáticamente a través de un canal de red privada con otras máquinas virtuales del mismo servicio en la nube o de la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual.

Al crear una máquina virtual en el Portal de Azure clásico, los puntos de conexión comunes, como los de Escritorio remoto, Windows PowerShell Remoting y Shell seguro (SSH), se suelen crear automáticamente, según el sistema operativo que elija. Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.

Cada punto de conexión cuenta con un *puerto público* y un *puerto privado*:

- El puerto público que usa el equilibrador de carga de Azure para escuchar el tráfico que entra a la máquina virtual desde Internet.
- La máquina virtual usa el puerto privado para escuchar el tráfico entrante, normalmente destinado a una aplicación o servicio que se ejecuta en la máquina virtual.

Se proporcionan los valores predeterminados para el protocolo IP y los puertos TCP o UDP para los protocolos de red conocidos cuando crea punto de conexión con el Portal de Azure clásico. Para los extremos personalizados, deberá especificar el protocolo IP correcto (TCP o UDP) y los puertos públicos y privados. Para distribuir el tráfico entrante de forma aleatoria entre varias máquinas virtuales, deberá crear un conjunto con equilibrio de carga que conste de varios extremos.

Tras la creación de un extremo, puede utilizar una lista de control de acceso (ACL) para definir reglas que permitan o denieguen el tráfico entrante al puerto público del extremo, en función de su dirección IP de origen. Sin embargo, si la máquina virtual está en una red virtual de Azure, debería usar grupos de seguridad de red en su lugar. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](virtual-networks-nsg.md).

> [AZURE.NOTE]La configuración del firewall de las máquinas virtuales de Azure se realiza automáticamente para los puertos asociados a Escritorio remoto y shell seguro (SSH), y en la mayoría de los casos para la comunicación remota de Windows PowerShell. Para los puertos especificados para todos los demás extremos, no se realiza ninguna configuración automáticamente en el firewall de la máquina virtual. Cuando se crea un extremo para la máquina virtual, deberá asegurarse de que el firewall de la máquina virtual también permite el tráfico para el protocolo y el puerto privado correspondiente a la configuración del extremo.

## Creación de un extremo

1.	Si no lo ha hecho todavía, inicie sesión en el Portal de Azure clásico.
2.	Haga clic en **Máquinas virtuales** y haga clic en el nombre de la máquina virtual que desea configurar.
3.	Haga clic en **Extremos**. En la página **Puntos de conexión** se enumeran todos los puntos de conexión actuales de la máquina virtual.

	![Extremos](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	En la barra de tareas, haga clic en **Agregar**.
5.	En la página **Agregar un extremo a una máquina virtual**, elija el tipo de extremo.

	- Si está creando un nuevo punto de conexión que no forma parte de un conjunto con equilibrio de carga o es el primer punto de conexión de un nuevo conjunto con equilibrio de carga, elija **Agregar un punto de conexión independiente** y haga clic en la flecha izquierda.
	- De lo contrario, elija **Agregar un punto de conexión a un conjunto de equilibrio de carga existente**, seleccione el nombre del conjunto con equilibrio de carga y haga clic en la flecha izquierda. En la página **Especifique los detalles del punto de conexión**, escriba un nombre para el punto de conexión y haga clic en la marca de verificación para crearlo.

6.	En la página **Especificar los detalles del extremo**, escriba un nombre para el extremo en **Nombre**. También puede elegir un nombre de protocolo de red de la lista, que rellenará los valores iniciales de **Protocolo**, **Puerto público** y **Puerto privado**.
7.	Para un extremo personalizado, en **Protocolo**, elija **TCP** o **UDP**.
8.	Para puertos personalizados, en **Puerto público**, escriba el número de puerto para el tráfico entrante desde Internet. En **Puerto privado**, escriba el número de puerto en el que escucha la máquina virtual. Estos números de puerto pueden ser diferentes. Asegúrese de que se ha configurado el firewall en la máquina virtual para permitir el tráfico correspondiente al protocolo (en el paso 7) y el puerto privado.
9.	Si este extremo será el primero en un conjunto con equilibrio de carga, haga clic en **Crear un conjunto con equilibrio de carga** y, a continuación, haga clic en la flecha derecha. En la página **Configurar el conjunto con equilibrio de carga**, especifique un nombre de conjunto de carga equilibrada, un puerto y protocolo de sondeo, y el intervalo de sondeo y el número de sondeos enviados. El equilibrador de carga de Azure envía sondeos a las máquinas virtuales en un conjunto con equilibrio de carga para supervisar su disponibilidad. El equilibrador de carga de Azure no reenvía el tráfico a las máquinas virtuales que no responden al sondeo. Haga clic en la flecha derecha.
10.	Haga clic en la marca de verificación para crear el extremo.

El nuevo punto de conexión se mostrará en la página **Puntos de conexión**.

![Creación correcta del extremo](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

Para usar un cmdlet de Azure PowerShell para configurar esta opción, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx). Si usa la CLI de Azure en modo de Administración de servicio, use el comando **azure vm endpoint create**.

## Administración de la ACL en un extremo

Para definir el conjunto de equipos que pueden enviar tráfico, la ACL en un extremo puede restringir el tráfico en función de la dirección IP de origen. Siga estos pasos para agregar, modificar o quitar una ACL en un extremo.

> [AZURE.NOTE] Si el extremo forma parte de un conjunto con equilibrio de carga, los cambios que realice en la ACL en un extremo se aplican a todos los extremos del conjunto.

Si la máquina virtual está en una red virtual de Azure, es recomendable usar grupos de seguridad de red en lugar de ACL. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](virtual-networks-nsg.md).

1.	Si no lo ha hecho todavía, inicie sesión en el Portal de Azure clásico.
2.	Haga clic en **Máquinas virtuales** y haga clic en el nombre de la máquina virtual que desea configurar.
3.	Haga clic en **Extremos**. Seleccione el extremo apropiado de la lista.

    ![Lista de ACL](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

5.	En la barra de tareas, haga clic en **Administrar dirección ACL** para abrir el cuadro de diálogo **Especificar los detalles de ACL**.

    ![Especificar los detalles de ACL](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6.	Use las filas de la lista para agregar, eliminar o editar las reglas de una ACL y cambiar su orden. El valor de la **Subred remota** es un intervalo de direcciones IP para el tráfico entrante de Internet que el equilibrador de carga de Azure usa para permitir o denegar el tráfico en función de la dirección IP de origen. Asegúrese de especificar el intervalo de direcciones IP en formato CIDR, también conocido como formato de prefijo de dirección. Un ejemplo es 131.107.0.0/16.

Puede usar reglas para permitir solo el tráfico desde equipos específicos correspondientes a los equipos en Internet o para denegar el tráfico desde intervalos concretos de direcciones conocidas.

Las reglas se evalúan en orden, comenzando por la primera regla y terminando por la última. Esto significa que las reglas deben estar ordenadas de menos restrictivas a más restrictivas. Para obtener ejemplos y más información, consulte [¿Qué es una lista de control de acceso de red?](../virtual-network/virtual-networks-acl/)

Para usar un cmdlet de Azure PowerShell para configurar esto, vea [Administración de las listas de control de acceso (ACL) de los puntos de conexión mediante PowerShell](../virtual-network/virtual-networks-acl-powershell.md).


## Recursos adicionales

[Introducción a la creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell](load-balancer-get-started-internet-arm-ps.md)

<!---HONumber=AcomDC_0204_2016-->