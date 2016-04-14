<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight mediante el portal de Azure clásico | Microsoft Azure"
	description="Vea cómo administrar el servicio HDInsight. Cree un clúster de HDInsight, abra la consola interactiva de JavaScript y la consola de comandos de Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure clásico

Con el [Portal de Azure clásico](https://manage.windowsazure.com), puede aprovisionar clústeres de Hadoop en HDInsight de Azure, cambiar la contraseña de usuario de Hadoop y habilitar el Protocolo de escritorio remoto (RDP) para que pueda tener acceso a la consola de comandos de Hadoop en el clúster.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md)

## Otras herramientas de administración de HDInsight
También hay otras herramientas disponibles para administrar HDInsight además del Portal de Azure clásico.

- Para obtener más información sobre la administración de HDInsight con PowerShell de Azure, consulte [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md).

- Para obtener más información sobre la administración de HDInsight con la CLI de Azure, consulte [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md).

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Cuenta de Almacenamiento de Azure**: un clúster de HDInsight usa contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información acerca de cómo el almacenamiento de blobs de Azure ofrece una experiencia perfecta con los clústeres de HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de Almacenamiento de Azure, consulte [Creación de una cuenta de Almacenamiento](../storage/storage-create-storage-account.md).


##Aprovisionamiento de clústeres de HDInsight

Puede aprovisionar clústeres de HDInsight desde el Portal de Azure clásico mediante la opción Creación rápida o Creación personalizada. Vea los vínculos siguientes para obtener instrucciones:

- [Aprovisionamiento de un clúster mediante Creación rápida](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Aprovisionamiento de un clúster mediante Creación personalizada](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##Personalización de clústeres de HDInsight

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md). Puede personalizar HDInsight mediante una de las opciones siguientes:

- Use Generar script de acción para ejecutar scripts que pueden personalizar un clúster para cambiar la configuración del clúster o para instalar los componentes personalizados, como Giraph o Solr. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante Generar acción de script](hdinsight-hadoop-customize-cluster.md).
- Use los parámetros de personalización de clústeres en .NET SDK de HDInsight o PowerShell de Azure durante el aprovisionamiento de clústeres. Estos cambios de configuración se conservan durante toda la vida del clúster y no se ven afectados por el restablecimiento de imágenes de nodos del clúster que la plataforma Azure realiza periódicamente para su mantenimiento. Para obtener más información acerca del uso de los parámetros de personalización del clúster, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).
- Algunos de los componentes nativos de Java, como Mahout y Cascading, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR se pueden distribuir al almacenamiento de blobs de Azure y enviarse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE] Si tiene problemas con la implementación de los archivos JAR en los clústeres de HDInsight o con la llamada de los archivos JAR en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

	> La presentación en cascada no se admite en HDInsight, y no puede optar a recibir soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](hdinsight-component-versioning.md).


La instalación de software personalizado en el clúster mediante la Conexión a Escritorio remoto no es compatible. Debe evitar el almacenamiento de cualquier archivo en las unidades del nodo principal, debido a que se perderán si necesita volver a crear los clústeres. Recomendamos almacenar los archivos en el almacenamiento de blobs de Azure. El almacenamiento de blobs es persistente.

