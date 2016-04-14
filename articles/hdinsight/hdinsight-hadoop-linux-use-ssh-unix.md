<properties
   pageTitle="Utilización de claves SSH con Hadoop basado en Linux desde Linux, Unix u OS X | Microsoft Azure"
   description="Puede acceder a HDInsight basado en Linux mediante Shell seguro (SSH). Este documento proporciona información sobre el uso de SSH con HDInsight desde clientes Linux, Unix u OS X."
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
   ms.date="01/28/2016"
   ms.author="larryfr"/>

#Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix y OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Shell seguro (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite realizar operaciones de forma remota en los clústeres de HDInsight basado en Linux con una interfaz de línea de comandos. Este documento proporciona información sobre el uso de SSH con HDInsight desde clientes Linux, Unix u OS X.

> [AZURE.NOTE] Los pasos que aparecen en este artículo suponen que está usando un cliente Linux, Unix u OS X. A pesar de que estos pasos se pueden realizar en un cliente Windows si tiene instalado un paquete que proporciona `ssh` y `ssh-keygen` (como Git para Windows), se recomienda que los clientes Windows sigan los pasos que aparecen en [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##Requisitos previos

* **ssh-keygen** y **ssh** para clientes Linux, Unix y OS X. Estas utilidades normalmente vienen con el sistema operativo o se encuentran disponibles a través del sistema de administración de paquetes.

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

1. Abra una sesión de terminal y use el comando siguiente para ver si cuenta con claves SSH existentes:

		ls -al ~/.ssh

	Busque los siguientes archivos en la lista de directorios. Los siguientes son nombres comunes para claves SSH públicas.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Si no desea usar un archivo existente o no tiene claves SSH existentes, use lo siguiente para generar un archivo nuevo:

		ssh-keygen -t rsa

	Se le solicitará la información siguiente:

	* La ubicación del archivo: se define de manera predeterminada en ~/.ssh/id\_rsa.
	* Una frase de contraseña: se le pedirá que vuelva a escribirla.

		> [AZURE.NOTE] Recomendamos encarecidamente que use una frase de contraseña segura para la clave. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.

	Una vez que finalice el comando, tendrá dos archivos nuevos: la clave privada (por ejemplo, **id\_rsa**) y la clave pública (por ejemplo, **id\_rsa.pub**).

##Creación de un clúster de HDInsight basado en Linux

Cuando cree un clúster de HDInsight basado en Linux, deberá proporcionar la clave pública anteriormente creada. Desde clientes Linux, Unix u OS X, hay dos formas de crear un clúster de HDInsight:

* **Portal de Azure**: usa un portal basado en web para crear el clúster.

* **CLI de Azure para Mac, Linux y Windows**: usa comandos de la línea de comandos para crear el clúster.

Cada uno de estos métodos requerirá una contraseña o una clave pública. Para información completa sobre la creación de un clúster de HDInsight basado en Linux, vea [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md).

###Portal de Azure

Al usar el [Portal de Azure][preview-portal] para crear un clúster de HDInsight basado en Linux, debe escribir un **NOMBRE DE USUARIO DE SSH** y seleccionar una **CONTRASEÑA** o una **CLAVE PÚBLICA DE SSH**.

Si selecciona **CLAVE PÚBLICA DE SSH**, puede pegar la clave pública (contenida en el archivo con la extensión **.pub**) en el campo __SSH PublicKey__ o elegir __Seleccionar un archivo__ para examinar y seleccionar el archivo de clave pública.

![Imagen del formulario que solicita la clave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] El archivo de la clave es simplemente un texto de archivo. El contenido debe ser similar al siguiente:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Con esto se crea un inicio de sesión para el usuario especificado, mediante la contraseña o la clave pública que proporciona.

###Interfaz de la línea de comandos de Azure para Mac, Linux y Windows

Puede utilizar [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md) con el fin de crear un clúster nuevo con el comando `azure hdinsight cluster create`.

Para obtener más información acerca del uso de este comando, consulte [Aprovisionamiento de clústeres de Hadoop Linux en HDInsight con opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

##Conexión a un clúster de HDInsight basado en Linux

Desde una sesión de terminal, use el comando SSH para conectarse al nodo principal del clúster proporcionando el nombre de usuario y la dirección:

* **Dirección SSH**: el nombre del clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

* **Nombre de usuario**: el nombre de usuario SSH que proporcionó al crear el clúster.

En el siguiente ejemplo se conectará al clúster **mycluster** como el usuario **me**:

	ssh me@mycluster-ssh.azurehdinsight.net

Si usó una contraseña para la cuenta del usuario, se le solicitará escribir la contraseña.

Si utilizó una clave SSH protegida con una frase de contraseña, se le pedirá que la escriba. De lo contrario, SSH intentará realizar la autenticación automáticamente con una de las claves privadas locales en el cliente.

> [AZURE.NOTE] Si SSH no realiza la autenticación automáticamente con la clave privada correcta, utilice el parámetro **-i** y especifique la ruta de acceso a la clave privada. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Si no se especifica ningún puerto, SSH establecerá el puerto 22 como predeterminado, lo cual provocará la conexión al nodo principal 0 en el clúster de HDInsight. Si usa el puerto 23, se conectará al nodo principal 1. Para más información sobre los nodos principales, vea [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md).

###Conexión a los nodos de trabajo

No es posible tener acceso a los nodos de trabajo directamente desde fuera del centro de datos de Azure, pero sí es posible hacerlo desde el nodo principal del clúster a través de SSH.

Si usa una clave SSH para autenticar la cuenta de usuario, debe completar los pasos siguientes en el cliente:

1. Abra `~/.ssh/config` con un editor de texto. Si este archivo no existe, puede crearlo si escribe `touch ~/.ssh/config` en el terminal.

2. Agregue al archivo lo siguiente. Reemplace *CLUSTERNAME* por el nombre del clúster de HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Con esto se configura el desvío del agente SSH para el clúster de HDInsight.

3. Use el siguiente comando desde el terminal para probar el desvío del agente SSH:

        echo "$SSH_AUTH_SOCK"

    Este comando debiera devolver información similar a la siguiente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si no se devuelve nada, indica que **ssh-agent** no está en ejecución. Vea la documentación del sistema operativo para seguir los pasos específicos sobre la instalación y configuración de **ssh-agent** o vea [Uso de ssh-agent con SSH](http://mah.everybody.org/docs/ssh).

4. Una vez que haya comprobado que **ssh-agent** está en ejecución, use lo siguiente para agregar la clave privada SSH al agente:

        ssh-add ~/.ssh/id_rsa

    Si la clave privada está almacenada en un archivo distinto, reemplace `~/.ssh/id_rsa` por la ruta de acceso al archivo.

Siga estos pasos para conectarse a los nodos de trabajo de su clúster.

> [AZURE.IMPORTANT] Si usa una clave SSH para autenticar su cuenta, debe completar los pasos anteriores para comprobar que el desvío de agente funciona.

1. Conéctese al clúster de HDInsight con SSH, tal como se describió anteriormente.

2. Una vez que esté conectado, use lo siguiente para recuperar una lista de los nodos del clúster. Reemplace *ADMINPASSWORD* por la contraseña de la cuenta del administrador de clúster. Reemplace *CLUSTERNAME* por el nombre del clúster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Esto devolverá información en formato JSON para los nodos del clúster, incluido `host_name`, que contiene el nombre de dominio completo (FDQN) para cada nodo. El siguiente es un ejemplo de una entrada `host_name` devuelta por el comando **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Una vez que tenga una lista de los nodos de trabajo a los que desea conectarse, use el comando siguiente desde la sesión SSH al servidor para abrir una conexión con un nodo de trabajo:

        ssh USERNAME@FQDN

    Reemplace *USERNAME* por el nombre de usuario SSH y *FQDN* por el FQDN del nodo de trabajo. Por ejemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Si usa una contraseña para realizar la autenticación de la sesión SSH, se le pedirá que la escriba nuevamente. Si usa una clave SSH, la conexión debiera finalizar sin que deba realizar ninguna acción.

4. Una vez establecida la sesión, el símbolo del sistema del terminal cambiará de `username@hn0-clustername` a `username@wk0-clustername` para indicar que está conectado al nodo de trabajo. Los comandos que ejecute en este punto se ejecutarán en el nodo de trabajo.

4. Una vez que haya terminado de realizar acciones en el nodo de trabajo, use el comando `exit` para cerrar la sesión en el nodo de trabajo. Con esto volverá al símbolo del sistema `username@hn0-clustername`.

##Incorporación de más cuentas

1. Genere una clave pública nueva y una clave privada nueva para la cuenta de usuario nueva, tal como se describe en la sección [Creación de una clave SSH](#create-an-ssh-key-optional).

	> [AZURE.NOTE] La clave privada se generará en un cliente que el usuario usará para conectarse al clúster, o bien, se transferirá de manera protegida a dicho cliente después de la creación.

1. Desde una sesión de SSH al clúster, agregue el usuario nuevo con el siguiente comando:

		sudo adduser --disabled-password <username>

	Con esto se creará una nueva cuenta de usuario, pero se deshabilitará la autenticación de contraseña.

2. Cree el directorio y los archivos para que contengan la clave con los siguientes comandos:

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Cuando se abra el editor nano, copie y pegue el contenido de la clave pública para la cuenta de usuario nueva. Finalmente, use **Ctrl-X** para guardar el archivo y salga del editor.

	![imagen del editor nano con la clave de ejemplo](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Use el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la cuenta de usuario nueva:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Ahora debiera poder autenticarse con el servidor con la cuenta de usuario nueva y la clave privada.

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