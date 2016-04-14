<properties 
	pageTitle="Conéctese a la Base de datos SQL con SQL Server Management Studio en Azure RemoteApp | Microsoft Azure" 
	description="Use este tutorial para aprender a utilizar SQL Server Management Studio en Azure RemoteApp para optimizar la seguridad y rendimiento al conectarse a la Base de datos SQL"
	services="sql-database" 
	documentationCenter=""
	authors="adhurwit" 
	manager=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="adhurwit"/>

# Utilización de SQL Server Management Studio en Azure RemoteApp para conectarse a una base de datos SQL

## Introducción  
Este tutorial le muestra cómo utilizar SQL Server Management Studio (SSMS) en Azure RemoteApp para conectarse a una base de datos SQL. Le guiará a través del proceso de configuración de SQL Server Management Studio en Azure RemoteApp, le explicará sus ventajas y le mostrará las características de seguridad que puede utilizar en Azure Active Directory.

**Tiempo estimado para completar el tutorial:** 45 minutos.

## SSMS en Azure RemoteApp

Azure RemoteApp es un servicio RDS de Azure que ofrece aplicaciones. Puede obtener más información acerca de esta herramienta aquí: [¿Qué es RemoteApp?](../remoteapp-whatis.md)

Si ejecuta SSMS en Azure RemoteApp obtendrá la misma experiencia que si lo ejecuta localmente.

![Captura de pantalla que muestra a SSMS ejecutándose en Azure RemoteApp][1]



## Ventajas

Hay muchas ventajas por utilizar SSMS en Azure RemoteApp, entre las que se incluyen:

- El puerto 1433 en el servidor SQL de Azure no tiene que exponerse externamente (fuera de Azure).
- No es necesario agregar y quitar direcciones IP en el firewall del servidor SQL de Azure. 
- Todas las conexiones de Azure RemoteApp se realizan a través de HTTPS en el puerto 443 mediante un protocolo cifrado de escritorio remoto
- Es multiusuario y se puede escalar.
- Hay una mejora del rendimiento si dispone de SSMS en la misma región que la base de datos SQL.
- Puede auditar la utilización de Azure RemoteApp con la versión Premium Edition de Azure Active Directory que tiene informes de actividad de usuario.
- Puede habilitar la autenticación multifactor (MFA).
- Puede obtener acceso a SSMS en cualquier lugar al utilizar cualquiera de los clientes compatibles con Azure RemoteApp, entre los que se incluyen iOS, Android, Mac, Windows Phone y Windows PC.


## Creación de una colección de Azure RemoteApp.

Estos son los pasos para crear la colección de Azure RemoteApp con SSMS:


### 1\. Cree una nueva máquina virtual de Windows desde una imagen
Utilice la imagen "Windows Server Remote Desktop Session Host Windows Server 2012 R2" de la galería para crear la nueva máquina virtual.


### 2\. Instale SSMS desde SQL Express

Vaya a la nueva VM y navegue hasta esta página de descarga: [Microsoft ® SQL Server ® 2014 Express](https://www.microsoft.com/es-ES/download/details.aspx?id=42299)

Hay una opción para descargar solo SSMS. Después de la descarga, vaya al directorio de instalación y ejecute el programa de instalación para instalar SSMS.

También debe instalar el Service Pack 1 de SQL Server 2014. Puede descargarlo aquí: [Service Pack 1 (SP1) de Microsoft SQL Server 2014](https://www.microsoft.com/es-ES/download/details.aspx?id=46694)

El Service Pack 1 de SQL Server 2014 incluye funciones esenciales para trabajar con la Base de datos SQL de Azure.


### 3\. Ejecute un script Validate y Sysprep

En el escritorio de la máquina virtual hay un script de PowerShell denominado Validate. Ejecútelo haciendo doble clic. Comprobará que la máquina virtual está lista para ser utilizada para el hospedaje remoto de aplicaciones. Cuando finalice la comprobación, se le pedirá que ejecute sysprep. Elija la opción para ejecutarlo.

Cuando finalice sysprep, apagará la máquina virtual.

Para obtener más información acerca de la creación de una imagen de Azure RemoteApp, consulte: [How to create a RemoteApp template image in Azure (Cómo crear una imagen de plantilla de RemoteApp en Azure)](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### 4\. Capture la imagen

Cuando la máquina virtual haya dejado de ejecutarse, busque la imagen en el portal actual y captúrela.

Para obtener más información acerca de la captura de una imagen, consulte [Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica](../virtual-machines-capture-image-windows-server.md)


### 5\. Agréguela a las imágenes de plantilla de Azure RemoteApp

En la sección de Azure RemoteApp del portal actual, vaya a la pestaña Imágenes de plantilla y haga clic en Agregar. En el cuadro emergente, seleccione "Importar una imagen de la biblioteca de máquinas virtuales" y, a continuación, elija la imagen que acaba de crear.



### 6\. Cree una colección en la nube

En el portal actual, cree una nueva colección en la nube de Azure RemoteApp. Elija la imagen de plantilla que acaba de importar con SSMS instalado.

![Creación de una nueva colección en la nube][2]


### 7\. Publique SSMS

En la pestaña Publicación de la nueva colección en la nube, seleccione Publicar una aplicación en el menú Inicio y, a continuación, elija SSMS en la lista.

![Aplicación de publicación][5]

### 8\. Agregar usuarios

En la pestaña Acceso de usuario puede seleccionar los usuarios que tendrán acceso a esta colección de Azure RemoteApp que solo incluye SSMS.

![Agregar usuario][6]


### 9\. Instale la aplicación de cliente de Azure RemoteApp

Puede descargar e instalar un cliente de Azure RemoteApp aquí: [Descargar | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## Configuración de un servidor SQL de Azure

La única configuración necesaria es asegurarse de que los servicios de Azure estén habilitados para el firewall. Si utiliza esta solución, no es necesario agregar ninguna dirección IP para abrir el firewall. El tráfico de red que se permite hacia SQL Server es de otros servicios de Azure.


![Permiso de Azure][4]



## Multi-Factor Authentication (MFA)

MFA se puede habilitar específicamente para esta aplicación. Vaya a la pestaña Aplicaciones de Azure Active Directory. Encontrará una entrada para Microsoft Azure RemoteApp. Si hace clic en esa aplicación y, a continuación, configura, verá la página siguiente, donde podrá habilitar MFA para esta aplicación.

![Habilitar MFA][3]



## Auditoría de actividad de usuario con Azure Active Directory Premium

Si no tiene Azure AD Premium, tendrá que activarlo en la sección de licencias de su directorio. Con Premium habilitado, puede asignar usuarios al nivel Premium.

Cuando vaya a un usuario de Azure Active Directory, a continuación, puede ir a la pestaña de actividad para ver la información de inicio de sesión en Azure RemoteApp.



## Pasos siguientes

Después de completar todos los pasos anteriores, podrá ejecutar el cliente de Azure RemoteApp y el inicio de sesión con un usuario asignado. Se le presentará SSMS como una de sus aplicaciones y podrá ejecutarla como lo haría si la hubiera instalado en el equipo con acceso al servidor SQL de Azure.

Para más información sobre cómo realizar la conexión a la base de datos SQL, consulte [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md).


Eso es todo por ahora. ¡Disfrute!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png

<!---HONumber=AcomDC_0211_2016-->