##Cambio de nombre de usuario y contraseña del clúster de HDInsight
Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight se crea durante el proceso de aprovisionamiento. También puede crear una cuenta de usuario de RDP para tener acceso al clúster mediante RDP. Consulte [Habilitación de escritorio remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Para cambiar el nombre de usuario y contraseña del clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight para el que desea restablecer el nombre de usuario y la contraseña.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **DESACTIVADO** junto a **SERVICIOS DE HADOOP**.
6. Haga clic en **GUARDAR** en la parte inferior de la página y espere a que se complete la deshabilitación.
7. Después de que el servicio se haya deshabilitado, haga clic en **ACTIVADO** junto a **SERVICIOS DE HADOOP**.
8. En **NOMBRE DE USUARIO** y **NUEVA CONTRASEÑA**, escriba el nuevo nombre de usuario y la contraseña (respectivamente) para el clúster.
8. Haga clic en **GUARDAR**.


##Conexión a los clústeres de HDInsight con RDP

Las credenciales para el clúster que proporcionó en su creación dan acceso a los servicios en el clúster, pero no al clúster mismo a través del Escritorio remoto. El acceso a Escritorio remoto está desactivado de manera predeterminada, por lo que el acceso directo al clúster que lo usa requiere una configuración adicional después de la creación.

**Para habilitar el Escritorio remoto**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Desde la parte inferior de la página, haga clic en **HABILITAR DE FORMA REMOTA**.
6. En el asistente para **Configurar Escritorio remoto**, escriba un nombre de usuario y una contraseña para el Escritorio remoto. Tenga en cuenta que el nombre de usuario debe ser diferente al usado para crear el clúster (**admin** de forma predeterminada con la opción Creación rápida). Escriba una fecha de caducidad en la casilla **CADUCA EL**. Tenga en cuenta que la fecha de caducidad debe estar en el futuro, 90 días como máximo a partir de ahora. La hora del día de la caducidad se supone de manera predeterminada en la medianoche de la fecha especificada. A continuación, haga clic en el icono de comprobación.

	![HDI.UsuarioCrearRDP][image-hdi-create-rpd-user]


> [AZURE.NOTE] También puede usar .NET SDK de HDInsight para habilitar el Escritorio remoto en un clúster. Use el método **EnableRdp** en el objeto de cliente de HDInsight de la siguiente manera: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. De forma similar, para deshabilitar el Escritorio remoto en el clúster, puede usar **client.DisableRdp(clustername, location)**. Para obtener más información sobre estos métodos, consulte [Referencia de .NET SDK de HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Esto solo se aplica a los clústeres de HDInsight que se ejecutan en Windows.



> [AZURE.NOTE] Una vez RDP está habilitado para un clúster, debe actualizar la página antes de poder conectarse al clúster.

**Para conectarse a un clúster con RDP**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **CONECTAR** y siga las instrucciones.

##Creación de un certificado autofirmado

Si desea realizar operaciones en el clúster usando .NET SDK, debe crear un certificado autofirmado en la estación de trabajo y cargarlo en el certificado de la suscripción de Azure. Esta tarea solo se realiza una vez. Puede instalar el mismo certificado en otras máquinas, siempre y cuando el certificado sea válido.

**Para crear un certificado autofirmado**

1. Cree el certificado autofirmado que se usa para autentica las solicitudes. Puede usar Internet Information Services (IIS) o [makecert](http://go.microsoft.com/fwlink/?LinkId=534000) para crear el certificado.

2. Vaya a la ubicación del certificado, haga clic con el botón secundario en él, después haga clic en **Instalar certificado** y, por último, instale el certificado en el almacén personal del equipo. Edite las propiedades del certificado para asignarle un nombre descriptivo.

3. Importe el certificado en el Portal de Azure clásico. Desde el portal, haga clic en **CONFIGURACIÓN** en la parte inferior izquierda de la página y después haga clic en **CERTIFICADOS DE ADMINISTRACIÓN**. En la parte inferior de la página, haga clic en **CARGAR** y siga las instrucciones para cargar el archivo .cer que creó en el paso anterior.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##Concesión/Revocación del acceso a los servicios de HTTP

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar o conceder el acceso desde el Portal de Azure clásico.

>[AZURE.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del clúster.

**Para conceder/revocar el acceso a los servicios web de HTTP**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight que desea configurar.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **ACTIVADO** o en **DESACTIVADO** junto a **SERVICIOS DE HADOOP**.
6. En **NOMBRE DE USUARIO** y **NUEVA CONTRASEÑA**, escriba el nuevo nombre de usuario y la contraseña (respectivamente) para el clúster.
7. Haga clic en **GUARDAR**.

Consulte [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md).

##Apertura de una línea de comandos de Hadoop

Para conectarse al clúster mediante el Escritorio remoto y usar la línea de comandos de Hadoop, primero debe tener habilitado el acceso del Escritorio remoto al clúster como se describe en la sección anterior.

**Para abrir una línea de comandos de Hadoop**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de Hadoop implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
3. Haga clic en **CONFIGURACIÓN** en la parte superior de la página.
4. Haga clic en **CONECTAR** en la parte inferior de la página.
5. Haga clic en **Abrir**.
6. Escriba las credenciales y, a continuación, haga clic en **Aceptar**. Use el nombre de usuario y la contraseña que configuró al crear el clúster.
7. Haga clic en **Sí**.
8. En el escritorio, haga doble clic en **Línea de comandos de Hadoop**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obtener más información acerca de los comandos de Hadoop, consulte [Referencia de comandos de Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión de Hadoop incrustado. El número de versión se puede cambiar según la versión de los componentes de Hadoop instalados en el clúster. Puede usar las variables de entorno de Hadoop para referirse a esas carpetas. Por ejemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%

##Escalado de clústeres
Consulte [Escalado de clústeres de Hadoop en HDInsight](hdinsight-administer-use-management-portal.md#scale-clusters).

##Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de Azure clásico y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal-v1/hdi.createrdpuser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal-v1/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal-v1/hdi.clustercreate.uploadcert.png

<!---HONumber=AcomDC_0218_2016-->