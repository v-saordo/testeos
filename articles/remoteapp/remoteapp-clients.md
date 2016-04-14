
<properties
    pageTitle="Acceso a sus aplicaciones desde cualquier dispositivo | Microsoft Azure"
    description="Obtenga información sobre qué clientes son compatibles con Azure RemoteApp y cómo tener acceso a sus aplicaciones."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/07/2016"
    ms.author="elizapo" />



# Acceso a las aplicaciones de Azure RemoteApp

Una de las ventajas de Azure RemoteApp es que puede tener acceso a las aplicaciones desde cualquiera de los dispositivos. Aún mejor, puede comenzar a trabajar en un dispositivo y, a continuación, realizar una transición a un segundo dispositivo y continuar el trabajo desde donde lo dejó. Para empezar, deberá descargar el cliente apropiado para el dispositivo e iniciar sesión en el servicio.

En este tema, revisaremos los clientes admitidos actualmente y cómo descargarlos antes de mostrar cómo iniciar sesión en RemoteApp desde cada uno de los clientes.

## Clientes compatibles

Puede acceder a RemoteApp mediante los siguientes pasos si el dispositivo está ejecutando uno de estos sistemas operativos:

 - Windows 10 
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows Phone 8.1
 - iOS
 - Mac OS X
 - Android


 Información acerca de los clientes ligeros. Se admiten los siguientes clientes ligeros de Windows Embedded:

- Windows Embedded Standard 7
- Windows Embedded 8 Standard
- Windows Embedded 8.1 Industry Pro
- Windows 10 IoT Enterprise


## Descarga del cliente

Independientemente de la plataforma que esté utilizando, el cliente que necesita que acceda a RemoteApp puede encontrarse en la página [Descarga del cliente de Escritorio remoto](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).

Al hacer clic en los diferentes vínculos se comenzará a descargar el cliente directamente o será dirigido a la página de descarga del cliente descarga de la tienda de aplicaciones de esa plataforma. Siga las instrucciones en pantalla para instalar el cliente.

Una vez haya instalado el cliente en el dispositivo y lo haya iniciado, salte a la sección correspondiente a continuación para obtener información sobre cómo iniciar sesión en RemoteApp desde ese cliente.

## Android

Una vez haya instalado la aplicación de Escritorio remoto de Microsoft desde la tienda Google Play, podrá encontrarla en la lista de aplicaciones en **Escritorio remoto**.

1. Al iniciar la aplicación se accede a un Centro de conexiones vacío, a menos que ya ha utilizado la aplicación. Para empezar a trabajar con Azure RemoteApp, toque el botón Agregar **"" +""** y **Azure RemoteApp**.	

	 ![Centro de conexiones vacío](./media/remoteapp-clients/Android1.png)

2. Debe iniciar sesión con su dirección de correo electrónico para tener acceso al servicio. Pulse **Primeros pasos**.

	![Solicitud de inicio de sesión](./media/remoteapp-clients/Android2.png)

3. En la siguiente página, escriba en su **dirección de correo electrónico** y toque **Continuar**. Esto comenzará el proceso de inicio de sesión mediante Azure Active Directory.

	![Primera página de Azure Active Directory](./media/remoteapp-clients/Android3.png)

4. Siga las instrucciones en pantalla para iniciar sesión con su cuenta de Microsoft (denominada anteriormente "LiveID") o su id. de organización. En cuanto haya iniciado sesión, es posible que se le presente una página con todas las invitaciones que ha recibido. Si es así, seleccione las invitaciones de confianza y toque en **Hecho**.

	![Página de invitaciones](./media/remoteapp-clients/Android4.png)

5. Después de aceptar las invitaciones, se descargará en el dispositivo la lista de aplicaciones a las que tiene acceso y estarán disponibles en el Centro de la conexión. Toque una de las aplicaciones para empezar a usarla.

	![Centro de conexiones con una fuente](./media/remoteapp-clients/Android5.png)

6. Si todavía no tiene una invitación, todavía puede probar el servicio. Para ello, toque **Ir a la versión de prueba gratuita** cuando se le solicite.

	![Solicitud de fuente de demostración](./media/remoteapp-clients/Android6.png)

7. Esto le dará acceso a un conjunto básico de aplicaciones para comenzar a usar RemoteApp.

	![Fuente de demostración de RemoteApp de Azure](./media/remoteapp-clients/Android7.png)

