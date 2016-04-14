<properties 
	pageTitle="Azure Multi-Factor Authentication - Cómo funciona" 
	description="Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas." 
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
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

#Cómo funciona Azure Multi-Factor Authentication

La seguridad de la autenticación mediante varias fases se basa en el enfoque por niveles. El uso de varias fases de autenticación supone un reto importante para los atacantes. Incluso si un atacante consigue descifrar la contraseña de usuario, no sirve de nada si no dispone también del dispositivo de confianza. Si el usuario pierde el dispositivo, la persona que lo encuentre no podrá usarlo a no ser que conozca la contraseña de usuario.

![Página de proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas:

- llamada de teléfono 
- mensaje de texto
- notificación de aplicación móvil, que permite a los usuarios elegir el método que prefieran
- código de comprobación de la aplicación móvil
- tokens OATH de terceros

Para obtener información adicional sobre cómo funciona, vea el vídeo siguiente.

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##Métodos disponibles para la autenticación multifactor
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario. Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

Método de comprobación | Descripción 
------------- | ------------- |
Llamada de teléfono | Se realiza una llamada al smartphone del usuario para solicitarle que compruebe que ha iniciado sesión presionando el signo #. Esto completará el proceso de comprobación. Esta opción es configurable y puede cambiarse por un código que especifique.
Mensaje de texto | Se enviará un mensaje de texto al smartphone del usuario con un código de 6 dígitos. Escriba este código para completar el proceso de comprobación.
Notificación de aplicación móvil | Se enviará al smartphone del usuario una solicitud de comprobación solicitándole que complete la comprobación mediante la selección de Comprobar en la aplicación móvil. Esto ocurrirá si ha seleccionado la notificación de aplicación como su método de comprobación principal. Si los usuarios lo reciben cuando no han iniciado sesión, pueden notificarlo como fraude.
Código de comprobación con la aplicación móvil | Se enviará un código de comprobación a la aplicación móvil que se ejecuta en el smartphone del usuario. Esto ocurrirá si ha seleccionado un código de verificación como su método de comprobación principal.


##Versiones disponibles de Azure Multi-Factor Authentication
Azure Multi-Factor Authentication está disponible en tres versiones diferentes. En la tabla siguiente se describe cada una de ellas con más detalle.

Versión | Descripción 
------------- | ------------- |
Multi-Factor Authentication para Office 365 | Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. De este modo, los administradores pueden proteger ahora sus recursos de Office 365 con la autenticación multifactor. Esta versión incluye una suscripción a Office 365.
Multi-Factor Authentication para administradores de Azure | La misma subred de capacidades Multi-Factor Authentication para Office 365 estará disponible sin que suponga ningún coste para los administradores de Azure. Las cuentas administrativas de la suscripción de Azure pueden obtener ahora una protección adicional mediante la habilitación de esta funcionalidad de autenticación a través de varias fases. Por lo tanto, un administrador que desee obtener acceso al portal de Azure para crear una VM o un sitio web, o administrar almacenamiento, servicios móviles o cualquier otro servicio de Azure, puede agregar la autenticación mediante varias fases a su cuenta de administrador.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication ofrece el mayor conjunto de capacidades. <br><br>Proporciona opciones de configuración adicionales mediante el Portal de administración de Azure, capacidades avanzadas de generación de informes y compatibilidad con una amplia variedad de aplicaciones locales y en la nube. Azure Multi-Factor Authentication se puede comprar como una licencia independiente y se incluye en Azure Active Directory Premium y Enterprise Mobility Suite. <br><br>También se puede comprar según el consumo creando un proveedor de Azure Multi-Factor Authentication en una suscripción a Azure.
##Comparación de características de las versiones
En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure Multi-Factor Authentication.


Característica | Multi-Factor Authentication para Office 365 (incluido en la SKU de Office 365)|Multi-Factor Authentication para administradores de Azure (incluido con la suscripción a Azure) | Azure Multi-Factor Authentication (incluido en Azure AD Premium y Enterprise Mobility Suite) 
------------- | :-------------: |:-------------: |:-------------: |
Los administradores pueden proteger cuentas con MFA.| * | * (Disponible solo para las cuentas de administrador de Azure)|*
Aplicación móvil como segundo factor|* | * | *
Llamada de teléfono como segundo factor|* | * | *
SMS como segundo factor|* | * | *
Contraseñas de aplicación para clientes que no admiten MFA|* | * | *
Control de administración sobre los métodos de autenticación| | | *
Modo de PIN| | | *
Alerta de fraude| | | *
Informes de MFA| | | *
Omisión por única vez| | | *
Saludos personalizados para las llamadas de teléfono| | | *
Personalización del identificador de llamada para llamadas telefónicas| | | *
Confirmación de evento| | | *
IP de confianza| | | *
Suspender MFA para dispositivos recordados (vista previa pública)| | | *
SDK de MFA| | | *
MFA para aplicaciones locales mediante servidor MFA| | | *


##Cómo conseguir Azure Multi-Factor Authentication

Si desea disponer de todas las funcionalidades que ofrece Azure Multi-Factor Authentication, en lugar de solo las que se proporcionan para los usuarios de Office 365 y administradores de Azure, hay varias opciones para obtenerlas:

1.	Comprar licencias de Azure Multi-Factor Authentication y asignarlas a los usuarios.
2.	Comprar licencias que incluyen Azure Multi-Factor Authentication, como Azure Active Directory Premium o Enterprise Mobility, y asignarlas a los usuarios.
3.	Crear un proveedor de Azure Multi-Factor Authentication dentro de una suscripción a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita de Azure. Las suscripciones de prueba tendrán que convertirse en suscripciones normales antes de que expire la prueba.

Al usar un proveedor de Azure Multi-Factor Authentication, existen dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:


- **Por usuario**. Generalmente para empresas que quieren habilitar la autenticación multifactor para un número fijo de empleados que con frecuencia necesitan autenticación.
- **Por autenticación**. Generalmente para empresas que quieren habilitar la autenticación multifactor para un número mayor de usuarios externos que no suelen necesitar frecuentemente autenticación.

Para obtener información detallada sobre los precios, consulte [Precios de Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Elija el modelo por puesto o según el consumo que mejor funcione para su organización. Para comenzar, consulte [Introducción](multi-factor-authentication-get-started.md)



 

<!---HONumber=AcomDC_0218_2016-->