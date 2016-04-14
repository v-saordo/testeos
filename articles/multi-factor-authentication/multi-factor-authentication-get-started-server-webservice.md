<properties 
	pageTitle="Introducción al servicio web de aplicación móvil del servidor MFA" 
	description="La aplicación Azure Multi-Factor Authentication ofrece una opción de autenticación de ancho de banda adicional. Permite al Servidor MFA utilizar notificaciones push en los usuarios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Introducción al servicio web de aplicación móvil del servidor MFA

La aplicación Azure Multi-Factor Authentication ofrece una opción de autenticación de ancho de banda adicional. En lugar de realizar una llamada de teléfono automatizada o SMS al usuario durante el inicio de sesión, Azure Multi-Factor Authentication inserta una notificación en la aplicación Azure Multi-Factor Authentication en el smartphone o en una tableta del usuario. El usuario simplemente toca "Autenticar" (o escribe un PIN y toca "Autenticar") en la aplicación para iniciar la sesión.

Para usar la aplicación Azure Multi-Factor Authentication, se requiere lo siguiente para que pueda comunicarse correctamente con el servicio web de aplicación móvil:

- Consulte los requisitos de hardware y software.
- Debe usar la versión 6.0 o posterior del Servidor Azure Multi-Factor Authentication.
- El servicio web de aplicación móvil debe estar instalado en un servidor web con conexión a Internet con Microsoft® Internet Information Services (IIS) 6.x o IIS 7.x.
- Si utiliza IIS 6.x, asegúrese de que ASP.NET versión 2.0.50727 esté instalado, registrado y establecido en Permitido
- Entre los servicios de rol necesarios cuando se usa IIS 7.x, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
- El servicio web de aplicación móvil debe ser accesible mediante una dirección URL pública.
- El servicio web para aplicaciones móviles debe estar protegido con un certificado SSL.
- Debe estar instalado el SDK del servicio web de Azure Multi-Factor Authentication en IIS 6.x o IIS 7.x en el servidor donde esté instalado el Servidor Azure Multi-Factor Authentication.
- El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
- El servicio web de aplicación móvil debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
- El servicio web de la aplicación móvil debe poder autenticarse en el SDK del servicio web de Azure Multi-Factor Authentication mediante las credenciales de una cuenta de servicio que pertenezca a un grupo de seguridad llamado "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se ejecuta en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.


Para instalar el Portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos tres pasos:

1. Instalación del SDK del servicio web
2. Instalación del servicio web de aplicación móvil
3. Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication
4. Activación de la aplicación Azure Multi-Factor Authentication para usuarios finales

## Instalación del SDK del servicio web

Si el SDK del servicio web de Azure Multi-Factor Authentication aún no está instalado en Servidor Azure Multi-Factor Authentication, vaya a dicho servidor y abra el servidor Azure Multi-Factor Authentication. Haga clic en el icono SDK del servicio web, haga clic en el botón Instalar SDK del servicio web y siga las instrucciones. El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito, pero debe importarse al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Instalación del servicio web de aplicación móvil
Antes de instalar el servicio web de aplicación móvil, tenga en cuenta lo siguiente:

- Si el Portal de usuarios de Azure Multi-Factor Authentication ya está instalado en el servidor con conexión a Internet, el nombre de usuario, la contraseña y la dirección URL del SDK del servicio web pueden copiarse desde el archivo web.config del Portal de usuarios. 
- Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
- Si existe un proxy inverso o un firewall en el servidor web del servicio web de aplicaciones móviles que está realizando la descarga de SSL, puede editar el archivo web.config del servicio web de aplicaciones móviles y agregar la siguiente clave en la sección <appSettings> para que el servicio web de aplicaciones móviles pueda usar http en lugar de https. Sin embargo, SSL sigue siendo necesario en la aplicación móvil para el proxy de firewall o inverso. <add key="SSL_REQUIRED" value="false"/> 

### Instalación del servicio web de aplicación móvil

