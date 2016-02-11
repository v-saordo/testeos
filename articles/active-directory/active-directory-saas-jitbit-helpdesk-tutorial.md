<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk | Microsoft Azure" 
    description="Aprenda a usar Jitbit Helpdesk con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk
  
El objetivo de este tutorial es mostrar la integración de Azure y Jitbit Helpdesk. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Jitbit Helpdesk
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Jitbit Helpdesk podrá realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Jitbit Helpdesk (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Jitbit Helpdesk
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Escenario")
##Habilitación de la integración de aplicaciones para Jitbit Helpdesk
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Jitbit Helpdesk.

###Siga estos pasos para habilitar la integración de aplicaciones para Jitbit Helpdesk:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Jitbit Helpdesk**.

    ![Galería de aplicaciones](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Jitbit Helpdesk** y luego haga clic en **Completar** para agregar la aplicación.

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Jitbit Helpdesk con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Para poder configurar el inicio de sesión único en el inquilino de Jitbit Helpdesk, deberá ponerse en contacto primero con el soporte técnico de Jitbit Helpdesk para habilitar esta característica.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Jitbit Helpdesk**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Jitbit Helpdesk?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Jitbit Helpdesk**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>.Jitbit.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Jitbit Helpdesk**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Jitbit Helpdesk.

6.  En la barra de herramientas de la parte superior, haga clic en **Administración**.

    ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administración")

7.  Haga clic en **Configuración general**.

    ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuarios, compañías y permisos")

8.  En la sección **Configuración de autenticación**, realice estos pasos:

    ![Configuración de autenticación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Configuración de autenticación")

    1.  Seleccione **Habilitar inicio de sesión único SAML 2.0** para iniciar sesión mediante inicio de sesión único (SSO) con **OneLogin**.
    2.  En el Portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Jitbit Helpdesk**, copie el valor de **Punto de conexión iniciado por el proveedor de servicios** y luego péguelo en el cuadro de texto **Dirección URL del punto de conexión**.
    3.  Cree un archivo **codificado en base 64** a partir del certificado descargado.
        
		>[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    4.  Abra el certificado codificado en base 64, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
    5.  Haga clic en **Guardar cambios**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Jitbit Helpdesk, deben aprovisionarse en Jitbit Helpdesk. En el caso de Jitbit Helpdesk, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en su inquilino de **Jitbit Helpdesk**.

2.  En el menú de la parte superior, haga clic en **Administración**.

    ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administración")

3.  Haga clic en **Usuarios, compañías y permisos**.

    ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuarios, compañías y permisos")

4.  Haga clic en **Agregar usuario**.

    ![Agregar usuario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Agregar usuario")

5.  En la sección Crear, escriba los datos de la cuenta de Azure AD que desee aprovisionar en los cuadros de texto siguientes: **Nombre de usuario**, **Correo electrónico**, **Nombre**, **Apellido**

    ![Crear](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Crear")

6.  Haga clic en **Crear**.

>[AZURE.NOTE]Puede usar cualquier herramienta de creación de cuentas de usuario de Jitbit Helpdesk u otras API proporcionadas por Jitbit Helpdesk para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Jitbit Helpdesk, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Jitbit Helpdesk**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->