## iOS

Una vez haya instalado la aplicación de Escritorio remoto de Microsoft desde App Store, podrá encontrarla en la lista de aplicaciones en **Cliente de RD**.

1. Al iniciar la aplicación se accede a un Centro de conexiones vacío, a menos que ya ha utilizado la aplicación. Para empezar a trabajar con Azure RemoteApp, toque el botón de agregar **""+""** y **Agregar Azure RemoteApp**.

	![Centro de conexiones vacío](./media/remoteapp-clients/IOS1.png)

2. Debe iniciar sesión con su dirección de correo electrónico para tener acceso al servicio. Para iniciar ese proceso, escriba su **dirección de correo electrónico** y toque **Continuar**.

	![Solicitud de inicio de sesión](./media/remoteapp-clients/picture1.png)

3. Siga las instrucciones en pantalla para iniciar sesión con su cuenta de Microsoft (LiveID) o su id. de organización. En cuanto haya iniciado sesión, es posible que se le presente una página con todas las invitaciones que ha recibido. Si es así, seleccione las invitaciones de confianza y toque en **Hecho**.

	![Página de invitaciones](./media/remoteapp-clients/IOS3.png)

4. Después de aceptar las invitaciones, se descargará en el dispositivo la lista de aplicaciones a las que tiene acceso y estarán disponibles en el Centro de la conexión. Toque una de las aplicaciones para iniciarla y empezar a usarla.

	![Centro de conexiones con una fuente](./media/remoteapp-clients/IOS4.png)

5. Si todavía no tiene una invitación, todavía puede probar el servicio. Para ello, toque **Ir a la versión de prueba gratuita** cuando se le solicite.

	![Solicitud de fuente de demostración](./media/remoteapp-clients/IOS5.png)

6. Esto le dará acceso a un conjunto básico de aplicaciones para comenzar a usar RemoteApp.

	![Fuente de demostración de RemoteApp de Azure](./media/remoteapp-clients/IOS6.png)

## Mac OS X

Una vez haya instalado la aplicación de Escritorio remoto de Microsoft desde la App Store, podrá encontrarla en la lista de aplicaciones en **Escritorio remoto de Microsoft**.

1. Al iniciar la aplicación se accede a un Centro de conexiones vacío, a menos que ya ha utilizado la aplicación. Para empezar a trabajar con Azure RemoteApp, haga clic en el botón **Azure RemoteApp**.

	![Centro de conexiones vacío](./media/remoteapp-clients/Mac1.png)

2. Debe iniciar sesión con su dirección de correo electrónico para tener acceso al servicio. Para iniciar ese proceso, toque **Introducción**.

	![Solicitud de inicio de sesión](./media/remoteapp-clients/Mac2.png)

3. En la siguiente página, escriba en su **dirección de correo electrónico** y toque **Continuar**. Esto comenzará el proceso de inicio de sesión mediante Azure Active Directory.

	![Primera página de Azure Active Directory](./media/remoteapp-clients/picture2.png)

4. Siga las instrucciones en pantalla para iniciar sesión con su cuenta de Microsoft (LiveID) o su id. de organización. En cuanto haya iniciado sesión, es posible que se le presente una página con todas las invitaciones que ha recibido. Si es así, seleccione las invitaciones de confianza y cierre el cuadro de diálogo.

	![Página de invitaciones](./media/remoteapp-clients/Mac4.png)

5. Después de aceptar las invitaciones, se descargará en el dispositivo la lista de aplicaciones a las que tiene acceso y estarán disponibles en el Centro de la conexión. Haga doble clic en una de las aplicaciones para iniciarla y empezar a usarla.

	![Centro de conexiones con una fuente](./media/remoteapp-clients/Mac5.png)

6. Si todavía no tiene una invitación, todavía puede probar el servicio. Para ello, haga clic en **Ir a la versión de prueba gratuita** cuando se le solicite.

	![Solicitud de fuente de demostración](./media/remoteapp-clients/Mac6.png)

7. Esto le dará acceso a un conjunto básico de aplicaciones para comenzar a usar RemoteApp.

	![Fuente de demostración de RemoteApp de Azure](./media/remoteapp-clients/Mac7.png)

## Windows (todas las versiones compatibles excepto Windows Phone)

