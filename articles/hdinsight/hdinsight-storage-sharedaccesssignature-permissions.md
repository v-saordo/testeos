<properties
pageTitle="Restringir el acceso de HDInsight a datos mediante firmas de acceso compartido"
description="Obtener información acerca de cómo usar firmas de acceso compartido para restringir el acceso de HDInsight a datos almacenados en blobs de Almacenamiento de Azure."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/01/2016"
ms.author="larryfr"/>

#Uso de firmas de acceso compartido de Almacenamiento de Azure para restringir el acceso a datos con HDInsight

HDInsight usa blobs de Almacenamiento de Azure para el almacenamiento de datos. Aunque HDInsight debe tener acceso completo al blob que se utiliza como almacenamiento predeterminado para el clúster, puede restringir los permisos a los datos almacenados en otros blobs que usa el clúster. Por ejemplo, quizá desee que algunos datos sean de solo lectura. Puede hacerlo mediante firmas de acceso compartido.

Las firmas de acceso compartido (SAS) son una característica de las cuentas de Almacenamiento de Azure que permite limitar el acceso a los datos. Por ejemplo, al proporcionar acceso de solo lectura a los datos. En este documento, obtendrá información sobre cómo utilizar SAS para habilitar el acceso de solo lista y lectura a un contenedor de blobs desde HDInsight.

##Requisitos

* Una suscripción de Azure

* C# o Python. El código de ejemplo de C# se proporciona como una solución de Visual Studio.

    * Se debe utilizar la versión de Visual Studio 2013 o 2015.
    
    * Se debe usar la versión de Python 2.7 o superior.

* Un clúster de HDInsight basado en Linux o [Azure PowerShell][powershell]\: si ya tiene un clúster basado en Linux, puede usar Ambari para agregar una firma de acceso compartido al clúster. Si no es así, puede usar Azure PowerShell para crear un nuevo clúster y agregar una firma de acceso compartido durante la creación de este.

* Los archivos de ejemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositorio tiene lo siguiente:

    * Un proyecto de Visual Studio que puede crear un contenedor de almacenamiento, una directiva almacenada y una SAS para su uso con HDInsight.
    
    * Un script de Python que puede crear un contenedor de almacenamiento, una directiva almacenada y una SAS para su uso con HDInsight.
    
    * Un script de PowerShell que puede crear un nuevo clúster de HDInsight y configurarlo para que use una SAS.

##Las firmas de acceso compartido

Hay dos formas de firmas de acceso compartido:

* Ad-hoc: la hora de inicio, la hora de expiración y los permisos para la SAS se especifican todos en el URI de SAS (o se encuentran implícitos en el caso en el que se omita la hora de inicio).

* Directiva de acceso almacenada: se define una directiva de acceso almacenada en un contenedor de recursos (un contenedor de blobs, una tabla, una cola o un recurso compartido de archivos) y se puede usar para administrar las restricciones de una o varias firmas de acceso compartido. Cuando asocia una SAS a una directiva de acceso almacenada, la SAS hereda las restricciones (hora de inicio, hora de expiración y permisos) definidas para la directiva de acceso almacenada.

La diferencia entre las dos formas es importante para un escenario principal: revocación. Una SAS es una dirección URL, por lo que cualquier persona que obtenga la SAS puede usarla, independientemente de quién la solicitó para comenzar. Si una SAS se encuentra disponible públicamente, cualquier persona del mundo puede usarla. Una SAS distribuida es válida hasta que se produzca una de las cuatro situaciones:

1. Se alcanza el tiempo de expiración especificado en la SAS.

2. Se alcanza el tiempo de expiración especificado en la directiva de acceso almacenada al que hace referencia la SAS (si se hace referencia a una directiva de acceso almacenada y si especifica una hora de expiración). Esto puede producirse porque transcurra el intervalo o porque haya modificado la directiva de acceso almacenado para tener una hora de expiración pasada, que es una forma de revocar la SAS.

3. Se elimina la directiva de acceso almacenada a la que hace referencia la SAS, que es otra forma de revocar la SAS. Tenga en cuenta que si vuelve a crear la directiva de acceso almacenada exactamente con el mismo nombre, todos los tokens de la SAS volverán a ser válidos según los permisos asociados a la directiva de acceso almacenada (si se presupone que la hora de expiración en la SAS no ha pasado). Si prevé revocar la SAS, asegúrese de usar un nombre distinto si vuelve a crear la directiva de acceso con una hora de expiración futura.

