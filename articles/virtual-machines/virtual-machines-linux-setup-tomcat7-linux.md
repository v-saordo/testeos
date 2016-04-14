<properties
	pageTitle="Configuración de Apache Tomcat en una máquina virtual Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo configurar Apache Tomcat7 con una máquina virtual Azure con Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Configuración de Tomcat7 en una máquina Virtual con Linux mediante Microsoft Azure

Apache Tomcat (o simplemente Tomcat, anteriormente denominado Jakarta Tomcat) es un servidor web y contenedor de servlet de código abierto desarrollado por Apache Software Foundation (ASF). Tomcat implementa los servlets de Java y las especificaciones de JavaServer Pages (JSP) de Sun Microsystems. Asimismo, proporciona un entorno de servidor web de Java HTTP en el que se ejecuta código de Java. En la configuración más sencilla, Tomcat se ejecuta en un proceso de sistema operativo único. Este proceso ejecuta una máquina virtual de Java (JVM). Todas las solicitudes HTTP desde un explorador para Tomcat se procesan como un subproceso independiente en el proceso de Tomcat.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En esta guía, se proporciona información acerca de la instalación de tomcat7 en una imagen de Linux así como sobre la implementación en Microsoft Azure.

Aprenderá a:

-	Crear una máquina virtual en Azure.
-	Preparar la máquina virtual para tomcat7.
-	Instalar tomcat7.

