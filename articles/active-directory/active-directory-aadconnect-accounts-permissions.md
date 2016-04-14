<properties
   pageTitle="Cuentas y permisos de Azure AD Connect | Microsoft Azure"
   description="En este tema se describen las cuentas usadas y creadas, así como los permisos necesarios."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="02/16/2016"
   ms.author="andkjell;billmath"/>


# Azure AD Connect: cuentas y permisos

El asistente para instalación de Azure AD Connect ofrece dos itinerarios diferentes:

- En configuración rápida se requieren más privilegios para que podamos realizar su configuración fácilmente, sin necesidad de crear o configurar permisos por separado.

- En configuración personalizada se ofrecen más opciones, pero existen algunas situaciones en las que necesitará asegurarse de tener los permisos correctos.

## Documentación relacionada
Si no leyó la documentación que se encuentra en [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md), en la tabla siguiente se proporcionan vínculos a temas relacionados.

| Tema. | |
| --------- | --------- |
| Instalación mediante configuración rápida | [Instalación rápida de Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Instalación mediante configuración personalizada | [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Actualización desde DirSync | [Actualización desde la herramienta Sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## Instalación de la configuración rápida
En la configuración rápida, el asistente para instalación pedirá las credenciales de administrador de organización de AD DS para que la instancia de Active Directory local pueda configurarse con los permisos necesarios para Azure AD Connect. Si va a actualizar desde DirSync, las credenciales de administradores de organización de AD DS se usan para restablecer la contraseña de la cuenta utilizada por DirSync. También necesitará credenciales de administrador global de Azure AD.

Página del asistente | Credenciales recopiladas | Permisos necesarios| Se usa para
------------- | ------------- |------------- |------------- |
N/D|Usuario que ejecuta el asistente para instalación| Administrador del servidor local| <li>Crea la cuenta local que se usará como [cuenta de servicio de motor de sincronización](#azure-ad-connect-sync-service-account).
Conectarse a Azure| Credenciales de directorio de Azure AD | Rol de administrador global en Azure AD | <li>Habilitación de la sincronización en el directorio de Azure AD.</li> <li>Creación de la [cuenta de Azure AD](#azure-ad-service-account) que se usará para operaciones de sincronización continua en Azure AD.</li>
Conectarse a AD DS | Credenciales de Active Directory local | Miembro del grupo de administradores de empresa (EA) en Active Directory| <li>Crea un [cuenta](#active-directory-account) en Active Directory y concede permisos en ella. Esta cuenta creada se usa para leer y escribir información de directorio durante la sincronización.</li>

### Credenciales de administrador de organización
Estas credenciales solo se usan durante la instalación y no se usarán una vez completada la misma. Se trata de administrador de empresa, no de administrador de dominio, para asegurarse de que los permisos de Active Directory se pueden establecer en todos los dominios.

### Credenciales de administrador global
Estas credenciales solo se usan durante la instalación y no se usarán una vez completada la misma. Se utiliza para crear la [cuenta de Azure AD](#azure-ad-service-account) usada para sincronizar cambios en Azure AD. La cuenta también habilitará la sincronización como una característica de Azure AD.

### Permisos de la cuenta de AD DS creada para la configuración rápida
La [cuenta](#active-directory-account) creada para leer y escribir en AD DS tendrá los permisos siguientes cuando se cree con la configuración rápida:

| Permiso | Usado para |
| ---- | ---- |
| <li>Replicación de cambios de directorio</li> <li>Replicación de todos los cambios de directorio | Sincronización de contraseñas |
| Lectura y escritura de todas las propiedades Usuario | Importación y Exchange híbrido |
| Lectura y escritura de todas las propiedades iNetOrgPerson | Importación y Exchange híbrido |
| Lectura y escritura de todas las propiedades Grupo | Importación y Exchange híbrido |
| Lectura y escritura de todas las propiedades Contacto | Importación y Exchange híbrido |
| Restablecimiento de contraseña | Preparación para habilitar la escritura diferida de contraseñas |

## Instalación de la configuración personalizada
Cuando se usa la configuración personalizada, la cuenta usada para conectarse a Active Directory debe crearse antes de la instalación. Los permisos que debe conceder a esta cuenta se pueden encontrar en [Creación de la cuenta de AD DS](#create-the-ad-ds-account).

Página del asistente | Credenciales recopiladas | Permisos necesarios| Se usa para
------------- | ------------- |------------- |-------------
N/D|Usuario que ejecuta el asistente para instalación|<li>Administrador del servidor local</li><li>Si usa SQL Server completo, el usuario debe ser administrador del sistema (SA) en SQL</li>| De forma predeterminada, crea la cuenta local que se usará como [cuenta de servicio del motor de sincronización](#azure-ad-connect-sync-service-account). La cuenta se crea únicamente si el administrador no especifica una cuenta determinada.
Instalar servicios de sincronización, opción de cuenta de servicio | Credenciales de cuenta de usuario local o de AD | Usuario, el asistente para instalación concederán los permisos|Si el administrador especifica una cuenta, esta se usa como cuenta de servicio para el servicio de sincronización.
Conectarse a Azure|Credenciales de directorio de Azure AD| Rol de administrador global en Azure AD| <li>Habilitación de la sincronización en el directorio de Azure AD.</li> <li>Creación de la [cuenta de Azure AD](#azure-ad-service-account) que se usará para operaciones de sincronización continua en Azure AD.</li>
Conectar sus directorios|Credenciales de Active Directory local para cada bosque que se conectará a Azure AD | Los permisos dependerán de qué características permite y se pueden encontrar en [Creación de la cuenta de AD DS](#create-the-ad-ds-account) |Esta cuenta se usa para leer y escribir información de directorio durante la sincronización.
Servidores de AD FS|Para cada servidor de la lista, el asistente recopila credenciales si las credenciales de inicio de sesión del usuario que ejecuta el asistente no son suficientes para conectarse|Administrador de dominio|Instalación y configuración del rol de servidor de AD FS.
Servidores proxy de aplicación web |Para cada servidor de la lista, el asistente recopila credenciales si las credenciales de inicio de sesión del usuario que ejecuta el asistente no son suficientes para conectarse|Administrador local en la máquina de destino|Instalación y configuración del rol de servidor de WAP.
Credenciales de confianza del proxy |Credenciales de confianza del servicio de federación (las credenciales que el proxy usará para inscribirse para obtener un certificado de confianza de FS) |Cuenta de dominio que es un administrador local del servidor de AD FS|Inscripción inicial de certificados de confianza de FS WAP
Página de cuenta de servicio de AD FS, "Usar una opción de cuenta de usuario de dominio"|Credenciales de cuenta de usuario de AD|Usuario de dominio|La cuenta de usuario de AD cuyas credenciales se proporcionan se usará como cuenta de inicio de sesión del servicio AD FS.

### Creación de la cuenta de AD DS
Al instalar Connect de Azure AD, la cuenta que especifique en la página **Conectar sus directorios** debe estar presente en Active Directory tener los permisos concedidos necesarios. El asistente para instalación no comprobará los permisos y los problemas solo se encontrarán durante la sincronización.

Los permisos que requiera dependen de las características opcionales que habilite. Si tiene varios dominios, se deben conceder los permisos para todos los dominios del bosque. Si no habilita ninguna de estas características de forma predeterminada, los permisos **Usuario de dominio** serán suficientes.

| Característica | Permisos |
| ------ | ------ |
| Sincronización de contraseñas | <li>Replicar cambios de directorio</li> <li>Replicar todos los cambios de directorio |
| Implementación híbrida de Exchange | Permisos de escritura en los atributos que se documentan en [Escritura diferida híbrida de Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para usuarios, grupos y contactos. |
| Escritura diferida de contraseñas | Permisos de escritura en los atributos que se documentan en [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) para los usuarios. |
| Escritura diferida de dispositivos | Los permisos concedidos con un script de PowerShell como se describe en [Escritura diferida de dispositivos](active-directory-aadconnect-feature-device-writeback.md).|
| Escritura diferida de grupos | Leer, crear, actualizar y eliminar objetos de grupo en la UO donde se deben ubicar los grupos de distribuciones.|

## Actualizar
Al actualizar desde una versión de Azure AD Connect a una nueva versión, necesitará los siguientes permisos:

| Principal | Permisos necesarios | Usado para |
| ---- | ---- | ---- |
| Usuario que ejecuta el asistente para instalación | Administrador del servidor local | Archivos binarios de la actualización. |
| Usuario que ejecuta el asistente para instalación | Miembro de ADSyncAdmins | Realice cambios en las reglas de sincronización y en otra configuración. |
| Usuario que ejecuta el asistente para instalación | Si utiliza un servidor SQL completo: DBO (o similar) de la base de datos del motor de sincronización | Realice los cambios de nivel de base de datos, como actualizar tablas con nuevas columnas. |

## Más información acerca de las cuentas creadas

### Cuenta de Active Directory

Si utiliza la configuración rápida, se creará una cuenta en Active Directory que se usará para sincronización. La cuenta creada se ubicará en el dominio raíz del bosque en el contenedor Usuarios y su nombre tendrá el prefijo **MSOL\_**. La cuenta se crea con una contraseña larga compleja que no expira. Si tiene una directiva de contraseñas en el dominio, asegúrese de que se permitan contraseñas largas y complejas para esta cuenta.

![Cuenta de AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Cuentas de servicio de sincronización de Azure AD Connect
El asistente para instalación crea una cuenta de servicio local (a menos que especifique la cuenta que se desea usar en la configuración personalizada). La cuenta lleva delante **AAD\_** y se usa para el servicio de sincronización real como cuenta de ejecución. Si instala Azure AD Connect en un controlador de dominio, la cuenta se crea en el dominio. Si usa un servidor remoto que ejecuta SQL Server, la cuenta de servicio **AAD\_** debe estar ubicada en el dominio.

![Cuenta de servicio de sincronización](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

La cuenta se crea con una contraseña larga compleja que no expira.

Esta cuenta la usará Windows para almacenar las claves de cifrado, por lo que no se debe restablecer o cambiar la contraseña para esta cuenta.

Si usa SQL Server completo, la cuenta de servicio será el DBO de la base de datos creada para el motor de sincronización. El servicio no funcionará como se pretende con ningún otro permiso. También se creará un inicio de sesión SQL.

A la cuenta también se le conceden permisos para archivos, claves del Registro y otros objetos relacionados con el motor de sincronización.

### Cuenta de servicio de Azure AD
Se creará una cuenta de Azure AD para el uso del servicio de sincronización. Esta cuenta se puede identificar por su nombre para mostrar.

![Cuenta de AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

El nombre del servidor en el que se usa la cuenta se puede identificar en la segunda parte del nombre de usuario. En la imagen anterior el nombre del servidor es FABRIKAMCON. Si tiene servidores de ensayo, cada servidor tendrá su propia cuenta. Hay un límite de 10 cuentas de servicio de sincronización en Azure AD.

La cuenta de servicio se crea con una contraseña larga compleja que no expira. Se le concede el rol especial **Cuentas de sincronización de directorio** que solo tiene permisos para realizar tareas de sincronización de directorios. Este rol integrado especial no se puede conceder fuera del asistente de Azure AD Connect y el portal de Azure solo mostrará esta cuenta con el rol **Usuario**.

![Rol de cuenta de AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Pasos siguientes

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->