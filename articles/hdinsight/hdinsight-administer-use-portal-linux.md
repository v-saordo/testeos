<properties
	pageTitle="Administración de clústeres de Hadoop basados en Linux en HDInsight usando el portal de Azure | Microsoft Azure"
	description="Aprenda a crear y administrar clústeres de HDInsight basados en Linux mediante el portal de Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="larryfr"/>

# Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]


Con el [Portal de Azure][preview-portal] puede aprovisionar y administrar clústeres de Hadoop basados en Linux en HDInsight de Azure.

> [AZURE.NOTE] Los pasos descritos en este documento son específicos para trabajar con clústeres de Hadoop basados en Linux. Para obtener información sobre cómo trabajar con clústeres basados en Windows, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md).


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Otras herramientas de administración de HDInsight
También hay otras herramientas disponibles para administrar HDInsight además del Portal de Azure.

- [Administrar HDInsight mediante la CLI de Azure](hdinsight-administer-use-command-line.md): la CLI de Azure es una herramienta de línea de comandos multiplataforma que le permite administrar los servicios de Azure

- [Administrar HDInsight mediante Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell proporciona cmdlets de PowerShell para administrar los servicios de Azure

## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consultar [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Aprovisionamiento de clústeres de HDInsight

Puede aprovisionar clústeres de HDInsight desde el portal de Azure si sigue estos pasos:

1. Inicie sesión en el [Portal de Azure][preview-portal].

2. Seleccione **NUEVO**, __Análisis de datos__ y __HDInsight__

	![Creación de un clúster nuevo en el Portal de Azure](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Escriba un __Nombre de clúster__ y seleccione el __Tipo de clúster__ que desea crear. Si está disponible, aparecerá una marca de verificación verde junto al __Nombre de clúster__.

	![Nombre del clúster, tipo de clúster y tipo de sistema operativo](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. Si tienes más de una suscripción, selecciona la entrada __Suscripción__ entrada para seleccionar la suscripción de Azure que se usará para el clúster.

5. Para el __Grupo de recursos__, puedes seleccionar la entrada para ver una lista de grupos de recursos existentes y después seleccionar en el que quieras crear el clúster. También puede seleccionar __Crear nuevo__ y escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE] Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Selecciona __Credenciales__ y después escribe una __Contraseña de inicio de sesión de clúster__ y un __Nombre de usuario de inicio de sesión de clúster__. También tienes que especificar un __Nombre de usuario de SSH__ y una __CONTRASEÑA__ o una __CLAVE PÚBLICA__, que se usará para autenticar al usuario de SSH. Por último, usa el botón __Seleccionar__ para establecer las credenciales. Escritorio remoto no se usará en este documento, por lo que puede dejarlo deshabilitado.

	![Hoja Credenciales de clúster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE] SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña o la clave pública que se use aquí se usará al conectarse al clúster a través de SSH.

    Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

7. Como __Origen de datos__, puede seleccionar la entrada para elegir un origen de datos existente o crear uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-administer-use-portal-linux/datasource.png)

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Usa lo siguiente para comprender las entradas de la hoja de __Origen de datos__.

	- __Método de selección__: establézcalo en __De todas las suscripciones__ para habilitar la exploración de cuentas de almacenamiento en sus suscripciones. Establézcalo como __Tecla de acceso__ si desea especificar el __Nombre de almacenamiento__ y la __Tecla de acceso__ de una cuenta de almacenamiento existente.

	- __Crear nuevo__: use esta opción para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- __Elegir contenedor predeterminado__: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- __Ubicación__: la región geográfica en la que se encontrará o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT] Al seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.

	- __Seleccionar__: use esta opción para guardar la configuración del origen de datos.

	
8. Seleccione __Planes de tarifa de nodo__ para mostrar información sobre los nodos que se crearán para este clúster. De forma predeterminada, el número de nodos de trabajo se establecerá en __4__.

	El costo estimado del clúster se mostrará en la parte inferior de esta hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	Use el botón __Seleccionar__ para guardar la información de los __Niveles de precios de nodo__.
    
    > [AZURE.NOTE] Los nodos de clúster utilizados por el clúster no se consideran máquinas virtuales, ya que las imágenes de máquinas virtuales que se usan para los nodos son un detalle de implementación del servicio de HDInsight. Sin embargo, los núcleos de proceso utilizados por los nodos sí cuentan para el número total de núcleos de proceso disponibles para su suscripción. Puede ver el número de núcleos que se utilizarán en el clúster, así como el número de núcleos disponibles, en la sección de resumen de la hoja Plan de tarifa de nodos al crear un clúster de HDInsight.

