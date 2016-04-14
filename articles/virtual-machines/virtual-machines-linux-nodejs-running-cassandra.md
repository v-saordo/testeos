<properties pageTitle="Ejecución de Cassandra con Linux en Azure | Microsoft Azure" description="Ejecución de un clúster de Cassandra en Linux en Máquinas virtuales de Azure desde una aplicación Node.js app" services="virtual-machines" documentationCenter="nodejs" authors="rmcmurray" manager="wpickett" editor="" azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="robmcm"/>

# Ejecución de Cassandra con Linux en Azure y acceso desde Node.js 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## Información general
Microsoft Azure es una plataforma de nube abierta que ejecuta tanto Microsoft como software ajeno a Microsoft que incluye sistemas operativos, servidores de aplicaciones, middleware de mensajería, así como las bases de datos SQL y NoSQL de ambos modelos comerciales y de código abierto. Crear servicios resistentes en nubes públicas, incluido Azure, requiere una cuidadosa planeación y una arquitectura deliberada para ambos servidores de aplicaciones, así como capas de almacenamiento. La arquitectura de almacenamiento distribuido de Cassandra ayuda naturalmente a crear sistemas de alta disponibilidad que son tolerantes a errores de clúster. Cassandra es una base de datos NoSQL de escala de nube mantenida por Apache Software Foundation en cassandra.apache.org; Cassandra está escrita en Java y, por tanto, se ejecuta tanto en plataformas Windows como Linux.

El enfoque de este artículo es mostrar la implementación de Cassandra en Ubuntu como un clúster único y de centro de múltiples datos que aproveche las máquinas virtuales y las redes virtuales de Microsoft Azure. La implementación del clúster para cargas de trabajo optimizadas de producción no se trata en este artículo, ya que requiere la configuración del nodo de múltiples discos, el diseño de la topología adecuada y el modelado de los datos para admitir la replicación, la coherencia de datos, el rendimiento y los requisitos de alta disponibilidad necesarios.

Este artículo se centra fundamentalmente en mostrar lo relacionado con la compilación del clúster de Cassandra en comparación con Docker, Chef o Puppet, por lo que la implementación de la infraestructura se vuelve mucho más sencilla.

## Los modelos de implementación 
Las redes de Microsoft Azure permiten la implementación de clústeres privados aislados, el acceso de los cuales puede restringirse para lograr una mejor seguridad de la red. Dado que este artículo trata de mostrar la implementación de Cassandra en un nivel fundamental, no nos centraremos en el nivel de coherencia y el diseño de almacenamiento óptimo para el rendimiento. Esta es la lista de los requisitos de red de nuestro clúster hipotético:

- Los sistemas externos no tienen acceso a la base de datos Cassandra desde dentro o fuera de Azure
- El clúster de Cassandra tiene que estar detrás de un equilibrador de carga para el tráfico
- Implemente nodos de Cassandra en dos grupos en cada centro de datos para una disponibilidad mejorada 
- Bloquee el clúster para que solo la granja de servidores de aplicación tenga acceso a la base de datos directamente
- Ningún extremo de red pública distinto a SSH
- Cada nodo de Cassandra necesita una dirección IP interna fija

Cassandra puede implementarse en una sola región de Azure o para varias regiones, según la naturaleza distribuida de la carga de trabajo. El modelo de implementación de varias regiones puede aprovecharse para dar servicio a los usuarios finales más cercanos a una geografía determinada a través de la misma infraestructura de Cassandra. La replicación de nodos integrada de Cassandra se encarga de la sincronización de escrituras de varios maestros procedentes de varios centros de datos y presenta una vista coherente de los datos a las aplicaciones. La implementación de varias regiones también puede ayudar a mitigar el riesgo de las interrupciones de servicio de Azure más amplias. La coherencia ajustable de Cassandra y la topología de la replicación le ayudará a satisfacer distintas necesidades RPO de aplicaciones.

### Implementación de una sola región
Se iniciará con una implementación de una sola región y recopilará lo aprendido en la creación de un modelo de varias regiones. La red virtual de Azure se utilizará para crear subredes aisladas, de modo que se pueden cumplir los requisitos de seguridad de red mencionados anteriormente. El proceso descrito en la creación de la implementación de región única usa Ubuntu 14.04 LTS y Cassandra 2.08; sin embargo, el proceso puede adoptar fácilmente las otras variantes de Linux. Estas son algunas de las características sistémicas de la implementación de una sola región.

