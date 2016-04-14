<properties 
	pageTitle="Notas de la versión: Emulador de Microsoft HDInsight para Azure | Microsoft Azure" 
	description="Obtenga información de última hora sobre las versiones más recientes del emulador de HDInsight Hadoop, un entorno de espacio aislado de Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="jgao"/>




# Notas de la versión: Emulador de Microsoft HDInsight para Hadoop 



> [AZURE.NOTE] 
La forma más sencilla de comprobar el número de versión es mirar en Agregar/Quitar programas en la entrada de **Emulador de Microsoft HDInsight para Azure** (para la versión 1.0.0.0 o superior) o **Microsoft HDInsight Developer Preview** (para versiones inferiores a 1.0.0.0).

## Versión 2.0.0.0, 29 de agosto de 2014

* En esta versión se actualiza el emulador de HDInsight para dirigirse al mismo conjunto de proyectos de Hadoop actualmente activos en la versión 3.1 del servicio.    

* Como ocurre con las vistas previas de este producto, esta versión continúa dirigiéndose a escenarios para el desarrollador y, en consecuencia, solo es compatible con implementaciones de un solo nodo.

### Novedades 
 
* [Versiones de componentes de Hadoop actualizadas](hdinsight-component-versioning.md) correspondientes a la versión 3.1 del servicio. Esta versión incluye compatibilidad con Tez y Hive 0.13.

## Versión 1.0.0.0, 28 de octubre de 2013

* Esta es la versión disponible con carácter general del emulador de Microsoft HDInsight para Azure, anteriormente conocido como Microsoft HDInsight Developer Preview. 

* Como ocurre con las vistas previas de este producto, esta versión continúa dirigiéndose a escenarios para el desarrollador y, en consecuencia, solo es compatible con implementaciones de un solo nodo.

### Novedades 
 
* Se han agregado scripts para simplificar la configuración de todos los servicios de Apache Hadoop para el inicio automático o manual. El valor predeterminado seguirá siendo igual de automático que antes, pero todos los servicios pueden cambiarse ahora mediante los scripts set-onebox-autostart.cmd o set-onebox-manualstart.cmd instalados en C:\\Hadoop. 

* El número de dependencias de instalación necesarias se ha reducido notablemente, lo que permite instalaciones más rápidas.

* Esta versión incluye una corrección de errores en el comando usado para ejecutar muestras de Pig en el script RunSamples.ps1 instalado en la carpeta GettingStarted.

* Esta versión contiene una actualización para la versión 1.1 de Hortonworks Data Platform (HDP) que coincide con los servicios de Hortonworks Data Platform disponibles con la versión 1.6 del clúster de HDInsight de Azure.

## Versión 0.11.0.0, 30 de septiembre de 2013

### Novedades 
		
* Se ha quitado el panel de HDInsight. 

* Esta versión contiene una actualización para Hortonworks Data Platform Developer Preview que coincide con Hortonworks Data Platform Preview en HDInsight de Azure.

## Versión 0.10.0.0, 9 de agosto de 2013

### Novedades 
	
* Esta versión contiene una actualización para Hortonworks Data Platform Developer Preview que coincide con Hortonworks Data Platform Preview en HDInsight de Azure.

## Versión 0.8.0.0, 7 de junio de 2013

### Novedades 

* Esta versión contiene una actualización para Hortonworks Data Platform Developer Preview que coincide con Hortonworks Data Platform Preview en HDInsight de Azure.

## Versión 0.6.0.0, 13 de mayo de 2013

### Novedades 

* Hive Server 2 está instalado ahora. Se requiere para la versión 0.9.2.0 de Microsoft ODBC Driver para Hive, que se publicó al mismo tiempo que esta actualización. 

* Todos los servicios se han configurado para iniciarse automáticamente, por lo que no es necesario iniciarlo todo de nuevo después de reiniciar una máquina.

## Versión 0.4.0.0, 25 de marzo de 2013

### Novedades 

* La nueva versión de Microsoft HDInsight Developer Preview y Hortonworks Data Platform para Windows Developer Preview. 

* Incluye Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog y Templeton.