9. Seleccione __Configuración opcional__. Esta hoja le permite configurar los siguientes elementos:

	* __Versión de HDInsight__: la versión de HDInsight que se usa para el clúster. Para obtener más información sobre las versiones de HDInsight, consulte [Versiones de los componentes de HDInsight](hdinsight-component-versioning.md)

	* __Tiendas de metadatos externas__: permite seleccionar una Base de datos SQL, que se usará para almacenar la información de configuración de Oozie y Hive. Esto te permite volver a usar la configuración cuando se elimina y se vuelve a crear un clúster, en lugar de tener que volver a crear la configuración de Hive y Oozie cada vez.

	* __Red virtual__: esto te permite colocar el clúster de HDInsight en la misma red virtual que otros recursos, como la base de datos SQL o una máquina virtual de Azure. Colocar los recursos en una red virtual les permite comunicarse directamente entre sí, omitiendo las puertas de enlace públicas que controlan el tráfico entrante desde Internet.
    
        Para obtener información sobre el uso de HDInsight con una red virtual, como los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

	* __Acciones de script__: permite especificar scripts de Bash que personalizan el clúster de HDInsight durante el aprovisionamiento. Por ejemplo, hay un [script que instala Hue](hdinsight-hadoop-hue-linux.md) (un cliente gráfico para trabajar con Hadoop). Para más información sobre las acciones de script, vea [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

	* __Claves de almacenamiento de Azure__: permite asociar cuentas de almacenamiento adicionales al servidor de HDInsight.

		> [AZURE.NOTE] HDInsight solo puede tener acceso a las cuentas de almacenamiento de Azure que se usen como almacén de datos predeterminado, se hayan agregado a través de esta sección de configuración o sean accesibles públicamente.

	![Hoja Configuración opcional](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. Asegúrese de que __Anclar a Panel de inicio__ está seleccionado y, después, seleccione __Crear__. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Usa el icono del Panel de inicio o la entrada __Notificaciones__ de la izquierda de la página para comprobar el proceso de aprovisionamiento.

## Administración de un clúster

Si selecciona un clúster desde el Portal de Azure, aparecerá información esencial sobre el clúster, como el nombre, el grupo de recursos, el sistema operativo y la dirección URL del panel de clúster (que se usa para tener acceso a la web de Ambari para clústeres de Linux).

![Detalles del clúster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Use la siguiente información para comprender los iconos de la parte superior de esta hoja y de las secciones __Conceptos básicos__ y __Vínculos rápidos__:

* __Configuración__ y __Toda la configuración__: muestra la hoja __Configuración__ del clúster, que permite obtener acceso a información de configuración detallada para el clúster.

* __Panel__, __Panel de clúster__ y __Dirección URL__: todas son formas de acceder al panel del clúster, que es la web de Ambari para los clústeres basados en Linux.

* __Shell seguro__: información necesaria para acceder al clúster mediante SSH.

* __Escalar clúster__: permite cambiar el número de nodos de trabajo para este clúster.

* __Eliminar__: elimina el clúster de HDInsight.

* __Inicio rápido (![icono de nube y rayo = inicio rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: muestra información que le ayudará a empezar a usar HDInsight.

* __Usuarios (![icono de usuarios](./media/hdinsight-administer-use-portal-linux/users.png))__: permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.

	> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

* __Etiquetas (![icono de etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))__: las etiquetas permiten establecer pares de clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

* __Documentación__: vínculos a la documentación de HDInsight de Azure.

> [AZURE.IMPORTANT] Para administrar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para más información sobre el uso de Ambari, vea [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="scaling"></a>Escalado

Para escalar un clúster mediante el portal, seleccione el clúster de HDInsight y elija __Escalar clúster__. Escriba el __número de nodos de trabajo__ que quieres establecer para el clúster y después haz clic en __Guardar__.

![imagen de la interfaz de usuario de escalado](./media/hdinsight-administer-use-portal-linux/scaling.png)

Para más información sobre cómo ampliar las operaciones, vea [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md#scaling).

## Supervisión de un clúster

La sección __Uso__ de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster.

![Información de uso](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

## Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de Azure y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0128_2016-->