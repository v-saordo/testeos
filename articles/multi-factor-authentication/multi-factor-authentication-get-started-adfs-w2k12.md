<properties 
	pageTitle="Protección de recursos en la nube y locales mediante el Servidor Azure MFA con Windows Server 2012 R2 AD FS" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS en Windows Server 2012 R2." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="get-started-article" ms.date="03/04/2016"" ms.author="billmath"/>


# Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS

Si su organización está federada con Azure AD y tiene recursos locales o en la nube que desea proteger, puede hacerlo mediante el Servidor Azure Multi-Factor Authentication y configurarlo para que funcione con AD FS de modo que la autenticación multifactor se desencadene para extremos de alto valor.

Esta documentación trata del uso del Servidor Azure Multi-Factor Authentication con AD FS en Windows Server 2012 R2. Para obtener información sobre el uso de Azure Multi-Factor Authentication con AD FS 2.0, consulte [Protección de recursos en la nube y locales mediante el Servidor Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Protección de Windows Server 2012 R2 AD FS con Servidor Azure Multi-Factor Authentication

Al instalar Servidor Azure Multi-Factor Authentication tiene las dos opciones siguientes:

- Instalar Servidor Azure Multi-Factor Authentication localmente en el mismo servidor que AD FS. 
- Instalar el adaptador de Azure Multi-Factor Authentication localmente en el servidor AD FS e instalar al servidor MFA en un equipo diferente.

Antes de comenzar, tenga en cuenta lo siguiente:

- No es necesario que Servidor Azure Multi-Factor Authentication esté instalado en el servidor de federación de AD FS. Sin embargo, el adaptador de Multi-Factor Authentication para AD FS debe estar instalado en un Windows Server 2012 R2 que ejecute AD FS. Puede instalar el servidor en un equipo diferente, siempre y cuando sea una versión compatible e instale al adaptador de AD FS por separado en el servidor de federación de AD FS. Consulte el procedimiento siguiente para obtener instrucciones acerca de cómo instalar el adaptador por separado.
- Cuando se diseñó el adaptador de AD FS del servidor de Multi-Factor Authentication, se estimó que AD FS podría pasar el nombre del usuario de confianza al adaptador que podría utilizarse como nombre de aplicación. Sin embargo, no fue así. Si se usan métodos de autenticación de aplicación móvil o mensaje de texto, las cadenas definidas en la configuración de la compañía contienen un marcador de posición "<$application\_name$>". Este marcador de posición no se reemplaza cuando se usa el adaptador de AD FS. Por eso se recomienda quitar el marcador de posición de las cadenas apropiadas al proteger AD FS.

- La cuenta en la que se ha iniciado sesión debe tener privilegios para crear grupos de seguridad en Active Directory.

- El asistente para la instalación del adaptador de AD FS de Multi-Factor Authentication crea un grupo de seguridad denominado PhoneFactor Admins en Active Directory y, a continuación, agrega la cuenta de servicio de AD FS del servicio de federación a este grupo. Se recomienda comprobar en el controlador de dominio que el grupo PhoneFactor Admins está creado y que la cuenta de servicio de AD FS sea un miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.
  

### Instalación de Servidor Azure Multi-Factor Authentication localmente en el mismo servidor que AD FS

1. Descargue e instale Servidor Azure Multi-Factor Authentication en el servidor de federación de AD FS. Para obtener información acerca de cómo instalar Servidor Azure Multi-Factor Authentication, consulte [Introducción a Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md)
2. En la interfaz de usuario de Servidor Azure Multi-Factor Authentication, seleccione el icono AD FS y elija las opciones Permitir inscripción de usuario y Permitir a los usuarios seleccionar el método.
3. Seleccione las opciones adicionales.
4. Haga clic en Instalar adaptador de AD FS.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si el equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el adaptador de AD FS y el servicio Multi-Factor Authentication está incompleta, se mostrará el paso de Active Directory. Haga clic en el botón Siguiente para completar esta configuración automáticamente o marque la casilla Omitir configuración automática de Active Directory y configurar los parámetros manualmente y haga clic en Siguiente.
6. Si el equipo no está unido a un dominio y la configuración de Grupo local para proteger la comunicación entre el adaptador de AD FS y el servicio Multi-Factor Authentication está incompleta, se mostrará el paso de Grupo local. Haga clic en el botón Siguiente para completar esta configuración automáticamente o marque la casilla Omitir configuración automática de grupo local y configurar los parámetros manualmente y haga clic en Siguiente.
7. Esto abrirá el asistente para la instalación; haga clic en Siguiente para permitir que Servidor Azure Multi-Factor Authentication cree el grupo PhoneFactor Admins y agregue la cuenta de servicio de AD FS para el grupo PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. En el paso Iniciar instalador, haga clic en Siguiente.
9. En el instalador del adaptador de AD FS de Multi-Factor Authentication, haga clic en Siguiente.
10. Cuando se haya completado la instalación, haga clic en Cerrar.
11. Ahora que se ha instalado el adaptador, se debe registrar con AD FS. Abra Windows PowerShell y ejecute lo siguiente: C:\\Archivos de programa\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Ahora tenemos que modificar la directiva de autenticación global en AD FS para poder usar el adaptador recién registrado. En la consola de administración de AD FS, vaya al nodo Directivas de autenticación y, en la sección Multi-factor Authentication, haga clic en el vínculo Editar situado junto a la subsección Configuración global. En la ventana Editar directiva de autenticación global, seleccione Multi-Factor Authentication como método de autenticación adicional y haga clic en Aceptar. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Debe reiniciar el servicio de AD FS para que surta efecto el registro.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

En este punto, Servidor Multi-Factor Authentication está configurado para ser un proveedor de autenticación adicional y utilizarlo con AD FS.

## Instalación del adaptador de AD FS de manera independiente mediante el SDK del servicio web
1. Instale el SDK del servicio web en el servidor que ejecuta Servidor Multi-Factor Authentication.
2. Copie el directorio MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1, and MultiFactorAuthenticationAdfsAdapter.config files from the \\Archivos de programa\\Multi-Factor Authentication Server en el servidor en el que prevé instalar el adaptador de AD FS.
3. Ejecute MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. En el adaptador de AD FS de Multi-Factor Authentication, haga clic en Siguiente para ejecutar la instalación.
5. Cuando se haya completado la instalación, haga clic en el botón Cerrar.
6. Edite el archivo MultiFactorAuthenticationAdfsAdapter.config haciendo lo siguiente:

Paso MultiFactorAuthenticationAdfsAdapter.config| Paso secundario
:------------- | :------------- |
Establezca el nodo UseWebServiceSdk en true.||
Establezca WebServiceSdkUrl en la dirección URL del SDK del servicio web de Multi-Factor Authentication.||
Opción 1 - Configuración del SDK del servicio web con nombre de usuario y contraseña.|<ol><li>Configure WebServiceSdkUsername como una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Use el <domain>formato <nombreDeUsuario>.<li>Establezca WebServiceSdkPassword en la contraseña de la cuenta adecuada.</li></ol>
Opción 2 - Configuración del SDK del servicio web con certificado de cliente.|<ol><li>Obtenga un certificado de cliente de una entidad de certificación para el servidor que ejecuta el SDK del servicio web.</li><li>Importe el certificado de cliente al almacén de certificados personales del equipo local en el servidor que ejecuta el SDK del servicio web. Nota: Asegúrese de que el certificado público de la entidad de certificación está en los certificados raíz de confianza.</li><li>Exporte las claves pública y privada del certificado de cliente a un archivo .pfx.</li><li>Exporte la clave pública en formato base 64 a un archivo .cer.</li><li>En el Administrador de servidores, compruebe que está instalada la característica (IIS)\\Web Server\\Security\\IIS Client Certificate Mapping Authentication.</li><li>Si no está instalada, elija Agregar roles y características para agregar esta característica.</li><li>En el Administrador de IIS, haga doble clic en el Editor de configuración en el sitio web que contiene el directorio virtual del SDK del servicio web. Nota: es muy importante hacerlo en el nivel de sitio web y no en el nivel de directorio virtual.</li><li>Vaya a la sección system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Establezca Enabled en true.</li><li>Configure oneToOneCertificateMappingsEnabled como verdadero.</li><li>Haga clic en el botón... junto a oneToOneMappings.</li><li>Haga clic en el vínculo Agregar.</li><li>Abra el archivo .cer de Base 64 exportado anteriormente. Quite -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- y cualquier salto de línea. Copie la cadena resultante.</li><li>Configure el certificado en la cadena copiada en el paso anterior.</li><li>Establezca Enabled en true.</li><li>Establezca userName en una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Utilice el <domain>formato <nombreDeUsuario>.</li><li>Establezca la contraseña en la contraseña de cuenta adecuada.</li><li>Cierre el Editor de colección.</li><li>Haga clic en el vínculo Aplicar.</li><li>Vaya al directorio virtual del SDK del servicio Web.</li><li>Haga doble clic en Autenticación.</li><li>Compruebe que Suplantación de ASP.NET y Autenticación básica están habilitadas y todos los demás elementos están deshabilitados.</li><li>Vaya al directorio virtual del SDK del servicio web de nuevo.</li><li>Haga doble clic en Configuración de SSL.</li><li>Establezca Certificados de cliente en Aceptar y haga clic en Aplicar.</li><li>Copie el archivo .pfx exportado anteriormente en el servidor que ejecuta el adaptador de AD FS.</li><li>Importe el archivo .pfx al almacén de certificados personales del equipo local.</li><li>Elija Administrar claves privadas en el menú contextual y conceda acceso de lectura a la cuenta en la que ha iniciado sesión el servicio Servicios de federación de Active Directory.</li><li>Abra el certificado de cliente y copie la huella digital de la pestaña Detalles.</li><li>En el archivo MultiFactorAuthenticationAdfsAdapter.config, configure WebServiceSdkCertificateThumbprint como la cadena copiada en el paso anterior.</li></ol>
Edite el script Register-MultiFactorAuthenticationAdfsAdapter.ps1 agregando -ConfigurationFilePath <path> al final del comando Register-AdfsAuthenticationProvider, donde <path> es la ruta de acceso completa al archivo MultiFactorAuthenticationAdfsAdapter.config.|


Ejecute ahora el script \\Archivos de programa\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 en PowerShell para registrar el adaptador. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Debe reiniciar el servicio de AD FS para que surta efecto el registro.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=AcomDC_0309_2016-->