<properties
	pageTitle="¿Qué es Azure Multi-Factor Authentication? | Microsoft Azure"
	description="En este tema se explica qué es Multifactor Authentication (mfa), por qué alguien podría usar MFA, aporta más información sobre el cliente de Multifactor Authentication y los distintos métodos y versiones disponibles. Azure Multi-Factor Authentication es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios."
	keywords="introducción a MFA, introducción general a mfa, qué es mfa"
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
	ms.date="02/29/2016"
	ms.author="billmath"/>

# ¿Qué es Azure Multi-Factor Authentication?
Multi-factor Authentication (MFA) es un método de autenticación que requiere el uso de más de un método de verificación y agrega un segundo nivel importante de seguridad a las transacciones e inicios de sesión del usuario. Funciona mediante la solicitud de dos o más de los siguientes métodos de verificación:

- Un elemento que conoce (normalmente una contraseña).
- Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
- Un elemento físico que le identifica (biométrica).

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas, como llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran.

Para obtener información general de cómo funciona Multi-Factor Authentication, vea el siguiente vídeo.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##¿Por qué usar Azure Multi-Factor Authentication?

Hoy en día, ahora más que nunca, las personas están cada vez más conectadas. Con los smartphones, tabletas, equipos portátiles y equipos de sobremesa, las personas tienen varias opciones para conectarse y permanecer conectados en cualquier momento. Las personas pueden acceder a sus cuentas y las aplicaciones desde cualquier lugar y esto significa que pueden ser más productivos y atender mejor a sus clientes.