4. Se vuelve a generar la clave de cuenta que se usó para crear la SAS. Tenga en cuenta que la realización de este procedimiento provocará que todos los componentes de la aplicación que usen esa clave de cuenta no puedan autenticarse hasta que se actualicen para usar la otra clave de cuenta válida o la clave de cuenta que se acaba de volver a generar.

> [AZURE.IMPORTANT] Los URI de firma de acceso compartido están asociados a la clave de la cuenta que se utiliza para crear la firma y a la directiva de acceso almacenada correspondiente (en su caso). Si no se especifica una directiva de acceso almacenada, la única forma de revocar una firma de acceso compartido es cambiar la clave de la cuenta.

Se recomienda usar siempre las directivas de acceso almacenadas, para que pueda revocar las firmas o ampliar la fecha de caducidad según sea necesario. Los pasos descritos en este documento utilizan directivas de acceso almacenadas para generar las SAS.

Para más información sobre firmas de acceso compartido, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md)

##Creación de una directiva almacenada y generación de una SAS

Ahora, debe crear una directiva almacenada mediante programación. Puede encontrar el ejemplo de C# y Python de creación de una directiva almacenada y SAS en [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###Creación de una directiva almacenada y una SAS mediante C#

1. Abra la solución en Visual Studio.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto __SASToken__ y seleccione __Propiedades__.

