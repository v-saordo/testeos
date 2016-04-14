<properties 
	pageTitle="Personalización de los clústeres de Hadoop para el proceso de análisis de Cortana | Microsoft Azure" 
	description="Están disponibles módulos de Python populares en los clústeres de Hadoop de HDInsight de Azure personalizados."
	services="machine-learning" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="hangzh;bradsev" />

# Personalización de los clústeres de Hadoop en HDInsight de Azure para el proceso de análisis de Cortana 

## Introducción

En este artículo se describe cómo personalizar un clúster de Hadoop para HDInsight mediante la instalación de Anaconda de 64 bits (Python 2.7) en cada nodo cuando el clúster se está aprovisionando en el servicio HDInsight. Esta personalización prepara el clúster para usarlo con el proceso de análisis de Cortana. También muestra cómo obtener acceso al nodo principal para enviar trabajos personalizados al clúster.

Esta personalización provoca que muchos módulos populares de Python incluidos en Anaconda estén disponibles convenientemente para su uso en funciones definidas por el usuario (UDF) que están diseñadas para procesar los registros de subárbol en el clúster. Para obtener instrucciones sobre los procedimientos usados en este escenario, consulte [Envío de consultas de Hive a clústeres de Hadoop de HDInsight en el proceso de análisis avanzado](machine-learning-data-science-hive-queries.md).

El menú a continuación vincula a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos que usa el proceso de Cortana Analytics (CAPS).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Personalizar el clúster de Hadoop de HDInsight de Azure

Para crear un clúster de Hadoop de HDInsight personalizado, los usuarios deben iniciar sesión en [**Portal de Azure clásico**](https://manage.windowsazure.com/), hacer clic en **Nuevo** en la esquina inferior izquierda y, a continuación, seleccionar SERVICIOS DE DATOS -> HDINSIGHT -> **CREACIÓN PERSONALIZADA** para que aparezca la ventana **Detalles del clúster**.

![Creación del espacio de trabajo][1]

Especifique el nombre del clúster a crear en la página de configuración 1 y acepte los valores predeterminados para los demás campos. Haga clic en la flecha para ir a la página de configuración siguiente.

![Creación del espacio de trabajo][2]

En la página de configuración 2, escriba el número de **NODOS DE DATOS**, seleccione la **REGIÓN/RED VIRTUAL** y seleccione los tamaños del **NODO PRINCIPAL** y del **NODO DE DATOS**. Haga clic en la flecha para ir a la página de configuración siguiente.

>[AZURE.NOTE] La **REGIÓN/RED VIRTUAL** tiene que ser la misma que la región de la cuenta de almacenamiento que se va a usar para el clúster de Hadoop de HDInsight. De lo contrario, en la cuarta página de configuración, la cuenta de almacenamiento que los usuarios desean utilizar no aparecerá en la lista desplegable de **NOMBRE DE LA CUENTA**.

![Creación del espacio de trabajo][3]

En la página de configuración 3, proporcione un nombre de usuario y una contraseña para el clúster de Hadoop de HDInsight. **No** seleccione _Especificar la tienda de metadatos de Hive/Oozie_. A continuación, haga clic en la flecha para ir a la página de configuración siguiente.

![Creación del espacio de trabajo][4]

En la página de configuración 4, especifique el nombre de la cuenta de almacenamiento, el contenedor predeterminado del clúster de Hadoop de HDInsight. Si los usuarios seleccionan _Crear contenedor predeterminado_ en la lista desplegable **CONTENEDOR PREDETERMINADO**, se creará un contenedor con el mismo nombre que el del clúster. Haga clic en la flecha para ir a la última página de configuración.

![Creación del espacio de trabajo][5]

En la última página de configuración de **Acciones de script**, haga clic en el botón **Agregar acción de script** y rellene los campos de texto con los valores siguientes.
 
* **NOMBRE**: cualquier cadena como el nombre de esta acción de script. 
* **TIPO DE NODO**: seleccione **Todos los nodos**. 
* **URI DE SCRIPT**: *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* es un contenedor público situado en la cuenta de almacenamiento. 
	* *getgoing* se usa para compartir archivos de script de PowerShell para facilitar el trabajo de los usuarios en Azure. 
* **PARÁMETROS**: (dejar en blanco)

Por último, haga clic en la marca de verificación para iniciar la creación del clúster Hadoop de HDInsight personalizado.

![Creación del espacio de trabajo][6]

## <a name="headnode"></a> Acceso al nodo principal del clúster de Hadoop

Los usuarios deben habilitar el acceso remoto al clúster de Hadoop en Azure para poder acceder al nodo principal del clúster de Hadoop a través de RDP.

1. Inicie sesión en el [**Portal de Azure clásico**](https://manage.windowsazure.com/), seleccione **HDInsight** a la izquierda, seleccione el clúster de Hadoop en la lista de clústeres, haga clic en la pestaña **CONFIGURACIÓN** y a continuación, haga clic en el icono **HABILITAR REMOTO** de la parte inferior de la página.
	
	![Creación del espacio de trabajo][7]

2. En la ventana **Configurar escritorio remoto**, escriba los campos NOMBRE DE USUARIO y CONTRASEÑA y seleccione la fecha de expiración del acceso remoto. A continuación, haga clic en la marca de verificación para habilitar el acceso remoto en el nodo principal del clúster de Hadoop.
	
	>[AZURE.NOTE] 
	>
	>1. El nombre de usuario y la contraseña para el acceso remoto no son el nombre de usuario y la contraseña que se utilizaron al crear el clúster de Hadoop. Se trata de un conjunto de credenciales diferente
	>
	>2. La fecha de expiración del acceso remoto debe estar dentro de 7 días respecto a la fecha actual.

	![Creación del espacio de trabajo][8]

3. Después de habilitar el acceso remoto, haga clic en **CONECTAR** en la parte inferior de la página para conectarse de manera remota al nodo principal. Inicie sesión en el nodo principal del clúster de Hadoop mediante la especificación de las credenciales del usuario de acceso remoto que especificó anteriormente.

	 ![Creación del espacio de trabajo][9]

Los pasos siguientes del proceso de análisis avanzado se asignan en la [Guía de aprendizaje: procesamiento avanzado de datos en Azure](machine-learning-data-science-advanced-data-processing.md) y pueden incluir pasos que muevan datos a HDInsight, los procese y muestree allí como preparación para el aprendizaje a partir de los datos con Aprendizaje automático de Azure.

Consulte [Envío de consultas de Hive a clústeres de Hadoop de HDInsight en el proceso de análisis avanzado](machine-learning-data-science-process-hive-tables.md) para obtener instrucciones sobre cómo tener acceso a los módulos de Python que se incluyen en Anaconda desde el nodo principal del clúster en las funciones definidas por el usuario (UDF) que se usan para procesar registros de Hive almacenados en el clúster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 

<!----HONumber=AcomDC_0211_2016-->