<ol>
<li>Abra el Explorador de Windows en el servidor Azure Multi-Factor Authentication y vaya a la carpeta donde esté instalado el Servidor Azure Multi-Factor Authentication (por ejemplo, C:\Archivos de programa\Azure Multi-Factor Authentication). Elija la versión de 32 bits o 64 bits del archivo de instalación Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup, según corresponda, para el servidor en el que se instalará el servicio web de la aplicación móvil. Copie el archivo de instalación en el servidor con conexión a Internet.</li>

<li>En el servidor web con conexión a Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más fácil de hacerlo es abrir un símbolo del sistema como administrador e ir a la ubicación donde se copió el archivo de instalación.</li>

<li>Ejecute el archivo de instalación Multi-Factor AuthenticationMobileAppWebServiceSetup, cambie el sitio si lo prefiere y cambie el directorio virtual a un nombre corto como "PA". Como los usuarios deben escribir la URL del servicio web de la aplicación móvil en el dispositivo móvil durante la activación, se recomienda un nombre de directorio virtual corto.</li>

<li>Después de finalizar la instalación de Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, vaya a C:C:\inetpub\wwwroot\PA (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo web.config.</li>

<li>Busque las claves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio que pertenece al grupo de seguridad PhoneFactor Admins (consulte la sección de requisitos anterior). Puede ser la misma cuenta que se utiliza como identidad del Portal de usuarios de Azure Multi-Factor Authentication si se ha instalado anteriormente. Asegúrese de especificar el nombre de usuario y la contraseña entre comillas al final de la línea (value=””/>). Se recomienda usar un nombre de usuario completo (por ejemplo, dominio\nombreDeUsuario o equipo\nombreDeUsuario).</li>

<li>Busque la configuración pfMobile App Web Service_pfwssdk_PfWsSdk y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK del servicio web que se ejecuta en el Servidor Azure Multi-Factor Authentication (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como se usa SSL para esta conexión, debe hacer referencia el SDK del servicio web por el nombre del servidor y no por la dirección IP, ya que el certificado SSL se habrá emitido para el nombre del servidor y la dirección URL usada debe coincidir con el nombre del certificado. Si el nombre del servidor no se resuelve en una dirección IP del servidor con conexión a Internet, agregue una entrada al archivo hosts en ese servidor para asignar el nombre de Servidor Azure Multi-Factor Authentication a su dirección IP. Una vez realizados los cambios, guarde el archivo web.config.</li>

<li>Si el sitio web donde se instaló el servicio web de la aplicación móvil (por ejemplo, el sitio web predeterminado) aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor si aún no lo está, abra el Administrador de IIS y enlace el certificado al sitio web.</li>

<li>Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el servicio web de la aplicación móvil (por ejemplo, https://www.publicwebsite.com/PA). Asegúrese de que no aparezca ningún error ni advertencia de certificado.</li>

### Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication
Ahora que el servicio web de la aplicación móvil está instalado, debe configurar Servidor Azure Multi-Factor Authentication para que funcione con el portal.

#### Para configurar los ajustes de la aplicación móvil en Servidor Azure Multi-Factor Authentication

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Portal de usuarios. Si los usuarios pueden controlar sus métodos de autenticación, en la pestaña Configuración, en Permitir a los usuarios seleccionar el método, active Aplicación móvil. Sin esta característica habilitada, los usuarios finales deberán ponerse en contacto con el departamento de soporte técnico para completar la activación de la aplicación móvil.
2. Active la casilla Permitir a los usuarios activar la aplicación móvil.
3. Active la casilla Permitir inscripción de usuario.
4. Haga clic en el icono de la aplicación móvil.
5. Escriba la dirección URL usada con el directorio virtual que creó al instalar Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Se puede escribir un nombre de cuenta en el espacio proporcionado. Este nombre de compañía se mostrará en la aplicación móvil. Si se deja en blanco, se mostrará el nombre de su proveedor de Multi-Factor Authentication creado en el Portal de administración de Azure. 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=AcomDC_0224_2016-->