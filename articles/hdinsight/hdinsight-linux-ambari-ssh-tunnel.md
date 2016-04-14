<properties
pageTitle="Uso de la tunelación SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web"
description="Obtenga información acerca de cómo usar un túnel SSH para ir con seguridad a los recursos web alojados en los nodos de HDInsight basados en Linux."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="get-started-article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/12/2016"
ms.author="larryfr"/>

#Uso de la tunelación SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web

Los clústeres de HDInsight basados en Linux proporcionan acceso a la interfaz de usuario web de Ambari en Internet, pero no a algunas características de la interfaz de usuario. Por ejemplo, la interfaz de usuario web para otros servicios que se muestran en Ambari. Para usar la funcionalidad completa de la interfaz de usuario web de Ambari, use un túnel SSH para el nodo principal del clúster.

##¿Qué requiere un túnel SSH?

Algunos de los menús de Ambari no se rellenarán totalmente sin un túnel SSH, ya que se basan en sitios web y servicios expuestos por otros servicios de Hadoop que se ejecutan en el clúster. A menudo, estos sitios web no están protegidos, por lo que no es seguro exponerlos directamente en internet. A veces, el servicio ejecuta el sitio web en otro nodo del clúster como un nodo de Zookeeper.

Los siguientes son servicios usados por la interfaz de usuario web de Ambari a los que no se puede tener acceso sin un túnel SSH:

* ResourceManager,
* JobHistory,
* NameNode,
* Pilas de subprocesos,
* Interfaz de usuario web de Oozie
* Interfaz de usuario de registros y maestro de HBase

Si usa las acciones de script para personalizar el clúster, todos los servicios o utilidades que instale y expongan una interfaz de usuario web requerirán un túnel SSH. Por ejemplo, si instala Hue mediante una acción de script, debe usar un túnel SSH para tener acceso a la interfaz de usuario web de Hue.

##¿Qué es un túnel SSH?

[Túnel Shell seguro (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) enruta el tráfico enviado a un puerto en su estación de trabajo local, a través de una conexión SSH con el nodo principal del clúster de HDInsight, donde la solicitud se resuelve como si se hubiera originado en el nodo principal. A continuación, la respuesta se enruta a través del túnel a la estación de trabajo.

##Requisitos previos

Cuando se usa un túnel SSH para el tráfico web, debe tener lo siguiente:

* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Si desea usar un cliente SSH distinto de `ssh` o PuTTY, vea la documentación de su cliente sobre cómo establecer un túnel SSH.

* Un explorador web que se puede configurar para usar un proxy SOCKS

* __(opcional)__: un complemento como [FoxyProxy](http://getfoxyproxy.org/,) que puede aplicar reglas que se limitan a enrutar solicitudes específicas a través del túnel.

	> [AZURE.WARNING] Sin un complemento como FoxyProxy, todas las solicitudes realizadas a través del explorador se pueden enrutar a través del túnel. Esto puede producir una carga más lenta de las páginas web en el explorador.

##<a name="usessh"></a>Creación de un túnel con el comando SSH

Use el siguiente comando para crear un túnel SSH con el comando `ssh`. Reemplace __USERNAME__ por un usuario SSH para su clúster de HDInsight y reemplace __CLUSTERNAME__ por el nombre de su clúster de HDInsight.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Con esto se crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

* **D 9876**: el puerto local que enrutará el tráfico a través del túnel.

* **C**: comprimir todos los datos, debido a que el tráfico web es principalmente texto.

* **2**: forzar a SSH a que intente solo la versión 2 del protocolo.

* **q**: modo silencioso.

* **T**: deshabilitar la asignación seudotty, debido a que solo estamos desviando un puerto.

* **n**: evitar la lectura de STDIN, debido a que solo estamos desviando un puerto.

* **N**: no ejecutar un comando remoto, debido a que solo estamos desviando un puerto.

* **f**: ejecutar en segundo plano.

Si ha configurado el clúster con una clave SSH, es posible que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la clave SSH privada.

Una vez que se completa el comando, el tráfico enviado al puerto 9876 del equipo local se enrutará sobre Capa de sockets seguros (SSL) al nodo principal del clúster y aparecerá como originado ahí.

##<a name="useputty"></a>Creación de un túnel mediante PuTTY

Use los siguientes pasos para crear un túnel SSH con PuTTY.

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usarlo con HDInsight.

2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).

3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):

	* **Source port**: el puerto en el cliente que desea desviar. Por ejemplo, **9876**.

	* **Destination**: la dirección SSH del clúster de HDInsight basado en Linux. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: habilita el enrutamiento dinámico del proxy SOCKS.

	![imagen de las opciones de tunelización](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.

5. Cuando se le solicite, inicie sesión en el servidor. Esto establecerá una sesión SSH y habilitará el túnel.

##Uso del túnel desde el explorador

> [AZURE.NOTE] Los pasos de esta sección usan el explorador FireFox, ya que es gratuito para sistemas Linux, Unix, Macintosh OS X y Windows. Otros exploradores modernos como Google Chrome, Microsoft Edge o Apple Safari deberían funcionar; sin embargo, el complemento FoxyProxy usado en algunos pasos no está disponible para todos los exploradores.

1. Configure el explorador para usar **localhost:9876** como un proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera. Si usa un puerto que no es 9876, cambie el puerto al que usa:

	![imagen de la configuración de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] Seleccionar **DNS remoto** se resuelven las solicitudes del sistema de nombres de dominio (DNS) mediante el uso de un clúster de HDInsight. Si no se selecciona, el DNS se resolverá de manera local.

2. Compruebe que el tráfico se enruta a través del túnel en un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/) con la configuración del proxy habilitada y deshabilitada en Firefox. Cuando la configuración esté habilitada, la dirección IP será la de una máquina en el centro de datos de Microsoft Azure.

