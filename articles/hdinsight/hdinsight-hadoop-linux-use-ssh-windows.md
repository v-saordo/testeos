<properties
   pageTitle="Utilización de claves SSH con Hadoop en clústeres basados en Linux desde Windows | Microsoft Azure"
   description="Aprenda a crear y usar claves SSH para autenticarse en clústeres de HDInsight basado en Linux. Conecte clústeres desde clientes basados en Windows mediante el cliente SSH PuTTY."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/22/2016"
   ms.author="larryfr"/>

#Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix y OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Shell seguro (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite realizar operaciones de forma remota en los clústeres de HDInsight basado en Linux con una interfaz de línea de comandos. Este documento proporciona información sobre cómo conectarse a HDInsight desde clientes Windows usando el cliente SSH PuTTY.

> [AZURE.NOTE] Los pasos que aparecen en este artículo suponen que está usando un cliente Windows. Si usa un cliente Linux, Unix u OS X, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##Requisitos previos

* **PuTTY** y **PuTTYGen** para clientes Windows. Estas utilidades se encuentran disponibles en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

* Un explorador web moderno que sea compatible con HTML5.

OR

* [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md).

##¿Qué es SSH?

SSH es una utilidad para iniciar sesión y ejecutar de manera remota comandos en un servidor remoto. Con HDInsight basado en Linux, SSH establece una conexión cifrada al nodo principal del clúster y proporciona una línea de comandos que se usa para escribir los comandos. Luego esos comandos se ejecutan directamente en el servidor.

###Nombre de usuario de SSH

Un nombre de usuario SSH es el nombre que se usa para autenticarse en el clúster de HDInsight. Cuando se especifica un nombre de usuario SSH durante la creación del clúster, este usuario se crea en todos los nodos del clúster. Una vez creado el clúster, puede usar este nombre de usuario para conectarse a los nodos principales del clúster de HDInsight. Desde los nodos principales, puede conectarse a los nodos de trabajo individuales.

###Clave pública o contraseña de SSH

Un usuario SSH puede usar una contraseña o una clave pública para la autenticación. Una contraseña es simplemente una cadena de texto que el usuario compone, mientras que una clave pública forma parte de un par de claves criptográficas generado para identificarle.

Una clave es más segura que una contraseña, pero requiere pasos adicionales para generar la clave y hay que mantener los archivos que contienen la clave en una ubicación segura. Si alguien obtiene acceso a los archivos de claves, obtiene acceso a su cuenta. O bien, si pierde los archivos de claves, no podrá iniciar sesión en su cuenta.

Un par de claves consta de una clave pública (que se envía al servidor de HDInsight) y una clave privada (que se mantiene en el equipo cliente.) Al conectarse al servidor de HDInsight con SSH, el cliente SSH usará la clave privada en su equipo para autenticarse con el servidor.

##Creación de una clave SSH

Use la siguiente información si planea usar claves SSH con el clúster. Si piensa usar contraseña, puede omitir esta sección.

1. Abra PuTTYGen.

