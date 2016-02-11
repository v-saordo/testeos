<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con e-Builder | Microsoft Azure" 
    description="Aprenda a usar e-Builder con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con e-Builder
  
El objetivo de este tutorial es mostrar la integración de Azure y e-Builder. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de e-Builder
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a e-Builder podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de e-Builder (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para e-Builder
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Escenario")
##Habilitación de la integración de aplicaciones para e-Builder
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para e-Builder.

###Siga estos pasos para habilitar la integración de aplicaciones para e-Builder:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **e-Builder**.

    ![Galería de aplicaciones](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **e-Builder** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse e-Builder con su cuenta de Azure AD mediante el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **e-Builder**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en e-Builder?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de e-Builder**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>.e-Builder.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en e-Builder**, para descargar los metadatos, haga clic en **Descargar metadatos** y, luego, el archivo de datos localmente como **c:\\E-BuilderMetaData.xml**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurar inicio de sesión único")

5.  Reenvíe el archivo de metadatos al equipo de soporte técnico de e-Builder. El equipo de soporte técnico necesita configurar un inicio de sesión único.

6.  Seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
No hay elemento de acción para que configure el aprovisionamiento de usuarios en e-Builder. Cuando un usuario asignado intenta iniciar sesión en e-Builder desde el panel de acceso, e-Builder comprueba si el usuario existe. Si no hay cuentas de usuario disponibles, e-Builder crea una automáticamente.
##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a e-Builder, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **e-Builder ** haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->