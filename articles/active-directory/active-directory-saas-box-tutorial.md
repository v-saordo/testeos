<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Box | Microsoft Azure" 
    description="Aprenda a usar Box con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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




#Tutorial: Integración de Azure Active Directory con Box


  
El objetivo de este tutorial es mostrar la integración de Azure y Box. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de prueba en Box
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Box podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Box (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Box
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-box-tutorial/IC769537.png "Escenario")



##Habilitación de la integración de aplicaciones para Box
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Box.

###Siga estos pasos para habilitar la integración de aplicaciones para Box:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-box-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-box-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-box-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Box**.

    ![Galería de aplicaciones](./media/active-directory-saas-box-tutorial/IC701023.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Box** y haga clic en **Completar** para agregar la aplicación.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Box con su cuenta de Azure AD mediante federación basada en el protocolo SAML. <br> Como parte de este procedimiento, es necesario cargar metadatos en Box.com.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Box**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769538.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Box?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769539.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inquilino de Box**, escriba la dirección URL del inquilino de Box (por ejemplo,: https://<mydomainname>.box.com) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-box-tutorial/IC669826.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Box**, para descargar sus metadatos, haga clic en **Descargar metadatos** y guarde el archivo de datos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC669824.png "Configurar inicio de sesión único")

5.  Reenvíe el archivo de metadatos al equipo de soporte técnico de Box. El equipo de soporte técnico necesita configurar un inicio de sesión único para usted.

6.  Seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769540.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Box.

###Siga estos pasos para configurar el inicio de sesión único:

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Box**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**. <br> <br> ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar el aprovisionamiento automático de usuarios")

2. En la página de diálogo **Habilitar aprovisionamiento de usuarios en Box**, haga clic en **Habilitar aprovisionamiento de usuarios**. <br><br> ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar el aprovisionamiento automático de usuarios")

3. En la página **Log in to grant access to Box**, proporcione las credenciales necesarias y haga clic en **Authorize**. <br><br> ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar el aprovisionamiento automático de usuarios")


4. Haga clic en **Grant access to Box** para autorizar esta operación y volver al Portal de administración de Azure. <br><br> ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar el aprovisionamiento automático de usuarios")

5. Para finalizar la configuración, haga clic en el botón Completar. <br><br> ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar el aprovisionamiento automático de usuarios")



##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Siga estos pasos para asignar usuarios a Box:

1. En el Portal de Azure AD, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **Box **, haga clic en **Asignar usuarios**. <br><br> ![Asignar usuarios](./media/active-directory-saas-box-tutorial/IC769552.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación. <br><br> ![Sí](./media/active-directory-saas-box-tutorial/IC767830.png "Sí")
  

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Box.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento, haciendo clic en Panel de la D en la página de integración de aplicaciones de Box en el Portal de administración de Azure.

<br><br> ![Panel](./media/active-directory-saas-box-tutorial/IC769553.png "Panel")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

<br><br> ![Estado de integración](./media/active-directory-saas-box-tutorial/IC769555.png "Estado de integración")


En su inquilino de Box, los usuarios sincronizados se muestran en **Usuarios administrados** en la **Consola de administración**.

<br><br> ![Estado de integración](./media/active-directory-saas-box-tutorial/IC769556.png "Estado de integración")


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0121_2016-->