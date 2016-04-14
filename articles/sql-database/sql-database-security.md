<properties
   pageTitle="Información general sobre la seguridad de Base de datos SQL"
   description="Obtenga información acerca de la seguridad de la Base de datos SQL de Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local en cuanto a la autenticación, autorización, seguridad de conexión, cifrado y cumplimiento normativo."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jeffreyg"
   editor="jeffreyg"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="01/22/2016"
   ms.author="thmullan;jackr"/>


# Protección de bases de datos SQL

## Información general

Este artículo describe los fundamentos de la protección de la capa de datos de una aplicación que use Base de datos SQL de Azure. En concreto, este artículo le ayudará a empezar a trabajar con los recursos para limitar el acceso, proteger datos y supervisar actividades en una base de datos creada en el tutorial [Introducción a la Base de datos SQL](sql-database-get-started.md). Para obtener una descripción completa de las características de seguridad disponibles en todas las versiones de SQL, consulte [Seguridad y protección (motor de base de datos)](https://msdn.microsoft.com/library/bb510589). Asimismo, tiene información adicional disponible en las [Notas del producto técnicas acerca de la seguridad y la base de datos SQL de Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (en PDF).

## Seguridad de conexión

Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una nueva base de datos, primero debe crear una regla de firewall de nivel de servidor mediante el Portal de Azure clásico, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible. Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/ee621782).

Todas las conexiones a Base de datos SQL de Azure requieren cifrado (SSL/TLS) siempre que haya datos "en tránsito" hacia y desde la base de datos. En la cadena de conexión de su aplicación, debe especificar los parámetros para cifrar la conexión y *no* confiar en el certificado de servidor (esto se hace automáticamente si copia la cadena de conexión en el Portal de Azure clásico). De lo contrario, la conexión no comprobará la identidad del servidor y será vulnerable a los ataques de tipo "Man in the middle". Por ejemplo, en el caso del controlador de ADO.NET, los parámetros de la cadena de conexión son **Encrypt = True** y **TrustServerCertificate = False**. Para obtener más información, consulte [Validación de certificados y cifrado de conexiones de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## Autenticación

La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Base de datos SQL admite dos tipos de autenticación:

 - **Autenticación de SQL**, la cual usa un nombre de usuario y una contraseña.
 - **Autenticación de Azure Active Directory**, la cual usa las identidades administradas por Azure Active Directory y es compatible con los dominios administrados e integrados

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo". Si desea usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal. Consulte el tutorial [Conectar a la Base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md), para obtener información acerca de cómo crear un administrador de Azure AD y así habilitar la autenticación de Azure Active Directory.

Como procedimiento recomendado, la aplicación debe usar una cuenta distinta para autenticar. De esta forma, puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de la aplicación sea vulnerable a ataques por inyección de código SQL. Se recomienda crear un [usuario de base de datos independiente](https://msdn.microsoft.com/library/ff929188), ya que este permitirá a la aplicación autenticarse directamente en una base de datos única. Para crear un usuario de base de datos independiente que utilice la autenticación de SQL, ejecute el comando de T-SQL siguiente cuando haya iniciado sesión como administrador de servidor en la base de datos de usuario a la que está conectado:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si creó un administrador de Azure AD, puede crear un usuario de base de datos independiente que use la autenticación de Azure Active Directory mediante la ejecución del comando de T-SQL siguiente mientras esté conectado a la base de datos de usuario como administrador de Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

En cualquiera de los casos, la cadena de conexión de la aplicación debe especificar estas credenciales de usuario para conectarse a la base de datos, en lugar del inicio de sesión de administrador de servidor.

Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Administración de bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](sql-database-manage-logins.md).


## Autorización
Autorización hace referencia a lo que se puede realizar en Base de datos SQL de Azure y la controlan los permisos y las pertenencias a grupos de roles de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Base de datos SQL de Azure facilita la administración con roles en T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db\_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen varias formas de limitar aún más lo que los usuarios pueden hacer con Base de datos SQL de Azure:

* Los [roles de base de datos](https://msdn.microsoft.com/library/ms189121) que no sean db\_datareader y db\_datawriter se pueden utilizar para crear cuentas de usuario de aplicación más eficaces o cuentas de administración menos eficaces.
* Los [permisos](https://msdn.microsoft.com/library/ms191291) granulares permiten control qué operaciones se pueden realizar en columnas individuales, tablas, vistas, procedimientos y otros objetos de la base de datos.
* La [suplantación](https://msdn.microsoft.com/library/vstudio/bb669087) y la [firma de módulos](https://msdn.microsoft.com/library/bb669102) se pueden utilizar para elevar los permisos temporalmente de forma segura.
* La [seguridad del nivel de fila](https://msdn.microsoft.com/library/dn765131) permite filtrar las filas que puede ver el usuario.
* El [enmascaramiento de datos](sql-database-dynamic-data-masking-get-started.md) puede utilizarse para limitar la exposición de información confidencial.
* Los [procedimientos almacenados](https://msdn.microsoft.com/library/ms190782) puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

La administración de bases de datos y servidores lógicos desde el Portal de Azure clásico o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en rol en el Portal de Azure](../role-based-access-control-configure.md).


## Cifrado

Base de datos SQL de Azure puede ayudar a proteger los datos mediante el cifrado de los mismos cuando estén "en reposo" o almacenados en archivos de base de datos y copias de seguridad, con el [cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242). Para cifrar una base de datos, conéctese como propietario de la base de datos y ejecute:

```
CREATE DATABASE ENCRYPTION KEY
   WITH ALGORITHM = AES_256
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Si desea conocer otras formas de cifrar los datos secretos, considere:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
* Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar el [Almacén de claves de Azure con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Siempre cifrado](https://msdn.microsoft.com/library/mt163865.aspx) (en vista previa) hace que el cifrado sea transparente para las aplicaciones y permite que los clientes cifren información confidencial dentro de aplicaciones cliente sin compartir las claves de cifrado con Base de datos SQL.

## Auditoría

La auditoría y el seguimiento de eventos de la base de datos pueden ayudarle a mantener el cumplimiento de las reglamentaciones y a identificar cualquier actividad sospechosa. La auditoría de Base de datos SQL permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Almacenamiento de Azure. La auditoría de Base de datos SQL también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados. Para obtener más información, consulte [Introducción a la auditoría de Base de datos SQL](sql-database-auditing-get-started.md).

## Cumplimiento normativo

Además de las anteriores características y funcionalidades que pueden ayudar a su aplicación a cumplir distintos requisitos de cumplimiento de normas de seguridad, la Base de datos SQL de Azure también participa en las auditorías regulares y ha sido certificada con una serie de estándares de cumplimiento normativo. Para obtener más información, consulte el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), donde podrá encontrar la lista más reciente de [certificaciones de cumplimiento de Base de datos SQL](https://azure.microsoft.com/support/trust-center/services/).

<!---HONumber=AcomDC_0128_2016-->