Azure Multi-Factor Authentication es una solución fácil de usar, escalable y confiable que proporciona un segundo método de autenticación para que los usuarios siempre estén protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalabilidad](./media/multi-factor-authentication/scalable.png)| ![Siempre protegido](./media/multi-factor-authentication/protected.png)|![Confiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalable**|**Siempre protegido**|**Confiable**

- **Fácil de usar**: Azure Multi-Factor Authenticaton es fácil de configurar y utilizar. La protección adicional que se incluye con Azure Multi-Factor Authenticaton permite a los usuarios utilizar y administrar sus propios dispositivos y, en muchos casos, puede instalarse con unos pocos clics.
- **Escalable**: Azure Multi-Factor Authenticaton utiliza la eficacia de la nube y se integra con Active Directory local y aplicaciones personalizadas. Esta protección se extiende incluso a los escenarios críticos de gran volumen.
- **Siempre protegido** Azure Multi-Factor Authenticaton ofrece autenticación segura mediante los más elevados estándares del sector.
- **Confiable**: se garantiza la disponibilidad del 99,9% de Azure Multi-Factor Authenticaton. Se considera que el servicio no está disponible cuando no puede recibir ni procesar las solicitudes de autenticación para la autenticación multifactor.  

Para obtener más información sobre por qué usar Azure Multi-Factor Authenticaton, vea el vídeo siguiente.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>


## Cómo funciona Azure Multi-Factor Authentication

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

## Métodos disponibles para la autenticación multifactor
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario. Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

Método de comprobación | Descripción
------------- | ------------- |
Llamada de teléfono | Se realiza una llamada al smartphone del usuario para solicitarle que compruebe que ha iniciado sesión presionando el signo #. Esto completará el proceso de comprobación. Esta opción es configurable y puede cambiarse por un código que especifique.
Mensaje de texto | Se enviará un mensaje de texto al smartphone del usuario con un código de 6 dígitos. Escriba este código para completar el proceso de comprobación.
Notificación de aplicación móvil | Se enviará al smartphone del usuario una solicitud de comprobación solicitándole que complete la comprobación mediante la selección de Comprobar en la aplicación móvil. Esto ocurrirá si ha seleccionado la notificación de aplicación como su método de comprobación principal. Si los usuarios lo reciben cuando no han iniciado sesión, pueden notificarlo como fraude.
Código de comprobación con la aplicación móvil | Se enviará un código de comprobación a la aplicación móvil que se ejecuta en el smartphone del usuario. Esto ocurrirá si ha seleccionado un código de verificación como su método de comprobación principal.


## Versiones disponibles de Azure Multi-Factor Authentication
Azure Multi-Factor Authentication está disponible en tres versiones diferentes. En la tabla siguiente se describe cada una de ellas con más detalle.

Versión | Descripción
------------- | ------------- |
Multi-Factor Authentication para Office 365 | Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. De este modo, los administradores pueden proteger ahora sus recursos de Office 365 con la autenticación multifactor. Esta versión incluye una suscripción a Office 365.
Multi-Factor Authentication para administradores de Azure | La misma subred de capacidades Multi-Factor Authentication para Office 365 estará disponible sin que suponga ningún coste para los administradores de Azure. Las cuentas administrativas de la suscripción de Azure pueden obtener ahora una protección adicional mediante la habilitación de esta funcionalidad de autenticación a través de varias fases. Por lo tanto, un administrador que desee obtener acceso al portal de Azure para crear una VM o un sitio web, o administrar almacenamiento, servicios móviles o cualquier otro servicio de Azure, puede agregar la autenticación mediante varias fases a su cuenta de administrador.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication ofrece el mayor conjunto de capacidades. Proporciona opciones de configuración adicionales mediante el Portal de administración de Azure, avanzadas capacidades de informe y soporte técnico para una amplia variedad de aplicaciones locales y en la nube. Azure Multi-Factor Authentication forma parte de Azure Active Directory Premium.

## Comparación de características de las versiones
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
Recordar MFA para dispositivos de confianza |* | * | *
SDK de MFA| | | *
MFA para aplicaciones locales mediante servidor MFA| | | *
Opciones de verificación seleccionables (versión preliminar pública)|* | * | *


## Cómo conseguir Azure Multi-Factor Authentication

Si desea disponer de todas las funcionalidades que ofrece Azure Multi-Factor Authentication, en lugar de solo las que se proporcionan para los usuarios de Office 365 y administradores de Azure, hay varias opciones para obtenerlas:

1.	Comprar licencias de Azure Multi-Factor Authentication y asignarlas a los usuarios.
2.	Comprar licencias que incluyen Azure Multi-Factor Authentication, como Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite, y asignarlas a los usuarios.
3.	Crear un proveedor de Azure Multi-Factor Authentication dentro de una suscripción a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita de Azure. Las suscripciones de prueba tendrán que convertirse en suscripciones normales antes de que expire la prueba.

Al usar un proveedor de Azure Multi-Factor Authentication, existen dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:


- **Por usuario**. Generalmente para empresas que quieren habilitar la autenticación multifactor para un número fijo de empleados que con frecuencia necesitan autenticación.
- **Por autenticación**. Generalmente para empresas que quieren habilitar la autenticación multifactor para un número mayor de usuarios externos que no suelen necesitar frecuentemente autenticación.

Azure Multi-Factor Authentication proporciona métodos de comprobación seleccionables tanto para la nube y como para servidor. Esto significa que puede elegir qué métodos hay disponibles para que los usuarios los utilicen con Multi-Factor Authentication. Esta característica es actualmente una versión preliminar pública para la versión en la nube de Multi-Factor Authentication. Para más información, consulte [Métodos de verificación seleccionables](multi-factor-authentication-whats-next.md#selectable-verification-methods-public-preview).

Para obtener información detallada sobre los precios, consulte [Precios de Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Elija el modelo por puesto o según el consumo que mejor funcione para su organización. Para comenzar, consulte [Introducción](multi-factor-authentication-get-started.md).

## Selección de la solución de seguridad multifactor más adecuada

Puesto que existen varios modelos de Azure Multi-Factor Authentication, es necesario determinar primero un par de cosas para descubrir cuál es el más adecuado para usar. Estas cosas son:

-	[Qué es lo que quiero proteger](#what-am-i-trying-to-secure)
-	[Dónde se encuentran los usuarios](#where-are-the-users-located)

Las siguientes secciones proporcionan instrucciones para determinar cada uno de estos puntos.

### ¿Qué es lo que quiero proteger?

Para determinar la solución correcta para la autenticación multifactor, en primer lugar, hay que responder a la pregunta de qué es lo que está intentando proteger con un segundo método de autenticación. ¿Es una aplicación que está en Azure? O es, por ejemplo, un sistema de acceso remoto. Mediante la determinación de lo que estamos intentando proteger, encontraremos la respuesta a la pregunta de dónde hay que habilitar la autenticación multifactor.



¿Qué intenta proteger?| Multi-Factor Authentication en la nube|Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Aplicaciones propias de Microsoft|* |* |
Aplicaciones de SaaS en la Galería de aplicaciones|* |* |
Aplicaciones de IIS que se publican a través del proxy de aplicación de Azure AD|* |* |
Aplicaciones de IIS que no se publican a través del proxy de aplicación de Azure AD | |* |
Acceso remoto como VPN, RDG| |* |



### Dónde se encuentran los usuarios

A continuación, dependiendo de dónde se encuentran los usuarios, podemos determinar la solución correcta a utilizar, la autenticación multifactor en la nube o local con el Servidor MFA.



Ubicación del usuario| Solución
------------- | :------------- |
Azure Active Directory| Multi-Factor Authentication en la nube|
Azure AD y AD local mediante la federación con AD FS| Tanto MFA en la nube como Servidor MFA son opciones que están disponibles
Azure AD y AD local a través de DirSync, Azure AD Sync, Azure AD Connect - sin sincronización de contraseñas|Tanto MFA en la nube como Servidor MFA son opciones que están disponibles
Azure AD y AD local a través de DirSync, Azure AD Sync, Azure AD Connect - con sincronización de contraseñas|Multi-Factor Authentication en la nube
Active Directory local|Servidor Multi-Factor Authentication

La tabla siguiente es una comparación de las características que aparecen en Multi-Factor Authentication en la nube con las del Servidor Multi-Factor Authentication.

 | Multi-Factor Authentication en la nube | Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notificación de aplicación móvil como segundo factor | ● | ● |
Código de comprobación de aplicación móvil como segundo factor | ● | ●
Llamada de teléfono como segundo factor | ● | ●
SMS unidireccional como segundo factor | ● | ●
SMS bidireccional como segundo factor | | ●
Tokens de hardware como segundo factor | | ●
Contraseñas de aplicación para los clientes que no son compatibles con MFA | ● |  
Control de administración sobre los métodos de autenticación | | ●
Modo de PIN | | ●
Alerta de fraude | ● | ●
Informes de MFA | ● | ●
Omisión por única vez | ● | ●
Saludos personalizados para las llamadas de teléfono | ● | ●
Identificador de llamada personalizable para llamadas telefónicas | ● | ●
IP de confianza | ● | ●
Recordar MFA para dispositivos de confianza (versión preliminar pública) | ● |  
Acceso condicional | ● | ●
Memoria caché | ● | ●

Ahora que hemos determinado cuál vamos a usar: la autenticación multifactor de nube o el Servidor MFA local, ya podemos comenzar a configurar y usar Azure Multi-Factor Authentication. **Seleccione el icono que representa su opción**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0302_2016-->