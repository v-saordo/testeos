<properties 
	pageTitle="Implementación del Portal de usuarios para Servidor Azure Multi-Factor Authentication" 
	description="En esta página de Azure Multi-Factor Authentication, se describe cómo empezar a trabajar con Azure MFA y el Portal de usuarios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Implementación del Portal de usuarios para Servidor Azure Multi-Factor Authentication

El Portal de usuarios permite al administrador instalar y configurar el Portal de usuarios de Azure Multi-Factor Authentication. El Portal de usuarios es un sitio web de IIS que permite a los usuarios inscribirse en Azure Multi-Factor Authentication y mantener sus cuentas. Un usuario puede cambiar el número de teléfono, modificar el PIN o evitar Azure Multi-Factor Authentication durante su próximo inicio de sesión.

Los usuarios iniciarán sesión en el Portal de usuarios mediante el nombre de usuario y la contraseña normales y completarán una llamada de Azure Multi-Factor Authentication o responderán a preguntas de seguridad para completar la autenticación. Si se permite la inscripción de usuarios, el usuario configurará el número de teléfono y el PIN la primera vez que inicien sesión en el Portal de usuarios.

Es posible que se configuren administradores para el Portal de usuarios y que se les conceda permiso para agregar usuarios nuevos y actualizar los existentes.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Implementación del Portal de usuarios en el mismo servidor que Servidor Azure Multi-Factor Authentication

Los siguientes requisitos previos son obligatorios para instalar el Portal de usuarios en el mismo servidor que Servidor Azure Multi-Factor Authentication:

- IIS debe estar instalado, incluido ASP.NET y Compatibilidad con la metabase de IIS 6 (para IIS 7 o posterior). 
- El usuario que haya iniciado sesión debe tener derechos de administrador para el equipo y el dominio, si corresponde. La razón es que la cuenta necesita permisos para crear grupos de seguridad de Active Directory.

### Para implementar el Portal de usuarios para Servidor Azure Multi-Factor Authentication

1. En Servidor Azure Multi-Factor Authentication: haga clic en el icono Portal de usuarios en el menú de la izquierda y después en el botón Instalar portal de usuarios. 
1. Haga clic en Siguiente.
1. Haga clic en Siguiente.
1. Si el equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el Portal de usuarios y el servicio Azure Multi-Factor Authentication está incompleta, se mostrará el paso de Active Directory. Haga clic en el botón Siguiente para completar la configuración de forma automática.
1. Haga clic en Siguiente.
1. Haga clic en Siguiente.
1. Haga clic en Cerrar.
1. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth ). Asegúrese de que no aparezca ningún error ni advertencia de certificado.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Implementación del Portal de usuarios de Servidor Azure Multi-Factor Authentication en otro servidor

Para usar la aplicación Azure Multi-Factor Authentication, se requiere lo siguiente para que pueda comunicarse correctamente con el Portal de usuarios:

Consulte los requisitos de hardware y software:

- Debe usar la versión 6.0 o posterior del Servidor Azure Multi-Factor Authentication.
- El Portal de usuarios debe estar instalado en un servidor web con conexión a Internet con Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x o posterior.
- Si utiliza IIS 6.x, asegúrese de que ASP.NET versión 2.0.50727 esté instalado, registrado y establecido en Permitido.
- Entre los servicios de rol necesarios cuando se usan IIS 7.x o versiones posteriores, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
- El Portal de usuarios debe estar protegido con un certificado SSL.
- Debe estar instalado el SDK del servicio web de Azure Multi-Factor Authentication en IIS 6.x, IIS 7.x o posterior en el servidor donde esté instalado Servidor Azure Multi-Factor Authentication.
- El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
- El Portal de usuarios debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
- El Portal de usuarios debe poder autenticarse en el SDK del servicio web de Azure Multi-Factor Authentication mediante las credenciales de una cuenta de servicio que pertenezca a un grupo de seguridad llamado "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se ejecuta en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.

Para instalar el Portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos tres pasos:

1. Instalación del SDK del servicio web
2. Instalación del Portal de usuarios
3. Establecimiento de la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication


### Instalación del SDK del servicio web

