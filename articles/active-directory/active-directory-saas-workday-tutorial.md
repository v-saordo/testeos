<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Workday | Microsoft Azure" 
    description="Aprenda cómo usar Workday con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Workday
  
El objetivo de este tutorial es mostrar la integración de Azure y Workday. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en Workday
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Workday
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Configuración del aprovisionamiento de usuario

![Escenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Escenario")

##Habilitación de la integración de aplicaciones para Workday
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Salesforce.

###Siga estos pasos para habilitar la integración de aplicaciones para Workday:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y luego en **Agregar una aplicación que mi organización use**.

    ![¿Qué desea hacer?](./media/active-directory-saas-workday-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba **Workday**.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  En el panel de resultados, seleccione **Workday** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Workday con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario crear un certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En la página de integración de aplicaciones de **Workday**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Workday?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan sus usuarios para iniciar sesión en Workday (por ejemplo, *https://impl.workday.com/\< inquilino>/login-saml2.htmld*).
    2.  En el cuadro de texto **Dirección URL de respuesta de Workday**, escriba la dirección URL de respuesta de Workday (por ejemplo, *https://impl.workday.com/\<inquilino>/login-saml.htmld*).

        >[AZURE.NOTE]Su URL de respuesta debe tener un subdominio (por ejemplo, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Usando algo como "**http://www.myworkday.com*" funciona pero no "**http://myworkday.com*".
 
4.  En la página **Configuración de inicio de sesión único en Workday**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Workday como administrador.

6.  Vaya a **Menú > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Vaya a **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-workday-tutorial/IC782924.png "Administración de cuentas")

8.  Vaya a **Editar configuración de inquilino: Seguridad**.

    ![Editar seguridad del inquilino](./media/active-directory-saas-workday-tutorial/IC782925.png "Editar seguridad del inquilino")

9.  En la sección **URL de redireccionamiento**, siga estos pasos:

    ![Direcciones URL de redirección](./media/active-directory-saas-workday-tutorial/IC7829581.png "Direcciones URL de redirección")

     9\.1. Haga clic en **Add Row** (Agregar fila).

     9\.2. En los cuadros de texto **Login Redirect URL** (Dirección URL de redireccionamiento de inicio de sesión) y **Mobile Redirect URL** (URL de redireccionamiento móvil), escriba la **Workday Tenant URL** (URL del inquilino de Workday) que ha escrito en la página **Configurar dirección URL de la aplicación** del portal de Azure.
    
     9\.3. En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.

     9\.4. En el cuadro de texto **Entorno**, escriba el nombre del entorno.


       >[AZURE.NOTE]El valor del atributo Environment (Entorno) está vinculado con el valor de la URL del inquilino: > >- Si el nombre de dominio de la URL de inquilino de Workday empieza por impl (por ejemplo, *https://impl.workday.com/\<inquilino>/login-saml2.htmld*), el atributo **Environment** (Entorno) debe establecerse en Implementación. >- Si el nombre de dominio empieza con algo más, deberá ponerse en contacto con Workday para obtener el valor de **Environment** (Entorno) coincidente.

10. En la sección **SAML Setup** (Configuración de SAML), realice los pasos siguientes:

    ![Instalación de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Instalación de SAML")

     10\.1. Seleccione **Enable SAML Authentication** (Habilitar autenticación SAML).

     10\.2. Haga clic en **Add Row** (Agregar fila).

11. En la sección Proveedores de identidades SAML, realice los pasos siguientes:

    ![Proveedores de identidades SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Proveedores de identidades SAML")

     11\.1. En el cuadro de texto Identity Provider Name (Nombre del proveedor de identidades), escriba un nombre de proveedor (por ejemplo, *SPInitiatedSSO*).

     11\.2. En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie el valor del **Id. del proveedor de identidades** y péguelo en el cuadro de texto **Emisor**.

     11\.3. Seleccione **Habilitar el cierre de sesión iniciado de Workday**.

     11\.4. En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie **Dirección URL del servicio de cierre de sesión único** y luego péguelo en el cuadro de texto **Dirección URL de solicitud de cierre de sesión**.


     11\.3. Haga clic en **Certificado de clave pública de proveedor de identidades** y luego haga clic en **Crear**. <br><br> ![Crear](./media/active-directory-saas-workday-tutorial/IC782928.png "Crear")

     11\.4. Haga clic en **Crear clave pública x509**. <br><br> ![Crear](./media/active-directory-saas-workday-tutorial/IC782929.png "Crear")

     11\.5. En la sección **Ver clave pública x509**, realice los pasos siguientes: <br><br> ![Ver clave pública x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Ver clave pública x509") <br>

      1.  En el cuadro de texto **Nombre**, escriba el nombre del certificado (por ejemplo, *PPE\_SP*).
      2.  En el cuadro de texto **Válido desde**, escriba el valor del atributo Válido desde del certificado.
      3.  En el cuadro de texto **Válido hasta**, escriba el valor del atributo Válido hasta del certificado.
		
           >[AZURE.NOTE]Puede obtener la fecha válido desde y la fecha válido hasta del certificado descargado haciendo doble clic en él. Las fechas se muestran bajo la pestaña **Detalles**.

      4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.

		>[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

      5.  Abra el certificado codificado en base 64 en el Bloc de notas y luego copie el contenido del mismo.
      6.  En el cuadro de texto **Certificado**, pegue el contenido del portapapeles.
      7.  Haga clic en **Aceptar**.

12.  Lleve a cabo los siguiente pasos: <br><br> ![Configuración de SSO](./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuración de SSO")

     12\.1. Habilite el **par de claves privadas x509**.

     12\.2. En el cuadro de texto **Id. de proveedor de servicios**, escriba ****http://www.workday.com**.

     12\.3. Seleccione **Habilitar autenticación SAML iniciada por el proveedor de servicios**.

     12\.4. En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie el valor de la **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL del servicio SSO de IdP**.
     
     12\.5 Seleccione **No desinflar la solicitud de autenticación iniciada por el SP**.

     12\.6. Como **Método de firma de solicitud de autenticación**, seleccione**SHA256**. <br><br> ![Método de firmas de solicitudes de autenticación](./media/active-directory-saas-workday-tutorial/IC782932.png "Método de firmas de solicitudes de autenticación") <br><br>
 
     12\.7 Haga clic en **Aceptar**. <br><br> ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. En el portal de Azure AD, en la página **Configurar inicio de sesión único en Workday**, haga clic en **Siguiente**. <br><br>

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar inicio de sesión único")

13. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurar inicio de sesión único")



##Configuración del aprovisionamiento de usuario
  
Para obtener un usuario de prueba aprovisionado en Workday, deberá ponerse en contacto con el equipo de soporte técnico de Workday. El equipo de soporte técnico de Workday creará el usuario por usted.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Workday, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Workday **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-workday-tutorial/IC782935.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-workday-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->