###Extensiones del explorador

A pesar de que la configuración del explorador para que use el túnel funciona, normalmente no desearía enrutar todo el tráfico a través del túnel. Las extensiones del explorador, como [FoxyProxy](http://getfoxyproxy.org/), son compatibles con la coincidencia de patrones para las solicitudes de dirección URL (FoxyProxy Standard o Plus solamente), de manera tal que solo las solicitudes para direcciones URL específicas se enviarán a través del túnel.

Si instaló FoxyProxy Standard, use los siguientes pasos para configurarlo para que solo desvíe tráfico para HDInsight a través del túnel.

1. Abra la extensión FoxyProxy en el explorador. Por ejemplo, en Firefox, seleccione el icono de FoxyProxy que se encuentra junto al campo de dirección.

	![icono de foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. Seleccione **Agregar proxy nuevo**, la pestaña **General** y, a continuación, escriba un nombre de proxy de **HDInsightProxy**.

	![configuración general de foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. Seleccione la pestaña **Detalles de proxy** y rellene los campos siguientes:

	* **Host y dirección IP**: localhost, debido a que usamos un túnel SSH en la máquina local.

	* **Puerto**: el puerto que usó para el túnel SSH.

	* **Proxy SOCKS**: seleccione para permitir que el explorador use el túnel como proxy.

	* **SOCKS v5**: seleccione para definir la versión requerida del proxy.

	![proxy foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. Seleccione la pestaña **Patrones de dirección URL** y, a continuación, seleccione **Agregar patrón nuevo**. Use lo siguiente para definir el patrón y, a continuación, haga clic en **Aceptar**:

	* **Nombre de patrón** - **clusternodes**: es solo un nombre descriptivo para el patrón.

	* **Patrón de URL** - ***internal.cloudapp.net*** - Define un patrón que coincide con el nombre de dominio completo interno de los nodos del clúster.

	![patrón de foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

4. Seleccione **Aceptar** para agregar el proxy y cierre la **Configuración de proxy**.

5. En la parte superior del cuadro de diálogo FoxyProxy, cambie **Seleccionar modo** a **Uso de servidores proxy según sus patrones y prioridades predefinidos** y, a continuación, haga clic en **Cerrar**.

	![seleccionar modo de foxyproxy](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

Después de seguir estos pasos, solo las solicitudes de direcciones URL que contienen la cadena __internal.cloudapp.net__ se enrutarán a través del túnel SSL.

##Compruebe con la interfaz de usuario web de Ambari

Una vez que se ha establecido el clúster, siga estos pasos para comprobar que puede tener acceso a las interfaces de usuario web del servicio desde la web de Ambari:

1. En el explorador, vaya a https://CLUSTERNAME.azurehdinsight.net, donde CLUSTERNAME es el nombre del clúster de HDInsight.

	Cuando se le solicite, escriba el nombre de usuario administrador (admin) y la contraseña del clúster. Es posible que se le pida una segunda vez mediante la interfaz de usuario web de Ambari. Si es así, vuelva a escribir la información.

2. En la interfaz de usuario web de Ambari, seleccione YARN en la lista de la izquierda de la página.

	![Imagen con YARN seleccionado](./media/hdinsight-linux-ambari-ssh-tunnel/yarnservice.png)

3. Cuando se muestra la información del servicio YARN, seleccione __Vínculos rápidos__. Aparecerá una lista de los nodos del clúster principal. Seleccione uno de los nodos principales y luego seleccione __IU de ResourceManager__.

	![Imagen con el menú Vínculos rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/yarnquicklinks.png)

	> [AZURE.NOTE] Si tiene una conexión a Internet lenta o el nodo principal está muy ocupado, es posible que obtenga un indicador de espera en lugar de un menú al seleccionar __Vínculos rápidos__. Si es así, espere un minuto o dos hasta que se reciban los datos del servidor e intente de nuevo la lista.


	> [AZURE.TIP] Si tiene un monitor con una resolución inferior o no se maximiza la ventana del explorador, algunas entradas del menú __Vínculos rápidos__ puede aparecer cortadas por el lado derecho de la pantalla. Si es así, expanda el menú con el mouse, use la tecla de flecha derecha para desplazarse por la pantalla hacia la derecha para ver el resto del menú.

4. Debería ver una página similar a la siguiente:

	![Imagen de la interfaz de usuario de ResourceManager YARN](./media/hdinsight-linux-ambari-ssh-tunnel/yarnresourcemanager.png)

	> [AZURE.TIP] Observe la dirección URL de esta página; debe ser similar a \_\___http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__. Esto usa el nombre de dominio completo interno (FQDN) del nodo y no es accesible sin usar un túnel SSH.

##Pasos siguientes

Ahora que ha aprendido a crear y usar un túnel SSH, consulte lo siguiente para obtener información sobre supervisión y administración del clúster con Ambari:

* [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0302_2016-->