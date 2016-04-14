<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con QuickHelp | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y QuickHelp."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con QuickHelp

El objetivo de este tutorial es mostrar cómo integrar QuickHelp con Azure Active Directory (Azure AD).<br>La integración de QuickHelp con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a QuickHelp. 
- Puede permitir que los usuarios inicien sesión automáticamente en QuickHelp (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con QuickHelp, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en QuickHelp


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar QuickHelp desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Agregar QuickHelp desde la galería
Para configurar la integración de QuickHelp en Azure AD, deberá agregar QuickHelp desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar QuickHelp desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escribs **QuickHelp**.<br><br> ![Aplicaciones][5]<br>
7. En el panel de resultados, seleccione **QuickHelp** y luego haga clic en **Completar** para agregar la aplicación. <br><br>![Aplicaciones][500]<br>


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con QuickHelp con un usuario de prueba llamado "Britta Simon".


Para configurar y probar el inicio de sesión único de Azure AD con QuickHelp, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de QuickHelp](#creating-a-quickhelp-test-user)**: para tener un homólogo de Britta Simon en QuickHelp que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el portal de Azure AD y configurar el inicio de sesión único en la aplicación QuickHelp.<br>

**Para configurar el inicio de sesión único de Azure AD con QuickHelp, realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **QuickHelp**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en QuickHelp?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**. <br><br> ![Inicio de sesión único de Azure AD][7] <br>

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar las opciones de la aplicación][8] <br>
 
     a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de QuickHelp (por ejemplo, *https://quickhelp.com/bsiazure/*)).

     > [AZURE.NOTE] Póngase en contacto con el equipo de soporte de QuickHelp si no conoce el valor de la URL de inicio de sesión.

     b. Haga clic en **Siguiente**.

 
4. En la página **Configurar inicio de sesión único en QuickHelp**, realice los siguientes pasos: haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos localmente en el equipo. <br><br>![Qué es Azure AD Connect][9] <br>



1. Inicie sesión en el sitio de la empresa de QuickHelp como administrador.

2. En el menú de la parte superior, haga clic en **Admin** (Administrador). <br><br>![Configurar inicio de sesión único][21]<br>


1. En el menú **Administrador de QuickHelp**, haga clic en **Configuración**. <br><br>![Configurar inicio de sesión único][22]<br>

1. Haga clic en **Configuración de autenticación**.

1. En la página **Configuración de autenticación**, realice los siguientes pasos. <br><br>![Configurar inicio de sesión único][23]<br>

    a. En **Tipo de SSO**, seleccione **WSFederation**.

    b. Para cargar el archivo de metadatos de Azure descargado, haga clic en **Examinar**, navegue hasta el archivo y luego haga clic en **Cargar metadatos**.

    c. En el cuadro de texto **Correo electrónico**, escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. En el cuadro de texto **First Name** (Nombre), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. En el cuadro de texto **Last Name** (Apellido), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. En la **barra de acción**, haga clic en **Guardar**.







6. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Qué es Azure AD Connect][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Qué es Azure AD Connect][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)<br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)<br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) <br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de QuickHelp

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en QuickHelp. Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de QuickHelp para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de QuickHelp.

QuickHelp admite el aprovisionamiento Just-In-Time. Esto significa que, si fuera necesario, se creará automáticamente una cuenta de usuario en QuickHelp y la cuenta se vinculará a la cuenta de Azure AD.

No hay ningún elemento de acción para usted en esta sección.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a QuickHelp. <br><br>![Asignar usuario][200] <br>

**Para asignar a Britta Simon a QuickHelp, realice los pasos siguientes:**

1. En el Portal de Azure, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **QuickHelp**. <br><br>![Asignar usuario][202]<br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de QuickHelp en el panel de acceso, debería iniciar sesión automáticamente en la aplicación.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=AcomDC_0302_2016-->