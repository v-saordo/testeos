
1. Para escalar privilegios, escriba:

		sudo -s

	Especifique la contraseña.

2. Para instalar MySQL Community Server Edition, escriba:

		zypper install mysql-community-server

	Espere hasta que MySQL se descargue e instale.

3. Para establecer MySQL para iniciarse con el arranque del sistema, escriba:

		insserv mysql

4. Inicie manualmente el demonio de MySQL (mysqld) con este comando:

		rcmysql start

	Para comprobar el estado del demonio MySQL, escriba:

		rcmysql status

	Si desea detener el demonio MySQL, escriba:

		rcmysql stop

	> [AZURE.IMPORTANT] después de la instalación, la contraseña raíz de MySQL se encuentra vacía de forma predeterminada. Se recomienda ejecutar **mysql\_secure\_installation**, un script que ayuda a proteger MySQL. El script le solicitará que cambie la contraseña raíz de MySQL, quite las cuentas de usuario anónimas, deshabilite los datos de inicio de sesión raíz remotos, quite las bases de datos de prueba y vuelva a cargar la tabla de privilegios. Es recomendable que diga que sí a todas las opciones y cambie la contraseña raíz.

5. Escriba lo siguiente para ejecutar el script de instalación de MySQL:

		mysql_secure_installation

6. Iniciar sesión en MySQL:

		mysql -u root -p

	Especifique la contraseña raíz de MySQL (que cambió en el paso anterior) y se mostrará un símbolo del sistema donde puede emitir certificados SQL para interactuar con la base de datos.

7. Para crear un nuevo usuario de MySQL, ejecute los siguientes comandos en el símbolo del sistema **mysql>**:

		CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que el punto y coma (;) al final de las líneas es fundamental para terminar los comandos.

8. Para crear una base de datos y conceder permisos de usuario `mysqluser` en ella, emita los siguientes comandos:

		CREATE DATABASE testdatabase;
		GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos. Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el sistema.

9. Para iniciar sesión desde otro equipo, escriba:

		GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	donde `ip-address` es la dirección IP del equipo desde el que se conectará a MySQL.

10. Para salir de la utilidad administración de base de datos MySQL, escriba:

		quit
		
## Agregación de un extremo

1. Cuando MySQL esté instalado, deberá configurar un extremo para que pueda acceder a MySQL de manera remota. Inicie sesión en el [Portal de Azure clásico][AzurePortal]. Haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y luego en **Extremos**.

2. Haga clic en **Agregar** en la parte inferior de la página.


3. Agregue un extremo con el nombre "MySQL", con el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "3306".

4. Para conectarse de forma remota a la máquina virtual desde su equipo, escriba:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por ejemplo, mediante la máquina virtual que hemos creado en este tutorial, escriba este comando:

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=AcomDC_0128_2016-->