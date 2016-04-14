<properties
	pageTitle="Implementación de la sincronización de contraseñas de sincronización de Azure AD Connect | Microsoft Azure"
	description="Le proporciona la información que necesita para comprender cómo funciona la sincronización de contraseñas y cómo habilitarla en su entorno."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/07/2016"
	ms.author="markusvi;andkjell"/>


# Sincronización de Azure AD Connect: implementación de la sincronización de contraseñas

Con la sincronización de contraseñas los usuarios podrán usar la misma contraseña que usan para iniciar sesión en Active Directory local para iniciar sesión en Azure Active Directory.

El objetivo de este tema es proporcionarle la información que necesita para comprender cómo funciona la sincronización de contraseñas y cómo habilitarla en su entorno.

## Qué es la sincronización de contraseñas

La sincronización de contraseñas es una característica de los servicios de sincronización de Azure Active Directory Connect (sincronización de Azure AD Connect) que sincroniza las contraseñas de usuario de Active Directory local con Azure Active Directory (Azure AD). Esta característica permite que los usuarios inicien sesión en sus servicios de Azure Active Directory (como Office 365, Microsoft Intune, CRM Online, etc.) con la misma contraseña que usan para iniciar sesión en la red local.

> [AZURE.NOTE] Para obtener más información acerca de los Servicios de dominio de Active Directory configurados para la sincronización de contraseñas y FIPS, consulte [Sincronización de contraseñas y FIPS](#password-synchronization-and-fips).

### Disponibilidad de sincronización de contraseñas

Cualquier cliente de Azure Active Directory es apto para ejecutar la sincronización de contraseñas. A continuación se ofrece información sobre la compatibilidad de la sincronización de contraseñas y otras características como la autenticación federada.

### Funcionamiento de la sincronización de contraseñas

La sincronización de contraseñas es una extensión de la característica de sincronización de directorios implementada por la sincronización de Azure AD Connect. Por lo tanto, esta característica requiere la sincronización de directorios entre su local y Azure Active Directory para realizar la configuración.

El Servicio de dominio de Active Directory almacena contraseñas en forma de representación de valor hash de la contraseña de usuario real. El hash de contraseña no puede usarse para iniciar sesión en la red local. También se ha diseñado para que no se pueda deshacer para obtener acceso a la contraseña de texto sin formato del usuario. Para sincronizar una contraseña, la sincronización de Azure AD Connect extrae el hash de contraseña del usuario de Active Directory local. El procesamiento de seguridad adicional se aplica al hash de contraseña antes de que se sincronice con el servicio de Azure Active Directory Authentication. El flujo de datos reales del proceso de sincronización de contraseñas es similar al de sincronización de datos de usuario, como el nombre para mostrar o las direcciones de correo electrónico.

Las contraseñas se sincronizan con más frecuencia que la ventana de sincronización de directorios estándar para otros atributos. Las contraseñas se sincronizan según el usuario y, por lo general, en orden cronológico. Cuando se sincroniza una contraseña de usuario desde AD local a la nube, se sobrescribirá la contraseña existente en la nube.

Al habilitar la característica de sincronización de contraseñas por primera vez, se realizará una sincronización inicial de las contraseñas de todos los usuarios en el ámbito de Active Directory local en Azure Active Directory. No puede definir explícitamente el conjunto de usuarios cuyas contraseñas se sincronizarán con la nube. Posteriormente, cuando un usuario local cambie una contraseña, la característica de sincronización de contraseñas detectará y sincronizará la contraseña cambiada, normalmente en cuestión de minutos. La característica de sincronización de contraseñas reintenta automáticamente las sincronizaciones de contraseñas de usuario erróneas. Si se produce un error al intentar sincronizar una contraseña, el error se registra en el visor de eventos.

La sincronización de una contraseña no influye en los usuarios con la sesión iniciada actualmente. Si un usuario que ha iniciado sesión en un servicio en la nube también cambia la contraseña local, la sesión del servicio en la nube continuará sin interrupciones. Sin embargo, si el servicio en la nube requiere que el usuario vuelva a realizar la autenticación, debe proporcionarse la nueva contraseña. En este punto, el usuario debe proporcionar la nueva contraseña: la contraseña que se ha sincronizado recientemente desde Active Directory local a la nube.

> [AZURE.NOTE] Solo se admite la sincronización de la contraseña para el usuario del tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.

### Funcionamiento de la sincronización de contraseñas con Servicios de dominio de Azure AD

Si habilita este servicio en Azure AD, se requiere la opción de sincronización de contraseñas para obtener una experiencia de inicio de sesión único. Con este servicio habilitado, cambia el comportamiento de sincronización de contraseñas y los valores de hash de contraseña también se sincronizarán tal cual desde el Active Directory local a los Servicios de dominio de Azure Active Directory. La funcionalidad es similar a ADMT (Active Directory Migration Tool) y permite a Servicios de dominio de Azure Active Directory poder autenticar al usuario con todos los métodos disponibles en el AD local.

### Consideraciones sobre la seguridad

Al sincronizar contraseñas, la versión de texto sin formato de una contraseña de usuario no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

Además, no hay ningún requisito en Active Directory local para almacenar la contraseña en un formato cifrado reversible. Se usa un resumen del hash de contraseña de Active Directory para la transmisión entre Azure Active Directory y AD local. El resumen del hash de contraseña no se puede usar para obtener acceso a recursos en el entorno local del cliente.

### Consideraciones de la directiva de contraseña

Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

1. Directiva de complejidad de contraseñas
2. Directiva de expiración de contraseñas

**Directiva de complejidad de contraseñas**

Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas configuradas en Active Directory local reemplazan a las directivas de complejidad que pueden definirse en la nube para los usuarios sincronizados. Esto significa que cualquier contraseña válida en el entorno del Active Directory local del usuario puede usarse para obtener acceso a servicios de Azure AD.

> [AZURE.NOTE] Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.

**Directiva de expiración de contraseñas**

Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de cuenta en la nube se establece en "*No caduca nunca*". Esto significa que es posible que la contraseña de un usuario expire en el entorno local, pero este puede seguir iniciando sesión en los servicios en la nube con la nueva contraseña tras el siguiente ciclo de sincronización de contraseñas.

La contraseña de la nube se actualizará la próxima vez que el usuario cambie la contraseña en el entorno local.

### Sobrescritura de las contraseñas sincronizadas

Un administrador puede restablecer manualmente la contraseña de un usuario con Azure Active Directory PowerShell.

En este caso, la nueva contraseña sobrescribirá la contraseña sincronizada del usuario y todas las directivas de contraseñas definidas en la nube se aplicarán a la nueva contraseña.

Si el usuario cambia la contraseña local de nuevo, la nueva contraseña se sincronizará con la nube y reemplazará a la contraseña actualizada manualmente.

## Preparación para la sincronización de contraseñas


### Habilitación de la sincronización de contraseñas

Si usa la configuración rápida cuando instala Azure AD Connect se habilitará de forma predeterminada la sincronización de contraseñas.

Si usa la configuración personalizada al instalar AD Azure Connect, habilita la sincronización de contraseñas en la página de inicio de sesión de usuario. ![usersignin](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

Si selecciona usar **Federación con AD FS**, opcionalmente puede habilitar la sincronización de contraseñas como una copia de seguridad en caso de que se produzca un error en la infraestructura de AD FS. También puede habilitarla si piensa usa Servicios de dominio de Azure Active Directory.

### Sincronización de contraseñas y FIPS

Si el servidor se bloquea de acuerdo con FIPS (Federal Information Processing Standard), el MD5 se deshabilita. Para habilitar esta opción para la sincronización de contraseñas, agregue la clave enforceFIPSPolicy en miiserver.exe.config en C:\\Archivos de programa\\Azure AD Sync\\Bin.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

El nodo configuration/runtime puede encontrarse al final del archivo config.

Para obtener información sobre la seguridad y FIPS, consulte [AAD Password Sync, Encryption and FIPS compliance](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx) (Sincronización de contraseñas de AAD, cifrado y cumplimiento FIPS)

## Administración de la sincronización de contraseñas

### Solución de problemas de sincronización de contraseñas

Inicie **Administrador del servicio de sincronización**, abra **Conectores**, seleccione el usuario en el que está ubicado el conector de Active Directory, seleccione **Espacio del conector de búsqueda** y busque el usuario que desea.

![csuser](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

En el usuario, seleccione la pestaña **linaje** y asegúrese de que al menos una regla de sincronización muestra la **sincronización de contraseñas** como **True**. Con la configuración predeterminada, sería la regla de sincronización denominada **In from AD - User AccountEnabled**.

Para ver los detalles de la sincronización de contraseñas del objeto, haga clic en el botón **Registro..** en la parte inferior de esta página. Esto producirá en la página una vista histórica de estado de sincronización de contraseñas del usuario de la semana pasada.

![registro de objetos](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

La columna de estado puede tener los valores siguientes y también indica el problema y por qué no se sincroniza una contraseña.

| Estado | Descripción |
| ---- | ----- |
| Correcto | La contraseña se sincronizó correctamente. |
| FilteredByTarget | La contraseña se establece en **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**. La contraseña no se ha sincronizado. |
| NoTargetConnection | No hay ningún objeto en el metaverso o en el espacio del conector de Azure AD. |
| SourceConnectorNotPresent | No se encontró ningún objeto en el espacio del conector de Active Directory local. |
| TargetNotExportedToDirectory | Aún no se exportó el objeto del espacio del conector de Azure AD. |
| MigratedCheckDetailsForMoreInfo | La entrada de registro se creó antes de la versión 1.0.9125.0 y se muestra en su estado heredado. |


### Desencadenamiento de una sincronización completa de todas las contraseñas
No es necesario forzar una sincronización completa de todas las contraseñas, pero si por alguna razón se necesita, aquí está PowerShell para realizarlo.

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0309_2016-->