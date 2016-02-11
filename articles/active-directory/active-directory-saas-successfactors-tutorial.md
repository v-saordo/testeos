<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con SuccessFactors | Microsoft Azure"
    description="Aprenda cómo usar SuccessFactors con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: integración de Azure Active Directory con SuccessFactors
  
El objetivo de este tutorial es mostrar la integración de Azure y SuccessFactors en **Modo de inicio de sesión único iniciado por el SP**.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada con inicio de sesión único de SuccessFactors en el modo iniciado por el SP
  
Después de completar este tutorial, los usuarios de Azure AD que asignó a SuccessFactors podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SuccessFactors (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para SuccessFactors
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Escenario")

##Habilitación de la integración de aplicaciones para SuccessFactors
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SuccessFactors.

###Siga estos pasos para habilitar la integración de aplicaciones para SuccessFactors:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **SuccessFactors**.

    ![Galería de aplicaciones](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **SuccessFactors** y luego haga clic en **Completar** para agregar la aplicación.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SuccessFactors con su cuenta de Azure AD usando el protocolo SAML basado en la federación.
  
Para configurar el inicio de sesión único, debe ponerse en contacto con el equipo de soporte técnico de SuccessFactors.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **SuccessFactors**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en SuccessFactors?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **URL de inicio de sesión de SuccessFactors**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de SuccessFactors (por ejemplo, "*https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*").
    2.  En el cuadro de texto **Dirección URL de respuesta de SuccessFactors**, escriba **https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**.

        >[AZURE.NOTE]Este valor solo es un marcador de posición temporal.  
        >Obtenga el valor real de su equipo de soporte técnico de SuccessFactors.  
        >Más adelante en este tutorial, encontrará instrucciones para ponerse en contacto con su equipo de soporte de SuccessFactors.  
        >En el contexto de esta conversación, recibirá su URL de respuesta real de SuccessFactors.

4.  En la página **Configuración de inicio de sesión único en SuccessFactors**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configurar inicio de sesión único")

5.  Para que se configure el inicio de sesión único basado en SAML, póngase en contacto con el equipo de soporte de SuccessFactors y ofrézcales los siguientes elementos:

    1.  El certificado descargado
    2.  La dirección URL de inicio de sesión remoto
    3.  La dirección URL de cierre de sesión remoto

    >[AZURE.IMPORTANT]Pida a su equipo de soporte de SuccessFactors que establezca el parámetro de formato NameId en "*Sin especificar*".

    El equipo de soporte de Successfactors le enviará la **URL de respuesta de Successfactors** correcta que necesita para el cuadro de diálogo **Configurar URL de aplicación**.

6.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en SuccessFactors, deben aprovisionarse en SuccessFactors.  
En el caso de SuccessFactors, el aprovisionamiento es una tarea manual.
  
Para que se creen usuarios creados en SuccessFactors, deberá ponerse en contacto con el equipo de soporte técnico de SuccessFactors.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a SuccessFactors, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **SuccessFactors**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!----HONumber=AcomDC_0114_2016-->
