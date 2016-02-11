<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Clever | Microsoft Azure" 
    description="Aprenda a usar Clever con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con Clever

El objetivo de este tutorial es mostrar la integración de Azure y Clever. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Clever

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Clever podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Clever (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Clever
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-clever-tutorial/IC798977.png "Escenario")
##Habilitación de la integración de aplicaciones para Clever

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Clever.

###Siga estos pasos para habilitar la integración de aplicaciones para Clever:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-clever-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-clever-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-clever-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Clever**.

    ![Galería de aplicaciones](./media/active-directory-saas-clever-tutorial/IC798978.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Clever** y luego haga clic en **Completar** para agregar la aplicación.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Clever con su cuenta de Azure AD mediante federación basada en el protocolo SAML. La aplicación Clever espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla muestra un ejemplo:

![Atributos](./media/active-directory-saas-clever-tutorial/IC798980.png "Atributos")

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Clever**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC784682.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Clever?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798981.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Clever**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación Clever (por ejemplo: **https://clever.com/in/azsandbox*)) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-clever-tutorial/IC798982.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Clever**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798983.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Clever como administrador.

6.  En la barra de herramientas, haga clic en **Inicio de sesión instantáneo**.

    ![Inicio de sesión instantáneo](./media/active-directory-saas-clever-tutorial/IC798984.png "Inicio de sesión instantáneo")

7.  En la página **Instant Login** (Inicio de sesión instantáneo), realice los pasos siguientes:

    ![Inicio de sesión instantáneo](./media/active-directory-saas-clever-tutorial/IC798985.png "Inicio de sesión instantáneo")

    1.  Escriba la **Dirección URL de inicio de sesión**.  

        >[AZURE.NOTE]La **URL de inicio de sesión** es un valor personalizado. El valor real puede obtenerlo del equipo de soporte técnico de Clever.

    2.  En **Sistema de identidades**, seleccione **ADFS**.
    3.  Haga clic en **Guardar**.

8.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798986.png "Configurar inicio de sesión único")

9.  En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-clever-tutorial/IC795920.png "Atributos")

10. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ![atributos de token de saml](./media/active-directory-saas-clever-tutorial/IC795921.png "atributos de token de saml")

	|Nombre del atributo|Valor de atributo|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para la fila.
    3.  En el cuadro de texto **Valor de atributo**, seleccione el valor de atributo que se muestra para la fila.
    4.  Haga clic en **Completo**.

11. Haga clic en **Aplicar cambios**.

##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Clever, deben aprovisionarse en Clever. En el caso de Clever, el aprovisionamiento es una tarea manual que debe realizar el equipo de soporte técnico de Clever.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Clever ofrecida por Clever para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Clever, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Clever **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-clever-tutorial/IC798987.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-clever-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->