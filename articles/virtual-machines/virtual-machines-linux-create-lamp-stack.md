<properties
	pageTitle="Creación de una pila LAMP con Azure | Microsoft Azure"
	description="Aprenda a crear una pila LAMP con Microsoft Azure usando máquinas virtuales (VM) de Azure que ejecuten Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Cómo crear una pila LAMP con Microsoft Azure

Una pila "LAMP" es un grupo de software de código abierto que normalmente se instala junto para permitir a un servidor hospedar sitios web dinámicos y aplicaciones web. Este término es en realidad un acrónimo que representa al sistema operativo Linux con el servidor web Apache. Los datos del sitio se almacenan en una base de datos MySQL y PHP procesa contenido dinámico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


En esta guía, instalaremos una pila LAMP en una imagen Linux y la implementaremos en Microsoft Azure.

Aprenderá a:

-	Crear una máquina virtual de Azure.
-	Preparar la máquina virtual para la pila LAMP.
-	Instalar el software que necesita el servidor LAMP en la máquina virtual.

Se supone que el lector ya tiene una suscripción de Azure. Si no, puede registrarse para una evaluación gratuita en [http://azure.microsoft.com](https://azure.microsoft.com/). Si tiene una suscripción de MSDN, consulte [Precios especiales de Microsoft Azure: Ventajas de MSDN, MPN y Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para obtener más información acerca de Azure, consulte [¿Qué es Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Además de este tema, si ya dispone de una máquina virtual y solo busca los aspectos básicos de la instalación de una pila LAMP en distintas distribuciones de Linux, consulte [Instalación de la pila LAMP en una máquina virtual de Linux en Azure](virtual-machines-linux-install-lamp-stack.md).

También puede implementar imágenes LAMP preconfiguradas desde Azure Marketplace. El vídeo de 10 minutos siguiente presenta la implementación de imágenes LAMP integradas desde Azure Marketplace: [pila LAMP en máquinas virtuales de Azure](https://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman).

##Fase 1: Creación de una imagen
En esta fase, creará una máquina virtual mediante una imagen de Linux en Azure.

###Paso 1: Generación de una clave de autenticación SSH
SSH es una herramienta importante para los administradores del sistema. Sin embargo, depender de una contraseña determinada por humano para la seguridad no siempre es aconsejable. Una clave SSH segura permite dejar acceso remoto abierto sin preocuparse por las contraseñas. El método consta de autenticación con criptografía asimétrica. La clave privada del usuario es la que concede la autenticación. Incluso puede bloquear la cuenta del usuario para denegar completamente la autenticación de contraseña.

Siga estos pasos para generar la clave de autenticación SSH.

-	Descargue e instale Puttygen en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
-	Ejecute puttygen.exe.
-	Haga clic en **Generar** para generar las claves. En el proceso puede aumentar la aleatoriedad al mover el mouse sobre el área en blanco en la ventana. ![][1]
-	Después del proceso de generar, puttygen.exe mostrará la clave generada. Por ejemplo: ![][2]
-	Seleccione y copie la clave pública en **Clave** y guárdela en un archivo denominado **publicKey.pem**. No haga clic en **Guardar clave pública**, porque el formato de archivo de la clave pública guardada es diferente de la clave pública que queremos.
-	Haga clic en **Guardar clave privada** y guárdela en un archivo denominado **privateKey.ppk**.

###Paso 2: Creación de la imagen en el Portal de Azure.
En el [Portal de Azure](https://portal.azure.com/), haga clic en **Nuevo** en la barra de tareas y cree una imagen siguiendo estas instrucciones. Elija una imagen de Linux según sus necesidades. En este ejemplo se usa la imagen de Ubuntu 14.04.

![][3]

Para **Nombre de host**, especifique el nombre de la dirección URL que los clientes de Internet van a usar para obtener acceso a esta máquina virtual. Defina la última parte del nombre DNS, por ejemplo LAMPDemo, y Azure generará la dirección URL como *lampdemo.cloudapp.net*.

Para **Nombre de usuario**, elija un nombre que usará más adelante para iniciar sesión en la máquina virtual.

Para **Clave de autenticación SSH**, copie el valor clave del archivo **publicKey.pem**, que contiene la clave pública generada por puttygen.

![][4]

Configure otras opciones según sea necesario y, luego, haga clic en **Crear**.

##Fase 2: Preparar la máquina virtual para la pila LAMP
En esta fase, configurará un extremo para el tráfico web y, luego, conéctese a la nueva máquina virtual.

###Paso 1: Apertura del puerto HTTP para permitir el acceso web
Los extremos en Azure constan de un protocolo (TCP o UDP), junto con un puerto público y privado. El puerto privado es el puerto que el servicio está escuchando en la máquina virtual. El puerto público es el puerto que el servicio en la nube de Azure está escuchando externamente para el tráfico de Internet. En algunos casos, se trata del mismo número de puerto.

El puerto TCP 80 es el número de puerto predeterminado que escucha Apache. Abrir este puerto con un extremo de Azure le permitirá a usted y a otros clientes de Internet acceder al servidor web Apache.

En el Portal de Azure, haga clic en **Examinar -> Máquina virtual** y luego haga clic en la máquina virtual que creó.

![][5]

Para agregar un extremo a una máquina virtual, haga clic en el cuadro **Extremos**.

![][6]

Haga clic en **Agregar**. Al aprovisionar una nueva máquina virtual puede habilitar o deshabilitar extremos según sea necesario.

Configurar el extremo:

1.	Escriba un nombre para el extremo en **Extremo**.
2.	Escriba 80 en **Puerto público**. Si cambia el puerto de escucha predeterminado de Apache, debe actualizar el Puerto privado para que coincida con el puerto de escucha de Apache.
3.	Escriba 80 en **Puerto público**. De forma predeterminada, el tráfico HTTP usa el puerto 80. Si se establece en 80, no necesita incluir el número de puerto en la dirección URL que le permite acceder al servicio web de Apache. Por ejemplo: http://lampdemo.cloudapp.net. Si establece el puerto de escucha de Apache a otro valor, como el 81, deberá agregar el número de puerto a la dirección URL para acceder al servicio web de Apache. Por ejemplo, http://lampdemo.cloudapp.net:81/.

![][7]

Haga clic en **Aceptar** para agregar el extremo a la máquina virtual.




###Paso 2: Conexión a la imagen creada
Puede elegir cualquier herramienta SSH para conectarse a la nueva máquina virtual. En este ejemplo, se usa Putty.

En primer lugar, obtenga el nombre DNS de la máquina virtual desde el Portal de Azure. Haga clic en **Examinar -> Máquinas virtuales ->** nombre de la máquina virtual **-> Propiedades** y luego preste atención al campo **Nombre de dominio** del icono **Propiedades**.

Obtenga el número de puerto para las conexiones SSH desde el campo **SSH**. Aquí tiene un ejemplo.

![][8]

Descargue Putty de [aquí](http://www.putty.org/).

Después de descargar, haga clic en el archivo ejecutable PUTTY.EXE. Configure las opciones básicas con el nombre de host y el número de puerto obtenido de las propiedades de la máquina virtual. Aquí tiene un ejemplo:

![][9]

En el panel izquierdo, haga clic en **Conexión -> SSH -> Autenticación** y luego haga clic en **Examinar** para especificar la ubicación del archivo **privateKey.ppk** que contiene la clave privada generada por puttygen en la Fase 1: Creación de una imagen. Aquí tiene un ejemplo:

![][10]

Haga clic en **Abrir**. Puede recibir una alerta en un cuadro de mensaje. Si ha configurado el nombre DNS y el número de puerto correctamente, haga clic en **Sí**.

![][11]


Verá lo siguiente.

![][12]

Escriba el nombre de usuario que especificó cuando creó la máquina virtual en la Fase 1: Creación de una imagen. Verá algo parecido a lo siguiente:

![][13]

##Fase 3: Instalar la pila LAMP

Dependiendo de qué distribución de Linux usó para crear la máquina virtual, existen varias formas de instalar la pila LAMP. Las secciones siguientes contienen los pasos típicos de algunos SO de Linux comunes.

###Red Hat, base de CentOS

####Instalar Apache
Para instalar Apache, abra la terminal y ejecute este comando:

	sudo yum install httpd

Una vez instalado, inicie Apache con este comando:

	sudo service httpd start

####Probar Apache
Para comprobar si Apache se ha instalado correctamente, busque el nombre DNS del servidor Apache (para la dirección URL de ejemplo en este artículo, http://lampdemo.cloudapp.net/)). La página debe mostrar las palabras “It works!" ![][14]

####Solución de problemas
Si se ejecuta Apache, pero no puede ver la página anterior predeterminada de Apache, deberá comprobar lo siguiente:

-	Puerto/dirección de escucha del servicio web de Apache
	-	Compruebe la configuración del extremo para la máquina virtual de Azure. Asegúrese de que la configuración del extremo es adecuada. Consulte la fase 1: Crear una imagen en este artículo.
	-	Abra /etc/httpd/conf/httpd.conf y, luego, busque la cadena "Listen". Asegúrese de que el puerto de escucha de Apache es el mismo que el puerto privado que configuró para su extremo. El puerto predeterminado para Apache es 80. Aquí tiene un ejemplo.  

			……
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			……
			……  

-	Firewall, configuración de iptables Si puede ver la página predeterminada de Apache desde el host local, el problema puede ser que el puerto en el que está escuchando Apache esté bloqueado por el firewall. Puede usar la herramienta w3m para explorar la página web de Apache. Los comandos siguientes instalan w3m y examinan la página predeterminada de Apache:

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Si el firewall o los iptables provocan el problema, agregue las líneas siguientes a /etc/sysconfig/iptables:

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Tenga en cuenta que la segunda línea solo es necesaria para el tráfico https.

	Asegúrese de que estas líneas están por encima de cualquier línea que limite el acceso globalmente, como las siguientes:

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	Para que la nueva configuración surta efecto, use el siguiente comando:

		service iptables restart

####Instalar MySQL
Para instalar MySQL, abra el terminal y ejecute estos comandos:

	sudo yum install mysql-server
	sudo service mysqld start

Durante la instalación, MySQL le pedirá permiso dos veces. Después de decir sí a ambas consultas, MySQL se instalará.

####Configuración de MySQL
Una vez que se complete la instalación, puede configurar una contraseña raíz MySQL con lo siguiente:

	sudo /usr/bin/mysql_secure_installation  

Se le solicitará la contraseña raíz actual.

Puesto que acaba de instalar MySQL, es probable que no tenga una, déjela en blanco presionando ENTRAR.

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

Se le solicitará que establezca una contraseña raíz. Siga adelante, elija Y y siga las instrucciones.

CentOS automatiza el proceso de configuración de MySQL con una serie de preguntas Sí o No. Estas preguntas se muestran a continuación. Elija Y o N para su configuración. Al final, MySQL volverá a cargarse e implementará los cambios nuevos.

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####Instalar PHP
PHP es un lenguaje de scripting de web de código abierto que se usa ampliamente para crear páginas web dinámicas.

Para instalar PHP en la máquina virtual, abra el terminal y ejecute este comando:

	sudo yum install php php-mysql  

Responda "y" para descargar los paquetes de software. Luego, responda "y" a la clave de importación GPG 0xE8562897 "Clave de CentOS 5 (clave de firma oficial de CentOS 5). PHP se instalará.

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian, base de Ubuntu
Esto se ha probado en Ubuntu 14.04.

Ubuntu se basa en Debian. Puede instalar la pila LAMP de la misma forma que la serie Red Hat. Para simplificar los pasos, use la herramienta Tasksel.

Tasksel es una herramienta de Debian y Ubuntu que instala varios paquetes relacionados como una tarea coordinada en el sistema. Para más información, vea [Tasksel: wiki de ayuda de la comunidad](https://help.ubuntu.com/community/Tasksel).

Use tasksel para instalar el software necesario para la pila LAMP.

- Para descargar las listas de paquetes desde los repositorios y actualizarlas para obtener información acerca de las versiones más recientes de los paquetes y sus dependencias:  

		sudo apt-get update
-	Para instalar la pila LAMP Ubuntu mediante Tasksel:  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

A continuación, siga los pasos del asistente y elija su **contraseña raíz de MySQL**.

![][15]


##Probar LAMP en el servidor
Puede probar el sistema LAMP mediante la creación de una página php de información rápida.

En primer lugar, cree un nuevo archivo:

	sudo nano /var/www/html/info.php  

Agregue las líneas siguientes:

	<?php
	phpinfo();
	?>  

Luego, guarde y salga.

Reinicie Apache para que todos los cambios surtan efecto en el equipo. Si el sistema operativo de la máquina virtual es CentOS, use el comando siguiente para reiniciar Apache:

	sudo service httpd restart

Si el sistema operativo de la máquina virtual es Ubuntu, use el comando siguiente para reiniciar Apache:

	sudo service apache2 restart  

Por último, examine la página php de información (para el servidor web de ejemplo en este tema, la dirección URL sería http://lampdemo.cloudapp.net/info.php)).

El explorador debe ser similar al siguiente:

![][16]

##Pasos adicionales

Como regla general, cambiará algunas configuraciones predeterminadas para preparar la implementación de aplicaciones web.

###Permitir el acceso remoto a MySQL
Si tiene más de una máquina virtual instalada con MySQL y necesitan intercambiar datos, deberá habilitar el acceso remoto de MySQL y conceder los permisos adecuados.

**Formato de la referencia de comandos:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Ejemplo:**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

También debe cambiar el perfil /etc/mysql/my.cnf. Si tiene líneas similares a la siguiente:

	skip-networking
	bind-address = 127.0.0.1  

Debería comentarlas (agregar un # al principio de las líneas) y, luego, reiniciar MySQL.

Para agregar un extremo para permitir acceso remoto, consulte las instrucciones en la Fase 1: Crear una imagen para crear un nuevo extremo. El número de puerto TCP de acceso remoto predeterminado de MySQL es 3306. Aquí tiene un ejemplo:

![][17]

###Implementar las aplicaciones web en el servidor Apache
Una vez que haya configurado la pila LAMP correctamente, puede implementar la aplicación web existente en el servidor web Apache (la máquina virtual). Se trata de los mismos pasos que para implementar una aplicación web existente en un servidor web físico.

-	La raíz del servidor web se encuentra en **/var/www/html**. Debe conceder privilegios a los usuarios que necesiten cargar archivos en esta carpeta. En el ejemplo siguiente se muestra cómo agregar el permiso de escritura a un grupo denominado lampappgroup y asignar el nombre de usuario azureuser a este grupo:  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE] Puede que necesite iniciar sesión de nuevo si quiere modificar un archivo en /var/www/html/.
-	Use cualquier cliente SFTP (por ejemplo, FileZilla) para conectar con el nombre DNS de la máquina virtual (por ejemplo, lampdemo.cloudapp.net) y navegue a /**var/www/html** para publicar el sitio. ![][18]



##Problemas comunes y solución de problemas

###No se puede acceder a la máquina virtual con Apache y Moodle desde Internet

-	**Síntoma** Apache se está ejecutando, pero no puede ver la página predeterminada de Apache con el explorador.
-	**Caso de raíz posible**
	1.	El puerto de escucha de Apache no coincide con el del puerto privado del punto de conexión de su máquina virtual para el tráfico web.</br> Compruebe la configuración del extremo del puerto público y del puerto privado, y asegúrese de que el puerto privado es el mismo que el puerto de escucha de Apache. Vea Fase 1: Crear una imagen para instrucciones sobre cómo configurar puntos de conexión para la máquina virtual.</br> Para determinar el puerto de escucha de Apache, abra /etc/httpd/conf/httpd.conf (versión Red Hat) o /etc/apache2/ports.conf (versión Debian), busque la cadena "Listen". El puerto predeterminado es 80.

	2.	El firewall ha deshabilitado el puerto de escucha de Apache.</br> Si puede ver la página predeterminada de Apache desde el host local, es probable que el problema sea que el puerto de escucha de Apache esté bloqueado por el firewall. Puede usar la herramienta w3m para explorar la página web. Los siguientes comandos instalan w3m y exploran la página predeterminada de Apache:

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**Solución**

	1.	Si el puerto de escucha de Apache no coincide con el puerto privado del extremo para el tráfico web en la máquina virtual, es necesario cambiar el puerto privado del extremo para que coincida con el puerto de escucha de Apache.
	2.	Si el problema se debe a los firewall o iptables, agregue las siguientes líneas a /etc/sysconfig/iptables:  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		Tenga en cuenta que la segunda línea solo es necesaria para el tráfico https.

		Asegúrese de que esta está por encima de cualquier línea que limite el acceso globalmente, como las siguientes:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Para volver a cargar iptables, ejecute el comando siguiente:

			service iptables restart  

		Esto se ha probado en CentOS 6.3.

###Permiso denegado cuando carga los archivos del proyecto a /var/www/html/  

-	**Síntoma** Al usar cualquier cliente SFTP (por ejemplo, FileZilla) para conectarse a la máquina virtual y navegar a /var/www/html para publicar el sitio, obtendrá un mensaje de error similar al siguiente:  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Caso de raíz posible** No tiene permisos para obtener acceso a la carpeta /var/www/html.
-	**Solución** Necesita obtener permiso de la cuenta raíz. Puede cambiar la propiedad de esa carpeta de raíz al nombre de usuario que se usó al aprovisionar la máquina. Este es un ejemplo con el nombre de cuenta de azureuser:  

		sudo chown azureuser -R /var/www/html  

	Utilice la opción -R para aplicar los permisos a todos los archivos en un directorio.

	Tenga en cuenta que este comando también funciona para directorios. La opción -R cambia los permisos de todos los archivos y directorios dentro del directorio. Aquí tiene un ejemplo:

		sudo chown -R username:group directory  

	Este comando cambia la propiedad (usuario y grupo) de todos los archivos y directorios dentro de un directorio y del mismo directorio.

	El comando siguiente solo cambia los permisos del directorio de la carpeta, pero no cambia los archivos y carpetas dentro del directorio.

		sudo chown username:group directory  

###No se pudo determinar de manera fiable el nombre de dominio completo del servidor

-	**Síntoma** Cuando se reinicia el servidor Apache mediante uno de los siguientes comandos:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	o

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Se produce el error siguiente:

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Caso de raíz posible** No se ha configurado el nombre del servidor de Apache.

-	**Solución** Inserte una línea "ServerName localhost" en httpd.conf (versión Red Hat) o apache2.conf (versión Debian) en /etc/apache2 y reinicie Apache. El aviso desaparecerá.



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg

<!---HONumber=AcomDC_0128_2016-->