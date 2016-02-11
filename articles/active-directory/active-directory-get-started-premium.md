<properties
	pageTitle="Introducción a Azure Active Directory Premium"
	description="Un tema que explica cómo suscribirse a la edición Azure Active Directory Premium."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="markvi"/>

# Introducción a Azure Active Directory Premium

Azure Active Directory viene en tres ediciones: Premium, Básico y Gratis. La edición Gratis se incluye con una suscripción de Azure u Office 365. Las ediciones Básico y Premium están disponibles a través de un [contrato Enterprise de Microsoft](https://www.microsoft.com/es-ES/licensing/licensing-programs/enterprise.aspx) o el programa [Licencias por volumen abiertas](https://www.microsoft.com/es-ES/licensing/licensing-programs/open-license.aspx). Los suscriptores de Azure y Office 365 también pueden comprar Active Directory Premium en línea. [Inicie sesión aquí](https://portal.office.com/Commerce/Catalog.aspx) para comprarlo.

> [AZURE.NOTE]
Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Azure Active Directory Premium también se incluye en **Enterprise Mobility Suite**. Enterprise Mobility Suite es una manera rentable de que las organizaciones usen Microsoft Intune, Azure Rights Management y los servicios de Active Directory Premium de servicios juntos en un plan de licencias. Para obtener más información, consulte el sitio web de [Enterprise Mobility Suite](https://www.microsoft.com/es-ES/server-cloud/enterprise-mobility/overview.aspx).

Para empezar a usar las características de Azure Active Directory Premium hoy, siga estos pasos. Se aplican los mismos pasos a la edición Básico de Azure Active Directory.

## Paso 1: Suscripción de Azure Active Directory Premium y Básico

Para suscribirse, consulte el sitio web de [Licencias por volumen](http://www.microsoft.com/es-ES/licensing/how-to-buy/how-to-buy.aspx).

## Paso 2: Activación del plan de licencias

Si es la primera vez que ha adquirido un plan de licencias a través del programa Licencias por volumen para empresas de Microsoft, tendrá que activar primero el plan de licencias antes de comenzar a asignar licencias desde el directorio de Azure Active Directory. Para ello, debe hacer clic en cualquier vínculo de inicio de sesión o registro que se encuentra en el correo electrónico de confirmación (ver abajo) que recibirá una vez que su primera compra de plan de licencia se haya completado. En las compras posteriores para este directorio, las licencias se activarán automáticamente en el mismo directorio.

![][1]

Si tiene un inquilino existente, seleccione el vínculo **Inicio de sesión** para iniciar sesión con la cuenta de administrador existente. Es importante que inicie sesión con las credenciales de administrador global desde el directorio donde se deben activar las licencias.

Si desea crear un nuevo inquilino de Azure Active Directory que usar con el plan de licencias, debe seleccionar el vínculo **Registrarse** que le llevará a la pantalla siguiente.

![][2]

Una vez que haya terminado con el proceso de suscripción o de inicio de sesión iniciados desde el correo electrónico, verá la pantalla siguiente que confirma que el plan de licencias se ha activado para el inquilino.

![][3]

## Paso 3: Activación de acceso de Azure Active Directory

Una vez que se aprovisionen las licencias en el directorio, recibirá un correo electrónico de bienvenida (consulte a continuación) que confirme que puede comenzar a administrar las características y licencias de Azure Active Directory Premium o Enterprise Mobility Suite. Si ha usado Microsoft Azure antes, puede continuar con http://manage.windowsazure.com para asignar las licencias nuevas (consulte el Paso 4 a continuación para obtener más información). Si es nuevo en Microsoft Azure, al seleccionar el vínculo de inicio de sesión en el correo electrónico o ir a la página de [acceso de la activación de Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P), se le dirigirá por una serie de pasos que le ayudarán a obtener acceso a su directorio a través del Portal de administración de Azure.

![][4]

Una vez que haya iniciado sesión correctamente, será necesario completar una pantalla de autenticación de 2.º factor (a continuación) proporcionando un número de teléfono móvil y validándolo. Una vez hecho esto, podrá activar el acceso a Azure Active Directory seleccionando **Suscripción**.

![][5]

La activación puede tardar unos minutos, como se muestra a continuación. Una vez que se active el acceso, la barra marrón desaparecerá y podrá hacer clic en el vínculo del portal en la esquina superior derecha o desplazarse al [Portal de administración de Azure](http://manage.windowsazure.com).

![][6]

En este caso, el acceso a Azure se limitará a Azure Active Directory.

![][7]

Es posible que ya haya obtenido acceso a Azure previamente. Además, puede actualizar su acceso a Azure Active Directory a acceso de Azure completo mediante la activación de suscripciones de Azure adicionales. En estos casos, el Portal de administración tendrán más características de la forma siguiente.

![][8]

Si intenta activar el acceso a Azure Active Directory antes de recibir el correo electrónico de bienvenida anterior, puede experimentar el siguiente mensaje de error. Vuelva a intentarlo en unos minutos una vez haya recibido el correo electrónico.

![][9]

Los nuevos administradores de su suscripción también pueden activar su acceso al Portal de administración a través de este vínculo.

## Paso 4: Asignación de licencias a cuentas de usuario

Antes de que pueda empezar a utilizar el plan que compró, deberá asignar manualmente licencias a cuentas de usuario dentro de su organización para que puedan usar las características enriquecidas que se proporcionan con Premium. Utilice los pasos siguientes para asignar licencias a los usuarios para que puedan usar las características de Azure Active Directory Premium.

Para asignar licencias a los usuarios:

1. Inicie sesión en el Portal de administración de Azure como el administrador global del directorio que desea personalizar.
2. Haga clic en **Active Directory** y, a continuación, seleccione el directorio donde desee asignar licencias.
3. Seleccione la pestaña **Licencias**, seleccione **Active Directory Premium** o **Enterprise Mobility Suite** y, a continuación, haga clic en **Asignar**.

    ![][10]

4. En el cuadro de diálogo, seleccione los usuarios a los que desee asignar las licencias y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.

    ![][11]

## Restricciones de licencia

Algunos planes de licencias son subconjuntos o superconjuntos de otros planes de licencias. En la mayoría de los casos, un usuario no se puede asignar a un plan de licencias ya asignado a ellos. Si tiene pensado asignar un plan de licencias que es un superconjunto, deberá quitar primero el plan de licencias de subconjunto.

## Requisitos de licencia

Al asignar una licencia a un usuario, puede especificar una ubicación de uso principal en las propiedades de su cuenta, como se muestra a continuación. Si no se especifica una ubicación de uso, la ubicación del inquilino se asigna automáticamente al usuario.

![][12]

La disponibilidad de servicios y características de un servicio en la nube de Microsoft varía según el país o la región. Un servicio, como Voz sobre IP (VoIP), puede estar disponible en un país o región y no disponible en otro. Las características dentro de un servicio pueden estar restringidas por motivos legales en determinados países o regiones. Para ver si un servicio o una característica está disponible con o sin restricciones, busque su país o región en el sitio de restricciones de licencia del servicio.

## Pasos siguientes

- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=AcomDC_0128_2016-->