3. Seleccione __Configuración__ y agregue valores para las siguientes entradas:

    * StorageConnectionString: la cadena de conexión de la cuenta de almacenamiento para la que desea crear una directiva almacenada y una SAS. El formato debe ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` donde `myaccount` es el nombre de la cuenta de almacenamiento y `mykey` es la clave para la cuenta de almacenamiento.
    
    * ContainerName: el contenedor de la cuenta de almacenamiento a la que desea restringir el acceso.
    
    * SASPolicyName: el nombre que se usará para la directiva almacenada que se creará.
    
    * FileToUpload: la ruta de acceso a un archivo que se cargará en el contenedor.
    
4. Ejecute el proyecto. Aparecerá una ventana de consola y, una vez generada la SAS, se mostrará información similar a la siguiente:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Guarde el token de directiva de la SAS, ya que lo necesitará al asociar la cuenta de almacenamiento al clúster de HDInsight. También necesitará el nombre de la cuenta de almacenamiento y el nombre del contenedor.
    
###Creación de una directiva almacenada y una SAS mediante Python

1. Abra el archivo SASToken.py y cambie los valores siguientes:

    * policy\_name: el nombre que se usará para la directiva almacenada que se creará.
    
    * storage\_account\_name: el nombre de la cuenta de almacenamiento.
    
    * storage\_account\_key: la clave de la cuenta de almacenamiento.
    
    * storage\_container\_name: el contenedor de la cuenta de almacenamiento a la que desea restringir el acceso.
    
    * example\_file\_path: la ruta de acceso a un archivo que se cargará en el contenedor.

2. Ejecute el script. Cuando finalice el script, mostrará un token de SAS similar al siguiente:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Guarde el token de directiva de la SAS, ya que lo necesitará al asociar la cuenta de almacenamiento al clúster de HDInsight. También necesitará el nombre de la cuenta de almacenamiento y el nombre del contenedor.
    
##Uso de las SAS con HDInsight

Al crear un clúster de HDInsight, debe especificar una cuenta de almacenamiento principal y puede especificar, opcionalmente, cuentas de almacenamiento adicionales. Estos dos métodos para agregar almacenamiento requieren tener un acceso total a las cuentas de almacenamiento y los contenedores que se utilizan.

Para usar una firma de acceso compartido a fin de limitar el acceso a un contenedor, debe agregar una entrada personalizada a la configuración __core-site__ para el clúster.

* Para clústeres de HDInsight __basados en Windows__ o __basados en Linux__, puede hacerlo durante la creación del clúster mediante PowerShell.

* Para clústeres de HDInsight __basados en Linux__, cambie la configuración después de crear el clúster con Ambari.

###Creación de un nuevo clúster que usa las SAS

Se incluye un ejemplo de creación de un clúster de HDInsight que usa la SAS en el directorio `CreateCluster` del repositorio. Para ello, siga estos pasos:

1. Abra el archivo `CreateCluster\HDInsightSAS.ps1` en un editor de texto y modifique los valores siguientes al principio del documento.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Por ejemplo, cambie `'mycluster'` por el nombre del clúster que desea crear. Los valores de SAS deben coincidir con los valores de los pasos anteriores al crear una cuenta de almacenamiento y el token de SAS.
    
    Una vez que haya cambiado los valores, guarde el archivo.

1. Abra un nuevo símbolo del sistema de Azure PowerShell. Si no está familiarizado con Azure PowerShell, o si no lo ha instalado, consulte [Cómo instalar y configurar Azure PowerShell][powershell].

2. En el símbolo del sistema, use lo siguiente para autenticarse en su suscripción de Azure:

        Login-AzureRmAccount
    
    Cuando se le solicite, inicie sesión con la cuenta de su suscripción de Azure.
    
    Si el inicio de sesión está asociado a varias suscripciones de Azure, debe usar `Select-AzureRmSubscription` para seleccionar la suscripción que desea utilizar.

2. En el símbolo del sistema, cambie los directorios al directorio `CreateCluster` que contiene el archivo HDInsightSAS.ps1. A continuación, utilice lo siguiente para ejecutar el script.
        
        .\HDInsightSAS.ps1
    
    Mientras se ejecuta el script, registrará la salida en el símbolo del sistema de PowerShell al ir creando el grupo de recursos y las cuentas de almacenamiento. Le pedirá entonces que escriba el usuario HTTP para el clúster de HDInsight. Se trata de la cuenta de usuario utilizada para proteger el acceso HTTP/s al clúster.
    
    Si está creando un clúster basado en Linux, se le solicitará también un nombre de cuenta de usuario SSH y una contraseña. Esto se utiliza para el inicio de sesión remota al clúster.
    
    > [AZURE.IMPORTANT] Cuando se le pida el nombre de usuario SSH o HTTP/s y la contraseña, debe proporcionar una contraseña que cumpla los criterios siguientes:
    >
    > - Debe tener como mínimo 10 caracteres.
    > - Debe contener al menos un dígito.
    > - Debe incluir al menos un carácter no alfanumérico.
    > - Debe contener al menos una mayúscula o una minúscula.


Este script tardará un tiempo en completarse, normalmente unos 15 minutos. Una vez finalizado el script sin errores, se creará el clúster.

###Actualización de un clúster existente para usar las SAS

Si tiene un clúster existente basado en Linux, puede agregar las SAS para la configuración __core-site__ mediante los pasos siguientes:

1. Abra la interfaz de usuario web Ambari del clúster. La dirección de esta página es https://YOURCLUSTERNAME.azurehdinsight.net. Cuando se le pida, autentíquese en el clúster con el nombre de administrador (admin) y la contraseña que usó al crear el clúster.

2. En el lado izquierdo de la interfaz de usuario web Ambari, seleccione __HDFS__ y, a continuación, seleccione la pestaña __Configs__ (Configuraciones) en el centro de la página.

3. Seleccione la pestaña __Advanced__ (Avanzadas) y, a continuación, desplácese hasta encontrar la sección __Custom core-site__ (Sitio principal personalizado).

4. Expanda la sección __Custom core-site__ (Sitio principal personalizado) y, a continuación, desplácese hasta el final y seleccione el vínculo __Add property...__ (Agregar propiedad...). Utilice los siguientes valores para los campos __Key__ (Clave) y __Value__ (Valor):

    * __Key__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Value__: la SAS devuelta por la aplicación de C# o Python ejecutada anteriormente.
    
    Reemplace __CONTAINERNAME__ por el nombre del contenedor utilizado con la aplicación de C# o de SAS. Reemplace __STORAGEACCOUNTNAME__ por el nombre de la cuenta de almacenamiento utilizada.

5. Haga clic en el botón __Add__ (Agregar) para guardar esta clave y este valor y, a continuación, haga clic en el botón __Save__ (Guardar) para guardar los cambios de configuración. Cuando se le pida, agregue una descripción del cambio ("Agregar acceso de almacenamiento de SAS", por ejemplo) y haga clic en __Save__ (Guardar).

    Haga clic en __OK__ (Aceptar) cuando se hayan completado los cambios.

    > [AZURE.IMPORTANT] Esto guarda los cambios de configuración pero debe reiniciar varios servicios para que el cambio surta efecto.

6. En la interfaz de usuario web Ambari, seleccione __HDFS__ en la lista de la izquierda y, a continuación, seleccione __Restart All__ (Reiniciar todos) en la lista desplegable __Service Actions__ (Acciones del servicio) de la derecha. Cuando se le solicite, seleccione __Turn on maintenance mode__ (Activar modo de mantenimiento) y, a continuación, "Confirm Restart All" ("Confirmar reiniciar todo").

    Repita este proceso para las entradas MapReduce2 y YARN en la lista de la izquierda de la página.

7. Una vez reiniciadas, seleccione cada una de ellas y deshabilite el modo de mantenimiento en la lista desplegable __Service Actions__ (Acciones del servicio).

##Prueba de acceso restringido

Para comprobar que tiene el acceso restringido, utilice los métodos siguientes:

* Para clústeres de HDInsight __basados en Windows__, use el Escritorio remoto para conectarse al clúster. Consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) para más información.

    Una vez conectado, use el icono de __línea de comandos de Hadoop__ en el escritorio para iniciar un símbolo del sistema.

* Para clústeres de HDInsight __basados en Linux__, use SSH para conectarse al clúster. Consulte una de las siguientes secciones para más información sobre el uso de SSH con clústeres basados en Linux:

    * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Una vez conectado al clúster, siga estos pasos para comprobar que solo puede leer y listar elementos en la cuenta de almacenamiento de SAS:

1. En el símbolo del sistema, escriba lo siguiente. Reemplace __SASCONTAINER__ por el nombre del contenedor creado para la cuenta de almacenamiento de SAS. Reemplace __SASACCOUNTNAME__ por el nombre de la cuenta de almacenamiento utilizada para la SAS:

        hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Se mostrará el contenido del contenedor, que debe incluir el archivo que se cargó al crear el contenedor y la SAS.
    
2. Utilice lo siguiente para comprobar que puede leer el contenido del archivo. Reemplace __SASCONTAINER__ y __SASACCOUNTNAME__ tal como lo ha hecho en el paso anterior. Reemplace __FILENAME__ por el nombre de archivo que aparece en el comando anterior:

        hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Se mostrará el contenido del archivo.
    
3. Para descargar el archivo en el sistema de archivos local, utilice lo siguiente:

        hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Se descargará el archivo en un archivo local denominado __testfile.txt__.

4. Utilice lo siguiente para cargar el archivo local en un nuevo archivo denominado __testupload.txt__ en el almacenamiento de SAS:

        hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Recibirá un mensaje similar al siguiente:
    
        put: java.io.IOException
        
    Este error se produce porque la ubicación de almacenamiento es solo de lectura y lista. Utilice lo siguiente para colocar los datos en el almacenamiento predeterminado para el clúster, que tiene permiso de escritura:
    
        hdfs dfs -put testfile.txt wasb:///testupload.txt
        
    Esta vez, la operación debe completarse correctamente.
    
##Solución de problemas

###Se ha cancelado una tarea

__Síntomas__: al crear un clúster mediante el script de PowerShell, puede recibir el mensaje de error siguiente:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Causa__: este error se puede producir si utiliza una contraseña para el usuario administrador/HTTP del clúster o, en clústeres basados en Linux, el usuario SSH.

__Solución__: utilice una contraseña que cumpla los criterios siguientes:

- Debe tener como mínimo 10 caracteres.
- Debe contener al menos un dígito.
- Debe incluir al menos un carácter no alfanumérico.
- Debe contener al menos una mayúscula o una minúscula.

##Pasos siguientes

Ahora que ha aprendido a agregar almacenamiento de acceso limitado al clúster de HDInsight, obtenga información acerca de otras maneras de trabajar con datos en el clúster:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0204_2016-->