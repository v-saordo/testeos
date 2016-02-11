<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Concur | Microsoft Azure" 
    description="Aprenda a usar Concur con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Concur  


El objetivo de este tutorial es mostrar la integración de Azure y Concur. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en Concur

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Concur
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-concur-tutorial/IC769766.png "Escenario")

>[AZURE.NOTE]La configuración de la suscripción a Concur para un SSO federado mediante SAML es una tarea independiente, que debe ponerse en contacto con Concur para llevarse a cabo.

##Habilitación de la integración de aplicaciones para Concur

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Concur.

###Siga estos pasos para habilitar la integración de aplicaciones para Concur:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directorio**, seleccione el directorio cuya integración quiere habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-concur-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y luego en **Agregar una aplicación que mi organización use**.

    ![¿Qué desea hacer?](./media/active-directory-saas-concur-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba **Concur**.

    ![Galería de aplicaciones](./media/active-directory-saas-concur-tutorial/IC721727.png "Galería de aplicaciones")

6.  En el panel de resultados, seleccione **Concur** y luego haga clic en **Completar** para agregar la aplicación.

    ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Concur con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

>[AZURE.NOTE]La configuración de la suscripción a Concur para un SSO federado mediante SAML es una tarea independiente, que debe ponerse en contacto con Concur para llevarse a cabo.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Concur**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/IC769767.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Concur?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/IC769768.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Concur**, escriba su dirección URL de inicio de sesión del inquilino de Concur y haga clic en **Siguiente**.

    ![Configurar dirección URL de inicio de sesión](./media/active-directory-saas-concur-tutorial/IC769769.png "Configurar dirección URL de inicio de sesión")

4.  En la página **Configurar inicio de sesión único en Concur**, siga estos pasos.

    ![Configurar dirección URL de inicio de sesión](./media/active-directory-saas-concur-tutorial/IC769770.png "Configurar dirección URL de inicio de sesión")

    1.  Haga clic en Descargar los metadatos y después proteja el archivo de datos en su equipo.
    2.  Póngase en contacto con el equipo de soporte técnico de Concur para configurar el SSO para el inquilino.
    3.  Seleccione la confirmación de la configuración de inicio de sesión único y luego haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.  

	>[AZURE.NOTE]La configuración de la suscripción a Concur para un SSO federado mediante SAML es una tarea independiente, que debe ponerse en contacto con Concur para llevarse a cabo.

##Configuración del aprovisionamiento de usuario

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Concur.

Para habilitar las aplicaciones en el servicio de gastos, debe tener la configuración correcta y el uso de un perfil de administrador de servicio web. No agregue simplemente el rol de administrador de servicio web al perfil de administrador existente que se usa para las funciones administrativas de tiempo y gastos.

Los asesores de Concur o el administrador de cliente debe crear un perfil de administrador de servicios web distintos y el administrador de cliente debe usar este perfil para las funciones de administrador de servicios web (por ejemplo, para habilitar aplicaciones). Estos perfiles deben mantenerse separados a diario del perfil de administración de tiempos y gastos del administrador de cliente (el perfil de administrador de tiempos y gastos no debe tener el rol de administrador de servicios web asignado).

Al crear el perfil que se usará para habilitar la aplicación, escriba el nombre del administrador de cliente en los campos de perfil de usuario. Esto asignará la propiedad al perfil. Una vez creado el perfil, el cliente debe iniciar sesión con este perfil para hacer clic en el botón "*Habilitar*" para una aplicación asociada en el menú de servicios web.

Por las siguientes razones, esta acción no debe realizarse con el perfil que se usa en la administración normal de tiempo y gastos.

1.  El cliente tiene que ser el que hace clic en "*Sí*" en la ventana de cuadro de diálogo que aparece después de la habilitación de la aplicación. Este clic confirma que el cliente está autorizado por la aplicación asociada a tener acceso a sus datos, por lo que usted o el asociado no pueden hacer clic en el botón Sí.
2.  Si un administrador de cliente que ha habilitado una aplicación mediante el perfil de administrador de tiempo y gastos abandona la empresa (lo que da lugar a la desactivación del perfil), las aplicaciones habilitadas mediante ese perfil no funcionarán hasta que la aplicación se habilite con otro perfil de administrador de servicios web activo. Es por este motivo por lo hay que crear distintos perfiles de administrador servicios web.
3.  Si un administrador deja la compañía, el nombre asociado al perfil de administrador de servicios web puede cambiarse al del administrador de sustitución si lo desea sin afectar a la aplicación habilitada, porque no necesita ese perfil desactivado

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su inquilino **Concur**.

2.  En el menú **Administración**, seleccione **Servicios web**.

    ![Inquilino de Concur](./media/active-directory-saas-concur-tutorial/IC721729.png "Inquilino de Concur")

3.  En el lado izquierdo, en el panel **Servicios web**, seleccione **Habilitar aplicación de asociado**.

    ![Habilitar aplicación de asociado](./media/active-directory-saas-concur-tutorial/IC721730.png "Habilitar aplicación de asociado")

4.  En la lista **Habilitar aplicación**, seleccione **Azure Active Directory** y después haga clic en **Habilitar**.

    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Haga clic en **Sí** para cerrar el cuadro de diálogo **Confirmar acción**.

    ![Confirmar acción](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmar acción")

6.  En el Portal de administración de Azure, seleccione **Concur** en la lista de aplicaciones para abrir la página del cuadro de diálogo **Concur**.

7.  Para abrir la página del cuadro de diálogo **Configurar aprovisionamiento de usuarios**, haga clic en **Configurar aprovisionamiento de usuarios**.

8.  Escriba el nombre de usuario y la contraseña del administrador de Concur y después haga clic en **Siguiente**.

9.  Para finalizar la configuración, en la página **Confirmación**, haga clic en el botón **Completar**.

Ahora ya puede crear una cuenta de prueba, espere 10 minutos y compruebe si la cuenta se ha sincronizado en Concur.
##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Concur, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Concur**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-concur-tutorial/IC769771.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-concur-tutorial/IC767830.png "Sí")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Concur.

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->