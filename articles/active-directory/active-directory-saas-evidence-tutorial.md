<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Evidence.com | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Evidence.com."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# Tutorial: Integración de Azure Active Directory con Evidence.com

El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (AAD) y Evidence.com. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:
	
* Una suscripción válida a Microsoft Azure
* Una suscripción a Evidence.com con el inicio de sesión único habilitado (envíe un correo electrónico a earlyaccess@evidence.com si el inicio de sesión único basado en SAML no está habilitado)

Después de completar este tutorial, los usuarios de AAD a quienes se haya asignado acceso a Evidence.com podrán realizar un inicio de sesión único en la aplicación mediante el panel de acceso de AAD.

## Incorporación de Evidence.com a su directorio

En esta sección, se describe cómo agregar Evidence.com como aplicación integrada en Azure Active Directory.

**Para habilitar la integración de aplicaciones para Evidence:**

1.	En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

2.	En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.	Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

4.	Para abrir la Galería de aplicaciones, haga clic en **Agregar** y después en **Agregar una aplicación de la galería**.

5.	En el cuadro de búsqueda, escriba **Evidence.com**.

6.	En el panel de resultados, seleccione **Evidence.com** y después haga clic en **Completar** para agregar la aplicación.


## Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir que los usuarios se autentiquen en Evidence.com con su cuenta de Azure Active Directory a través de la federación basada en el protocolo SAML.

**Siga estos pasos para configurar el inicio de sesión único:**

1.	Después de agregar Evidence.com en el Portal de Azure clásico, haga clic en **Configurar inicio de sesión único**. 
 
2.	En la siguiente pantalla, seleccione **Inicio de sesión único de Microsoft Azure AD** y después haga clic en **Siguiente**.

3.	En la pantalla Configurar dirección URL de la aplicación, escriba la dirección URL donde los usuarios iniciarán sesión en la dirección URL de inquilino de Evidence.com (ejemplo: https://yourtenant.evidence.com) y después haga clic en **Siguiente**.

4.	Haga clic en el vínculo **Descargar certificado** y guárdelo en la unidad local. Se usarán este certificado y las direcciones URL de metadatos (Id. de entidad, Dirección URL de inicio de sesión único y Dirección URL de cierre de sesión) para configurar el SSO en el sitio Evidence.com.

5.	En una ventana de explorador web diferente, inicie sesión como administrador en su inquilino de Evidence.com y vaya a la pestaña **Admin** (Administrador).
      
6.	Haga clic en **Agency Single Sign On** (Inicio de sesión único de agencia).
 
7.	Seleccione **SAML Based Single Sign On** (Inicio de sesión único basado en SAML).
 
8.	Copie los valores de **URL del emisor**, **Inicio de sesión único** y **Cierre de sesión único** que se muestran en el Portal de Azure clásico a los campos correspondientes en Evidence.com.

9.	Abra el certificado que descargó en el paso 4 con un editor de texto como Notepad.exe, y copie y pegue el contenido en el cuadro **Security Certificate** (Certificado de seguridad).

10. Guarde la configuración en Evidence.com.
 
11.	En el Portal de Azure clásico, active la casilla **Confirme que ha configurado el inicio de sesión único como se ha descrito anteriormente**. Esta comprobación permitirá que el certificado actual empiece a funcionar para la aplicación.
 
12.	En la página Confirmación del inicio de sesión único, haga clic en **Completar**.


## Creación de un usuario de prueba de Evidence.com

Para que los usuarios de Azure AD puedan iniciar sesión, se les debe aprovisionar para el acceso dentro de la aplicación Evidence.com. En esta sección, se describe cómo crear cuentas de usuario de Azure AD en Evidence.com.

**Para aprovisionar una cuenta de usuario en Evidence.com:**

1.	En una ventana del explorador web, inicie sesión en el sitio de la compañía en Evidence.com como administrador.

2.	Vaya a pestaña **Admin** (Administrador).

3.	Haga clic en **Add User** (Agregar usuario).

4.	Haga clic en el botón **Add** (Agregar).

5.  El valor de **Email Address** (Dirección de correo electrónico) del usuario agregado debe coincidir con el nombre de usuario en Azure AD de los usuarios a quienes desea conceder acceso. Si el nombre de usuario y la dirección de correo electrónico no usan el mismo valor en su organización, puede usar la sección **Evidence.com > Atributos > Inicio de sesión único** del Portal de Azure clásico para hacer que el identificador de nombre enviado a Evidence.com sea la dirección de correo electrónico.


## Asignación de usuarios a Evidence.com

Para que los usuarios de AAD aprovisionados vean Evidence.com en su panel de acceso, se les debe asignar acceso desde el Portal de Azure clásico.

**Para asignar usuarios a Evidence.com:**

1.	En la página de inicio rápido de Evidence.com en el Portal de Azure clásico, haga clic en **Asignar usuarios a Evidence.com**.
 
2.	En el menú **Mostrar**, seleccione si desea asignar un usuario o un grupo a Evidence.com y haga clic en el botón de marca de verificación.
 
3.	En la lista **Usuarios**, seleccione los usuarios o el grupo a los que desea asignar a Evidence.com.
 
4.	En el pie de página, haga clic en el botón **Asignar**.

<!---HONumber=AcomDC_0224_2016-->