El cliente se inicia automáticamente una vez finalizada la instalación, sin embargo, cuando necesite tener acceso a este más adelante, lo encontrará en la lista de aplicaciones con el nombre **Azure RemoteApp**.

1. Después de iniciar el cliente, la primera página que verá le dará la bienvenida a Azure RemoteApp. Para continuar, haga clic en **Introducción**.

	![Página de bienvenida del cliente RemoteApp de Azure](./media/remoteapp-clients/Windows1.png)

2. La página siguiente inicia el proceso de inicio de sesión de Azure RemoteApp mediante Azure Active Directory. Este proceso debe resultarle familiar si ha usado servicios Microsoft en el pasado. Comience escribiendo su **dirección de correo electrónico** y haga clic en **Continuar**.

	![Primer aviso del sistema de Azure Active Directory](./media/remoteapp-clients/Windows2.png)

3. Siga las instrucciones en pantalla para iniciar sesión con su cuenta de Microsoft (LiveID) o su id. de organización. En cuanto haya iniciado sesión, es posible que se le presente una página con todas las invitaciones que ha recibido. Si es así, seleccione las invitaciones de confianza y haga clic en **Hecho**.

	![Página de invitaciones del cliente RemoteApp de Azure](./media/remoteapp-clients/Windows3.png)

4. Después de aceptar las invitaciones, se descargará en el dispositivo la lista de aplicaciones a las que tiene acceso y estarán disponibles en el Centro de la conexión. Haga doble clic en una de las aplicaciones para iniciarla y empezar a usarla.

	![Centro de conexiones del cliente RemoteApp de Azure](./media/remoteapp-clients/Windows4.png)

5. No se preocupe si nadie le ha enviado una invitación aún, nosotros nos ocupamos. Continuará teniendo acceso a una colección de demostración para que pueda probar el servicio.

	![Fuente de demostración de RemoteApp de Azure](./media/remoteapp-clients/Windows5.png)

## Windows Phone 8.1

Una vez haya instalado la aplicación de Escritorio remoto de Microsoft desde la tienda Windows Phone 8.1, podrá encontrarla en la lista de aplicaciones en **Escritorio remoto**.

1. Al iniciar la aplicación se accede directamente a un Centro de conexiones vacío, a menos que ya ha utilizado la aplicación. Para empezar a trabajar con Azure RemoteApp, toque el botón de agregar **""+""** en la parte inferior de la pantalla.

	![Centro de conexiones vacío](./media/remoteapp-clients/WinPhone1.png)

2. A continuación, toque **Azure RemoteApp**.

	![Agregar página de elemento](./media/remoteapp-clients/WinPhone2.png)

3. Debe iniciar sesión con su dirección de correo electrónico para tener acceso al servicio. Para iniciar este proceso, toque **Conectar**.

	![Solicitud de inicio de sesión](./media/remoteapp-clients/WinPhone3.png)

4. En la siguiente página, escriba en su **dirección de correo electrónico** y toque **Continuar**. Esto comenzará el proceso de inicio de sesión mediante Azure Active Directory.

	![Primera página de Azure Active Directory](./media/remoteapp-clients/WinPhone4.png)

5. Siga las instrucciones en pantalla para iniciar sesión con su cuenta de Microsoft (LiveID) o su id. de organización. En cuanto haya iniciado sesión, es posible que se le presente una página con todas las invitaciones que ha recibido. Si es así, seleccione las invitaciones de confianza y toque **Guardar**.

	![Página de invitaciones](./media/remoteapp-clients/WinPhone5.png)

6. Después de aceptar las invitaciones, se descargará en el dispositivo la lista de aplicaciones a las que tiene acceso y estarán disponibles en el Centro de la conexión. Toque una de las aplicaciones para iniciarla y empezar a usarla.

	![Centro de conexiones con una fuente](./media/remoteapp-clients/WinPhone6.png)

7. Si todavía no tiene una invitación, todavía puede probar el servicio. Para ello, toque **Sí** cuando se le solicite.

	![Solicitud de fuente de demostración](./media/remoteapp-clients/WinPhone7.png)

8. Esto le dará acceso a un conjunto básico de aplicaciones para comenzar a usar RemoteApp.

	![Fuente de demostración de RemoteApp de Azure](./media/remoteapp-clients/WinPhone8.png)
 

<!---HONumber=AcomDC_0114_2016-->