* Nuevo panel de HDInsight con las siguientes características:
 
	* Conexión a varios clústeres, incluida la instalación local y aquellos que se ejecutan remotamente con el servicio HDInsight de Azure. Para obtener más información sobre el servicio HDInsight, consulte [http://azure.microsoft.com/documentation/services/hdinsight/](https://azure.microsoft.com/documentation/services/hdinsight/).
 
	* Configure el almacenamiento de blobs de Azure en el clúster local. Consulte las instrucciones detalladas a continuación.

	* Cree y edite las consultas de Hive en la nueva consola de Hive interactiva.

	* Visualice y descargue los resultados y el historial de trabajos.

### Notas de la versión 

* Se obtiene acceso a los extremos de la API de REST en una instalación local de HDInsight y al servicio HDInsight de Azure a través de distintos números de puerto para los mismos servicios: 

	Local: Oozie: http://localhost:11000/oozie/v1/admin/status Templeton: http://localhost:50111/templeton/v1/status ODBC: usan el puerto 10000 en la cadena de conexión o configuración DSN.

	Servicio HDInsight: Oozie: http://ServerFQDN:563/oozie/v1/admin/status Templeton: http://ServerFQDN:563/templeton/v1/status ODBC: usan el puerto 563 en la cadena de conexión o configuración DSN.


* Configuración del almacenamiento de blobs de Azure en el clúster local:

	En el panel, verá un clúster local predeterminado con el nombre de "local (hdfs)". Si desea usar el almacenamiento de blobs de Azure como almacenamiento para la instalación local, realice los siguientes pasos:

	1. Agregue la etiqueta de cuenta en core-site.xml que se encuentra en C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Ejemplo:

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Abra el shell de comandos de Hadoop en el escritorio en el modo elevado y ejecute el siguiente comando:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Obtenga acceso a cualquier archivo de la cuenta con el URI completo: asv://{container}@{account}/{path} (o asvs:// si desea utilizar HTTPS para acceder a los datos). Ejemplo:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Elimine el clúster local registrado actualmente y vuelva a registrarlo con las nuevas credenciales del almacenamiento de blobs de Azure.


## Versión 0.3.0.0, 13 de diciembre de 2012 

* El sitio web del panel ha cambiado a la autenticación anónima en lugar de usar las credenciales de Windows. De esta forma, se elimina el problema con la solicitud de inicio de sesión que se mencionó en las notas de la versión anterior. 

* Se han corregido algunos errores de Sqoop relacionados con la exportación y algunos tipos de importación.

### Problemas de la versión 

* Se produce un error al cargar la consola JavaScript. Consulte las notas de versión 0.2.0.0 para obtener más información. 
* La línea de comandos de Sqoop mostrará advertencias de la forma que se muestran a continuación. Esto se solucionará en una actualización futura y puede ignorarse con seguridad. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## Versión 0.2.0.0, 3 de diciembre de 2012

* Introducción a la versión semántica para Windows Installer 

* Se han corregido varios errores de instalación notificados en foros de MSDN, especialmente en relación con el panel de HDInsight.

* Se han agregado elementos del menú **Inicio** para ofrecer una mayor detectabilidad.

* Se ha corregido la entrada de varias líneas de la consola de Hive.

* Se han realizado pequeñas actualizaciones en el contenido de inicio.

### Problemas de la versión 

* Se produce un error al cargar la consola JavaScript. 

	* En algunas instalaciones, se producirá un error en la consola JavaScript y se mostrará un error HTTP 404 en la página. Para solucionar esto, diríjase directamente a http://localhost:8080 para usar la consola. 

* Si accede al panel de HDInsight, se realizará una solicitud de inicio de sesión.

	* Hemos recibido varios informes que indican que se muestra un cuadro de diálogo de inicio de sesión al acceder al panel de HDInsight. En ese caso, puede proporcionar la información de inicio de sesión del usuario actual y debería poder acceder al panel. 


## Versión 1.0.0.0, 23 de octubre de 2012

* Versión inicial. 

### Problemas de la versión 

* Consola de Hive 

	* Si se ha incluido una nueva línea en el comando de Hive enviado, obtendrá un error de sintaxis. Quite las nuevas líneas y la consulta debe ejecutarse según lo previsto. 



## Problemas conocidos generales

* Expiración de la contraseña de usuario de Hadoop. 

	La contraseña del usuario de Hadoop puede expirar, según las directivas de Active Directory insertadas en la máquina. Con el siguiente script de Windows PowerShell, la contraseña no expira y puede ejecutarla desde el símbolo del sistema administrativo:

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Directorio temporal
	
	El archivo hadoop.tmp.dir hace referencia a una ubicación incorrecta. en lugar de apuntar a C:\\hadoop\\hdfs, apunta a c:\\hdfs. Este error se corregirá en la siguiente actualización de bits de HDP.

* Restricciones del SO

	HDInsight Server debe instalarse en un SO de 64 bits.

* Resultados de la búsqueda del instalador de plataforma web (WebPI)

	HDInsight no se encuentra en los resultados de búsqueda de WebPI. Esto se debe normalmente a una restricción del SO. HDInsight requiere un sistema operativo de 64 bits con una versión mínima de Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack 1, Windows 8 o Windows Server 2012.

* Documentación de símbolo del sistema administrativo

	* Para ejecutar comandos como **hadoop mradmin** o **hadoop defadmin**, debe realizar la ejecución como usuario de Hadoop. 

	* Para crear fácilmente un shell que se ejecute como ese usuario, abra el símbolo del sistema de Hadoop y ejecute lo siguiente:
	 
	 		start-hadoopadminshell

	* De esta forma, se abrirá un nuevo shell de comandos con privilegios de administrador de Hadoop.

##<a name="nextsteps"></a> Pasos siguientes

- [Introducción al emulador de HDInsight][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 

<!---HONumber=AcomDC_0211_2016-->