Si el SDK del servicio web de Azure Multi-Factor Authentication aún no está instalado en Servidor Azure Multi-Factor Authentication, vaya a dicho servidor y abra el servidor Azure Multi-Factor Authentication. Haga clic en el icono SDK del servicio web, haga clic en el botón Instalar SDK del servicio web y siga las instrucciones. El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito, pero debe importarse al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Instalación del Portal de usuarios

Antes de instalar el Portal de usuarios en un servidor independiente, tenga en cuenta lo siguiente:

- Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
- Si existe un proxy inverso o un firewall en el servidor web del Portal de usuarios que está realizando la descarga de SSL, puede editar el archivo web.config del Portal de usuarios y agregar la siguiente clave en la sección <appSettings> para que el Portal de usuarios pueda usar http en lugar de https. <add key="SSL_REQUIRED" value="false"/>

#### Para instalar el Portal de usuarios

1. Abra el Explorador de Windows en el servidor Azure Multi-Factor Authentication y vaya a la carpeta donde esté instalado Servidor Azure Multi-Factor Authentication (por ejemplo, C:\\Archivos de programa\\Multi-Factor Authentication Server). Elija la versión de 32 o 64 bits del archivo de instalación MultiFactorAuthenticationUserPortalSetup según corresponda al servidor donde se instalará el Portal de usuarios. Copie el archivo de instalación en el servidor con conexión a Internet.
2. En el servidor web con conexión a Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más fácil de hacerlo es abrir un símbolo del sistema como administrador e ir a la ubicación donde se copió el archivo de instalación.
3. Ejecute el archivo de instalación MultiFactorAuthenticationUserPortalSetup64 y cambie el nombre de directorio virtual y sitio si lo desea.
4. Después de finalizar la instalación del Portal de usuarios, vaya a C:\\inetpub\\wwwroot\\MultiFactorAuth (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo web.config.
5. Busque la clave USE\_WEB\_SERVICE\_SDK y cambie el valor de false a true. Busque las claves WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME y WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio que pertenece al grupo de seguridad PhoneFactor Admins (consulte la sección de requisitos anterior). Asegúrese de especificar el nombre de usuario y la contraseña entre comillas al final de la línea (value=””/>). Se recomienda usar un nombre de usuario completo (por ejemplo, dominio\\nombreDeUsuario o equipo\\nombreDeUsuario).
6. Busque la configuración pfup\_pfwssdk\_PfWsSdk y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK del servicio web que se ejecuta en Servidor Azure Multi-Factor Authentication (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx) ). Como se usa SSL para esta conexión, debe hacer referencia el SDK del servicio web por el nombre del servidor y no por la dirección IP, ya que el certificado SSL se habrá emitido para el nombre del servidor y la dirección URL usada debe coincidir con el nombre del certificado. Si el nombre del servidor no se resuelve en una dirección IP del servidor con conexión a Internet, agregue una entrada al archivo hosts en ese servidor para asignar el nombre de Servidor Azure Multi-Factor Authentication a su dirección IP. Una vez realizados los cambios, guarde el archivo web.config.
7. Si el sitio web donde se instaló el Portal de usuarios (por ejemplo, Sitio web predeterminado) aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor si aún no lo está, abra el Administrador de IIS y enlace el certificado al sitio web.
8. Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth ). Asegúrese de que no aparezca ningún error ni advertencia de certificado.



## Establecimiento de la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication
Ahora que el portal está instalado, debe configurar el servidor Azure Multi-Factor Authentication para que funcione con el portal.

Servidor Azure Multi-Factor Authentication ofrece varias opciones para el portal de usuarios. En la tabla siguiente se proporciona una lista de estas opciones y se obtiene una explicación de para qué se usan.