2. En **Type of key to generate** (Tipo de clave a generar), seleccione **SSH-2 RSA** y, a continuación, haga clic en **Generate** (Generar).

	![Interfaz de PuTTYGen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Mueva el mouse en el área debajo de la barra de progreso hasta que la barra se complete. Mover el mouse genera datos aleatorios que se usan para generar la clave.

	![movimiento del mouse](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Una vez que se genere la clave, aparecerá la clave pública.

4. Para mayor seguridad, puede escribir una frase de contraseña en el campo **Key passphrase** (Frase de contraseña de clave) y, a continuación, escribir el mismo valor en el campo **Confirm passphrase** (Confirmar frase de contraseña).

	![frase de contraseña](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE] Recomendamos encarecidamente que use una frase de contraseña segura para la clave. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.

5. Haga clic en **Save private key** (Guardar clave privada) para guardar la clave en un archivo **.ppk**. Esta clave se usará para autenticarse en el clúster de HDInsight basado en Linux.

	> [AZURE.NOTE] Debe almacenar esta clave en una ubicación segura, puesto que se puede usar para tener acceso a su clúster de HDInsight basado en Linux.

6. Haga clic en **Save public key** (Guardar clave pública) para guardar la clave como archivo **.txt**. Esto le permitirá volver a usar la clave pública cuando cree clústeres adicionales de HDInsight basado en Linux.

	> [AZURE.NOTE] La clave pública también aparece en la parte superior de PuTTYGen. Puede hacer clic con el botón secundario en este campo, copiar el valor y, a continuación, pegarlo en un formulario al crear un clúster mediante el Portal de Azure.

##Creación de un clúster de HDInsight basado en Linux

Cuando cree un clúster de HDInsight basado en Linux, deberá proporcionar la clave pública anteriormente creada. Desde clientes Windows, hay dos maneras de crear un clúster de HDInsight basado en Linux:

* **Portal de Azure**: usa un portal basado en web para crear el clúster.

* **CLI de Azure para Mac, Linux y Windows**: usa comandos de la línea de comandos para crear el clúster.

Cada uno de estos métodos requerirá la clave pública. Para información completa sobre la creación de un clúster de HDInsight basado en Linux, vea [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md).

###Portal de Azure

Al usar el [Portal de Azure][preview-portal] para crear un clúster de HDInsight basado en Linux, debe escribir un **Nombre de usuario de SSH** y seleccionar una **CONTRASEÑA** o una **CLAVE PÚBLICA DE SSH**.

Si selecciona **CLAVE PÚBLICA DE SSH**, puede pegar la clave pública (que se muestra en el campo __Clave pública para pegar en el archivo OpenSSH authorized\_keys__ de PuttyGen) en el campo __SSH PublicKey__ o elija __Seleccionar un archivo__ para examinar y seleccionar el archivo que contiene la clave pública.

![Imagen del formulario que solicita la clave pública](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Esta acción crea un inicio de sesión para el usuario especificado y le permite una autenticación por contraseña o por clave SSH.

###Interfaz de la línea de comandos de Azure para Mac, Linux y Windows

Puede utilizar [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md) con el fin de crear un clúster nuevo con el comando `azure hdinsight cluster create`.

Para obtener más información acerca del uso de este comando, consulte [Aprovisionamiento de clústeres de Hadoop Linux en HDInsight con opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

##Conexión a un clúster de HDInsight basado en Linux

1. Abra PuTTY.

	![interfaz de putty](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Si proporcionó una clave SSH cuando creó la cuenta de usuario, debe realizar el siguiente paso para seleccionar la clave privada que se usará al autenticarse en el clúster.

	En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Auth** (Autenticar). Finalmente, haga clic en **Browse** (Examinar) y seleccione el archivo .ppk que contiene su clave privada.

	![interfaz de putty, seleccionar clave privada](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. En **Category** (Categoría), seleccione **Session** (Sesión). En la pantalla **Basic options for your PuTTY session** (Opciones básicas de la sesión de PuTTY), escriba la dirección SSH del servidor de HDInsight en el campo **Host name (or IP address)** (Nombre de host (o dirección IP)). La dirección SSH es el nombre de su clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

	![interfaz de putty con dirección ssh introducida](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Para guardar la información de conexión para un uso posterior, escriba un nombre para esta conexión en **Saved Sessions** (Sesiones guardadas) y, a continuación, haga clic en **Save** (Guardar). La conexión se agregará a la lista de sesiones guardadas.

5. Haga clic en **Open** (Abrir) para conectarse al clúster.

	> [AZURE.NOTE] Si es la primera vez que se conecta al clúster, recibirá una alerta de seguridad. Esto es normal. Seleccione **Yes** (Sí) para almacenar en caché la clave RSA2 del servidor para continuar.

6. Cuando se le solicite, escriba el usuario que ingresó cuando creó el clúster. Si proporcionó una contraseña para el usuario, también se le pedirá escribirla.

> [AZURE.NOTE] En los pasos anteriores se da por hecho que está usando el puerto 22, que se conectará al nodo principal 0 en el clúster de HDInsight. Si usa el puerto 23, se conectará al nodo principal 1. Para más información sobre los nodos principales, vea [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md).

###Conexión a los nodos de trabajo

No es posible tener acceso a los nodos de trabajo directamente desde fuera del centro de datos de Azure, pero sí es posible hacerlo desde el nodo principal del clúster a través de SSH.

Si proporcionó una clave SSH cuando creó la cuenta de usuario, debe realizar los siguientes pasos para usar la clave privada cuando realice la autenticación del clúster si desea conectarse a los nodos de trabajo.

1. Instale Pageant desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Esta utilidad se usa para almacenar en caché claves SSH para PuTTY.

2. Ejecute Pageant. Se minimizará a un icono en la bandeja de estado. Haga clic con el botón secundario en el icono y seleccione **Add Key** (Agregar clave).

    ![agregar clave](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Cuando aparezca el cuadro de diálogo para examinar, seleccione el archivo .ppk que contiene la clave y, a continuación, haga clic en **Open** (Abrir). Con esto la clave se agrega a Pageant, que la proporcionará a PuTTY cuando se conecte al clúster.

    > [AZURE.IMPORTANT] Si usa una clave SSH para proteger la cuenta, deberá completar los pasos anteriores para poder conectarse a los nodos de trabajo.

4. Abra PuTTY.

5. Si usa una clave SSH para autenticarse, en la sección **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Auth** (Autenticar).

    En la sección **Authentication parameters** (Parámetros de autenticación), habilite **Allow agent forwarding** (Permitir desvío de agente). Esto permite que PuTTY pase automáticamente la autenticación de certificado a través de la conexión al nodo principal del clúster cuando se conecte a los nodos de trabajo.

    ![permitir desvío de agente](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Conéctese al clúster como se indicó anteriormente. Si usa una clave SSH para la autenticación, no necesita seleccionar la clave: se usará la clave SSH agregada a Pageant para realizar la autenticación en el clúster.

7. Una vez establecida la conexión, use lo siguiente para recuperar una lista de los nodos del clúster. Reemplace *ADMINPASSWORD* por la contraseña de la cuenta del administrador de clúster. Reemplace *CLUSTERNAME* por el nombre del clúster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Esto devolverá información en formato JSON para los nodos del clúster, incluido `host_name`, que contiene el nombre de dominio completo (FDQN) para cada nodo. El siguiente es un ejemplo de una entrada `host_name` devuelta por el comando **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Una vez que tenga una lista de los nodos de trabajo a los que desea conectarse, use el comando siguiente desde la sesión de PuTTY para abrir una conexión con un nodo de trabajo:

        ssh USERNAME@FQDN

    Reemplace *USERNAME* por el nombre de usuario SSH y *FQDN* por el FQDN del nodo de trabajo. Por ejemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Si usa una contraseña para realizar la autenticación de la sesión SSH, se le pedirá que la escriba nuevamente. Si usa una clave SSH, la conexión debiera finalizar sin que deba realizar ninguna acción.

9. Una vez establecida la sesión, el símbolo del sistema de la sesión de PuTTY cambiará de `username@hn0-clustername` a `username@wn0-clustername` para indicar que está conectado al nodo de trabajo. Los comandos que ejecute en este punto se ejecutarán en el nodo de trabajo.

10. Una vez que haya terminado de realizar acciones en el nodo de trabajo, use el comando `exit` para cerrar la sesión en el nodo de trabajo. Con esto volverá al símbolo del sistema `username@hn0-clustername`.

##Incorporación de más cuentas

Si necesita agregar más cuentas al clúster, siga estos pasos:

1. Genere una clave pública nueva y una clave privada nueva para la cuenta de usuario nueva, tal como se describió anteriormente.

2. Desde una sesión de SSH al clúster, agregue el usuario nuevo con el siguiente comando:

		sudo adduser --disabled-password <username>

	Con esto se creará una nueva cuenta de usuario, pero se deshabilitará la autenticación de contraseña.

3. Cree el directorio y los archivos para que contengan la clave con los siguientes comandos:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Cuando se abra el editor nano, copie y pegue el contenido de la clave pública para la cuenta de usuario nueva. Finalmente, use **Ctrl-X** para guardar el archivo y salga del editor.

	![imagen del editor nano con la clave de ejemplo](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Use el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la cuenta de usuario nueva:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Ahora debiera poder autenticarse con el servidor con la cuenta de usuario nueva y la clave privada.

##<a id="tunnel"></a>Tunelización de SSH

SSH se puede usar para tunelizar las solicitudes locales, como solicitudes web, al clúster de HDInsight. La solicitud se enrutará al recurso solicitado como si se hubiese originado en el nodo principal del clúster de HDInsight.

> [AZURE.IMPORTANT] El túnel SSH es un requisito para acceder a la interfaz de usuario web de algunos servicios de Hadoop. Por ejemplo, solo se puede acceder a la interfaz de usuario del historial de trabajos o la interfaz de usuario del administrador de recursos usando un túnel SSH.

Para más información sobre la creación y el uso de un túnel SSH, vea [Uso de la tunelización de SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

##Pasos siguientes

Ahora que sabe cómo realizar la autenticación con una clave SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->