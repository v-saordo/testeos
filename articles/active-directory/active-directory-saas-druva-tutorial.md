<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Druva | Microsoft Azure" 
    description="Aprenda a usar Druva con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con Druva

El objetivo de este tutorial es mostrar la integración de Azure y Druva. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Druva

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Druva podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Druva (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Druva
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Escenario")
##Habilitación de la integración de aplicaciones para Druva

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Druva.

###Siga estos pasos para habilitar la integración de aplicaciones para Druva:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-druva-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-druva-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-druva-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Druva**.

    ![Galería de aplicaciones](./media/active-directory-saas-druva-tutorial/IC795085.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Druva** y luego haga clic en **Completar** para agregar la aplicación.

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Druva con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

La aplicación Druva espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla muestra un ejemplo:

![Atributos de token de SAML](./media/active-directory-saas-druva-tutorial/IC795087.png "Atributos de token de SAML")

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure Active Directory, en la página de integración de aplicaciones de **Druva**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795027.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Druva?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795088.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Druva**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación Druva (por ejemplo: "**https://cloud.druva.com/home/*”)) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-druva-tutorial/IC795089.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Druva**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795090.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Druva como administrador.

6.  Vaya a **Administrar > Configuración**.

    ![Settings](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")

7.  En el cuadro de diálogo Single Sign-On Settings (Configuración de inicio de sesión único), siga estos pasos:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795092.png "Configuración de inicio de sesión único")

    1.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Druva**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de proveedor de Id.**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Druva**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de proveedor de Id.**.
    3.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de Id.**.
    5.  Para abrir la página **Configuración**, haga clic en **Guardar**.

8.  En la página **Configuración**, haga clic en**Generar token de SSO**.

    ![Settings](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")

9.  En el cuadro de diálogo **Token de autenticación de inicio de sesión único**, siga estos pasos:

    ![Token de SSO](./media/active-directory-saas-druva-tutorial/IC795094.png "Token de SSO")

    1.  Haga clic en **Copiar**.
    2.  Haga clic en **Cerrar**.

10. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795095.png "Configurar inicio de sesión único")

11. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-druva-tutorial/IC795096.png "Atributos")

12. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

	|Nombre del atributo|Valor de atributo|
    |---|---|
    |insync\_auth\_token|<*valor Portapapeles*>|

    1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para esa fila.
    3.  En el cuadro de texto **Valor de atributo**, escriba el valor de atributo que se muestra para la fila.
    4.  Haga clic en **Completo**.

13. Haga clic en **Aplicar cambios**.
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Druva, deben aprovisionarse en Druva. En el caso de Druva, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en el sitio de la compañía de **Druva** como administrador.

2.  Vaya a **Administrar > Usuarios**.

    ![Administrar usuarios](./media/active-directory-saas-druva-tutorial/IC795097.png "Administrar usuarios")

3.  Haga clic en **Create New**.

    ![Administrar usuarios](./media/active-directory-saas-druva-tutorial/IC795098.png "Administrar usuarios")

4.  En el cuadro de diálogo Create New User (Crear nuevo usuario), realice los pasos siguientes:

    ![Crear usuario nuevo](./media/active-directory-saas-druva-tutorial/IC795099.png "Crear usuario nuevo")

    1.  Escriba la dirección de correo electrónico y el nombre de la cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Crear usuario**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Druva que proporcione Druva para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Druva, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Druva **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-druva-tutorial/IC795100.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-druva-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->