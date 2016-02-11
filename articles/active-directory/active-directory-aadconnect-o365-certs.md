<properties
	pageTitle="Orientación de renovación de certificado para los usuarios de Office 365 y Azure AD | Microsoft Azure"
	description="Este artículo explica a los usuarios de Office 365 cómo solucionar problemas con mensajes de correo electrónico que informan sobre la renovación de un certificado."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>


# Renovación de certificados de federación para Office 365 y Azure AD

Si recibe un correo electrónico o una notificación de portal que le solicita renovar el certificado de Office 365, este artículo está pensado para ayudarle a resolver el problema y evitar que vuelva a producirse. En este artículo se supone que está usando AD FS como el servidor de federación.

>[AZURE.IMPORTANT] Tenga en cuenta que se puede producir un error en la autenticación a través del proxy en Windows Server 2012 o Windows Server 2008 R2 después de realizar una de las siguientes acciones:
>
- El proxy renueva su token de confianza después de la sustitución de certificados en AD FS.
- Ha reemplazado manualmente sus certificados de AD FS.
>
Hay una revisión disponible para corregir este problema. Vea [Authentication through proxy fails in Windows Server 2012 or Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446) (Se produce un error en la autenticación a través del proxy en Windows Server 2012 o Windows 2008 R2 SP1).

## Compruebe si tiene que hacer algo

Si utiliza AD FS 2.0 o versiones posteriores, Office 365 y Azure AD actualizan automáticamente el certificado antes de que expire. No es necesario realizar los pasos manualmente ni ejecutar un script como una tarea programada. Para que funcione, las siguientes opciones de configuración predeterminadas de AD FS deben estar vigentes:

- La propiedad de AD FS AutoCertificateRollover debe establecerse en True, que indica que AD FS generará automáticamente nuevos certificados de descifrado y firma de tokens antes de que expiren los antiguos.
	- Si el valor es False, está usando la configuración de certificado personalizada. Vaya [aquí](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) para obtener instrucciones completas.
- Los metadatos de federación deben estar disponibles para la red de Internet pública.

	Aquí se muestra cómo realizar la comprobación:

	- Compruebe que la instalación de AD FS usa la sustitución automática de certificados ejecutando el siguiente comando en una ventana de comandos de PowerShell en el servidor de federación principal:

	`PS C:\> Get-ADFSProperties`

(tenga en cuenta que si utiliza AD FS 2.0, necesitará ejecutar primero Microsoft.Adfs.Powershell Add-Pssnapin).

Compruebe que se puede obtener acceso públicamente a los metadatos de federación, desplácese hasta la siguiente dirección URL desde un equipo de la red de Internet pública (fuera de la red corporativa):


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

donde `(your_FS_name) ` se reemplaza por el nombre de host de servicio de federación que usa su organización, por ejemplo, fs.contoso.com. Si es capaz de comprobar ambos de estos valores correctamente, no tiene que hacer nada más.

Ejemplo: https://fs.contos.com/federationmetadata/2007-06/federationmetadata.xml

## Si la propiedad AutoCertificateRollover está establecida en False

Si la propiedad AutoCertificateRollover se establece en False, está usando la configuración de certificados de AD FS no predeterminada. La razón más común es que su organización administra certificados de AD FS inscritos de una entidad de certificación profesional. En este caso, deberá renovar y actualizar los certificados por su cuenta. Use la guía [aquí](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

## Si no se puede acceder a los metadatos públicamente
Si el valor de AutocertificateRollover es True, pero los metadatos de federación no están disponibles públicamente, utilice el procedimiento siguiente para asegurarse de que los certificados se actualizan de forma local y en la nube:

### Compruebe que el sistema de AD FS ha generado un nuevo certificado.

- Compruebe que la sesión en el servidor de AD FS principal está iniciada.
- Compruebe los certificados de firma actuales en AD FS abriendo una ventana de comandos de PowerShell y ejecutando el siguiente comando:

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(tenga en cuenta que si usa AD FS 2.0, tendrá que ejecutar Add-Pssnapin Microsoft.Adfs.Powershell primero)


- Examine la salida del comando en los certificados que se muestran. Si AD FS ha generado un nuevo certificado, debería ver dos certificados en la salida: uno para el que el valor de IsPrimary es True y la fecha de NotAfter está dentro de 5 días y uno para el que IsPrimary es False y NotAfter es aproximadamente un año en el futuro.

- Si solo ve un certificado y la fecha de NotAfter está dentro de 5 días, deberá generar un nuevo certificado mediante la ejecución de los pasos siguientes.

- Para generar un nuevo certificado, ejecute el siguiente comando en un símbolo del sistema de PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- Compruebe la actualización ejecutando de nuevo el comando siguiente: PS C:\>Get-ADFSCertificate –CertificateType token-signing
- A continuación, para actualizar manualmente las propiedades de confianza de federación de Office 365, siga estos pasos.

Ahora deben aparecer dos certificados, uno de los cuales tiene una fecha de NotAfter de aproximadamente un año en el futuro y para el que el valor de IsPrimary es False.


### Para actualizar manualmente las propiedades de confianza de federación de Office 365, siga estos pasos.

1.	Abra el módulo Microsoft Azure Active Directory para Windows PowerShell.
2.	Ejecute $cred=Get-Credential. Cuando este cmdlet le solicite las credenciales, escriba sus credenciales de cuenta de administrador de servicios en la nube.
3.	Ejecute Connect-MsolService –Credential $cred. Este cmdlet le permite conectarse al servicio en la nube. Es necesario crear un contexto que le permita conectarse con el servicio en la nube antes de ejecutar cualquiera de los cmdlets adicionales que instala la herramienta.
4.	Si ejecuta estos comandos en un equipo que no sea el servidor de federación principal de AD FS, ejecute Set-MSOLAdfscontext -Computer <AD FS primary server>, donde <AD FS primary server> es el nombre de dominio completo interno del servidor de AD FS principal. Este cmdlet crea un contexto que le permite conectarse a AD FS.
5.	Ejecute Update-MSOLFederatedDomain –DomainName <domain>. Este cmdlet actualiza la configuración de AD FS en el servicio en la nube y configura la relación de confianza entre los dos.

>[AZURE.NOTE] Si necesita admitir varios dominios de nivel superior, por ejemplo, contoso.com y fabrikam.com, debe utilizar el modificador SupportMultipleDomain con cualquier cmdlet. Para obtener más información, vea Compatibilidad con varios dominios de nivel superior. Por último, compruebe que todos los servidores proxy de aplicación web se actualizan con el paquete acumulativo de [Windows Server de mayo de 2014](http://support.microsoft.com/kb/2955164). De lo contrario, es posible que los servidores proxy no se actualicen con el nuevo certificado y se produzca una interrupción del sistema.

<!---HONumber=AcomDC_0128_2016-->