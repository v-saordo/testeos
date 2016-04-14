<properties
   pageTitle="Creación de un entorno de pruebas de rendimiento para Elasticsearch | Microsoft Azure "
   description="Cómo configurar un entorno para probar el rendimiento de un clúster de Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

En este documento se describe el modo de configurar un entorno para probar el rendimiento de un clúster de Elasticsearch. Esta configuración se usó para probar el rendimiento de la ingesta de datos y de las cargas de trabajo de consulta, tal y como se describe en [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure][].

El proceso de pruebas de rendimiento usó [Apache JMeter](http://jmeter.apache.org/), con el [conjunto estándar](http://jmeter-plugins.org/wiki/StandardSet/) de complementos instalados en una configuración de maestro/subordinado y un conjunto de máquinas virtuales dedicadas (que no forman parte del clúster Elasticsearch) configurado específicamente para este propósito.

Se instaló [PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/) en cada nodo de Elasticsearch. En las secciones siguientes se proporcionan instrucciones para volver a crear el entorno de prueba para que pueda llevar a cabo sus propias pruebas de rendimiento con JMeter. Estas instrucciones dan por hecho que ya creó un clúster Elasticsearch con nodos conectados mediante una red virtual de Azure.

Tenga en cuenta que el entorno de prueba también se ejecuta como un conjunto de máquinas virtuales de Azure que se administran con un solo grupo de recursos de Azure.

También se instaló y configuró [Marvel](https://www.elastic.co/products/marvel) para facilitar la supervisión y el análisis de los aspectos internos del clúster de Elasticsearch; si las estadísticas de JMeter muestran un pico o una caída del rendimiento, la información disponible en Marvel puede ser de gran ayuda para determinar la causa de las fluctuaciones.

La siguiente imagen muestra la estructura de todo el sistema.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Tenga en cuenta los siguientes puntos:

- La máquina virtual maestra de JMeter ejecuta Windows Server para proporcionar el entorno de interfaz gráfica de usuario para la consola de JMeter. La máquina virtual maestra de JMeter proporciona la interfaz gráfica de usuario (la aplicación *jmeter*) para que un evaluador pueda crear y ejecutar pruebas, y visualizar los resultados. Esta máquina virtual se coordina con las máquinas virtuales de servidor de JMeter que envían las solicitudes que forman las pruebas.

- Las máquinas virtuales subordinadas de JMeter ejecutan Ubuntu Server (Linux); no hay ningún requisito de GUI para estas máquinas virtuales. Las máquinas virtuales de servidor de JMeter ejecutan el software de servidor de JMeter (la aplicación *jmeter-server*) para enviar solicitudes al clúster de Elasticsearch.

- No se usaron nodos de cliente dedicados, aunque había nodos maestros dedicados.

- El número de nodos de datos en el clúster puede variar según el escenario que se esté probando.

- Todos los nodos del clúster de Elasticsearch ejecutan Marvel para observar el rendimiento en tiempo de ejecución y el agente de servidor de JMeter para recopilar datos de supervisión para su análisis posterior.

- Al probar Elasticsearch 2.0.0 y versiones posteriores, uno de los nodos de datos también ejecuta Kibana; esto es un requisito de la versión de Marvel que se ejecuta en Elasticsearch 2.0.0 o versiones posteriores.

## Creación de un grupo de recursos de Azure para las máquinas virtuales

La máquina virtual maestra de JMeter debe ser capaz de conectarse directamente a cada uno de los nodos del clúster de Elasticsearch para recopilar los datos de rendimiento. Si la red virtual de JMeter es distinta de la red virtual del clúster de Elasticsearch, esto implica configurar cada nodo de Elasticsearch con una dirección IP pública. Si esto es un problema con su configuración de Elasticsearch, considere la posibilidad de implementar las máquinas virtuales de JMeter en la misma red virtual que el clúster de Elasticsearch usando el mismo grupo de recursos; en este caso, puede omitir este primer procedimiento.

En primer lugar, [cree un grupo de recursos](../articles/resource-group-portal/#create-resource-group-and-resources). En este documento se da por hecho que el grupo de recursos se llama *JMeterPerformanceTest*. Si desea ejecutar las máquinas virtuales de JMeter en la misma red virtual que el clúster de Elasticsearch, use el mismo grupo de recursos que ese clúster en lugar de crear uno nuevo.

## Creación de la máquina virtual maestra de JMeter

A continuación, [cree una máquina virtual de Windows](../articles/virtual-machines-windows-tutorial/) mediante la imagen *Windows Server 2008 R2 SP1*. Se recomienda seleccionar un tamaño de máquina virtual con suficientes núcleos y memoria para ejecutar las pruebas de rendimiento. Lo ideal es que sea una máquina con al menos 2 núcleos y 3,5 GB de RAM (A2 estándar o mayor).

<!-- TODO add info on why disabling diagnostics is positive -->

Se recomienda deshabilitar los diagnósticos. Para crear la máquina virtual en el portal, se hace en la hoja *Configuración* en la sección *Supervisión* de *Diagnósticos*. Deje las otras opciones de configuración con sus valores predeterminados.

Compruebe que la máquina virtual y todos los recursos asociados se crearon correctamente; para ello, [examine el grupo de recursos](../articles/resource-group-portal/#browse-resource-groups) en el portal. Los recursos enumerados deben constar de una máquina virtual, un grupo de seguridad de red y una dirección IP pública, todos con el mismo nombre, y la interfaz de red y la cuenta de almacenamiento con nombres basados en el nombre de la máquina virtual.

## Creación de las máquinas virtuales subordinadas de JMeter

Ahora, [cree una máquina virtual de Linux](../articles/virtual-machines-linux-tutorial-portal-rm/) mediante la imagen *Ubuntu Server 14.04 LTS*. Al igual que la máquina virtual maestra de JMeter, seleccione un tamaño de máquina virtual con suficientes núcleos y memoria para ejecutar las pruebas de rendimiento. Lo ideal es que sea una máquina con al menos 2 núcleos y 3,5 GB de RAM (A2 estándar o mayor).

De nuevo, se recomienda deshabilitar los diagnósticos.

Puede crear tantas máquinas virtuales subordinadas como desee.

## Instalación del servidor de JMeter en las máquinas virtuales subordinadas de JMeter

Las máquinas virtuales subordinadas de JMeter ejecutan Linux y, de forma predeterminada, no se puede conectar con ellas abriendo una conexión a Escritorio remoto (RDP). En su lugar, puede [usar PuTTY para abrir una ventana de línea de comandos](../articles/virtual-machines-linux-how-to-log-on/) en cada máquina virtual.

Una vez conectados a una de las máquinas virtuales subordinadas, usaremos bash para configurar JMeter.

En primer lugar, instale la versión de Java Runtime Environment necesaria para ejecutar JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Ahora, descargue el software de JMeter empaquetado como un archivo zip.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Instale el comando unzip y úselo para expandir el software JMeter. El software se copia en una carpeta llamada **jmeter-apache-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Cambie al directorio *bin* que contiene los ejecutables de JMeter y haga que los programas *jmeter-server* y *jmeter* sean ejecutables.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

Ahora, será necesario editar el archivo `jmeter.properties` ubicado en la carpeta actual (use el editor de texto con el que esté más familiarizado, como *vi* o *vim*). Busque las líneas siguientes:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Quite los comentarios (quite los caracteres ## al principio de la línea) y modifique estas líneas tal y como se muestra a continuación; a continuación, guarde el archivo y cierre el editor:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Ahora, ejecute los siguientes comandos para abrir el puerto 4441 al tráfico TCP entrante (este es el puerto que en el que acaba de configurar *jmeter-server* para que escuche):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Descargue un archivo zip que contiene la colección de complementos estándar para JMeter (estos complementos ofrecen contadores de supervisión de rendimiento) y, a continuación, descomprima el archivo. Al descomprimir el archivo en esta ubicación, los complementos se colocan en las carpetas correctas.

Si se le pide que reemplace el archivo LICENSE, escriba A (todo):

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Use `nohup` para iniciar el servidor de JMeter en segundo plano. Debe responder mostrando un identificador de proceso y un mensaje que indica que creó un objeto remoto y está listo para empezar a recibir comandos.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Si la máquina virtual se apaga, el programa de servidor de JMeter finaliza. Debe conectarse a la máquina virtual y reiniciarla de nuevo manualmente. Como alternativa, puede configurar el sistema para ejecutar el comando *jmeter-server* automáticamente en el inicio; para ello, agregue los siguientes comandos al archivo `/etc/rc.local` (antes del comando *exit 0*):

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Reemplace `<username>` por su nombre de inicio de sesión.

Quizás le resulte útil mantener abierta la ventana de terminal para poder supervisar el progreso del servidor de JMeter mientras la prueba está en curso.

Debe repetir estos pasos para cada máquina virtual subordinada de JMeter.

## Instalación del agente de servidor de JMeter en los nodos de Elasticsearch

En este procedimiento se da por hecho que tiene acceso a los nodos de Elasticsearch. Si creó el clúster mediante la plantilla ARM, puede conectarse a cada nodo mediante la máquina virtual Jump Box, tal y como se muestra en la sección [Topología de producción de Azure](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies). También puede conectarse con Jump Box mediante PuTTY.

Desde allí, puede usar el comando *ssh* para iniciar sesión en cada uno de los nodos del clúster de Elasticsearch.

Inicie sesión en uno de los nodos de Elasticsearch como administrador. En el símbolo del sistema de bash, escriba los siguientes comandos para crear una carpeta para guardar el agente de servidor de JMeter y vaya a esa carpeta:

```bash
mkdir server-agent
cd server-agent
```

Ejecute los comandos siguientes para instalar el comando *unzip* (si no está ya instalado), descargue el software del agente de servidor de JMeter y descomprímalo:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Ejecute el comando siguiente para configurar el firewall y permitir que el tráfico TCP pase a través del puerto 4444 (es decir, el puerto usado por el agente de servidor de JMeter):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Ejecute el siguiente comando para iniciar el agente de servidor de JMeter en segundo plano:

```bash
nohup ./startAgent.sh &
```

El agente de servidor de JMeter debe responder con mensajes que indican que se ha iniciado y que está escuchando en el puerto 4444. Presione Entrar para abrir un símbolo del sistema y luego ejecute el siguiente comando: Reemplace `<nodename>` por el nombre del nodo. Si no está seguro del nombre del nodo, puede encontrarlo con el comando `hostname`:

```bash
telnet <nodename> 4444
```

Este comando abre una conexión de telnet al puerto 4444 del equipo local. Puede usar esta conexión para comprobar que el agente del servidor de JMeter se ejecuta correctamente.

Si no se está ejecutando el agente del servidor de JMeter, recibirá la respuesta

`*telnet: Unable to connect to remote host: Connection refused*.`

Si el agente de servidor de JMeter está ejecutando y el puerto 4444 se configuró correctamente, debería ver la siguiente respuesta:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] La sesión de telnet no proporciona a ningún tipo de mensaje una vez que conectada.

En la sesión de telnet, escriba el siguiente comando:

``` 
test
```

Si el agente de servidor de JMeter está configurado y escuchando correctamente, deberá indicar que recibió el comando y responder con el mensaje *Yep*.

> [AZURE.NOTE] Puede escribir otros comandos para obtener datos de supervisión de rendimiento. Por ejemplo, el comando `metric-single:cpu:idle` le dará la proporción actual de tiempo que la CPU está inactiva (esto es una instantánea). Para obtener una lista completa de los comandos, visite la página del [agente de servidor de PerfMon](http://jmeter-plugins.org/wiki/PerfMonAgent/).

En la sesión de telnet, escriba el siguiente comando para salir de la sesión y volver al símbolo del sistema de bash:

``` 
exit
```

> [AZURE.NOTE] Al igual que con las máquinas virtuales subordinadas de JMeter, si cierra la sesión o si este equipo se apaga y se reinicia, el agente de servidor de JMeter deberá reiniciarse manualmente con el comando `startAgent.sh`. Si desea que el agente de servidor de JMeter se inicie automáticamente, agregue el siguiente comando al final del archivo `/etc/rc.local`, antes del comando *exit 0*. Reemplace `<username>` por su nombre de inicio de sesión.

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Reemplace `<username>` por su nombre de inicio de sesión.

Ahora, puede repetir este proceso para todos los demás nodos del clúster de Elasticsearch, o puede usar el comando `scp` para copiar la carpeta del agente de servidor y el contenido en todos los demás nodos y usar el comando `ssh` para iniciar el agente de servidor de JMeter, tal y como se muestra a continuación. Reemplace `<username>` por su nombre de usuario y `<nodename>` por el nombre del nodo donde desea copiar y ejecutar el software (puede que se le pida que proporcione la contraseña cuando se ejecute cada comando):

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Instalación y configuración de JMeter en la máquina virtual maestra de JMeter

En el Portal de Azure, haga clic en *Grupos de recursos*. En la hoja *Grupos de recursos*, haga clic en el grupo de recursos que contiene las máquinas virtuales maestra y subordinadas de JMeter. En la hoja *Grupo de recursos*, haga clic en la máquina virtual maestra de JMeter. En la hoja de la máquina virtual, en la barra de herramientas, haga clic en *Conectar*. Abra el archivo RDP cuando se lo solicite el explorador web. Windows crea una conexión de Escritorio remoto a la máquina virtual. Cuando se le solicite, escriba el nombre de usuario y la contraseña de la máquina virtual.

En la máquina virtual, use Internet Explorer para ir a la página [Descargar Java para Windows](http://www.java.com/en/download/ie_manual.jsp). Siga las instrucciones para descargar y ejecutar el programa de instalación de Java.

En el explorador web, vaya a la página [Download Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) (Descargar Apache JMeter) y descargue el archivo zip que contiene el binario más reciente. Guarde el archivo zip en una ubicación adecuada en la máquina virtual.

Vaya al sitio [Custom JMeter Plugins](http://jmeter-plugins.org/) (Complementos de JMeter personalizados) y descargue el conjunto de complementos estándar. Guarde el archivo zip en la misma carpeta que la descarga de JMeter del paso anterior.

En el Explorador de Windows, vaya a la carpeta que contiene el archivo zip apache-jmeter-*xx* (donde *xx* es la versión actual de JMeter) y extraiga los archivos en la carpeta actual (el archivo zip creará una subcarpeta llamada apache-jmeter-*xxx*).

Extraiga los archivos del archivo JMeterPlugins-Standard-*yyy*.zip (donde *yyy* es la versión actual de los complementos) en la carpeta apache-jmeter-*xxx*. Esto agregará los complementos a la carpeta correcta para JMeter (puede combinar las carpetas lib con seguridad y sobrescribir los archivos de licencia y el archivo Léame si se le solicita).

Vaya a la carpeta apache-jmeter-*xxx*/bin y edite el archivo de jmeter.properties con el Bloc de notas. En el archivo `jmeter.properties`, busque la sección llamada *Remote hosts and RMI configuration* (Hosts remotos y configuración de RMI). En esta sección del archivo, busque la línea siguiente:

```yaml
remote_hosts=127.0.0.1
```

Cambie esta línea y reemplace la dirección IP 127.0.0.1 por una lista de direcciones IP o nombres de host separados por coma para cada uno de los servidores subordinados de JMeter IP. Por ejemplo:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Busque la línea siguiente, quite el carácter `#` del principio de esta línea y modifique el valor de la opción de configuración client.rmi.localport de:

```yaml
#client.rmi.localport=0
```

a:

```yaml
client.rmi.localport=4440
```

Guarde el archivo y cierre el Bloc de notas.

En la barra de herramientas de Windows, haga clic en *Inicio*, en *Herramientas administrativas* y en *Firewall de Windows con seguridad avanzada*. En la ventana *Firewall de Windows con seguridad avanzada*, en el panel de la izquierda, haga clic con el botón derecho en *Reglas de entrada* y, a continuación, haga clic en *Nueva regla*.

En el *Asistente* *para nueva regla de entrada*, en la página *Tipo de regla*, seleccione *Puerto* y luego haga clic en *Siguiente*. En la página *Protocolos y puertos*, seleccione *TCP*, seleccione *Puertos locales específicos*, en el cuadro de texto escriba `4440-4444` y haga clic en *Siguiente*. En la pantalla *Acción*, seleccione *Permitir la conexión* y haga clic en *Siguiente*. En la página *Perfil*, deje todas las opciones activadas y haga clic en *Siguiente*. En la página *Nombre*, en el cuadro de texto *Nombre*, escriba *JMeter* haga clic en *Finalizar*. Cierre la ventana *Firewall de Windows con seguridad avanzada*.

En el Explorador de Windows, en la carpeta apache-jmeter-*xx*/bin, haga doble clic en el archivo por lotes de Windows *jmeter* para iniciar la GUI. Aparecerá la interfaz de usuario:

![](./media/guidance-elasticsearch/performance-image17.png)

En la barra de menús, haga clic en *Ejecutar*, haga clic en *Inicio remoto* y compruebe que aparecen las dos máquinas subordinadas de JMeter:

![](./media/guidance-elasticsearch/performance-image18.png)

Ya está preparado para comenzar las pruebas de rendimiento.

## Instalación y configuración de Marvel

La plantilla de inicio rápido de Elasticsearch para Azure instalará y configurará automáticamente la versión de Marvel adecuada si establece los parámetros MARVEL y KIBANA en true cuando cree el clúster:

![](./media/guidance-elasticsearch/performance-image19.png)

Si va a agregar Marvel a un clúster existente, debe realizar manualmente la instalación y el proceso es diferente según si está usando la versión de Elasticsearch 1.7 o 2.x, tal y como se describe en los procedimientos siguientes.

### Instalación de Marvel con Elasticsearch 1.73 o versiones anteriores

Si está usando Elasticsearch 1.7.3 o versiones anteriores, realice los pasos siguientes *en todos los nodos* del clúster:

- Inicie sesión en el nodo y vaya al directorio principal de Elasticsearch. En Linux, el directorio principal típico es `/usr/share/elasticsearch`.

-  Ejecute el comando siguiente para descargar e instalar el complemento Marvel para Elasticsearch:

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Detenga y reinicie Elasticsearch en el nodo:

```bash
sudo service elasticsearch restart
```

- Para comprobar que Marvel se instaló correctamente, abra un explorador web y vaya a la dirección URL `http://<server>:9200/_plugin/marvel`. Reemplace `<server>` por el nombre o la dirección IP de un servidor de Elasticsearch del clúster. Compruebe que aparece una página similar a la que se muestra a continuación:

![](./media/guidance-elasticsearch/performance-image20.png)


### Instalación de Marvel con Elasticsearch 2.0.0 o versiones posteriores

Si está usando Elasticsearch 2.0.0 o versiones posteriores, realice las tareas siguientes *en todos los nodos* del clúster:

Inicie sesión en el nodo y vaya al directorio principal de Elasticsearch (normalmente es `/usr/share/elasticsearch`). Ejecute los comandos siguientes para descargar e instalar el complemento Marvel para Elasticsearch:

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Detenga y reinicie Elasticsearch en el nodo:

```bash
sudo service elasticsearch restart
```

En el procedimiento siguiente, reemplace `<kibana-version>` por 4.2.2 si usa Elasticsearch 2.0.0 o Elasticsearch 2.0.1 o por 4.3.1 si usa Elasticsearch 2.1.0 o versiones posteriores. Reemplace `<marvel-version>` por 2.0.0. si usa Elasticsearch 2.0.0 o Elasticsearch 2.0.1 o por 2.1.0 si usa Elasticsearch 2.1.0 o versiones posteriores. Realice las siguientes tareas *en un nodo* del clúster:

Inicie sesión en el nodo y descargue la versión de Kibana apropiada para su versión de Elasticsearch desde el [sitio web de descarga de Elasticsearch](https://www.elastic.co/downloads/past-releases) y, a continuación, extraiga el paquete:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Abra el puerto 5601 para aceptar las solicitudes entrantes:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Vaya a la carpeta de configuración de Kibana (`kibana-<kibana-version>-linux-x64/config`), edite el archivo `kibana.yml` y agregue la siguiente línea. Reemplace `<server>` por el nombre o la dirección IP de un servidor de Elasticsearch del clúster:

```yaml
elasticsearch.url: "http://<server>:9200"
```

Vaya a la carpeta bin de Kibana (`kibana-<kibana-version>-linux-x64/bin`) y ejecute el comando siguiente para integrar el complemento Marvel en Kibana:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Inicie Kibana:

```bash
sudo nohup ./kibana &
```

Para comprobar la instalación de Marvel, abra un explorador web y vaya a la dirección URL `http://<server>:5601/app/marvel`. Reemplace `<server>` por el nombre o la dirección IP del servidor que ejecuta Kibana.

Compruebe que aparece una página similar a la que se muestra a continuación (el nombre del clúster probablemente variará respecto al que se muestra en la imagen).

![](./media/guidance-elasticsearch/performance-image21.png)

Haga clic en el vínculo que corresponda a su clúster (elasticsearch210 en la imagen anterior). Se mostrará una página similar a la que se muestra a continuación:

![](./media/guidance-elasticsearch/performance-image22.png)

[Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->
