<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con ImageRelay | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory e ImageRelay."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="markusvi"/>


# Tutorial: Integración de Azure Active Directory con ImageRelay

El objetivo de este tutorial es mostrar cómo integrar ImageRelay con Azure Active Directory (Azure AD).<br>La integración de ImageRelay con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ImageRelay.
- Puede permitir que los usuarios inicien sesión automáticamente en ImageRelay (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con ImageRelay, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en ImageRelay


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ImageRelay desde la galería

2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de ImageRelay desde la galería
Para configurar la integración de ImageRelay en Azure AD, es preciso agregar ImageRelay desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ImageRelay desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del Portal de Azure, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Pata abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **ImageRelay**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. En el panel de resultados, seleccione **ImageRelay** y luego haga clic en **Completar** para agregar la aplicación. <br><br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con ImageRelay con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita una cuenta de usuario que represente al usuario relacionado en ImageRelay. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ImageRelay.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ImageRelay.

Para configurar y probar el inicio de sesión único de Azure AD con ImageRelay, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de ImageRelay](#creating-a-userecho-test-user)**: para tener un homólogo de Britta Simon en ImageRelay que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD y configurar el inicio de sesión único en la aplicación ImageRelay.


**Para configurar el inicio de sesión único de Azure AD con ImageRelay, realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **ImageRelay**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

     ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en ImageRelay?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

     ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación ImageRelay (por ejemplo, **https://fabrikam.ImageRelay.com/*)).

    b. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en ImageRelay**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.

5. En otra ventana del explorador, inicie sesión en su sitio de la compañía de ImageRelay como administrador.

    a. En la barra de herramientas en la parte superior, haga clic en la carga de trabajo **Users & Permissions** (Usuarios y permisos).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) <br>

    b. Haga clic en **Create New Permission** (Crear nuevo permiso).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) <br>

    c. En la carga de trabajo **Single Sign On Settings** (Configuración de inicio de sesión único), active la casilla **This Group can only sign-in via Single Sign On** (Este grupo solo puede iniciar sesión mediante inicio de sesión único) y luego haga clic en **Save** (Guardar).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. Vaya a **Account Settings** (Configuración de cuenta).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) <br>

    e. Vaya a la carga de trabajo **Single Sign On Settings** (Configuración de inicio de sesión único).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. Rellene el formulario tal y como se indica y luego haga clic en **Guardar**.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **Login URL (SSO)** (Dirección URL de inicio de sesión único (SSO)): es la dirección URL del servicio de inicio de sesión único de Azure Active Directory.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **Logout Service URL** (Dirección URL del servicio de cierre de sesión): es el servicio de cierre de sesión único de Azure Active Directory.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - En **Name Id Format** (Formato de id. de nombre), seleccione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - En **Binding Options for Requests from the Service Provider (Image Relay)** (Opciones de enlace para solicitudes del proveedor de servicio (ImageRelay)), seleccione **POST Binding** (Enlace POST).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>

  	- En **x.509 Certificate** (Certificado x.509), haga clic en **Update Certificate** (Actualizar certificado).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - En el Bloc de notas, abra el certificado que descargó desde Azure Active Directory en el paso 4 y luego copie y pegue el contenido del certificado aquí.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - En **Just-In-Time User Provisioning** (Aprovisionamiento de usuario Just-In-Time), active la casilla **Enable Just-In-Time User Provisioning** (Habilitar el aprovisionamiento de usuario Just-In-Time).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - Seleccione el grupo de permisos, por ejemplo, **SSO Basic** (SSO básico), que podrá iniciar sesión solo mediante inicio de sesión único.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.

    ![Inicio de sesión único de Azure AD][11]


### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) <br>

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **crear**.<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de ImageRelay

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en ImageRelay.

**Para crear un usuario llamado Britta Simon en ImageRelay, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de ImageRelay como administrador.

1. Vaya a **Users & Permissions** (Usuarios y permisos) y seleccione **Create SSO User** (Crear usuario de SSO).<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. Escriba la **dirección de correo electrónico**, el **nombre**, los **apellidos** y la **compañía** del usuario que quiere aprovisionar y seleccione el grupo de permisos (por ejemplo, SSO Basic (SSO básico)), que es el grupo que solo puede iniciar sesión mediante inicio de sesión único.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. Haga clic en **Crear**.

### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a ImageRelay. <br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a ImageRelay, realice los pasos siguientes:**

1. En el Portal de Azure, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **ImageRelay**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]


### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de ImageRelay en el panel de acceso, debería iniciar sesión automáticamente en su aplicación ImageRelay.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO4-->