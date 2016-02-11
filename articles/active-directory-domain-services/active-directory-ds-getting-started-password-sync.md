<properties
	pageTitle="Vista previa de los Servicios de dominio de Azure Active Directory: introducción | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: introducción

## Paso 5: Habilitación de la sincronización de contraseñas
Después de habilitar los Servicios de dominio de Azure AD para su inquilino de Azure AD, el siguiente paso es habilitar la sincronización de contraseñas. Así los usuarios pueden iniciar sesión en el dominio con sus credenciales corporativas.

Los pasos que se deben seguir son distintos en función de si su organización es un inquilino de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

### Inquilinos solo en la nube: habilitación de la generación de hash de credenciales de NTLM y Kerberos en Azure AD
Si su organización es un inquilino de Azure AD solo de nube, los usuarios que tengan que usar los Servicios de dominio de Azure AD deberán cambiar sus contraseñas. Con este paso se generan en Azure AD los valores de hash de credenciales que necesitan los Servicios de dominio de Azure AD para la autenticación Kerberos y NTLM. Puede caducar las contraseñas de todos los usuarios en el inquilino que tiene que usar los Servicios de dominio de Azure AD o indicar a estos usuarios que cambien sus contraseñas.

Estas son las instrucciones que debe proporcionar a los usuarios para cambiar sus contraseñas:

1. Navegue a la página del panel de acceso de Azure AD de su organización. Normalmente se encuentra en [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Seleccione la pestaña **Perfil** en esta página.
3. Haga clic en el icono **Cambiar contraseña** de esta página para iniciar un cambio de contraseña.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Se abrirá la página **Cambiar contraseña**. Los usuarios pueden escribir su contraseña existente (antigua) y proceder a cambiarla.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Después de que los usuarios han cambiado su contraseña, la nueva contraseña se podrá usar en breve en los Servicios de dominio de Azure AD. Después de unos minutos, los usuarios pueden iniciar sesión en equipos unidos al dominio administrado con su contraseña recién cambiada.


### Inquilinos sincronizados: habilitación de la sincronización de valores hash de credenciales de NTLM y Kerberos con Azure AD
Si el inquilino de Azure AD de la organización está configurado para sincronizarse con su directorio local mediante Azure AD Connect, deberá configurar Azure AD Connect para sincronizar los valores de hash de credenciales que son necesarios para la autenticación NTLM y Kerberos. Estos valores de hash no están sincronizados con Azure AD de forma predeterminada pero los pasos siguientes le permitirán sincronizar los valores de hash con el inquilino de Azure AD.

#### Instalación o actualización de Azure AD Connect

Deberá instalar la versión recomendada más reciente de Azure AD Connect en un equipo unido a un dominio. Si tiene una instancia existente del programa de instalación de Azure AD Connect, deberá actualizarla para usar la compilación de disponibilidad general de Azure AD Connect. Asegúrese de usar la versión más reciente de Azure AD Connect, con el fin de evitar errores y problemas conocidos.

**[Descarga de Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versión mínima recomendada: **1.0.9131**, publicada el 3 de diciembre de 2015.

  >[AZURE.WARNING] Para permitir que las credenciales de contraseñas heredadas (necesarias para la autenticación de NTLM y Kerberos) se sincronicen con el inquilino de Azure AD, DEBE instalar la versión recomendada más reciente de Azure AD Connect. Esta funcionalidad no está disponible en versiones anteriores de Azure AD Connect o con la herramienta DirSync heredada.

Puede encontrar las instrucciones de instalación de Azure AD Connect en el siguiente artículo: [Introducción a Azure AD Connect](../active-directory/active-directory-aadconnect.md).


#### Forzar la sincronización completa de contraseñas con Azure AD

Para forzar la sincronización completa de contraseñas y permitir que los valores de hash de contraseña (incluidos los valores de hash de credenciales necesarios para la autenticación NTLM o Kerberos) de los usuarios locales se sincronicen con el inquilino de Azure AD, ejecute el siguiente script de PowerShell en cada bosque de AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

En función del tamaño de su directorio (número de usuarios, grupos etc.), la sincronización de credenciales con Azure AD llevará tiempo. Las contraseñas se podrán usar en el dominio administrado de los Servicios de dominio de Azure AD poco después de que los valores hash se hayan sincronizado con Azure AD.

<!---HONumber=AcomDC_0128_2016-->