Se supone que el lector ya tiene una suscripción de Azure. Si no, puede registrarse para una evaluación gratuita en [http://azure.microsoft.com](https://azure.microsoft.com/). Si tiene una suscripción de MSDN, consulte [Precios especiales de Microsoft Azure: Ventajas de MSDN, MPN y Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para obtener más información acerca de Azure, consulte [¿Qué es Azure?](https://azure.microsoft.com/overview/what-is-azure/).

En este tema se supone que tiene conocimientos prácticos básicos de tomcat y Linux.

##Fase 1: Creación de una imagen
En esta fase, creará una máquina virtual mediante una imagen de Linux en Azure.

###Paso 1: Generación de una clave de autenticación SSH
SSH es una herramienta importante para los administradores del sistema. Sin embargo, no se recomienda configurar la seguridad del acceso mediante una contraseña determinada por humanos. Los usuarios malintencionados pueden interrumpir su sistema protegido por un nombre de usuario y una contraseña débiles.

La buena noticia es que hay una manera de dejar abierto el acceso remoto sin tener que preocuparse por las contraseñas. El método consta de autenticación con criptografía asimétrica. La clave privada del usuario es la que concede la autenticación. Incluso puede bloquear la cuenta del usuario para denegar completamente la autenticación de contraseña.

Otra ventaja de este método es que no es necesario disponer de distintas contraseñas para iniciar sesión en distintos servidores. Puede realizar la autenticación utilizando la clave privada personal en todos los servidores, con lo que evitará tener que recordar varias contraseñas.

También es posible iniciar sesión requiriendo una contraseña con este método.

Siga estos pasos para generar la clave de autenticación SSH.

1.	Descargue e instale Puttygen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Ejecute PUTTYGEN.EXE.
3.	Haga clic en **Generar** para generar las claves. En el proceso puede aumentar la aleatoriedad al mover el mouse sobre el área en blanco en la ventana. ![][1]
4.	Después del proceso de generar, Puttygen.exe mostrará la clave generada. Por ejemplo: ![][2]
5.	Seleccione y copie la clave pública en **Clave** y guárdela en un archivo denominado publicKey.pem. No haga clic en **Guardar clave pública**, porque el formato de archivo de la clave pública guardada es diferente de la clave pública que queremos.
6.	Haga clic en **Guardar clave privada** y guárdela en un archivo denominado privateKey.ppk.

###Paso 2: Creación de la imagen en el Portal de Azure.
En el [Portal de Azure](https://portal.azure.com/), haga clic en **Nuevo** en la barra de tareas para crear una imagen. Elija la imagen de Linux según sus necesidades. En el ejemplo siguiente se utiliza la imagen de Ubuntu 14.04. ![][3]

Para **Nombre de host**, especifique el nombre de la dirección URL que tanto usted como los clientes de Internet van a usar para tener acceso a esta máquina virtual. Defina la última parte del nombre DNS como, por ejemplo tomcatdemo; Azure generará la dirección URL como tomcatdemo.cloudapp.net.

Para **Clave de autenticación SSH**, copie el valor clave del archivo **publicKey.pem**, que contiene la clave pública generada por puttygen. ![][4]

Configure otras opciones según sea necesario y, luego, haga clic en Crear.

##Fase 2: Preparación de la máquina virtual para Tomcat7
En esta fase, configurará un extremo para el tráfico de tomcat y, a continuación, se conectará a la nueva máquina virtual.
###Paso 1: Apertura del puerto HTTP para permitir el acceso web
Los extremos en Azure constan de un protocolo (TCP o UDP), junto con un puerto público y privado. El puerto privado es el puerto que el servicio está escuchando en la máquina virtual. El puerto público es el puerto al que el servicio de nube de Azure está escuchando externamente para el tráfico entrante, basado en Internet.

El puerto TCP 8080 es el número de puerto predeterminado al que escucha tomcat. Abrir este puerto con un extremo de Azure le permitirá a usted y a otros clientes de Internet tener acceso a páginas de tomcat.

1.	En el Portal de Azure, haga clic en **Examinar** -> **Máquina virtual** y, luego, haga clic en la máquina virtual que creó. ![][5]
2.	Para agregar un extremo a la máquina virtual, haga clic en el cuadro **Extremos**. ![][6]
3.	Haga clic en **Agregar**.  
	1.	Para el **extremo**, escriba un nombre para el extremo en Extremo y, a continuación, escriba 80 en **Puerto público**.  

		Si se establece con el valor 80, no necesitará incluir el número de puerto en la dirección URL que permite obtener acceso a tomcat. Por ejemplo: http://tomcatdemo.cloudapp.net.

		Si se establece en otro valor como, por ejemplo, el 81, deberá agregar el número de puerto a la dirección URL para tener acceso a tomcat. Por ejemplo: http://tomcatdemo.cloudapp.net:81/.
	2.	Escriba 8080 en Puerto privado. De forma predeterminada, tomcat escucha en el puerto TCP 8080. Si se cambió el puerto de escucha predeterminado de tomcat, deberá actualizar el Puerto privado para que sea que el mismo puerto de escucha de tomcat. ![][7]

4.	Haga clic en **Aceptar** para agregar el extremo a la máquina virtual.



###Paso 2: Conexión a la imagen creada
Puede elegir cualquier herramienta SSH para conectarse a la máquina virtual. En este ejemplo, se usa Putty.

En primer lugar, obtenga el nombre DNS de la máquina virtual desde el Portal de Azure. **Haga clic en Examinar** -> **Máquinas virtuales** -> nombre de la máquina virtual -> **Propiedades** y, a continuación, preste atención al campo **Nombre de dominio** del icono **Propiedades**.

Obtenga el número de puerto para las conexiones SSH desde el campo **SSH**. Aquí tiene un ejemplo. ![][8]

Descargue Putty de [aquí](http://www.putty.org/).

Después de descargar, haga clic en el archivo ejecutable PUTTY.EXE. Configure las opciones básicas con el nombre de host y el número de puerto obtenido de las propiedades de la máquina virtual. Aquí tiene un ejemplo: ![][9]

En el panel izquierdo, haga clic en **Conexión** -> **SSH** -> **Autenticación** y, a continuación, haga clic en **Examinar** para especificar la ubicación del archivo **privateKey.ppk** que contiene la clave privada generada por puttygen en la Fase 1: Creación de una imagen. Aquí tiene un ejemplo: ![][10]

Haga clic en **Abrir**. Puede recibir una alerta en un cuadro de mensaje. Si ha configurado el nombre DNS y el número de puerto correctamente, haga clic en **Sí**. ![][11]


Verá lo siguiente: ![][12]

Escriba el nombre de usuario que especificó cuando creó la máquina virtual en la Fase 1: Creación de una imagen. Verá algo parecido a lo siguiente: ![][13]





##Fase 3: Instalación del software
En esta fase, se instalarán Java Runtime Environment, tomcat y otros componentes de tomcat.

###Java Runtime Environment
Tomcat está escrito en Java. Existen dos tipos de kits de desarrollo de Java (JDKs) (OpenJDK y JDK de Oracle). Puede elegir el que más desee.

>AZURE.NOTE: Ambos JDKs tienen casi el mismo código para las clases de la API de Java, aunque el código de la máquina virtual es diferente. En cuanto a las bibliotecas, OpenJDK suele usar bibliotecas abiertas; mientras Oracle tiende a usar las bibliotecas cerradas. No obstante, el JDK de Oracle tiene más clases y algunos errores corregidos. Además, el JDK de Oracle es más estable que OpenJDK.

Los comandos siguientes permiten descargar los distintos JDKs.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	Para descargar el JDK desde el sitio web de Oracle:  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	Para crear un directorio que contenga los archivos JDK:

		sudo mkdir /usr/lib/jvm  

-	Para extraer los archivos JDK en el directorio /usr/lib/jvm/:

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Para establecer el JDK de Oracle como el JVM predeterminado:

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####Prueba:
Puede usar un comando similar al siguiente para comprobar si el entorno de tiempo de ejecución de Java está instalado correctamente:

	java -version  

Si ha instalado open-jdk, verá un mensaje similar al siguiente: ![][14]

Si ha instalado oracle-jdk, verá un mensaje similar al siguiente: ![][15]

###Tomcat7
Uso del comando siguiente para instalar tomcat7:

	sudo apt-get install tomcat7  

Si no usa tomcat7, use la variación adecuada de este comando.

####Prueba:

Para comprobar si tomcat7 se ha instalado correctamente, busque el nombre DNS del servidor tomcat7 (http://tomcatexample.cloudapp.net/ es la dirección URL de ejemplo en este artículo). Si ve una página similar a la siguiente, tomcat7 está instalado correctamente. ![][16]


###Instalación de otros componentes de Tomcat
Hay otros componentes de tomcat opcionales que puede instalar.

Use el comando **sudo apt-cache search tomcat7** para ver todos los componentes disponibles. Los comandos siguientes son ejemplos que permiten instalar algunas partes útiles.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##Fase 4: Configuración de Tomcat
En esta fase es donde se administra tomcat.
###Inicio y detención de tomcat7
El servidor tomcat7 se iniciará automáticamente cuando al instalarse. También puede iniciarlo usted mismo con el comando siguiente:

	sudo /etc/init.d/tomcat7 start

Para detener tomcat7：

	sudo /etc/init.d/tomcat7 stop

Para ver el estado de tomcat7：

	sudo /etc/init.d/tomcat7 status

Para reiniciar los servicios de tomcat：

	sudo /etc/init.d/tomcat7 restart

###Administración de Tomcat
Puede editar el archivo de configuración de usuario de Tomcat para configurar las credenciales de administrador con el siguiente comando:

	sudo vi  /etc/tomcat7/tomcat-users.xml   

Aquí tiene un ejemplo: ![][17]

>AZURE.NOTE: Creación de una contraseña segura para el nombre de usuario de administrador.

Después de editar este archivo, deberá reiniciar los servicios de tomcat7 con el siguiente comando para asegurarse de que los cambios surtan efecto:

	sudo /etc/init.d/tomcat7 restart  

Abra el explorador y escriba la dirección URL **http://<your tomcat server DNS name>/manager/html**. Por ejemplo, en este artículo, la dirección URL es http://tomcatexample.cloudapp.net/manager/html.

Después de conectarse, debería ver algo parecido a lo siguiente: ![][18]

##Problemas comunes

###No se puede tener acceso a la máquina virtual con Tomcat y Moodle desde Internet

-	**Síntoma** Tomcat se está ejecutando, pero no puede ver la página predeterminada de Tomcat con el explorador.
-	**Caso de raíz posible**   
	1.	El puerto de escucha de tomcat no es el mismo que el puerto privado del extremo de su máquina virtual para el tráfico de tomcat.  

		Compruebe la configuración del extremo del puerto público y del puerto privado. Asegúrese de que el puerto privado es el mismo que el puerto de escucha de Apache. Consulte Fase 1: Crear una imagen para obtener instrucciones acerca de cómo configurar extremos para la máquina virtual.

		Para determinar el puerto de escucha de tomcat, abra /etc/httpd/conf/httpd.conf (versión de Red Hat) o /etc/tomcat7/server.xml (versión Debian). De forma predeterminada, el puerto de escucha de tomcat es el 8080. Aquí tiene un ejemplo:

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Si usa una máquina virtual como Debian o Ubuntu y desea cambiar el valor predeterminado de puerto de escucha de Tomcat (por ejemplo, 8081), deberá abrir el puerto para el sistema operativo. En primer lugar, abra el perfil:

			sudo vi /etc/default/tomcat7  

		A continuación, deberá quitarse la marca de comentario de la última línea y cambiar "no" por "yes".

			AUTHBIND=yes

	2.	El firewall deshabilitó el puerto de escucha de tomcat.

		Si solo puede ver la página predeterminada de Tomcat desde el host local, el problema es más probable es que el puerto que escucha de tomcat está bloqueado por el firewall. Puede usar la herramienta w3m para explorar la página web. Los siguientes comandos instalan w3m y permiten examinar la página predeterminada de Tomcat:

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**Solución**
	1. Si el puerto de escucha de tomcat no es el mismo que el Puerto privado del extremo para el tráfico a la máquina virtual, es necesario cambiar el puerto privado para que sea el mismo que el puerto de escucha de tomcat.   

	2.	Si el problema se debe a los firewall o iptables, agregue las siguientes líneas a /etc/sysconfig/iptables:

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		Tenga en cuenta que la segunda línea solo es necesaria para el tráfico https.

		Asegúrese de que esta está por encima de cualquier línea que limite el acceso globalmente, como las siguientes:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Para volver a cargar iptables, ejecute el comando siguiente:

			service iptables restart  

		Esto se ha probado en CentOS 6.3.

###Permiso denegado al cargar archivos de proyecto a /var/lib/tomcat7/webapps/  

-	**Síntoma** Al usar cualquier cliente SFTP (por ejemplo, FileZilla) para conectarse a la máquina virtual y navegar a /var/lib/tomcat7/webapps/ para publicar el sitio, se obtiene un mensaje de error similar al siguiente:  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**Caso de raíz posible** No tiene permiso para tener acceso a la carpeta /var/lib/tomcat7/webapps.
-	**Solución** Necesita obtener permiso de la cuenta raíz. Puede cambiar la propiedad de esa carpeta de raíz al nombre de usuario que se usó al aprovisionar la máquina. Este es un ejemplo con el nombre de cuenta de azureuser:  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	Utilice la opción -R para aplicar los permisos a todos los archivos en un directorio.

	Tenga en cuenta que este comando también funciona para directorios. La opción -R cambia los permisos de todos los archivos y directorios dentro del directorio. Aquí tiene un ejemplo:

		sudo chown -R username:group directory  

	Este comando cambia la propiedad (usuario y grupo) de todos los archivos y directorios dentro de un directorio y del mismo directorio.

	El comando siguiente solo cambia los permisos del directorio de la carpeta, pero no cambia los archivos y carpetas dentro del directorio.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=AcomDC_0128_2016-->