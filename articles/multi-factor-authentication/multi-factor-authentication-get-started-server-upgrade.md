<properties 
	pageTitle="Actualización de PhoneFactor Agent al Servidor Azure Multi-Factor Authentication" 
	description="En este documento se describe cómo empezar a trabajar con el Servidor Azure MFA y cómo actualizar a partir del PhoneFactor Agent anterior." 
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

# Actualización de PhoneFactor Agent al Servidor Azure Multi-Factor Authentication

Para la actualización de PhoneFactor Agent v5.x o anterior al Servidor Azure Multi-Factor Authentication es necesario desinstalar PhoneFactor Agent y sus componentes afiliados antes de instalar el Servidor Multi-Factor Authentication Server y sus componentes afiliados.

## Para actualizar el PhoneFactor Agent al Servidor Azure Multi-Factor Authentication
<ol>
<li>En primer lugar, haga una copia del archivo de datos de PhoneFactor. La ubicación de instalación predeterminada es C:\\Archivos de programa\\PhoneFactor\\Data\\Phonefactor.pfdata.


<li>Si se ha instalado el Portal de usuarios:</li>
<ol>
<li>Desplácese hasta la carpeta de instalación y haga una copia de seguridad del archivo web.config. La ubicación de instalación predeterminada es C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Si ha agregado temas personalizados al portal, haga una copia de seguridad de la carpeta personalizada en el directorio C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstale el Portal de usuarios con PhoneFactor Agent (solo disponible si está instalado en el mismo servidor que PhoneFactor Agent) o con Programas y características de Windows.</li></ol>




<li>Si está instalado el servicio web de la aplicación móvil: <ol> <li>vaya a la carpeta de instalación y haga una copia de seguridad del archivo web.config. La ubicación de instalación predeterminada es C:\\inetpub\\wwwroot\\PhoneFactorPhoneAppWebService.</li> <li>Desinstale el servicio web de la aplicación móvil con Programas y características de Windows.</li></ol>

<li>Si está instalado el SDK del servicio web, desinstálelo con PhoneFactor Agent o con Programas y características de Windows.

<li>Desinstale el servicio web de la aplicación móvil con Programas y características de Windows.

<li>Instale el Servidor Multi-Factor Authentication. Tenga en cuenta que la ruta de instalación se selecciona en el registro de la instalación anterior de PhoneFactor Agent, por lo que se instalará en la misma ubicación (por ejemplo, C:\\Archivos de programa\\PhoneFactor). Las instalaciones nuevas tendrán una ruta de instalación predeterminada diferente (por ejemplo, C:\\Archivos de programa\\Servidor Multi-Factor Authentication). El archivo de datos que el PhoneFactor Agent anterior ha dejado, se debe actualizar durante la instalación, por lo que los usuarios y la configuración deben seguir allí después de instalar el nuevo Servidor Multi-Factor Authentication.

<li>Si se lo pide, active el Servidor Multi-Factor Authentication y asegúrese de que esté asignado al grupo de replicación correcto.

<li>Si el SDK del servicio web se ha instalado previamente, instale el nuevo SDK del servicio web desde la interfaz de usuario del Servidor Multi-Factor Authentication. Tenga en cuenta que el nombre del directorio virtual predeterminado es ahora "MultiFactorAuthWebServiceSdk" en lugar de "PhoneFactorWebServiceSdk". Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, tendrá que cambiar la dirección URL en las aplicaciones que hagan referencia al SDK del servicio web como el Portal de usuarios para que apunten a la ubicación correcta.

<li>Si previamente se ha instalado el Portal de usuarios en el servidor de PhoneFactor Agent, instale el nuevo Portal de usuarios de Multi-Factor Authentication desde la interfaz de usuario del Servidor Multi-Factor Authentication. Tenga en cuenta que el nombre de directorio virtual predeterminado es ahora “MultiFactorAuth” en lugar de “PhoneFactor”. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Portal de usuarios del Servidor Multi-Factor Authentication y actualizar la dirección URL del Portal de usuarios en la pestaña Configuración.

<li>Si el Portal de usuarios o el servicio web de la aplicación móvil estaba instalado previamente en un servidor diferente de PhoneFactor Agent: <ol> <li>vaya a la ubicación de instalación (por ejemplo, C:\\Program programa\\PhoneFactor) y copie los instaladores adecuados en el otro servidor. Hay instaladores de 32 bits y 64 bits para el Portal de usuarios y el servicio web de la aplicación móvil. Se denominan MultiFactorAuthenticationUserPortalSetupXX.msi y MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respectivamente.</li> <li>Para instalar el Portal de usuarios en el servidor web, abra un símbolo del sistema como administrador y ejecute MultiFactorAuthenticationUserPortalSetupXX.msi. Tenga en cuenta que el nombre de directorio virtual predeterminado es ahora “MultiFactorAuth” en lugar de “PhoneFactor”. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Portal de usuarios del Servidor Multi-Factor Authentication y actualizar la dirección URL del Portal de usuarios en la pestaña Configuración. Será necesario informar a los usuarios existentes de la nueva dirección URL.</li> <li>Vaya a la ubicación de instalación del Portal de usuarios (por ejemplo, C:\\inetpub\\wwwroot\\MultiFactorAuth) y edite el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original del que se ha hecho copia de seguridad antes de actualizar al nuevo archivo web.config. Si se ha conservado el nuevo nombre de directorio virtual predeterminado al instalar el SDK del servicio web, cambie la dirección URL en la sección applicationSettings para que apunte a la ubicación correcta. Si se han cambiado otros valores predeterminados en el archivo web.config anterior, aplica estos mismos cambios al nuevo archivo web.config.</li> <li>Para instalar el servicio web de aplicación móvil en el servidor web, abra un símbolo del sistema como administrador y ejecute MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Tenga en cuenta que el nombre del directorio virtual predeterminado es ahora “MultiFactorAuthMobileAppWebService” en lugar de “PhoneFactorPhoneAppWebService”. Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. Puede elegir un nombre más corto para que les sea más fácil a los usuarios finales escribirlo en sus dispositivos móviles. De lo contrario, si permite que la instalación use el nuevo nombre predeterminado, debe hacer clic en el icono de Aplicación móvil en el Servidor Multi-Factor Authentication y actualizar la dirección URL del servicio web de la aplicación móvil.</li> <li>Vaya a la ubicación de instalación del servicio web de aplicación móvil (por ejemplo, C:\\inetpub\\wwwroot\\MultiFactorAuthMobileAppWebService) y edite el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original del que se ha hecho copia de seguridad antes de actualizar al nuevo archivo web.config. Si se ha conservado el nuevo nombre de directorio virtual predeterminado al instalar el SDK del servicio web, cambie la dirección URL en la sección applicationSettings para que apunte a la ubicación correcta. Si se han cambiado otros valores predeterminados en el archivo web.config anterior, aplique estos mismos cambios al nuevo archivo web.config.</li></ol>


 


 

<!---HONumber=AcomDC_0224_2016-->