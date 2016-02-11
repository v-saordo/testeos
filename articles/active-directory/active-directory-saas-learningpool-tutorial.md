<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Learningpool | Microsoft Azure" 
    description="Aprenda a usar Learningpool con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Learningpool
  
El objetivo de este tutorial es mostrar la integración de Azure y Learningpool. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Learningpool
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Learningpool podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Learningpool (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Learningpool
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Escenario")
##Habilitación de la integración de aplicaciones para Learningpool
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Learningpool.

###Siga estos pasos para habilitar la integración de aplicaciones para Learningpool:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Learningpool**.

    ![Galería de aplicaciones](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Learningpool** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Learningpool con su cuenta de Azure AD mediante federación basada en el protocolo SAML.
  
La aplicación Learningpool espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla muestra un ejemplo.

![Atributos de token de SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "Atributos de token de SAML")

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Learningpool**, en el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Atributos")

2.  Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ###

    |Nombre del atributo |Valor de atributo |
	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
	|-------------------------------|--------------------------|  
	 urn:oid:2.5.4.42|User.givenname   
    |urn:oid:0.9.2342.19200300.100.1.3|User.mail
    |urn:oid:2.5.4.4|User.surname

    1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para la fila.
    3.  En la lista **Valor de atributo**, seleccione el valor de atributo que se muestra para esa fila.
    4.  Haga clic en **Completo**.

3.  Haga clic en **Aplicar cambios**.

4.  En el explorador, haga clic en **Atrás** para volver a abrir el cuadro de diálogo **Inicio rápido**.

5.  Para abrir el diálogo **Configurar inicio de sesión único**, haga clic en **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurar inicio de sesión único")

6.  En la página **¿Cómo desea que los usuarios inicien sesión en Learningpool?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurar inicio de sesión único")

7.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Learningpool**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en la aplicación Learningpool (por ejemplo: "*https://parliament.preview.learningpool.com/auth/shibboleth/index.php)) y, luego, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurar dirección URL de la aplicación")

8.  En la página **Configurar inicio de sesión único en Learningpool**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurar inicio de sesión único")

9.  Reenvíe el archivo de metadatos al equipo de soporte técnico de Learningpool.

    >[AZURE.NOTE]El equipo de soporte técnico de Learningpool es el que debe habilitar el inicio de sesión único.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Learningpool, deben aprovisionarse en Learningpool.
  
No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Learningpool. Los usuarios deben ser creados por el equipo de soporte de Learningpool.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Learningpool ofrecida por Learningpool para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Learningpool, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Learningpool**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->