Configuración del portal de usuarios|Descripción|
:------------- | :------------- | 
URL del portal de usuarios| Permite especificar la dirección URL en la que se hospeda el portal.
Autenticación principal| Permite especificar el tipo de autenticación que se usará al iniciar sesión en el portal. Autenticación de Windows, Radius o LDAP.
Permitir que los usuarios inicien sesión|Permite a los usuarios especificar un nombre de usuario y una contraseña en la página de inicio de sesión para el portal de usuarios. Si no está seleccionada, las casillas se atenuarán.
Permitir inscripción de usuario|Permite al usuario inscribirse en la autenticación multifactor al llevarlo a una pantalla de configuración en la que se solicita información adicional, como el número de teléfono. Solicitar teléfono de reserva permite a los usuarios especificar un número de teléfono secundario. Solicitar token OATH de terceros permite a los usuarios especificar un token OATH de terceros.
Permitir a los usuarios iniciar Omisión por única vez| Permite a los usuarios iniciar una omisión una única vez. Si un usuario lo configura, entrará en vigor la próxima vez que el usuario inicia sesión. Solicitar segundos de omisión proporciona al usuario un cuadro para que puedan cambiar el valor predeterminado de 300 segundos. De lo contrario, la omisión por única vez solo funciona durante 300 segundos.
Permitir a los usuarios seleccionar el método| Permite a los usuarios especificar su método de contacto principal. Puede tratarse de llamada de teléfono, un mensaje de texto, una aplicación móvil o una token OATH.
Permitir a los usuarios seleccionar el idioma| Permite al usuario cambiar el idioma que se usa para la llamada de teléfono, el mensaje de texto, la aplicación móvil o el token OATH.
Permitir a los usuarios activar la aplicación móvil| Permite a los usuarios generar un código de activación para completar el proceso de activación de la aplicación móvil que se usa con el servidor. También puede establecer el número de dispositivos que pueden activarla. Entre 1 y 10.
Usar preguntas de seguridad para la reserva|Permite usar preguntas de seguridad en caso de que se produzca un error en la autenticación multifactor. Puede especificar el número de preguntas de seguridad que se deben responder correctamente.
Permitir a los usuarios asociar el token OATH de terceros| Permite a los usuarios especificar un token OATH de terceros.
Usar token OATH para la reserva|Permite el uso de un token OATH en caso de que la autenticación multifactor no sea correcta. También puede especificar el tiempo de espera de la sesión en minutos.
Habilitación del registro|Habilita el registro en el portal de usuarios. Los archivos de registro se encuentra en C:\\Archivos de programa\\Multi-Factor Authentication Server\\Logs.

La mayoría de estas configuraciones son visibles para el usuario una vez que están habilitados y el usuario inicia sesión en el portal de usuarios.

