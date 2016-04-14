<properties
   pageTitle="Conectarse a una Base de datos SQL mediante autenticación de Azure Active Directory | Microsoft Azure"
   description="Aprenda a conectarse a una Base de datos SQL mediante autenticación de Azure Active Directory"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="jeffreyg"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/01/2016"
   ms.author="rick.byham@microsoft.com"/>

# Conexión a Base de datos SQL mediante autenticación de Azure Active Directory

La autenticación de Azure Active Directory es un mecanismo de conexión a la Base de datos SQL de Microsoft Azure mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure Active Directory puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece un lugar único para administrar usuarios de la Base de datos SQL y simplifica la administración de permisos. Entre las ventajas se incluyen las siguientes:

- Ofrece una alternativa a la autenticación de SQL Server.
- Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
- Permite la rotación de contraseñas en un solo lugar
- Los clientes pueden administrar los permisos de la base de datos con grupos externos (AAD).
- Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
- La autenticación de Azure Active Directory utiliza usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.

> [AZURE.IMPORTANT] Azure Active Directory es una característica de vista previa y está sujeta a los términos de la versión de vista previa incluidos en el contrato de licencia (por ejemplo, el Contrato Enterprise, el Contrato de Microsoft Azure o el contrato Microsoft Online Subscription), así como cualquier otro [Términos de Uso Complementarios para Vistas Previas de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicable.

En los pasos de configuración se incluyen los siguientes procedimientos para configurar y usar la autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory.
2. Asegurarse de que la base de datos está en Base de datos SQL de Azure V12.
3. Opcional: asociar o cambiar el Active Directory que está asociado actualmente a la suscripción de Azure.
4. Crear un administrador de Azure Active Directory para Azure SQL Server.
5. Configurar los equipos cliente.
6. Crear usuarios de base de datos independiente en la base de datos y asignados a identidades de Azure AD.
7. Conectarse a la base de datos mediante identidades de Azure AD.


## Arquitectura de confianza

En el siguiente diagrama de alto nivel se resume la arquitectura de la solución del uso de la autenticación de Azure AD con la Base de datos SQL de Azure. Las flechas indican las rutas de comunicación.

![diagrama de autenticación de aad][1]

En el diagrama siguiente se indica la federación, la confianza y las relaciones de hospedaje que permiten que un cliente se conecte a una base de datos mediante el envío de un token que un Azure AD autenticó y que es de confianza para la base de datos. Es importante comprender que el acceso a una base de datos mediante la autenticación de Azure AD requiere que la suscripción de hospedaje está asociada al Azure Active Directory.

![relación de suscripción][2]

## Estructura del administrador

Cuando se usa la autenticación de Azure AD, existirán dos cuentas de administrador para el servidor de Base de datos SQL: el administrador del servidor SQL Server original y el administrador de Azure AD. Solo el administrador basado en una cuenta de Azure AD puede crear el primer usuario de base de datos independiente en Azure AD en una base de datos de usuario. El inicio de sesión del administrador de Azure AD puede ser un usuario de Azure AD o un grupo de Azure AD. Cuando el administrador es una cuenta de grupo, cualquier miembro del grupo lo puede usar, lo que permite varios administradores de Azure AD para la instancia de SQL Server. Mediante el uso de la cuenta de grupo como un administrador, se mejora la administración al permitir agregar y quitar miembros de grupo de forma centralizada en Azure AD sin cambiar los usuarios ni los permisos de Base de datos SQL. Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en cualquier momento.

![estructura de administración][3]

## Permisos

Para crear nuevos usuarios, debe tener el permiso **ALTER ANY USER** en la base de datos. El permiso **ALTER ANY USER** se pueden conceder a cualquier usuario de la base de datos. Las cuentas de administrador del servidor también disponen del permiso **ALTER ANY USER**, los usuarios de la base de datos con los permisos **CONTROL ON DATABASE** o **ALTER ON DATABASE** para esa base de datos y los miembros del rol de la base de datos **db\_owner**.

Para crear un usuario de la base de datos independiente en la Base de datos SQL de Azure, debe conectarse a la base de datos con una identidad de Azure AD. Para crear el primer usuario de base de datos independiente , debe conectarse a la base de datos con un administrador de Azure AD (que es el propietario de la base de datos). Esto se muestra a continuación, en los pasos 4 y 5.

## Características y limitaciones de Azure AD

Es posible aprovisionar los siguientes miembros de Azure Active Directory en Azure SQL Server: - miembros nativos (un miembro creado en Azure AD en el dominio administrado o en un dominio de cliente). Para más información, consulte [Incorporación de su propio nombre de dominio a Azure AD]( https://azure.microsoft.com/documentation/articles/active-directory-add-domain/); - miembros de dominio federado (un miembro creado en Azure AD con un dominio federado). Para obtener más información, consulte [Microsoft Azure ahora admite la federación con Windows Server Active Directory]( https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/); - miembros importados desde otros directorios de Azure Active Directory que son miembros del dominio nativo o federado; - grupos de Active Directory creados como grupos de seguridad.

No se admiten las cuentas de Microsoft (por ejemplo, outlook.com, hotmail.com, live.com) ni otras cuentas de invitado (por ejemplo, gmail.com, yahoo.com). Si puede iniciar sesión en [https://login.live.com](https://login.live.com) con la cuenta y la contraseña, está usando una cuenta de Microsoft que no se admite para la autenticación de Azure AD para la Base de datos SQL de Azure.

### Consideraciones adicionales

- Para mejorar la capacidad de administración, se recomienda que aprovisione un grupo dedicado de Azure Active Directory como administrador.
- Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en un servidor Azure SQL Server en cualquier momento.
- Inicialmente, solo un administrador de Azure Active Directory puede conectarse al servidor Azure SQL Server con una cuenta de Azure Active Directory. El Administrador de Active Directory puede configurar los usuarios de la base de datos de Azure Active Directory sucesivos.
- Se recomienda establecer el tiempo de espera de conexión a 30 segundos.
- No se admiten algunas herramientas como BI y Excel.
- La autenticación de Azure Active Directory solo admite el **Proveedor de datos de .NET Framework para SqlServer** (como mínimo, la versión 4.6 de .NET Framework). Por lo tanto, Management Studio (disponible con SQL Server 2016) y las aplicaciones de capa de datos (DAC y .bacpac) pueden conectarse, pero **sqlcmd.exe** no puede conectarse porque **sqlcmd** usa el proveedor ODBC.
- No se admiten la autenticación en dos fases ni otras formas de autenticación interactiva.


## 1\. Crear y rellenar un Azure Active Directory.

Cree un Azure Active Directory y rellénelo con usuarios y grupos. Esto incluye:

- Crear el dominio administrado de Azure AD de dominio inicial.
- Federar Servicios de dominio de Active Directory local con Azure Active Directory.
- Con la herramienta **AD FS**, en la sección **Servicio**, **Puntos de conexión**, habilite **WS-Trust 1.3** para la ruta de acceso a la dirección URL **/adfs/services/trust/13/windowstransport**.

Para obtener más información, consulte [Incorporación de su propio nombre de dominio a Azure AD]( https://azure.microsoft.com/documentation/articles/active-directory-add-domain/), [Microsoft Azure ahora admite la federación con Windows Server Active Directory]( https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administración de su directorio de Azure AD]( https://msdn.microsoft.com/library/azure/hh967611.aspx) y [Administrar Azure AD mediante Windows PowerShell]( https://msdn.microsoft.com/library/azure/jj151815.aspx).

## 2\. Asegurarse de que la base de datos está en Base de datos SQL de Azure V12.

La última versión de Base de datos SQL V12 admite la autenticación de Azure Active Directory. Para obtener información sobre la Base de datos SQL V12 y para saber si está disponible en su región, consulte [Novedades de la Base de datos SQL V12](sql-database-v12-whats-new.md).

Si tiene una base de datos existente, compruebe que esté alojada en Base de datos SQL V12 conectándose a la base de datos (por ejemplo, con SQL Server Management Studio) y ejecutando `SELECT @@VERSION;`. El resultado esperado de una base de datos en Base de datos SQL V12 es como mínimo **Microsoft SQL Azure (RTM) - 12.0**.

Si la base de datos no está hospedada en Base de datos SQL V12, consulte [Planeación y preparación para actualizar a SQL Database V12](sql-database-v12-plan-prepare-upgrade.md) y después visite el Portal de Azure clásico para migrar la base de datos a Base de datos SQL V12.

También puede crear una base de datos en Base de datos SQL V12 siguiendo los pasos que se muestran en [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). **Sugerencia**: Lea el paso siguiente antes de seleccionar una suscripción para la nueva base de datos.

## 3\. Opcional: asociar o cambiar el Active Directory que está asociado actualmente a la suscripción de Azure.

Para asociar la base de datos con el directorio de Azure AD para su organización, cree el directorio como un directorio de confianza para la suscripción de Azure que hospeda la base de datos. Para obtener más información, consulte [Cómo se asocian las suscripciones a Azure con Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Información adicional:** cada suscripción de Azure tiene una relación de confianza con una instancia de Azure AD. Esto significa que confía en ese directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero una suscripción confía solo en un único directorio. Puede ver el directorio en que confía la suscripción en la pestaña **Configuración**, en [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, el acceso a esos otros recursos asociados a la suscripción también se detiene. Sin embargo, el directorio permanece en Azure y puede asociar otra suscripción a ese directorio y continuar con la administración de los usuarios del directorio. Para más información sobre recursos, consulte [Descripción de acceso a los recursos de Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

En los procedimientos siguientes se ofrecen instrucciones paso a paso sobre cómo cambiar el directorio asociado para una suscripción determinada.

1. Conéctese a su [Portal de Azure clásico](https://manage.windowsazure.com/) mediante un administrador de suscripciones de Azure.
2. En el área de la izquierda, seleccione **CONFIGURACIÓN**.
3. Las suscripciones aparecen en la pantalla de configuración. Si la suscripción deseada no aparece, haga clic en el apartado **Suscripciones** de la parte superior, despliegue la lista del cuadro **FILTRAR POR DIRECTORIO**, seleccione el directorio que contiene las suscripciones y después haga clic en **APLICAR**.

	![seleccionar suscripción][4]
4. En el área **Configuración**, haga clic en la suscripción y luego en **EDITART DIRECTORIO** en la parte inferior de la página.

	![portal de configuración de ad][5]
5. En el cuadro **EDITAR DIRECTORIO**, seleccione el directorio de Azure Active Directory que está asociado con su servidor SQL Server y después haga clic en la flecha siguiente.

	![selección de editar directorio][6]
6. En el cuadro de diálogo de la asociación de directorios **CONFIRMAR**, confirme que "**se quitarán todos los coadministradores**".

	![confirmación de editar directorio][7]
7. Haga clic en la marca de verificación para volver a cargar el portal.

> [AZURE.NOTE] Cuando se cambia el directorio, el acceso de todos los coadministradores, usuarios y grupos de Azure AD y usuarios de recursos con respaldo del directorio se quitarán y dejarán de tener acceso a la suscripción y sus recursos. Solo usted, como administrador del servicio, podrá configurar el acceso para las entidades de seguridad según el nuevo directorio. Este cambio podría tardar una cantidad considerable de tiempo en propagarse a todos los recursos. El cambio del directorio también modificará el administrador de Azure AD de la Base de datos SQL y deshabilitará el acceso a la Base de datos SQL de los usuarios de Azure AD existentes. El administrador de Azure AD debe volver a configurarse (como se describe a continuación) y se deben crear nuevos usuarios de Azure AD.

## 4\. Crear un administrador de Azure Active Directory para Azure SQL Server.

Cada servidor Azure SQL Server se inicia con una única cuenta de administrador de servidor que es el administrador de todo el servidor Azure SQL Server. Se debe crear un segundo administrador del servidor, que sea una cuenta de Azure AD. Esta entidad de seguridad se crea como un usuario de base de datos independiente en la base de datos maestra. Como administradores, las cuentas de administrador del servidor son miembros del rol **db\_owner** en todas las bases de datos de usuarios y se introducen en cada base de datos de usuarios como el usuario **dbo**. Para más información sobre las cuentas de administrador del servidor, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md) y la sección **Inicios de sesión y usuarios** sección de [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](sql-database-security-guidelines.md).

> [AZURE.NOTE] Los usuarios que no se basen en una cuenta de Azure AD (incluida la cuenta de administrador del servidor Azure SQL Server) no pueden crear usuarios basados en Azure AD porque no tienen permisos para validar los usuarios de la base de datos propuesta con Azure AD.

### Aprovisionar un administrador de Azure Active Directory para el servidor Azure SQL Server mediante el Portal de Azure clásico

1. En el [Portal de Azure clásico](https://portal.azure.com/), en la esquina superior derecha, haga clic en la conexión para desplegar una lista de posibles directorios de Active Directory. Elija el Active Directory correcto como el valor predeterminado de Azure AD. En este paso se vincula la asociación de la suscripción de Active Directory con la Base de datos SQL de Azure, asegurándose de que la misma suscripción se use tanto para Azure AD como para SQL Server.

	![elegir ad][8]
2. En el área de la izquierda, seleccione **Servidores SQL Server** y su **Servidor SQL server** y, después, en la parte superior de la hoja **SQL Server**, haga clic en **Configuración**.

	![configuración de ad][9]
3. En la hoja **Configuración**, haga clic en **Administrador de Active Directory (vista previa)** y acepte la cláusula de vista previa.
4. En la hoja **Administrador de Active Directory (vista previa)**, haga clic para revisarla y, después, haga clic en **Aceptar** para aceptar los términos de la vista previa.
5. En la hoja **Administrador de Active Directory (vista previa)**, haga clic en **Administrador de Active Directory** y después, en la parte superior, haga clic en **Establecer administrador**.
6. En la hoja **Agregar administrador**, busque un usuario, seleccione el usuario o grupo que convertirá en administrador y, después, haga clic en **Seleccionar**. (En la hoja del administrador de Active Directory se mostrarán todos los miembros y grupos del directorio de Active Directory). No se pueden seleccionar los usuarios o grupos que aparecen atenuados porque no se admiten como administradores de Azure AD. (Consulte la lista de administradores admitidos en la sección anterior **Características y limitaciones de Azure AD**). El control de acceso basado en rol (RBAC) se aplica solo al portal y no se propaga a SQL Server.
7. En la parte superior de la hoja **Administrador de Active Directory**, haga clic en **GUARDAR**. ![elegir administrador][10]

	El proceso de cambio del el administrador puede tardar varios minutos. Aparecerá el nuevo administrador en el cuadro **Administrador de Active Directory**.

> [AZURE.NOTE] Al configurar el nuevo administrador de Azure AD, el nuevo nombre del administrador (usuario o grupo) no puede estar en la base de datos maestra a modo de inicio de sesión para la autenticación de SQL Server. Si estuviera presente, se producirá un error en el programa de instalación del administrador de Azure AD y, por lo tanto, deberá revertir su creación e indicar que ese administrador (nombre) ya existe. Puesto que tal inicio de sesión para la autenticación de SQL Server no forma parte de Azure AD, se producirá un error cada vez que intente conectarse al servidor mediante la autenticación de Azure AD.

Para quitar un administrador más adelante, en la parte superior de la hoja **Administrador de Active Directory**, haga clic en **Quitar administrador**.

### Aprovisionar un administrador de Azure AD para Azure SQL Server mediante PowerShell



Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Para aprovisionar un administrador de Azure AD, debe ejecutar los siguientes comandos de Azure PowerShell:

- Add-AzureRmAccount
- Select-AzureRmSubscription


Cmdlets que se usan para aprovisionar y administrar administradores de Azure AD:

| Nombre del cmdlet | Descripción |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Aprovisiona un administrador de Azure Active Directory para el servidor de Azure SQL Server. (Debe ser de la suscripción actual). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Quita un administrador de Azure Active Directory del servidor de Azure SQL Server. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | Devuelve información sobre un administrador de Azure Active Directory configurado actualmente para el servidor de Azure SQL Server. |

Use el comando de PowerShell get-help para ver más detalles de cada uno de estos comandos; por ejemplo, ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

El script siguiente aprovisiona un grupo de administradores de Azure AD denominado **DBA\_Group** (Id. de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para el servidor **demo\_server**, en un grupo de recursos llamado **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

El parámetro de entrada **DisplayName** acepta tanto el nombre para mostrar de Azure AD, como el nombre principal de usuario. Por ejemplo, ``DisplayName="John Smith"`` y ``DisplayName="johns@contoso.com"``. En los grupos de Azure AD solo se admite el nombre para mostrar de Azure AD.

> [AZURE.NOTE] El comando ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` de Azure PowerShell no le impedirá aprovisionar administradores de Azure AD para usuarios no admitidos. Es posible aprovisionar un usuario que no se admita, pero no podrá conectarse a una base de datos. (Consulte la lista de administradores admitidos en la sección anterior **Características y limitaciones de Azure AD**).

En el ejemplo siguiente se usa el elemento opcional **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] El elemento **ObjectID** de Azure AD es necesario cuando el valor **DisplayName** no es único. Para recuperar los valores **ObjectID** y **DisplayName**, use la sección Active Directory del Portal de Azure clásico y visualice las propiedades de un usuario o grupo.

En el ejemplo siguiente se devuelve información sobre el administrador actual de Azure AD para el servidor de Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

En el ejemplo siguiente, se quita un administrador de Azure AD: 
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5\. Configurar los equipos cliente.

En todos los equipos cliente, desde el que las aplicaciones o los usuarios se conectan a la Base de datos SQL de Azure mediante identidades de Azure AD, debe instalar el software siguiente:

- .NET Framework 4.6 o una versión posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- La Biblioteca de autenticación de Azure Active Directory para SQL Server (**ADALSQL.DLL**) está disponible en varios idiomas (x86 y amd64) en el centro de descarga en la sección [Biblioteca de autenticación de Microsoft Active Directory para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### Herramientas

- La instalación de [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o de [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) cumple con los requisitos de .NET Framework 4.6.
- SSMS instalará la versión x86 de **ADALSQL.DLL**. (En este momento, SSMS no solicita un reinicio obligatorio después de la instalación. Esto deberá solucionarse en una futura versión de CTP).
- SSMS instala la versión amd64 de **ADALSQL.DLL**. SSDT solo admite parcialmente la autenticación de Azure AD.
- La versión más reciente de Visual Studio de la sección [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumple con los requisitos de .NET Framework 4.6, pero no instala la versión amd64 necesaria de **ADALSQL.DLL**.

## 6\. Crear usuarios de base de datos independiente en la base de datos y asignados a identidades de Azure AD.

### Sobre los usuarios de bases de datos independientes

La autenticación de Azure Active Directory requiere que los usuarios de la base de datos se creen como usuarios de bases de datos independientes. Un usuario de base de datos independiente basado en una identidad de Azure AD es un usuario de base de datos que no tiene un inicio de sesión en la base de datos maestra y que se asigna a una identidad en el directorio de Azure AD que está asociado a la base de datos. La identidad de Azure AD puede ser una cuenta de usuario individual o un grupo. Para más información sobre los usuarios de bases de datos independientes, vea [Usuarios de bases de datos independientes: cómo hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx). No se pueden crear usuarios de base de datos (con la expectativa de administradores) mediante el portal y los roles des RBAC no se propagan a SQL Server.

### Conectarse a la base de datos de usuarios mediante SQL Server Management Studio

Para confirmar que el administrador de Azure AD está correctamente configurado, conéctese a la base de datos **maestra** con la cuenta de administrador de Azure AD. Para aprovisionar un usuario de base de datos independiente basada en un directorio de Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD que tenga acceso a la base de datos.

> [AZURE.IMPORTANT] La compatibilidad con la autenticación de Azure Active Directory está disponible con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

#### Conectarse mediante la autenticación integrada de Active Directory

Use este método si tiene la sesión iniciada en Windows con sus credenciales de Azure Active Directory desde un dominio federado.

1. Inicie Management Studio y, en el cuadro de diálogo denominado **Conectarse al motor de base de datos** (o **Conectarse al servidor**), que encontrará en el cuadro **Autenticación**, seleccione **Autenticación integrada de Active Directory**. No se necesita contraseña ni se puede especificar porque se presentarán las credenciales existentes para la conexión.
2. Haga clic en el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectarse a la base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse.

#### Conectarse mediante la autenticación de contraseña de Active Directory

Use este método al conectarse con un nombre de entidad de seguridad de Azure AD mediante el dominio administrado de Azure AD. También puede utilizarlo para la cuenta federada sin acceso al dominio, por ejemplo, cuando se trabaja de forma remota.

Use este método si tiene la sesión iniciada en Windows con las credenciales de un dominio que no está federado con Azure, o cuando utiliza la autenticación de Azure AD mediante Azure AD basado en el dominio inicial o del cliente.

1. Inicie Management Studio y, en el cuadro de diálogo **Conectarse al motor de base de datos** (o **Conectarse al servidor**), en el cuadro **Autenticación**, seleccione **Autenticación de contraseña de Active Directory**.
2. En el cuadro **Nombre de usuario**, escriba el nombre de usuario de Azure Active Directory en el formato **username@domain.com**. Debe tratarse de una cuenta de Azure Active Directory o una cuenta de un dominio federado con el directorio de Azure Active Directory.
3. En el cuadro **Contraseña**, escriba la contraseña de usuario de la cuenta de Azure Active Directory o la de la cuenta de dominio federado.
4. Haga clic en el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectarse a una base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse.


### Crear un usuario de base de datos independiente en Azure AD en una base de datos de usuario

Para crear un usuario de base de datos independiente basada en Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD (como se describe en el procedimiento anterior), como un usuario con al menos el permiso **ALTER ANY USER**. Después, use la sintaxis de Transact-SQL siguiente:

	CREATE USER Azure_AD_principal_name
	FROM EXTERNAL PROVIDER;


*Azure\_AD\_principal\_name* puede ser el nombre principal de usuario de un usuario de Azure AD, o el nombre para mostrar de un grupo de Azure AD.

**Por ejemplo:** para crear un usuario de base de datos independiente que represente un usuario de dominio administrado o federado de Azure AD:

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para crear un usuario de base de datos independiente que represente un grupo de dominio federado o de Azure AD:

	CREATE USER [Nurses] FROM EXTERNAL PROVIDER;


Para más información sobre la creación de usuarios de bases de datos independientes basados en identidades de Azure Active Directory, vea [CREAR USUARIO (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

Cuando se crea un usuario de base de datos, dicho usuario recibe el permiso **CONNECT** y puede conectarse a esa base de datos como un miembro con el rol **PUBLIC**. En un principio, los únicos permisos disponibles para el usuario son los permisos que se conceden al rol **PUBLIC** o cualquier otro permiso que se conceda a los grupos de Windows de los que sea miembro. Cuando se aprovisiona un usuario de base de datos de independiente basada en AD Azure, se pueden conceder permisos adicionales al usuario, del mismo modo que se conceden permisos a cualquier otro tipo de usuario. Normalmente, se conceden permisos a roles de base de datos y después se agregan usuarios a los roles. Para obtener más información, consulte [Conceptos básicos de los permisos de los motores de las bases de datos](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obtener más información sobre los roles especiales de Base de datos SQL, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md). Un usuario de dominio federado que se importa en un dominio de administración debe usar la identidad de dominio administrado.

> [AZURE.NOTE] Los usuarios de Azure AD se marcan en los metadatos de la base de datos con el tipo E (EXTERNAL\_USER) y en los grupos con el tipo X (EXTERNAL\_GROUPS). Para obtener información, vea [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## 7\. Conectarse a la base de datos mediante identidades de Azure Active Directory

La autenticación de Azure Active Directory admite los siguientes métodos de conexión a una base de datos mediante identidades de Azure AD:

- Mediante la autenticación integrada de Windows.
- Con el nombre y la contraseña de una entidad de seguridad de Azure AD

### 7\.1. Conexión mediante autenticación integrada (Windows)

Para usar la autenticación integrada de Windows, el directorio de Active Directory de su dominio debe estar federado con Azure Active Directory y la aplicación cliente (o un servicio) que se conecte a la base de datos debe ejecutarse en un equipo unido al dominio con las credenciales de dominio de un usuario.

Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication de la cadena de conexión de la base de datos debe establecerse como Active Directory Integrated. En el siguiente ejemplo de código de C# se usa ADO.NET.

	string ConnectionString =
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Tenga en cuenta que no se admite la palabra clave ``Integrated Security=True`` de la cadena de conexión para conectarse a la Base de datos SQL de Azure.

### 7\.2. Conexión con el nombre y la contraseña de una entidad de seguridad de Azure AD
Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication debe establecerse como Active Directory Password y la cadena de conexión debe contener los valores y las palabras clave identificador/UID y constraseña/PWD del usuario En el siguiente ejemplo de código de C# se usa ADO.NET.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Para ejemplos de código específicos y que estén relacionados con la autenticación de Azure AD, vea el [Blog de seguridad de SQL Server](http://blogs.msdn.com/b/sqlsecurity/) en MSDN.

## Consulte también

[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)

[Bases de datos independientes](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png

<!----HONumber=AcomDC_0204_2016-->