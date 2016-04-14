<properties 
   pageTitle="Uso de Apache Phoenix y SQuirreL en HDInsight | Microsoft Azure" 
   description="Aprenda a usar Apache Phoenix en HDInsight y cómo instalar y configurar SQuirreL en su estación de trabajo para conectarse a un clúster de HBase en HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Usar Apache Phoenix y SQuirreL con clústeres de HBase en HDinsight  

Aprenda a usar [Apache Phoenix](http://phoenix.apache.org/) en HDInsight y cómo instalar y configurar SQuirreL en su estación de trabajo para conectarse a un clúster de HBase en HDInsight. Para obtener más información acerca de Phoenix, consulte [Phoenix en 15 minutos o menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para la gramática de Phoenix, vea [Gramática de Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Para obtener información de la versión de Phoenix en HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][hdinsight-versions].

##Uso de SQLLine
[SQLLine](http://sqlline.sourceforge.net/) es una utilidad de línea de comandos para ejecutar SQL.

###Requisitos previos
Antes de usar SQLLine, debe tener lo siguiente:

- **Un clúster de HBase en HDInsight**. Para obtener información sobre el aprovisionamiento del clúster de HBase, consulte [Introducción a HBase Apache en HDInsight][hdinsight-hbase-get-started].
- **Conexión al clúster de HBase a través del protocolo de escritorio remoto**. Para conocer las instrucciones, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure clásico][hdinsight-manage-portal].

**Para averiguar el nombre de host**

1. Abra la **línea de comandos de Hadoop** desde el escritorio.
2. Ejecute el siguiente comando para obtener el sufijo de DNS:

		ipconfig

	Escriba el **sufijo DNS específico de la conexión**. Por ejemplo, *myhbasecluster.f5.internal.cloudapp.net*. Cuando se conecta a un clúster de HBase, necesitará conectarse a uno de los Zookeepers mediante FQDN. Cada clúster de HDInsight tiene 3 Zookeepers. Son *zookeeper0*, *zookeeper1* y *zookeeper2*. El FQDN será algo parecido a *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Para usar SQLLine**

1. Abra la **línea de comandos de Hadoop** desde el escritorio.
2. Ejecute los siguientes comandos para abrir SQLLine:

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	Los comandos usados en el ejemplo:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables;
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

Para obtener más información, vea [Manual de SQLLine](http://sqlline.sourceforge.net/#manual) y [Gramática de Phoenix](http://phoenix.apache.org/language/index.html).


















##Uso de SQuirreL

[Cliente SQL SQuirreL](http://squirrel-sql.sourceforge.net/) es un programa gráfico de Java que le permitirá ver la estructura de una base de datos compatible con JDBC, examinar los datos de tablas, emitir comandos SQL etc. Se puede usar para conectarse a Apache Phoenix en HDInsight.

En esta sección se muestra cómo instalar y configurar SQuirreL en su estación de trabajo para conectarse al clúster de HBase en HDInsight a través de VPN.

###Requisitos previos

Antes de seguir los procedimientos, debe disponer de lo siguiente:

- Un clúster de HBase implementado en una red virtual de Azure con una máquina virtual DNS. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]. 

	>[AZURE.IMPORTANT] Debe instalar un servidor DNS en la red virtual. Para obtener instrucciones, vea [Configuración de DNS entre dos redes virtuales de Azure](hdinsight-hbase-geo-replication-configure-DNS.md).

- Obtenga el sufijo DNS específico de la conexión del clúster de HBase. Para obtenerlo, establezca RDP en el clúster y, a continuación, ejecute IPConfig. El sufijo DNS es similar a:

		myhbase.b7.internal.cloudapp.net
- Descargue e instale [Microsoft Visual Studio Express 2013 para escritorio de Windows](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) en su estación de trabajo. Necesitará makecert del paquete para crear un certificado.  
- Descargue e instale [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) en su estación de trabajo. La versión de cliente SQL SQuirreL 3.0 y superiores requieren la versión 1.6 o superior de JRE.  


###Configuración de una conexión VPN de punto a sitio a la red virtual de Azure

Hay tres pasos implicados en la configuración de una conexión VPN de punto a sitio:

1. [Configuración de una red virtual y una puerta de enlace de enrutamiento dinámico](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Creación de certificados](#Create-your-certificates)
3. [Configuración del cliente VPN](#Configure-your-VPN-client)

Consulte [Configuración de una conexión VPN de punto a sitio a la red virtual de Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md) para obtener más información.

#### Configuración de una red virtual y una puerta de enlace de enrutamiento dinámico

Asegúrese de que ha realizado el aprovisionamiento de un clúster de HBase en una red virtual de Azure (consulte los requisitos previos de esta sección). El siguiente paso es configurar una conexión punto a sitio.

**Para configurar la conectividad punto a sitio**

1. Inicie sesión en el [Portal de Azure clásico][azure-portal].
2. A la izquierda, haga clic en **REDES**.
3. Haga clic en la red virtual que ha creado (consulte [Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]).
4. Haga clic en **CONFIGURAR** en la parte superior.
5. En la sección **Conectividad punto a sitio**, seleccione **Configurar la conectividad punto a sitio**. 
6. Configure **Dirección IP de inicio** y **CIDR** para especificar el intervalo de direcciones IP desde la que los clientes de VPN recibirán una dirección IP cuando se conecten. El intervalo no puede coincidir con cualquiera de los intervalos de la red local y la red virtual de Azure a la que se va a conectar. Por ejemplo. Si selecciona 10.0.0.0/20 para la red virtual, puede seleccionar 10.1.0.0/24 para el espacio de direcciones de cliente. Consulte la página [Conectividad punto a sitio][vnet-point-to-site-connectivity] para obtener más información.
7. En la sección de espacios de direcciones de red virtual, haga clic en **Agregar subred de puerta de enlace**.
7. Haga clic en **GUARDAR** en la parte inferior de la página.
8. Haga clic en **SÍ** para continuar. Espere hasta que el sistema haya terminado de realizar el cambio antes de continuar con el siguiente procedimiento.


**Para crear una puerta de enlace de enrutamiento dinámico**

1. En el Portal de Azure clásico, haga clic en **PANEL** en la parte superior de la página.
2. Haga clic en **CREAR PUERTA DE ENLACE** desde la parte inferior de la página.
3. Haga clic en **SÍ** para continuar. Espere hasta que se cree la puerta de enlace.
4. Haga clic en **PANEL** en la parte superior. Verá un diagrama visual de la red virtual:

	![Diagrama virtual de punto a sitio de red virtual de Azure][img-vnet-diagram]

	El diagrama muestra 0 conexiones de cliente. Después de realizar una conexión a la red virtual, se actualizará el número a uno.

#### Creación de certificados

Una forma de crear un certificado X.509 es mediante la herramienta de creación de certificados (makecert.exe) que se incluye con [Microsoft Visual Studio Express 2013 para escritorio de Windows](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).


**Para crear un certificado raíz autofirmado**

1. Abra la ventana del símbolo del sistema desde la estación de trabajo.
2. Navegue hasta la carpeta de herramientas de Visual Studio. 
3. El siguiente comando del siguiente ejemplo creará e instalará un certificado raíz en el almacén de certificados Personal de su estación de trabajo, y también creará un archivo .cer correspondiente que podrá cargar más adelante en el Portal de Azure clásico. 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Cambie al directorio en el que desee que se encuentre el archivo .cer, donde HBaseVnetVPNRootCertificate es el nombre que desea usar para el certificado.

	No cierre el símbolo del sistema. Lo necesitará en el siguiente procedimiento.

	>[AZURE.NOTE] Puesto que ha creado un certificado raíz desde el que se generarán los certificados de cliente, puede que desee exportar este certificado junto con su clave privada y guardarlo en una ubicación segura donde se pueda recuperar.

**Para crear un certificado de cliente**

- Desde el mismo símbolo del sistema (debe estar en el mismo equipo donde ha creado el certificado raíz. Se debe generar el certificado de cliente desde el certificado raíz), ejecute el siguiente comando:

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate es el nombre del certificado raíz. Tiene que coincidir con el nombre del certificado raíz.

	Tanto el certificado raíz como el certificado cliente se almacenan en el almacén de certificados personales del equipo. Use certmgr.msc para comprobar.

	![Certificado VPN de punto a sitio de red virtual de Azure][img-certificate]

	Se debe instalar un certificado de cliente en cada equipo que desee conectar a la red virtual. Se recomienda crear certificados de cliente único para cada equipo que desee conectar a la red virtual. Para exportar los certificados de cliente, use certmgr.msc.

**Para cargar el certificado raíz en el Portal de Azure clásico**

1. En el Portal de Azure clásico, haga clic en **REDES** en la parte izquierda.
2. Haga clic en la red virtual en la que se implementa el clúster de HBase.
3. Haga clic en **CERTIFICADOS** en la parte superior.
4. Haga clic en **CARGAR** desde la parte inferior y especifique el archivo de certificado raíz que ha creado en el procedimiento antes del último. Espere hasta que el certificado se importe.
5. En la parte superior, haga clic en **PANEL**. El diagrama virtual muestra el estado.


#### Configuración del cliente VPN



**Para descargar e instalar el paquete VPN cliente**

1. Desde la página PANEL de la red virtual, en la sección de vista rápida, haga clic en **Descargar el paquete de VPN de cliente de 64 bits** o **Descargar el paquete de VPN de cliente de 32 bits** según la versión de sistema operativo de la estación de trabajo.
2. Haga clic en **Ejecutar** para instalar el paquete.
3. En el símbolo del sistema de seguridad, haga clic en **Obtener más información**, y, a continuación, haga clic en **Ejecutar de todas maneras**.
4. Haga clic en **Sí**

**Para conectarse a VPN**

1. En el escritorio de la estación de trabajo, haga clic en el icono Redes en la barra de tareas. Verá una conexión VPN con el nombre de red virtual.
2. Haga clic en el nombre de la conexión VPN.
3. Haga clic en **Conectar**.

**Para probar la conexión VPN y la resolución de nombre de dominio**

- Desde la estación de trabajo, abra un símbolo del sistema y haga ping en uno de los siguientes nombres teniendo en cuenta que el sufijo DNS del clúster de HBase es myhbase.b7.internal.cloudapp.net:

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###Instalación y configuración de SQuirreL en su estación de trabajo

**Para instalar SQuirreL**

1. Descargue el archivo jar de cliente SQL SQuirreL de [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Abra/ejecute el archivo jar. Requiere [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Haga clic en **Siguiente** dos veces.
4. Especifique una ruta de acceso donde tenga permiso de escritura y, a continuación, haga clic en **Siguiente**.
	>[AZURE.NOTE] La carpeta de instalación predeterminada es C:\\Archivos de programa\\squirrel-sql-3.6. Para poder escribir en esta ruta de acceso, el programa de instalación debe disponer de privilegios de administrador. Puede abrir un símbolo del sistema como administrador, ir a la carpeta bin de Java y, a continuación, ejecutar
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. Haga clic en **Aceptar** para confirmar la creación del directorio de destino.
6. El valor predeterminado es instalar los paquetes Standard y Base. Haga clic en **Siguiente**.
7. Haga clic en **Siguiente** dos veces y, a continuación, en **Hecho**.


**Para instalar al controlador de Phoenix**

El archivo jar del controlador de Phoenix se encuentra en el clúster de HBase. La ruta de acceso es similar a la siguiente según las versiones:

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Debe copiarla en la estación de trabajo en la ruta de acceso [carpeta de instalación de SQuirreL]/lib. La forma más sencilla es aplicar RDP en el clúster y, a continuación, usar las tareas de copia y pega de archivos (CTRL+C y CTRL+V) para copiarlo en su estación de trabajo.

**Para agregar un controlador de Phoenix a SQuirreL**

1. Abra el cliente SQL SQuirreL desde la estación de trabajo.
2. Haga clic en la ficha **Controlador** a la izquierda.
2. Desde el menú **Controladores**, haga clic en **Nuevo controlador**.
3. Escriba la siguiente información:

	- **Nombre**: Phoenix
	- **Dirección URL de ejemplo**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Nombre de la clase**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING] Usuario todo en minúsculas en la dirección URL de ejemplo. Puede usar el cuórum de zookeeper completo en caso de que uno de ellos esté inactivo. Los nombres de host son zookeeper0, zookeeper1 y zookeeper2.

	![Controlador SQuirreL de HBase Phoenix para HDInsight][img-squirrel-driver]
4. Haga clic en **Aceptar**.

**Para crear un alias para el clúster de HBase**

1. Desde SQuirreL, haga clic en la pestaña **Alias** de la izquierda.
2. Desde el menú **Alias** menú, haga clic en **Nuevo alias**.
3. Escriba la siguiente información:

	- **Nombre**: el nombre del clúster de HBase o de cualquier nombre que prefiera.
	- **Controlador**: Phoenix. Debe coincidir con el nombre del controlador que creó en el último procedimiento.
	- **Dirección URL**: se copia la dirección URL de la configuración del controlador. Asegúrese de que al usuario está todo en minúsculas.
	- **Nombre de usuario**: puede ser cualquier texto. Debido a que usa conectividad VPN aquí, el nombre de usuario no se usa en absoluto.
	- **Contraseña**: puede ser cualquier texto.

	![Controlador SQuirreL de HBase Phoenix para HDInsight][img-squirrel-alias]
4. Haga clic en **Probar**. 
5. Haga clic en **Conectar**. Cuando realiza la conexión, SQuirreL tendrá este aspecto:

	![SQuirreL de Phoenix HBase][img-squirrel]

**Para ejecutar una prueba**

1. Haga clic en la ficha **SQL** derecha junto a la ficha **Objetos**.
2. Copie y pegue el código siguiente:

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Haga clic en el botón Ejecutar.

	![SQuirreL de Phoenix HBase][img-squirrel-sql]
4. Vuelva a la ficha **Objetos**.
5. Expanda el nombre de alias y, a continuación, expanda **TABLA**. Verá la nueva tabla que aparece debajo.
 
##Pasos siguientes
En este artículo, ha aprendido cómo utilizar Phoenix Apache en HDInsight. Para obtener más información, consulte:

- [Información general de HBase de HDInsight][hdinsight-hbase-overview]\: HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona acceso aleatorio y una coherencia sólida para grandes cantidades de datos no estructurados y semiestructurados.
- [Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]\: con la integración de redes virtuales, los clústeres de HBase se pueden implementar en la misma red virtual que sus aplicaciones para que estas puedan comunicarse directamente con HBase.
- [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-geo-replication.md): aprenda a configurar la replicación de HBase entre dos centros de datos de Azure. 
- [Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]\: descubra cómo realizar [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) en tiempo real de grandes volúmenes de datos con HBase en un clúster de Hadoop en HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0218_2016-->