![Configuración del portal de usuarios](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Para establecer la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication




1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Portal de usuarios. En la pestaña Configuración, escriba la dirección URL del Portal de usuarios en el cuadro de texto URL del portal de usuarios. Esta dirección URL se insertará en los mensajes de correo electrónico que se envían a los usuarios cuando se importan a Servidor Azure Multi-Factor Authentication si se habilitó la funcionalidad de correo electrónico.
2. Elija la configuración que desea usar en el Portal de usuarios. Por ejemplo, si se permite que los usuarios controlen sus métodos de autenticación, asegúrese de que activar Permitir a los usuarios seleccionar el método junto con los métodos entre los que pueden elegir.
3. Haga clic en el vínculo Ayuda en la esquina superior derecha para obtener ayuda sobre cualquiera de las opciones que aparecen.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Pestaña Administradores
Esta pestaña simplemente permite agregar los usuarios que tendrán privilegios de administrador. Al agregar un administrador, puede ajustar los permisos que reciben. De este modo, puede estar seguro de otorgar solo los permisos necesarios al administrador. Simplemente, haga clic en el botón Agregar; a continuación, seleccione un usuario y sus permisos y haga clic en Agregar.

![Administradores del portal de usuarios](./media/multi-factor-authentication-get-started-portal/admin.png)


## Preguntas de seguridad
Esta pestaña permite especificar las preguntas de seguridad que los usuarios necesitarán responder si se selecciona la opción Usar preguntas de seguridad para la reserva. El Servidor Azure Multi-Factor Authentication incluye preguntas predeterminadas que puede usar. También puede cambiar el orden o agregar sus propias preguntas. Al agregar sus propias preguntas, también puede especificar el idioma en que desea que aparezcan esas preguntas.

![Preguntas de seguridad del portal de usuarios](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Sesiones superadas

## SAML
Permite configurar el portal de usuarios para aceptar notificaciones de un proveedor de identidades con SAML. Puede especificar el tiempo de espera de la sesión, el certificado de verificación y la dirección URL de redireccionamiento al cerrar la sesión.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## IP de confianza
Esta pestaña permite especificar direcciones IP individuales o intervalos de direcciones IP que pueden agregarse para que si un usuario inicia sesión desde una de estas direcciones IP se omita la autenticación multifactor.

![IP de confianza del portal de usuarios](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Autoservicio de inscripción de usuarios
Si desea que los usuarios inicien sesión y se inscriban, debe seleccionar las opciones Permitir que los usuarios inicien sesión y Permitir inscripción de usuario. Recuerde que la configuración que seleccione afectará a la experiencia de inicio de sesión del usuario.

Por ejemplo, cuando un usuario inicia sesión en el Portal de usuarios y hace clic en el botón Iniciar sesión, pasa a la página Configuración de usuario de Azure Multi-Factor Authentication. Dependiendo de cómo haya configurado Azure Multi-Factor Authentication, el usuario puede seleccionar el método de autenticación.

Si seleccionan el método de autenticación Llamada de voz o está preconfigurados para usar ese método, la página solicitará a los usuarios que escriban su número de teléfono principal y la extensión, si procede. También puede especificar un número de teléfono de reserva.

![IP de confianza del portal de usuarios](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN. Después de escribir sus números de teléfono y PIN (si procede), el usuario hace clic en el botón Llamarme ahora para autenticar. Azure Multi-Factor Authentication llevará a cabo una autenticación mediante llamada de teléfono al número de teléfono principal del usuario. El usuario debe responder a la llamada de teléfono y escribir su PIN (si procede) y presione # para avanzar al siguiente paso del proceso de autoinscripción.

Si el usuario selecciona el método de autenticación Texto SMS o se ha configurado previamente para usar este método, la página solicitará al usuario su número de teléfono móvil. Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN. Después de escribir su número de teléfono y PIN (si procede), el usuario hace clic en el botón Enviarme un SMS ahora para autenticar. Azure Multi-Factor Authentication llevará a cabo una autenticación mediante un SMS al teléfono móvil del usuario. El usuario debe recibir el SMS que contiene una contraseña de una vez (OTP) y responder al mensaje con esa OTP más su PIN (si procede) para avanzar al siguiente paso del proceso de autoinscripción.

![SMS del portal de usuarios](./media/multi-factor-authentication-get-started-portal/text.png)

Si el usuario selecciona el método de autenticación Aplicación móvil o se ha configurado previamente para usar este método, la página solicitará al usuario que instale la aplicación Azure Multi-Factor Authentication en su dispositivo y genere un código de activación. Después de instalar la aplicación Azure Multi-Factor Authentication, el usuario hace clic en el botón Generar código de activación.

>[AZURE.NOTE]Para poder usar la aplicación Azure Multi-Factor Authentication, el usuario debe habilitar las notificaciones de inserción para su dispositivo.

A continuación, la página muestra un código de activación y una dirección URL, junto con la imagen de un código de barras. Si el usuario debe usar un PIN al realizar la autenticación, la página también le pedirá que escriba un PIN. El usuario escribe el código de activación y la dirección URL en la aplicación Azure Multi-Factor Authentication o usa el escáner para digitalizar la imagen del código de barras y hace clic en el botón Activar.

Una vez completada la activación, el usuario hace clic en el botón Autenticarme ahora. Azure Multi-Factor Authentication llevará a cabo una autenticación en la aplicación móvil del usuario. El usuario debe escribir su PIN (si procede) y presionar el botón Autenticar en su aplicación móvil para avanzar al siguiente paso del proceso de autoinscripción.


Si los administradores han configurado Servidor Azure Multi-Factor Authentication para recopilar preguntas y respuestas acerca de la seguridad, el usuario pasa a la página de preguntas de seguridad. El usuario debe seleccionar cuatro preguntas de seguridad y proporcionar respuestas a las preguntas seleccionadas.

![Preguntas de seguridad del portal de usuarios](./media/multi-factor-authentication-get-started-portal/secq.png)

La autoinscripción del usuario ahora está completa y el usuario inicia sesión en el Portal de usuarios. Los usuarios pueden iniciar sesión en el Portal de usuarios en cualquier momento para cambiar sus números de teléfono, PIN, métodos de autenticación y preguntas de seguridad si sus administradores lo permiten.

 

<!---HONumber=AcomDC_0224_2016-->