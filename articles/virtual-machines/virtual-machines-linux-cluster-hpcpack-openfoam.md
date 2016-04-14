<properties
 pageTitle="Ejecución de OpenFOAM con HPC Pack en máquinas virtuales de Linux | Microsoft Azure"
 description="Implemente un clúster de Microsoft HPC Pack en Azure y ejecute un trabajo de OpenFOAM en varios nodos de ejecución de Linux en una red RDMA."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/25/2015"
 ms.author="danlep"/>

# Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure

Este artículo muestra cómo implementar un clúster de Microsoft HPC Pack en Azure y ejecutar un trabajo [OpenFoam](http://openfoam.com/) con Intel MPI en varios nodos de ejecución de Linux que se conectan a través de la red de acceso directo a memoria remota (RDMA) de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

OpenFOAM (del inglés operación y manipulación de campo abierto) es un paquete de software gratuito de código abierto de cálculo de dinámicas de fluidos (CFD) que se utiliza ampliamente en ingeniería y ciencias en organizaciones académicas y comerciales. Incluye herramientas para la creación de mallas, especialmente snappyHexMesh, una herramienta de creación de mallas en paralelo para las geometrías complejas de CAD y para el preprocesamiento y el posprocesamiento. Casi todos los procesos se ejecutan en paralelo, lo que permite a los usuarios aprovechar al máximo el hardware del equipo a su disposición.

Microsoft HPC Pack proporciona características para ejecutar una variedad de aplicaciones HPC y paralelas a gran escala, incluidas las aplicaciones MPI, en clústeres de máquinas virtuales de Microsoft Azure. A partir de Microsoft HPC Pack 2012 R2 Update 2, HPC Pack también admite la ejecución de las aplicaciones HPC Linux en máquinas virtuales de nodos de ejecución de Linux implementadas en un clúster de HPC Pack. Consulte [Introducción a los nodos de ejecución de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-cluster-hpcpack.md) para ver información básica relativa al uso de los nodos de ejecución de Linux con HPC Pack.

>[AZURE.NOTE]En este artículo se supone que tiene cierta familiaridad con la administración del sistema Linux y con la ejecución de cargas de trabajo MPI en clústeres de HPC de Linux.

## Requisitos previos

*   **Clúster de HPC Pack con nodos de ejecución de Linux**: consulte [Introducción a los nodos de ejecución de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-cluster-hpcpack.md) para conocer los requisitos previos y los pasos para implementar un clúster de HPC Pack con nodos de ejecución de Linux en Azure mediante un script de Azure PowerShell y las imágenes de HPC Pack en Azure Marketplace. Para ver consideraciones adicionales para usar las instancias de proceso intensivo A8 para tener acceso a la red RDMA de Azure, consulte [Sobre las instancias de proceso intensivo A8, A9, A10 y A11](virtual-machines-a8-a9-a10-a11-specs.md).

    A continuación, se muestra un archivo de configuración XML de ejemplo para usar con el script para implementar un clúster de HPC Pack basado en Azure que consta de un nodo principal de Windows Server 2012 R2 de tamaño A8 y dos nodos de ejecución SUSE Linux Enterprise Server 12 de tamaño A8. Sustituya los valores apropiados por su nombre de suscripción y servicio.

    >[AZURE.NOTE]Actualmente, las redes de Azure de Linux RDMA solo se admiten en las máquinas virtuales creadas desde la imagen habilitada para RDMA de SUSE Linux Enterprise Server 12 en Azure Marketplace (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708).

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **Aspectos adicionales que debe conocer**

    *   Debe implementar todos los nodos de ejecución de Linux en un servicio en la nube para usar la conexión de red RDMA entre los nodos.

    *   Después de implementar los nodos de Linux, si necesita conectarse mediante SSH para realizar tareas administrativas adicionales, debe encontrar los detalles de conexión de SSH para cada máquina virtual de Linux en el portal de Azure.
        
*   **Intel MPI**: para ejecutar OpenFOAM en nodos de ejecución de Linux en Azure, necesita el tiempo de ejecución de Intel MPI Library 5 desde el [sitio web Intel.com](https://software.intel.com/es-ES/intel-mpi-library/). En un paso posterior, instalará Intel MPI en las notas de proceso de Linux. Para prepararse, después de registrarse con Intel, siga el vínculo del correo electrónico de confirmación a la página web relacionada y copie el vínculo de descarga para el archivo .tgz para obtener la versión adecuada de Intel MPI. Este artículo se basa en Intel MPI versión 5.0.3.048.

*   **Paquete de origen OpenFOAM**: descargue el software del paquete de origen OpenFOAM para Linux desde el [sitio de OpenFOAM Foundation](http://www.openfoam.org/download/source.php). Este artículo se basa en la versión del paquete de origen 2.3.1, disponible para su descarga como OpenFOAM 2.3.1.tgz. Siga las instrucciones que aparecen más adelante en este artículo para desempaquetar y compilar OpenFOAM en los nodos de ejecución de Linux.

*   **EnSight** (opcional): para ver los resultados de la simulación de OpenFOAM, descargue e instale la versión de Windows del programa de visualización y análisis [EnSight](https://www.ceisoftware.com/download/) en el nodo principal del clúster de HPC Pack. Hay más información de licencia y descarga en el sitio web de EnSight.


## Configuración de la confianza mutua entre nodos de proceso

La ejecución de un trabajo entre nodos en varios nodos de Linux requiere que los nodos confíen entre sí (por **rsh** o **ssh**). Al crear el clúster de HPC Pack con el script de implementación IaaS de Microsoft HPC Pack, el script establece automáticamente una confianza mutua permanente con la cuenta de administrador que especifique. Para los usuarios sin privilegios de administrador que ha creado en el dominio del clúster, tiene que configurar la confianza mutua temporal entre los nodos cuando se les asigna un trabajo y destruir la relación una vez completado el trabajo. Para hacerlo para cada usuario, proporcione un par de claves RSA al clúster que usa HPC Pack para establecer la relación de confianza.

### Generación de un par de claves RSA

Es fácil generar un par de claves RSA, con clave pública y clave privada, mediante la ejecución del comando de Linux **ssh keygen**.

1.	Inicie sesión en un equipo con Linux.

2.	Ejecute el siguiente comando.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]Presione **Entrar** para usar la configuración predeterminada hasta que se complete el comando. No escriba aquí una frase de contraseña. Cuando se le pida una contraseña, solo tiene que presionar la tecla **Entrar**.

    ![Generación de un par de claves RSA][keygen]

3.	Cambie el directorio al directorio ~/.ssh. La clave privada se almacena en id\_rsa y la clave pública en id\_rsa.pub.

    ![Claves públicas y privadas][keys]

### Adición del par de claves al clúster de HPC Pack
1.	Realice una conexión a Escritorio remoto en el nodo principal con la cuenta de administrador de HPC Pack (la cuenta de administrador que configuró al ejecutar el script de implementación).

2. Use los procedimientos estándar de Windows Server para crear una cuenta de usuario de dominio en el dominio de Active Directory del clúster. Por ejemplo, use la herramienta Usuario y equipos de Active Directory en el nodo principal. Los ejemplos de este artículo asumen que crean un usuario de dominio denominado hpclab\\hpcuser.

3.	Cree un archivo denominado C:\\cred.xml y copie los datos de la clave RSA en él. Puede encontrar un ejemplo de este archivo en los archivos de ejemplo, al final de este artículo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	Abra un símbolo del sistema y escriba el siguiente comando para establecer los datos de credenciales para la cuenta hpclab\\hpcuser. Use el parámetro **extendeddata** para pasar el nombre del archivo C:\\cred.xml que creó para los datos clave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando se completa correctamente sin salida. Después de establecer las credenciales para las cuentas de usuario que se necesitan para ejecutar los trabajos, guarde el archivo cred.xml en una ubicación segura o elimínelo.

5.	Si generó el par de claves RSA en uno de los nodos de Linux, no se olvide de eliminar las claves después de acabar de usarlas. HPC Pack no establece una confianza mutua si encuentra un archivo id\_rsa o id\_rsa.pub existente.

>[AZURE.IMPORTANT]No se recomienda ejecutar un trabajo de Linux como administrador de clústeres en un clúster compartido, porque un trabajo enviado por un administrador se ejecuta bajo la cuenta raíz en los nodos de Linux. Un trabajo enviado por un usuario sin privilegios de administrador se ejecuta bajo una cuenta de usuario de Linux local con el mismo nombre que el usuario del trabajo y HPC Pack establece una confianza mutua para este usuario de Linux en todos los nodos asignados al trabajo. Puede configurar el usuario de Linux manualmente en los nodos de Linux antes de ejecutar el trabajo o bien HPC Pack crea el usuario automáticamente cuando se envía el trabajo. Si HPC Pack crea el usuario, HPC Pack lo elimina una vez finalizado el trabajo. Se quitan las claves después de la finalización del trabajo en los nodos para reducir las amenazas de seguridad.

## Configuración de un recurso compartido de archivos para nodos de Linux

Ahora configure un recurso compartido SMB estándar en una carpeta en el nodo principal y monte la carpeta compartida en todos los nodos de Linux para permitir que los nodos de Linux tengan acceso a los archivos de la aplicación con una ruta de acceso común. Si lo desea, puede usar otra opción para compartir archivos, como un recurso compartido de archivos de Azure, recomendado para muchos escenarios, o un recurso compartido NFS. Consulte la información y los pasos detallados sobre el uso compartido de archivos en [Introducción a los nodos de ejecución de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-cluster-hpcpack.md).

1.	Cree una carpeta en el nodo principal y compártala con todos los usuarios estableciendo privilegios de lectura y escritura. Por ejemplo, comparta C:\\OpenFOAM en el nodo principal como \\\SUSE12RDMA-HN\\OpenFOAM. En donde *SUSE12RDMA-HN* es el nombre de host del nodo principal.

2.	Abra una ventana de Windows PowerShell y ejecute los siguientes comandos para montar la carpeta compartida.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

El primer comando crea una carpeta denominada /openfoam en todos los nodos del grupo LinuxNodes. El segundo comando monta la carpeta compartida //SUSE12RDMA-HN/OpenFOAM en los nodos de Linux con los bits dir\_mode y file\_mode establecidos en 777. El *nombre de usuario* y la *contraseña* del comando deben ser las credenciales de un usuario en el nodo principal.

>[AZURE.NOTE]El símbolo “`” del segundo comando es un símbolo de escape de PowerShell. “`,” significa que “,” (coma) forma parte del comando.

## Instalación de MPI y OpenFOAM

Para ejecutar OpenFOAM como un trabajo de MPI en la red RDMA, debe compilar OpenFOAM con las bibliotecas de Intel MPI.

En primer lugar, ejecutará varios comandos **clusrun** para instalar las bibliotecas de Intel MPI y OpenFOAM en todos los nodos de Linux. Use el recurso compartido de nodo principal configurado previamente para compartir los archivos de instalación entre los nodos de Linux.

>[AZURE.IMPORTANT]Estos pasos de instalación y compilación son ejemplos y requieren cierto conocimiento de administración del sistema Linux, especialmente para asegurarse de que los compiladores y las bibliotecas dependientes están instalados correctamente. Tendrá que modificar ciertas variables de entorno u otros valores necesarios según las versiones de Intel MPI y OpenFOAM. Para más información, consulte [Intel MPI Library for Linux Installation Guide](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html) (Guía de instalación de la biblioteca Intel MPI para Linux) y [OpenFOAM Source Pack Installation](http://www.openfoam.org/download/source.php) (Instalación del paquete de origen de OpenFOAM).


### Instalación de Intel MPI

Guarde el paquete de instalación descargado para Intel MPI (l\_mpi\_p\_5.0.3.048.tgz en este ejemplo) en C:\\OpenFoam en el nodo principal para que los nodos de Linux puedan tener acceso a este archivo desde /openfoam. A continuación, ejecute **clusrun** para instalar la biblioteca Intel MPI en todos los nodos de Linux.

1.  Los siguientes comandos permiten copiar el paquete de instalación y extraerlo a /opt/intel en cada nodo.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Para instalar la biblioteca Intel MPI de forma silenciosa, use un archivo silent.cfg. Puede encontrar un ejemplo en los archivos de ejemplo, al final de este artículo. Coloque este archivo en la carpeta compartida /openfoam. Para más información acerca del archivo silent.cfg, consulte [Intel MPI Library for Linux Installation Guide - Silent Installation](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall) (Guía de instalación de la biblioteca Intel MPI para Linux).

    >[AZURE.TIP]Asegúrese de que guarda el archivo silent.cfg como un archivo de texto con finales de línea de Linux (solo LF, no CR LF). Esto garantiza que se ejecuta correctamente en los nodos de Linux.

3.  Instale la biblioteca Intel MPI en modo silencioso.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### Configuración de MPI

Para realizar las pruebas, debe agregar las siguientes líneas al archivo /etc/security/limits.conf en cada uno de los nodos de Linux:

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

Reinicie los nodos de Linux después de actualizar el archivo limits.conf. Por ejemplo, use el siguiente comando **clusrun**.

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Después de reiniciar, asegúrese de que la carpeta compartida está montada como /openfoam.

### Compilación e instalación de OpenFOAM

Guarde el paquete de instalación descargado para el paquete de origen de OpenFOAM (OpenFOAM-2.3.1.tgz en este ejemplo) en C:\\OpenFoam en el nodo principal para que los nodos de Linux puedan tener acceso a este archivo desde /openfoam. A continuación, ejecute **clusrun** para compilar OpenFOAM en todos los nodos de Linux.


1.  Cree una carpeta /opt/OpenFOAM en cada nodo de Linux, copie el paquete de origen en esta carpeta y extráigalo allí.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Para compilar OpenFOAM con la biblioteca Intel MPI, configure primero algunas variables de entorno tanto para Intel MPI como para OpenFOAM. Para ello puede utilizar un script de bash llamado settings.sh. Puede encontrar un ejemplo en los archivos de ejemplo, al final de este artículo. Coloque este archivo (guardado con el fin de línea de Linux) en la carpeta compartida /openfoam. Este archivo también contiene la configuración para los tiempos de ejecución de MPI y OpenFOAM que utiliza posteriormente para ejecutar un trabajo de OpenFOAM.

3. Instale los paquetes dependientes necesarios para compilar OpenFOAM. Dependiendo de la distribución de Linux, puede que primero necesite agregar un repositorio para hacerlo. Ejecute los comandos **clusrun** que sean similares al siguiente:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Si fuera necesario, use el script SSH con cada nodo de Linux para ejecutar los comandos y así confirmar que se ejecutan correctamente.

4.  Ejecute el siguiente comando para compilar OpenFOAM. El proceso de compilación tardará algún tiempo en completarse y generará una gran cantidad de información de registro en la salida estándar, así que use la opción **/ interleaved** para mostrar el resultado intercalado.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]El símbolo “`” en el comando es un símbolo de escape de PowerShell. “`&” significa que “&” forma parte del comando.

## Preparación para ejecutar un trabajo de OpenFOAM

Prepárese ahora para ejecutar un trabajo MPI denominado sloshingTank3D, que es uno de los ejemplos de OpenFoam, en 2 nodos de Linux.

### Configuración de entorno en tiempo de ejecución

Ejecute el siguiente comando en una ventana de Windows PowerShell en el nodo principal para configurar los entornos en tiempo de ejecución para MPI y OpenFOAM en todos los nodos de Linux. (Este comando sólo es válido para SUSE Linux).

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### Preparación de datos de ejemplo

Use el recurso compartido del nodo principal configurado anteriormente para compartir archivos entre los nodos de Linux (montados como /openfoam).

1.  Use el script ssh con uno de los nodos de ejecución de Linux.

2.  Ejecute el siguiente comando para configurar el entorno en tiempo de ejecución de OpenFOAM si todavía no lo ha hecho.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copie el ejemplo sloshingTank3D en la carpeta compartida y navegue hasta él.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Cuando use los parámetros predeterminados de este ejemplo, puede tardar decenas de minutos o más en ejecutarse, por lo que si lo desea, puede modificar algunos parámetros para que se ejecute más rápido. Una opción sencilla es modificar las variables de período deltaT y writeInterval en el archivo system/controlDict, que almacena todos los datos de entrada sobre el control de tiempo y los datos de la solución de lectura y escritura. Por ejemplo, puede cambiar el valor de deltaT de 0,05 a 0,5 y el valor de writeInterval de 0,05 a 0,5.

    ![Modificar variables de paso][step_variables]

5.  Especifique los valores deseados para las variables en el archivo system/decomposeParDict. Este ejemplo utiliza 2 nodos de Linux con 8 núcleos cada uno, así que establezca numberOfSubdomains en 16 y n de hierarchicalCoeffs en (1 1 16), lo que significa ejecutar OpenFOAM en paralelo con 16 procesos. Para obtener más información acerca de cómo ejecutar OpenFOAM en paralelo, consulte [Guía del usuario de OpenFOAM: 3.4 Ejecución de aplicaciones en paralelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Descomponer procesos][decompose]

6.  Ejecute los siguientes comandos desde el directorio sloshingTank3D para preparar los datos de ejemplo.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  En el nodo principal, debería ver que los archivos de datos de ejemplo se copian en C:\\OpenFoam\\sloshingTank3D. (C:\\OpenFoam es la carpeta compartida en el nodo principal).

    ![Archivos de datos en el nodo principal][data_files]

### Archivo de host para mpirun

En este paso creará un archivo de host (una lista de nodos de ejecución) que el comando **mpirun** usará.

1.	En uno de los nodos de Linux, cree un nuevo archivo denominado hostfile en /openfoam, de tal modo que se pueda llegar a este archivo en /openfoam/hostfile en todos los nodos de Linux.

2.	Escriba los nombres de los nodos de Linux en este archivo. En este ejemplo, el archivo tiene el siguiente aspecto:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]También puede crear este archivo en C:\\OpenFoam\\hostfile en el nodo principal. En ese caso, guarde el script como un archivo de texto con finales de línea de Linux (solo LF, no CR LF). Esto garantiza que se ejecuta correctamente en los nodos de Linux.

    **Contenedor de script de Bash**

    Si tiene muchos nodos de Linux y el trabajo se ejecutará solo en algunos de ellos, no es una buena idea usar un archivo de host fijo, ya que no sabe qué nodos se asignarán al trabajo. En este caso, escriba un contenedor de script de Bash para **mpirun** y así poder crear el archivo de host automáticamente. Puede encontrar un contenedor de script de Bash de ejemplo denominado hpcimpirun.sh en los archivos de emplo que están al final de este artículo, y guardarlo como /openfoam/hpcimpirun.sh. Este script de ejemplo hace lo siguiente:

    1.	Configura las variables de entorno para **mpirun** y algunos parámetros de comando adicionales para ejecutar el trabajo MPI a través de la red RDMA. En este caso, establece lo siguiente:

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	Crea un archivo de host según la variable del entorno $CCP\_NODES\_CORES, que establece el nodo principal de HPC cuando se activa el trabajo.

        El formato de $CCP\_NODES\_CORES sigue este patrón:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        donde

        * `<Number of nodes>`: es el número de nodos asignados a este trabajo.  
        
        * `<Name of node_n_...>`: es el nombre de cada nodo asignado a este trabajo.
        
        * `<Cores of node_n_...>`: es el número de núcleos en el nodo asignado a este trabajo.

        Por ejemplo, si el trabajo necesita 2 núcleos para ejecutarse, $CCP\_NODES\_CORES será similar a
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	Llama al comando **mpirun** y anexa 2 parámetros a la línea de comandos.

        * `--hostfile <hostfilepath>: <hostfilepath>`: es la ruta de acceso del archivo de host que crea el script

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`: es una variable de entorno establecida por el nodo principal de HPC Pack, que almacena el número total de núcleos asignados a este trabajo. En este caso, especifica el número de procesos de **mpirun**.


## Envío de un trabajo OpenFOAM

Ahora puede enviar un trabajo en el Administrador de clústeres HPC. Debe pasar el script hpcimpirun.sh en las líneas de comandos para algunas de las tareas del trabajo.

1. Conéctese al nodo principal del clúster e inicie el administrador de clústeres de HPC.

2. En **Administración de recursos**, asegúrese de que los nodos de ejecución de Linux están en el estado **En línea**. Si no lo están, selecciónelos y haga clic en **Conectar**.

3.  En **Administración de trabajos**, haga clic en **Nuevo trabajo**.

4.  Escriba un nombre para el trabajo como _sloshingTank3D_.

    ![Detalles del trabajo][job_details]

5.	En **Recursos del trabajo**, seleccione el tipo de recurso como “Nodo” y establezca el valor de Mínimo en 2. Esto hará que se ejecute el trabajo en 2 nodos de Linux, cada uno de los cuales tiene 8 núcleos en este ejemplo.

    ![Recursos del trabajo][job_resources]

6.	Agregue 4 tareas al trabajo con las siguientes líneas de comandos y configuraciones para las tareas.

    >[AZURE.NOTE]La ejecución de `source /openfoam/settings.sh` configura los entornos en tiempo de ejecución de OpenFOAM y MPI, por lo que cada una de las siguientes tareas la llama antes que el comando OpenFOAM.

    *   **Tarea 1**. Ejecute **decomposePar** para generar archivos de datos para ejecutar **interDyMFoam** en paralelo.
    
        *   Asigne 1 nodo a la tarea

        *   **Línea de comandos**: `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Directorio de trabajo**: /openfoam/sloshingTank3D
        
        Consulte la siguiente figura. Configure las tareas restantes de forma similar.

        ![Detalles de la tarea 1][task_details1]

    *   **Tarea 2**. Ejecute **interDyMFoam** en paralelo para calcular el ejemplo.

        *   Asigne 2 nodos a la tarea

        *   **Línea de comandos**: `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Directorio de trabajo**: /openfoam/sloshingTank3D

    *   **Tarea 3**. Ejecute **reconstructPar** para combinar los conjuntos de directorios de tiempo de cada directorio de procesador\_N\_ en un único conjunto de directorios de tiempo.

        *   Asigne 1 nodo a la tarea

        *   **Línea de comandos**: `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Directorio de trabajo**: /openfoam/sloshingTank3D

    *   **Tarea 4**. Ejecute **foamToEnsight** en paralelo para convertir los archivos de resultados de OpenFOAM a formato EnSight y colocar los archivos de EnSight en un directorio denominado Ensight en el directorio de casos.

        *   Asigne 2 nodos a la tarea

        *   **Línea de comandos**: `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Directorio de trabajo**: /openfoam/sloshingTank3D

6.	Agregue dependencias a estas tareas por orden ascendente de tareas.

    ![Dependencias de las tareas][task_dependencies]

7.	Haga clic en **Enviar** para ejecutar este trabajo.

    De forma predeterminada, HPC Pack envía el trabajo como cuenta del usuario que inició la sesión actual. Un cuadro de diálogo puede pedirle que escriba el nombre de usuario y la contraseña después de hacer clic en **Enviar**.

    ![Credenciales del trabajo][creds]

    En determinadas condiciones, HPC Pack recuerda la información de usuario que escribió y no muestra este cuadro de diálogo. Para que HPC Pack la muestre de nuevo, escriba lo siguiente en una ventana del símbolo del sistema y después envíe el trabajo.

    ```
    hpccred delcreds
    ```

8.	El trabajo puede tardar desde decenas de minutos a varias horas, según los parámetros que haya definido para el ejemplo. En el mapa de calor, verá el trabajo que se ejecuta en 2 nodos de Linux.

    ![Mapa térmico][heat_map]

    En cada nodo se inician los 8 procesos.

    ![Procesos de Linux][linux_processes]

9.  Cuando finalice el trabajo, busque los resultados del trabajo en las carpetas en C:\\OpenFoam\\sloshingTank3D y los archivos de registro en C:\\OpenFoam.


## Visualización de los resultados en EnSight

Opcionalmente, puede usar [EnSight](https://www.ceisoftware.com/) para visualizar y analizar los resultados del trabajo de OpenFOAM. Para obtener más información acerca de la visualización y la animación en EnSight, consulte esta [guía de vídeo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Después de instalar EnSight en el nodo principal, inícielo.

2.  Abra C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case.

    Verá un depósito en el visor.

    ![Depósito de EnSight][tank]

3.	Cree una **Isosuperficie** a partir de **internalMesh** y, a continuación, elija la variable **alpha\_water**.

    ![Crear una isosuperficie][isosurface]

4.	Establezca el color de la **Isosurface\_part** creada en el paso anterior. Por ejemplo, establézcalo como water blue (azul agua).

    ![Editar color de la isosuperficie][isosurface_color]

5.  Cree un **Isovolumen** desde **muros** seleccionando **muros** en el panel **Piezas** y haga clic en el botón **Isosuperficies** de la barra de herramientas.

6.	En el cuadro de diálogo, seleccione **Tipo** como **Isovolumen** y establezca el valor mínimo de **Intervalo de isovolumen** en 0,5. Haga clic en **Crear con piezas seleccionadas** para crear el isovolumen.

7.	Establezca el color de la **Iso\_volume\_part** creada en el paso anterior. Por ejemplo, establézcalo como deep water blue (azul marino).

8.	Establezca el color para **muros**. Por ejemplo, establézcalo en transparent white (blanco transparente).

9. Ahora haga clic en **Reproducir** para ver los resultados de la simulación.

    ![Depósito resultante][tank_result]

## Archivos de ejemplo


### Archivo cred.xml de ejemplo

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### Archivo de ejemplo silent.cfg

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### Script de ejemplo settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###Script hpcimpirun.sh de ejemplo

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/linux_processes.png

<!---HONumber=AcomDC_1203_2015-->