**Alta disponibilidad:** Los nodos de Cassandra que se muestran en la Ilustración 1 se implementan en dos conjuntos de disponibilidad para que los nodos se repartan entre varios dominios de error para una alta disponibilidad. Las máquinas virtuales que se anotan con cada conjunto de disponibilidad se asignan a dos dominios de error. Microsoft Azure utiliza el concepto de dominio de error para administrar el tiempo de inactividad no planeado (por ejemplo, errores de hardware o software) mientras que el concepto de dominio de actualización (por ejemplo, revisiones y actualizaciones del sistema operativo de host o invitado, actualizaciones de aplicaciones) se utiliza para administrar el tiempo de inactividad programado. Consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](http://msdn.microsoft.com/library/dn251004.aspx) para el rol de dominios de error y de actualización a fin de alcanzar alta disponibilidad.

![Implementación de una sola región](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Ilustración 1: Implementación de una sola región

Tenga en cuenta que en el momento de redactar este artículo, Azure no permite la asignación explícita de un grupo de máquinas virtuales a un dominio de error concreto; por lo tanto, incluso con el modelo de implementación que se muestra en la Figura 1, es estadísticamente probable que todas las máquinas virtuales se puedan asignar a dos dominios de error en lugar de a cuatro.

**Tráfico de Thrift de equilibrio de carga:** Las bibliotecas de cliente de Thrift en el servidor web se conectan al clúster a través de un equilibrador de carga interno. Esto requiere el proceso de agregar el equilibrador de carga interno a la subred "datos" (consulte la Ilustración 1) en el contexto del servicio en la nube que hospeda el clúster de Cassandra. Una vez definido el equilibrador de carga interno, cada nodo requiere agregar el extremo con equilibrio de carga con las anotaciones de un conjunto con equilibrio de carga con el nombre del equilibrador de carga previamente definido. Consulte [Equilibrio de carga interno de Azure ](../load-balancer/load-balancer-internal-overview.md)para obtener más detalles.

**Valores de inicialización de clúster:** Es importante seleccionar los nodos más disponibles para los valores de inicialización, ya que los nuevos nodos se comunicarán con los nodos de valores de inicialización para detectar la topología del clúster. Un nodo de cada conjunto de disponibilidad se designa como nodos de valores de inicialización para evitar un punto único de error.

**Factor de replicación y nivel de coherencia:** La durabilidad de los datos y la alta disponibilidad de la integración de Cassandra se caracterizan por el factor de replicación (RF: número de copias de cada fila almacenada en el clúster) y el nivel de coherencia (número de réplicas para leer/escribir antes de devolver el resultado al autor de llamada). El factor de replicación se especifica durante la creación del espacio de claves (similar a una base de datos relacional), mientras que el nivel de coherencia se especifica al emitir la consulta CRUD. Consulte la documentación de Cassandra en [Configuring for Consistency](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configuración de coherencia) para obtener detalles de la coherencia y la fórmula de cálculo de quórum.

Cassandra admite dos tipos de modelos de integridad de datos: coherencia y coherencia eventual; el factor de replicación y el nivel de coherencia juntos determinarán si los datos serán coherentes en cuanto se completa una operación de escritura o si será coherente con el tiempo. Por ejemplo, al especificar QUORUM como el nivel de coherencia asegura siempre la coherencia de los datos, mientras que cualquier nivel de coherencia por debajo del número de réplicas para escribir según sea necesario para mantener QUORUM (por ejemplo, uno) hará que los datos sean coherentes con el tiempo.

El clúster de 8 nodos mostrado anteriormente, con un factor de replicación de 3 y un nivel de coherencia de lectura/escritura de QUÓRUM (2 nodos leídos o escritos para coherencia), puede superar la pérdida teórica de como máximo 1 nodo por grupo de replicación antes de que la aplicación advierta el error. Se supone que todos los espacios de clave tienen solicitudes de lectura/escritura equilibradas. Los siguientes son los parámetros que se utilizarán en el clúster implementado:

Configuración de clúster de Cassandra de una sola región:

| Parámetro de clúster | Valor | Comentarios |
| ----------------- | ----- | ------- |
| Número de nodos (N) | 8 | Número total de nodos del clúster |
| Factor de replicación (RF) | 3 |	Número de réplicas de una fila determinada |
| Nivel de coherencia (escritura) | QUORUM[(RF/2) +1) = 2] Se redondea a la baja el resultado de la fórmula | Escribe como máximo 2 réplicas antes de enviar la respuesta al llamador; la 3.ª réplica se escribe de forma coherente finalmente. |
| Nivel de coherencia (lectura) | QUORUM[(RF/2) +1= 2] Se redondea a la baja el resultado de la fórmula | Lee 2 réplicas antes de enviar la respuesta al llamador. |
| Estrategia de replicación | NetworkTopologyStrategy vea la [replicación de datos](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) en la documentación de Cassandra para obtener más información | Comprende la topología de implementación y sitúa las réplicas en los nodos para que todas las réplicas no terminen en el mismo bastidor. |
| Snitch | GossipingPropertyFileSnitch. Vea [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) en la documentación de Cassandra para obtener más información | NetworkTopologyStrategy usa un concepto de snitch para entender la topología. GossipingPropertyFileSnitch proporciona un mejor control en la asignación de cada nodo al centro de datos y al bastidor. El clúster utiliza gossip para propagar esta información. Esto es mucho más sencillo en la configuración de IP dinámica en relación con PropertyFileSnitch |


**Consideraciones de Azure para el clúster de Cassandra:** La funcionalidad de las máquinas virtuales de Microsoft Azure usa el almacenamiento de blobs de Azure para la persistencia de disco; Almacenamiento de Azure guarda tres réplicas de cada disco para una gran durabilidad. Esto significa que cada fila de datos que se inserta en una tabla de Cassandra ya está almacenada en tres réplicas y, por tanto, la consistencia de los datos ya está controlada incluso si el Factor de replicación (RF) es 1. El principal problema con el Factor de replicación 1 es que la aplicación experimentará un tiempo de inactividad, incluso si se produce un error en un único nodo de Cassandra. Sin embargo, si un nodo está inactivo para los problemas (por ejemplo, errores de hardware y de software del sistema) reconocidos por el controlador de tejido de Azure, se proporcionará un nuevo nodo en su lugar utilizando las mismas unidades de almacenamiento. El aprovisionamiento de un nuevo nodo para reemplazar el antiguo puede tardar unos minutos. De forma similar para las actividades de mantenimiento planeado, como los cambios en el SO invitado, Cassandra actualiza y la aplicación cambia. El controlador de tejido de Azure realiza actualizaciones sucesivas en los nodos del clúster. Las actualizaciones sucesivas también pueden hacer que haya varios nodos inactivos a la vez y, por tanto, el clúster puede experimentar breves períodos de inactividad para algunas particiones. Sin embargo, los datos no se perderán debido a la redundancia integrada de Almacenamiento de Azure.

Para los sistemas implementados en Azure que no requieren alta disponibilidad (por ejemplo, 99,9 aproximadamente, lo que equivale a 8,76 horas por año; consulte [Alta disponibilidad](http://en.wikipedia.org/wiki/High_availability) para obtener más detalles), es posible que puedan ejecutarse con RF = 1 y nivel de coherencia = uno. Para las aplicaciones con requisitos de alta disponibilidad, RF = 3 y el nivel de coherencia = QUÓRUM tolerarán el tiempo de inactividad de uno de los nodos de las réplicas. El RF = 1 en implementaciones tradicionales (por ejemplo, locales) no se puede utilizar debido a la posible pérdida de datos resultante de problemas como errores de disco.

## Implementación en varias regiones
La replicación compatible del centro de datos de Cassandra y el modelo de coherencia descrito anteriormente ayudan con la implementación de varias regiones sin la necesidad de las herramientas externas. Esto es muy diferente de las bases de datos relacionales tradicionales, donde la escritura del programa de instalación para la creación de reflejo de base de datos de varios maestros puede ser bastante compleja. Cassandra en una configuración de varias regiones puede ayudar en los escenarios de uso, incluidos los siguientes:

**Implementación basada en proximidad:** Las aplicaciones de varios inquilinos, con una asignación clara de los usuarios inquilino a la región, se pueden beneficiar de las bajas latencias del clúster de varias regiones Por ejemplo, los sistemas de administración de aprendizaje para instituciones educativas pueden implementar un clúster distribuido en regiones del Este de EE. UU. y del Oeste de EE. UU. para servir a los campus correspondientes de forma transaccional, así como analítica. Los datos pueden ser coherentes localmente en las lecturas y escrituras en el momento, y pueden ser coherentes con el tiempo entre ambas regiones. Hay otros ejemplos como la distribución de medios, el comercio electrónico y todo lo que sirva a una base de usuario concentrada geográficamente es un buen caso de uso para este modelo de implementación.

**Alta disponibilidad:** La redundancia es un factor clave para lograr una alta disponibilidad de software y hardware; consulte Creación de sistemas en nube confiables en Microsoft Azure para obtener más información. En Microsoft Azure, la única manera confiable de lograr una verdadera redundancia es mediante la implementación de un clúster de varias regiones. Se pueden implementar aplicaciones en modo activo-activo o activo-pasivo y si una de las regiones está inactiva, el Administrador de tráfico de Azure puede redirigir el tráfico a la región activa. Con la implementación de una sola región, si la disponibilidad es de 99,9, una implementación de dos regiones puede alcanzar una disponibilidad de 99,9999 calculada mediante la fórmula: (1-(1-0.999) * (1-0,999)) * 100); consulte la nota anterior para obtener más información.

**Recuperación ante desastres:** El clúster de Cassandra de varias regiones, si está correctamente diseñado, puede resistir interrupciones del centro de datos por catástrofes. Si una región está inactiva, la aplicación implementada en otras regiones puede empezar a servir a los usuarios finales. Al igual que cualquier otra implementación de continuidad empresarial, la aplicación tiene que ser tolerante a errores de pérdida de datos resultantes de los datos en la canalización asincrónica. Sin embargo, Cassandra hace que la recuperación sea mucho más rápida que el tiempo empleado por los procesos de recuperación de bases de datos tradicionales. La Ilustración 2 muestra el modelo de implementación típica de varias regiones de ocho nodos en cada región. Ambas regiones son imágenes reflejadas entre sí para la misma de simetría; los diseños reales dependen del tipo de carga de trabajo (por ejemplo, transaccional o analítico), el RPO, RTO, la coherencia de los datos y los requisitos de disponibilidad.

![Implementación de varias regiones](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Ilustración 2: Implementación de Cassandra en varias regiones

### Integración de red
Los conjuntos de máquinas virtuales implementados en las redes privadas que se encuentran en dos regiones se comunican entre sí mediante un túnel VPN. El túnel VPN conecta dos puertas de enlace software aprovisionados durante el proceso de implementación de la red. Ambas regiones tienen una arquitectura de red similar en términos de subredes "web" y de "datos"; las redes de Azure permiten la creación de tantas subredes como sea necesario y aplican las ACL según sea necesario para la seguridad de la red. Al diseñar la topología de clúster, debe tenerse en cuenta la latencia de comunicación entre centros de datos y el impacto económico del tráfico de red.

### Coherencia de los datos para la implementación de varios centros de datos
Las implementaciones distribuidas deben tener en cuenta el impacto de la topología de clúster en el rendimiento y la alta disponibilidad. El nivel de coherencia y el RF deben seleccionarse de manera que el quórum no dependa de la disponibilidad de los centros de datos. Para un sistema que necesita una coherencia alta, un LOCAL\_QUORUM de nivel de coherencia (para lecturas y escrituras) se asegurará de que las lecturas y escrituras locales se atiendan desde los nodos locales mientras los datos se replican de forma asincrónica a los centros de datos remotos. La tabla 2 resume los detalles de configuración para el clúster de varias regiones descritos más adelante.

**Configuración del clúster de Cassandra de dos regiones**


| Parámetro de clúster | Valor | Comentarios |
| ----------------- | ----- | ------- |
| Número de nodos (N) | 8 + 8 | Número total de nodos del clúster |
| Factor de replicación (RF) | 3 | Número de réplicas de una fila determinada |
| Nivel de coherencia (escritura) | LOCAL\_QUORUM [(sum(RF)/2) +1) = 4] Se redondea a la baja el resultado de la fórmula | Se escribirán 2 nodos en el primer centro de datos de forma sincrónica; los 2 nodos adicionales necesarios para el quórum se escribirán de forma asincrónica en el segundo centro de datos. |
| Nivel de coherencia (lectura) | LOCAL\_QUORUM ((RF/2) + 1) = 2Se redondea a la baja el resultado de la fórmula | Las solicitudes de lectura se atienden desde solo una región; 2 nodos se leen antes de enviar la respuesta al cliente. |
| Estrategia de replicación | NetworkTopologyStrategy vea la [replicación de datos](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) en la documentación de Cassandra para obtener más información | Comprende la topología de implementación y sitúa las réplicas en los nodos para que todas las réplicas no terminen en el mismo bastidor. |
| Snitch | GossipingPropertyFileSnitch. Vea [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) en la documentación de Cassandra para obtener más información | NetworkTopologyStrategy usa un concepto de snitch para entender la topología. GossipingPropertyFileSnitch proporciona un mejor control en la asignación de cada nodo al centro de datos y al bastidor. El clúster utiliza gossip para propagar esta información. Esto es mucho más sencillo en la configuración de IP dinámica en relación con PropertyFileSnitch | 
 

##LA CONFIGURACIÓN DE SOFTWARE
Las siguientes versiones de software se utilizan durante la implementación:

<table>
<tr><th>Software</th><th>Origen</th><th>Versión</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Puesto que la descarga de JRE requiere la aceptación manual de licencia de Oracle, para simplificar la implementación, descargue todo el software necesario en el escritorio para su carga más adelante en la imagen de plantilla Ubuntu que crearemos como paso previo a la implementación del clúster.

Descargue el software anterior en un directorio conocido de descargas (por ejemplo, %TEMP%/downloads en Windows o ~/Downloads en la mayoría de distribuciones de Linux o Mac) en el escritorio local.

### CREAR LA MÁQUINA VIRTUAL DE UBUNTU
En este paso del proceso crearemos una imagen de Ubuntu con el software de requisito previo para que la imagen se pueda reutilizar para el aprovisionamiento de varios nodos de Cassandra.
####PASO 1: Generación de par de claves de SSH
Azure necesita una clave pública X509 con codificación PEM o DER en el momento del aprovisionamiento. Genere un par de claves públicas/privadas con las instrucciones ubicadas en Utilización de SSH con Linux en Azure. Si planea utilizar putty.exe como un cliente SSH en Windows o Linux, debe convertir la clave privada RSA codificada del PEM a formato PPK mediante puttygen.exe; las instrucciones para esto pueden encontrarse en la página web anterior.

####PASO 2: Creación de la máquina virtual de la plantilla de Ubuntu
Para crear la máquina virtual de la plantilla, inicie sesión en el Portal de Azure clásico y use la siguiente secuencia: haga clic en NUEVO, PROCESO, MÁQUINA VIRTUAL, DESDE LA GALERÍA, UBUNTU, Ubuntu Server 14.04 LTS y, después, haga clic en la flecha derecha. Si necesita un tutorial en que se describe cómo crear una máquina virtual de Linux, consulte Creación de una máquina virtual que ejecuta Linux.

En la pantalla "Configuración de la máquina virtual" #1, escriba la siguiente información:

<table>
<tr><th>NOMBRE DEL CAMPO              </td><td>       VALOR DEL CAMPO               </td><td>         COMENTARIOS                </td><tr>
<tr><td>FECHA DE LANZAMIENTO DE LA VERSIÓN    </td><td> Seleccione una fecha del menú desplegable</td><td></td><tr>
<tr><td>NOMBRE DE MÁQUINA VIRTUAL    </td><td> cass-template	               </td><td> El nombre de host de la máquina virtual </td><tr>
<tr><td>NIVEL	                 </td><td> ESTÁNDAR	                       </td><td> Deje el valor predeterminado              </td><tr>
<tr><td>TAMAÑO	                 </td><td> A1                              </td><td>Seleccione la máquina virtual en función de las necesidades de E/S; Para ello, deje el valor predeterminado </td><tr>
<tr><td> NOMBRE DE USUARIO NUEVO	         </td><td> localadmin	                   </td><td> "admin" es un nombre de usuario reservado en Ubuntu 12.xx y versiones posteriores</td><tr>
<tr><td> AUTENTICACIÓN	     </td><td> Haga clic en la casilla                 </td><td>Seleccione la casilla si desea protección con una clave SSH </td><tr>
<tr><td> CERTIFICADO	         </td><td> nombre de archivo del certificado de clave pública </td><td> Use la clave pública generada previamente</td><tr>
<tr><td> New Password	</td><td> contraseña segura </td><td> </td><tr>
<tr><td> Confirm Password	</td><td> contraseña segura </td><td></td><tr>
</table>

En la pantalla "Configuración de la máquina virtual" #2, escriba la siguiente información:

<table>
<tr><th>NOMBRE DEL CAMPO             </th><th> VALOR DEL CAMPO	                   </th><th> COMENTARIOS                                 </th></tr>
<tr><td> SERVICIO EN LA NUBE	</td><td> Crear un nuevo servicio en la nube	</td><td>El servicio de nube es un recurso de proceso de contenedor como máquinas virtuales</td></tr>
<tr><td> NOMBRE DE DNS DEL SERVICIO EN LA NUBE	</td><td>ubuntu-template.cloudapp.net	</td><td>Defina un nombre de equilibrador de carga independiente de la máquina</td></tr>
<tr><td> REGION/GRUPO DE AFINIDAD/RED VIRTUAL </td><td>	Oeste de EE.&#160;UU.	</td><td> Seleccione una región desde donde las aplicaciones web tienen acceso al clúster de Cassandra</td></tr>
<tr><td>STORAGE ACCOUNT </td><td>	Use el valor predeterminado	</td><td>Use la cuenta de almacenamiento predeterminada o una cuenta de almacenamiento creada previamente en una región determinada</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDAD </td><td>	None </td><td>	Deje este parámetro en blanco</td></tr>
<tr><td>EXTREMOS	</td><td>Use el valor predeterminado </td><td>	Use la configuración predeterminada de SSH </td></tr>
</table>

Haga clic en la flecha a la derecha, deje los valores predeterminados en la pantalla #3 y haga clic en el botón "comprobar" para completar el proceso de aprovisionamiento de VM. Después de unos minutos, la máquina virtual con el nombre "ubuntu-template" debe aparecer con el estado "en ejecución".

###INSTALAR EL SOFTWARE NECESARIO
####PASO 1: Carga de tarballs 
Mediante scp o pscp, copie el software descargado anteriormente en el directorio ~/downloads mediante el comando siguiente:

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Repita el comando anterior para JRE, así como para los bits de Cassandra.

####PASO 2: Preparación de la estructura de directorios y extracción de archivos
Inicie sesión en la máquina virtual y cree la estructura de directorios y extraiga el software como un superusuario mediante la siguiente secuencia de comandos de bash:

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


Si pega esta secuencia de comandos en la ventana de vim, asegúrese de quitar el retorno de carro ('\\r ") mediante el comando siguiente:

	tr -d '\r' <infile.sh >outfile.sh

####Paso 3: Edición de perfil/etc.
Agregue lo siguiente al final:

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####Paso 4: Instalación de JNA para sistemas de producción
Utilice el siguiente script: El siguiente comando instalará jna 3.2.7.jar y jna-platform-3.2.7.jar en el directorio /usr/share.java sudo apt-get install libjna-java

Cree vínculos simbólicos en el directorio de $CASS\_HOME/lib para que el script de inicio de Cassandra pueda encontrar estos JAR:

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####Paso 5: Configuración de cassandra.yaml
Edite cassandra.yaml en cada máquina virtual para reflejar la configuración necesaria para todas las máquinas virtuales [ajustaremos esto durante el aprovisionamiento real]:

<table>
<tr><th>Nombre del campo   </th><th> Valor  </th><th>	Comentarios </th></tr>
<tr><td>cluster_name </td><td>	"CustomerService"	</td><td> Utilice el nombre que refleje la implementación</td></tr> 
<tr><td>listen_address	</td><td>[deje este parámetro en blanco]	</td><td> Elimine "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[deje este parámetro en blanco]	</td><td> Elimine "localhost" </td></tr>
<tr><td>seeds	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>Lista de todas las direcciones IP que están designadas como valores de inicialización.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> NetworkTopologyStrateg lo utiliza para deducir el centro de datos y el bastidor de la máquina virtual</td></tr>
</table>

####Paso 6: Captura de imagen de máquina virtual
Inicie sesión en la máquina virtual con el nombre de host (hk-cas-template.cloudapp.net) y la clave privada de SSH creada anteriormente. Vea cómo utilizar SSH con Linux en Azure para obtener más información sobre cómo iniciar sesión utilizando el comando ssh o putty.exe.

Ejecute la siguiente secuencia de acciones para capturar la imagen:
#####1\. Desaprovisionamiento
Use el comando “sudo waagent –deprovision+user” para quitar la información específica de la instancia de máquina virtual. Consulte [Captura de una máquina virtual de Linux para usar como plantilla](virtual-machines-linux-capture-image.md). Encontrará más detalles en el proceso de captura de imagen.

#####2: Apagado de la máquina virtual
Asegúrese de que la máquina virtual está resaltada y haga clic en el vínculo de cierre de la barra de comandos de la parte inferior.

#####3: Captura de la imagen
Asegúrese de que la máquina virtual está resaltada y haga clic en el vínculo de captura de la barra de comandos de la parte inferior. En la siguiente pantalla, asigne un nombre de imagen (por ejemplo, hk-cas-2-08-ub-14-04-2014071), una descripción de imagen apropiada, haga clic en la marca de verificación para finalizar el proceso de captura.

Esto tardará unos segundos. La imagen debe estar disponible en la sección Mis imágenes de la Galería de imágenes. La VM de origen se eliminará automáticamente después de que la imagen se capture correctamente.

##Proceso de implementación en una sola región
**Paso 1: Creación de la red virtual** Inicie sesión en el Portal de Azure clásico y cree una red virtual con los atributos que se muestran en la tabla. Consulte [Configurar una red virtual solo en la nube en el Portal de Azure clásico](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para obtener pasos detallados del proceso.

<table>
<tr><th>Nombre del atributo de VM</th><th>Valor</th><th>Comentarios</th></tr>
<tr><td>Nombre</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>Region</td><td>Oeste de EE.&#160;UU.</td><td></td></tr>	
<tr><td>Servidores DNS	</td><td>None</td><td>Ignore esto, ya que no estamos usando un servidor DNS</td></tr>
<tr><td>Configurar una VPN de punto a sitio</td><td>None</td><td> Ignore esto</td></tr>
<tr><td>Configurar una VPN de sitio a sitio</td><td>None</td><td> Ignore esto</td></tr>
<tr><td>Espacio de direcciones</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>IP inicial:</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Agregue las siguientes subredes:

<table>
<tr><th>Nombre</th><th>IP inicial:</th><th>CIDR</th><th>Comentarios</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Subred de la granja de servidores web</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Subred para los nodos de la base de datos</td></tr>
</table>

Las subredes web y de datos se pueden proteger mediante grupos de seguridad de red, cuya cobertura está fuera del ámbito de este artículo.

**Paso 2: Aprovisionamiento de máquinas virtuales** Con la imagen creada anteriormente, se crearán las siguientes máquinas virtuales en el servidor de la nube “hk-c-svc-west” y se unirán a las subredes correspondientes, tal y como se muestra a continuación:

<table>
<tr><th>Nombre de máquina    </th><th>Subred	</th><th>Dirección IP	</th><th>El conjunto de disponibilidad</th><th>Controlador de dominio/bastidor</th><th>¿Valor de inicialización?</th></tr>
<tr><td>hk-c1-west-us	</td><td>data	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>Sí</td></tr>
<tr><td>hk-c2-west-us	</td><td>data	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>No </td></tr>
<tr><td>hk-c3-west-us	</td><td>data	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Sí</td></tr>
<tr><td>hk-c4-west-us	</td><td>data	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>No </td></tr>
<tr><td>hk-c5-west-us	</td><td>data	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Sí</td></tr>
<tr><td>hk-c6-west-us	</td><td>data	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>No </td></tr>
<tr><td>hk-c7-west-us	</td><td>data	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Sí</td></tr>
<tr><td>hk-c8-west-us	</td><td>data	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>No </td></tr>
<tr><td>hk-w1-west-us	</td><td>web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
<tr><td>hk-w2-west-us	</td><td>web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
</table>

La creación de la lista anterior de máquinas virtuales requiere el siguiente proceso:

1.  Crear un servicio de nube vacía en una región determinada
2.	Cree una máquina virtual desde la imagen capturada anteriormente y adjúntela a la red virtual creada anteriormente; repita este paso para todas las máquinas virtuales
3.	Agregue un equilibrador de carga interno para el servicio de nube y adjúntelo a la subred "data"
4.	Para cada máquina virtual creada anteriormente, agregue un extremo con equilibrio de carga para tráfico de thrift a través de un conjunto con equilibrio de carga conectado al equilibrador de carga interno creado anteriormente

Puede ejecutar el proceso anterior mediante el Portal de Azure clásico. Use una máquina de Windows (use una máquina virtual en Azure si no tiene acceso a un equipo de Windows) y use el siguiente script de PowerShell para aprovisionar automáticamente las ocho máquinas virtuales.

**Lista 1: script de PowerShell para el aprovisionamiento de máquinas virtuales**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**Paso 3: Configuración de Cassandra en cada máquina virtual**

Inicie sesión en la máquina virtual y realice lo siguiente:

* Edite $CASS\_HOME/conf/cassandra-rackdc.properties para especificar las propiedades del bastidor y del centro de datos:
      
       dc =EASTUS, rack =rack1

* Edite cassandra.yaml para configurar los nodos de inicialización de la forma siguiente:
     
       Valores de inicialización: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Paso 4: Inicio de las máquinas virtuales y prueba del clúster**

Inicie sesión en uno de los nodos (como hk-c1-west-us) y ejecute el cmdlet siguiente para ver el estado del clúster:
       
       nodetool –h 10.1.2.4 –p 7199 status 

Debería ver la pantalla similar a la siguiente para un clúster de 8 nodos:

<table>
<tr><th>Status</th></th>Dirección	</th><th>Carga	</th><th>Tokens	</th><th>Propiedad </th><th>Identificador de host	</th><th>Bastidor</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87,81 KB	</td><td>256	</td><td>38,0&#160;%	</td><td>GUID (eliminado)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41,08 KB	</td><td>256	</td><td>68,9&#160;%	</td><td>GUID (eliminado)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55,29 KB	</td><td>256	</td><td>68,8&#160;%	</td><td>GUID (eliminado)</td><td>rack4</td></tr>
</table>

## Probar el clúster de una sola región
Para probar el clúster, siga estos pasos:

1.    Mediante el cmdlet Get-AzureInternalLoadbalancer de comando de Powershell, obtenga la dirección IP del equilibrador de carga interno (por ejemplo, 10.1.2.101). A continuación se muestra la sintaxis del comando: Get-AzureLoadbalancer –ServiceName "hk-c-svc-west-us” [muestra los detalles del equilibrador de carga interno junto con su dirección IP]
2.	Inicie sesión en la VM de la granja de servidores web (por ejemplo, hk-w1-west-us) mediante Putty o ssh
3.	Ejecute $CASS\_HOME/bin/cqlsh 10.1.2.101 9160 
4.	Utilice los siguientes comandos CQL para comprobar si funciona el clúster:

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

Verá una pantalla como la siguiente:

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Julia </td><td> Doe </td></tr>
</table>

Tenga en cuenta que el espacio de claves que creó en el paso 4 utiliza SimpleStrategy con un replication\_factor de 3. SimpleStrategy se recomienda para las implementaciones de un solo centro de datos, mientras que NetworkTopologyStrategy para implementaciones de varios centros de datos. Un replication\_factor de 3 proporcionará tolerancia a errores de nodo.

##<a id="tworegion"> </a>Proceso de implementación de varias regiones
Aprovechará la implementación de región única completada y repetirá el mismo proceso para la instalación de la segunda región. La diferencia clave entre la implementación de región única o múltiple es la configuración de túnel VPN para la comunicación entre regiones; empezaremos con la instalación de red, aprovisionamiento de las máquinas virtuales y configuración de Cassandra.

###Paso 1: Creación de la red virtual en la segunda región
Inicie sesión en el Portal de Azure clásico y cree una red virtual con los atributos que se muestran en la tabla. Consulte [Configurar una red virtual solo en la nube en el Portal de Azure clásico](../virtual-network/virtual-networks-create-vnet.md) para obtener pasos detallados del proceso.

<table>
<tr><th>Nombre del atributo    </th><th>Valor	</th><th>Comentarios</th></tr>
<tr><td>Nombre	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>Region	</td><td>Este de EE.&#160;UU.</td><td></td></tr>	
<tr><td>Servidores DNS		</td><td></td><td>Ignore esto, ya que no estamos usando un servidor DNS</td></tr>
<tr><td>Configurar una VPN de punto a sitio</td><td></td><td>		Ignore esto</td></tr>
<tr><td>Configurar una VPN de sitio a sitio</td><td></td><td>		Ignore esto</td></tr>
<tr><td>Espacio de direcciones	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>IP inicial:	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>

Agregue las siguientes subredes: <table> <tr><th>Nombre </th><th>IP inicial</th><th>CIDR </th><th>Comentarios</th></tr> <tr><td>web </td><td>10.2.1.0 </td><td>/24 (251) </td><td>Subred para la granja de servidores web</td></tr> <tr><td>datos </td><td>10.2.2.0 </td><td>/24 (251) </td><td>Subnet para los nodos de la base de datos</td></tr> </table>


###Paso 2: Creación de redes locales
Una red local en la red virtual de Azure es un espacio de direcciones de proxy que se asigna a un sitio remoto como una nube privada u otra región de Azure. Este espacio de direcciones proxy se enlaza a una puerta de enlace remota para la red de enrutamiento a los destinos de redes adecuados. Consulte [Configurar una conexión VNet a VNet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para obtener instrucciones acerca de cómo establecer la conexión de red virtual a red virtual.

Cree dos redes locales con los siguientes detalles:

| Nombre de red | Dirección de puerta de enlace VPN | Espacio de direcciones | Comentarios |
| ------------ | ------------------- | ------------- | ------- |
| hk-lnet-map-to-east-us | 23\.1.1.1 | 10\.2.0.0/16 | Al crear la red local, proporcione una dirección de puerta de enlace de marcador de posición. La dirección de puerta de enlace real se completará una vez creada la puerta de enlace. Asegúrese de que el espacio de direcciones coincide exactamente con la red virtual remota correspondiente; en este caso, la red virtual se ha creado en la región Este de EE. UU. |
| hk-lnet-map-to-west-us | 23\.2.2.2 | 10\.1.0.0/16 | Al crear la red local, proporcione una dirección de puerta de enlace de marcador de posición. La dirección de puerta de enlace real se completará una vez creada la puerta de enlace. Asegúrese de que el espacio de direcciones coincide exactamente con la red virtual remota correspondiente; en este caso, la red virtual se ha creado en la región Oeste de EE. UU. |


###Paso 3: Asignación de red "Local" a las redes virtuales respectivas
Desde el Portal de Azure clásico, seleccione cada red virtual, haga clic en "Configurar", consulte "Conectarse a la red local" y seleccione las redes locales con los siguientes detalles:


| Red virtual | Red local |
| --------------- | ------------- |
| hk-vnet-west-us | hk-lnet-map-to-east-us |
| hk-vnet-east-us | hk-lnet-map-to-west-us |


###Paso 4: Creación de puertas de enlace en VNET1 y VNET2
En el panel de las dos redes virtuales, haga clic en Crear puerta de enlace, lo que desencadenará el proceso de aprovisionamiento de puerta de enlace VPN. Después de unos minutos, el panel de cada red virtual debe mostrar la dirección de puerta de enlace real.

###Paso 5: Actualización de redes "Local" con las direcciones correspondientes de "Puerta de enlace"###
Edite las redes locales para reemplazar la dirección IP de puerta de enlace de marcador de posición por la dirección IP real de las puertas de enlace que acaba de aprovisionar. Utilice la asignación siguiente:

<table>
<tr><th>Red local    </th><th>Puerta de enlace de red virtual</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Puerta de enlace de hk-vnet-west-us</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Puerta de enlace de hk-vnet-east-us</td></tr>
</table>

###Paso 6: Actualización de la clave compartida
Use el siguiente script de Powershell para actualizar la clave IPSec de cada puerta de enlace VPN [utilice la clave segura para ambas puertas de enlace]: Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK

###Paso 7: Establecimiento de la conexión de red virtual a la red virtual
Desde el Portal de Azure clásico, use el menú "Panel" de ambas redes virtuales para establecer conexiones de puerta de enlace a puerta de enlace. Utilice los elementos de menú "Conectar" en la barra de herramientas inferior. Después de unos minutos, el panel debe mostrar gráficamente los detalles de conexión.

###Paso 8: Creación de las máquinas virtuales en la región 2 
Cree la imagen de Ubuntu, como se describe en la implementación de la región #1, siguiendo los mismos pasos, o bien copie el archivo de imagen VHD en la cuenta de almacenamiento de Azure ubicada en la región #2 y cree la imagen. Utilice esta imagen y cree la siguiente lista de máquinas virtuales en un nuevo servicio de nube hk-c-svc-east-us:


| Nombre de máquina | Subred | Dirección IP | El conjunto de disponibilidad | Controlador de dominio/bastidor | ¿Valor de inicialización? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| hk-c1-east-us | data | 10\.2.2.4 | hk-c-aset-1 | dc =EASTUS rack =rack1 | Sí |
| hk-c2-east-us | data | 10\.2.2.5 | hk-c-aset-1 | dc =EASTUS rack =rack1 | No |
| hk-c3-east-us | data | 10\.2.2.6 | hk-c-aset-1 | dc =EASTUS rack =rack2 | Sí |
| hk-c5-east-us | data | 10\.2.2.8 | hk-c-aset-2 | dc =EASTUS rack =rack3 | Sí |
| hk-c6-east-us | data | 10\.2.2.9 | hk-c-aset-2 | dc =EASTUS rack =rack3 | No |
| hk-c7-east-us | data | 10\.2.2.10 | hk-c-aset-2 | dc =EASTUS rack =rack4 | Sí |
| hk-c8-east-us | data | 10\.2.2.11 | hk-c-aset-2 | dc =EASTUS rack =rack4 | No |
| hk-w1-east-us | web | 10\.2.1.4 | hk-w-aset-1 | N/D | N/D |
| hk-w2-east-us | web | 10\.2.1.5 | hk-w-aset-1 | N/D | N/D |


Siga las mismas instrucciones de la región #1, pero use el espacio de direcciones 10.2.xxx.xxx.

###Paso 9: Configuración de Cassandra en cada máquina virtual
Inicie sesión en la máquina virtual y realice lo siguiente:

1. Edite $CASS\_HOME/conf/cassandra-rackdc.properties para especificar las propiedades del bastidor y del centro de datos con el formato: dc =EASTUS rack =rack1
2. Edite cassandra.yaml para configurar los nodos de valores de inicialización: Valores de inicialización: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###Paso 10: Inicio de Cassandra
Inicie sesión en cada máquina virtual e inicie Cassandra en segundo plano, ejecutando el comando siguiente: $CASS\_HOME/bin/cassandra

## Probar el clúster de varias regiones
Ya ha implementado Cassandra en 16 nodos, con 8 nodos en cada región de Azure. Estos nodos están en el mismo clúster con el nombre común del clúster y la configuración de nodo de valor de inicialización. Utilice el siguiente proceso para probar el clúster:

###Paso 1: Obtención de la dirección IP de equilibrador de carga interno para ambas regiones mediante PowerShell
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    Tenga en cuenta que aparecerán las direcciones IP (por ejemplo, west - 10.1.2.101, east - 10.2.2.101).

###Paso 2: Ejecución de lo siguiente en la región Oeste tras iniciar sesión en hk-w1-west-us
1.    Ejecute $CASS\_HOME/bin/cqlsh 10.1.2.101 9160 
2.	Ejecute los siguientes comandos CQL:

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Verá una pantalla como la siguiente:

| customer\_id | firstname | Lastname |
| ----------- | --------- | -------- |
| 1 | John | Doe |
| 2 | Julia | Doe |


###Paso 3: Ejecución de lo siguiente en la región Este tras iniciar sesión en hk-w1-east-us:
1.    Ejecute $CASS\_HOME/bin/cqlsh 10.2.2.101 9160 
2.	Ejecute los siguientes comandos CQL:

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Debe ver la misma pantalla que la vista en la región Oeste:


| customer\_id | firstname | Lastname |
|------------ | --------- | ---------- |
| 1 | John | Doe |
| 2 | Julia | Doe |


Ejecute unas cuantas inserciones más y vea las replicadas en la parte west-us del clúster.

## Probar el clúster de Cassandra desde Node.js
Mediante una de las máquinas virtuales de Linux creadas en el nivel "web" previamente, se ejecutará una secuencia de comandos simple de Node.js para leer los datos insertados anteriormente

**Paso 1: Instalación de Node.js y del cliente de Cassandra**

1. Instalar Node.js y NPM
2. Instalar el paquete de nodo "cassandra-client" con NPM
3. Ejecute el script siguiente en el símbolo del shell que muestra la cadena json de los datos recuperados: 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
		                         keyspace: 'system', use_bigints: false };
		   var con = new pooledCon(sysConOptions);
		   con.execute(cql,[],function(err) {
		   if (err) {
		     console.log("Failed to create Keyspace: " + ksName);
		     console.log(err);
		   }
		   else {
		     console.log("Created Keyspace: " + ksName);
		     callback(ksConOptions, populateCustomerData);
		   }
		   });
		   con.shutdown();
		} 
		
		function createColumnFamily(ksConOptions, callback){
		  var params = ['customers_cf','custid','varint','custname',
		                'text','custaddress','text'];
		  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
		var con =  new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) {
		         console.log("Failed to create column family: " + params[0]);
		         console.log(err);
		      }
		      else {
		         console.log("Created column family: " + params[0]);
		         callback();
		      }
		  });
		  con.shutdown();
		} 
		
		//populate Data
		function populateCustomerData() {
		   var params = ['John','Infinity Dr, TX', 1];
		   updateCustomer(ksConOptions,params);
		
		   params = ['Tom','Fermat Ln, WA', 2];
		   updateCustomer(ksConOptions,params);
		}
		
		//update will also insert the record if none exists
		function updateCustomer(ksConOptions,params)
		{
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
		//read the two rows inserted above
		function readCustomer(ksConOptions)
		{
		  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,[],function(err,rows) {
		      if (err) 
		         console.log(err);
		      else 
		         for (var i=0; i<rows.length; i++)
		            console.log(JSON.stringify(rows[i]));
		    });
		   con.shutdown();
		}
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


## Conclusión 
Microsoft Azure es una plataforma flexible que permite la ejecución de Microsoft, así como un software de código abierto, como se muestra en este ejercicio. Los clústeres Cassandra de alta disponibilidad pueden implementarse en un solo centro de datos a través de la propagación de los nodos del clúster en varios dominios de error. También se pueden implementar clústeres Cassandra en varias regiones de Azure geográficamente distantes para sistemas de prueba ante desastres. Azure y Cassandra juntos permiten la construcción de servicios en la nube de recuperación ante desastres, altamente disponibles y altamente escalables, necesarios para los servicios de Internet actuales.

##Referencias##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 

<!---HONumber=AcomDC_0211_2016-->