<properties
   pageTitle="Creación de clústeres de Hadoop en HDInsight | Microsoft Azure"
   	description="Aprenda a crear clústeres para HDInsight de Azure con el Portal de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Windows en HDInsight con el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear un clúster de Hadoop en HDInsight con el Portal de Azure. El [Portal de Microsoft Azure](../azure-portal-overview.md) es una ubicación central desde donde se pueden aprovisionar y administrar los recursos de Azure. El Portal de Azure es una de las herramientas que puede utilizar para crear el clúster de Hadoop basado en Linux o en Windows en HDInsight. Para consultar otras herramientas y características de creación de clústeres, haga clic en la selección de pestaña de la parte superior de esta página o consulte los [métodos de creación de clústeres](hdinsight-provision-clusters.md#cluster-creation-methods).

###Requisitos previos:

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Creación de clústeres


**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

  ![Creación de un clúster nuevo en el Portal de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creación de un clúster nuevo en el Portal de Azure")

3. Escriba o seleccione los valores siguientes:

  * **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
  * **Tipo de clúster**: seleccione **Hadoop**. Otra opciones incluyen **HBase**, **Storm** y **Spark**.
  * **Sistema operativo de clústeres**: seleccione **Windows**. Para crear un clúster basado en Linux, seleccione **Linux**.
  * **Versión**: consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
  * **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
  * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo. Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
  * **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.

![Proporcione las credenciales del clúster](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

 * **Origen de datos**: cree una nueva cuenta de Almacenamiento de Azure o seleccione una ya existente para usar como sistema de archivos predeterminado para el clúster.

   ![Hoja Origen de datos](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

  * **Método de selección**: establezca esta opción en **De todas las suscripciones** para permitir la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.
  * **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
  * **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
  * **Ubicación**: región geográfica en la que se encuentra la cuenta de almacenamiento o donde se creará. Esta ubicación determinará la ubicación del clúster. El clúster y su cuenta de almacenamiento predeterminada debe colocarse en el mismo centro de datos Azure.
  	
  * **Niveles de precio de nodo**: establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.
  

![Hoja Niveles de precios de nodo](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")


 * **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unir una **Red virtual**, configurar una **Tienda de metadatos externa** para almacenar datos de Hive y Oozie, use las acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

 * **Versión de HDInsight**: seleccione la versión que desee usar para el clúster. Para obtener más información, consulte [Versiones del clúster de HDInsight](hdinsight-component-versioning.md).
 * **Red virtual**: seleccione una red virtual de Azure y la subred si quiere colocar el clúster en una red virtual.  

![Hoja de red virtual](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Especifique detalles de red virtual")

Para obtener información sobre el uso de HDInsight con una red virtual, como los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).
  

  		
* **Tiendas de metadatos externas**: especifique una Base de datos SQL de Azure para guardar los metadatos de Hive y Oozie asociados con el clúster.
 
  > [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

![Hoja de tiendas de metadatos personalizados](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Especificar tiendas de metadatos externas")


En **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.


>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.
		
 * **Acciones de script** si quiere usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre acciones de script, vea [Personalización de clústeres de HDInsight con una acción de script](hdinsight-hadoop-customize-cluster.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.
  	

![Hoja de acción de script](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Especifique una acción de script")


 * **Claves de Almacenamiento de Azure**: especifique cuentas de almacenamiento adicionales para asociar con el clúster. En la hoja **Claves de Almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y, luego, seleccione una cuenta de almacenamiento existente o cree una nueva.
    

![Hoja de almacenamiento adicional](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Especificar cuentas de almacenamiento adicionales")


4. Haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio**, se agregará un icono del clúster en el Panel de inicio de su Portal. El icono indicará que el clúster se está creando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.
	
  El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
	

5. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.


![Hoja de clúster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propiedades de clúster")


Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos**:


* **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
* **Panel**, **Panel de clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.
* **Escritorio remoto**: permite habilitar o deshabilitar Escritorio remoto en los nodos de clúster.
* **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.
* **Eliminar**: elimina el clúster de HDInsight.
* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-provision-clusters/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
* **Usuarios** (![icono de usuarios](./media/hdinsight-provision-clusters/users.png)): permite establecer permisos de _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.
	

> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
		
* **Etiquetas** (![icono de etiqueta](./media/hdinsight-provision-clusters/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

##Personalización de los clústeres

- Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).

##Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0224_2016-->