<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con UserEcho | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UserEcho."
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
	ms.date="02/17/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con UserEcho

El objetivo de este tutorial es mostrar cómo integrar UserEcho con Azure Active Directory (Azure AD).<br>La integración de UserEcho con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a UserEcho. 
- Puede permitir que los usuarios inicien sesión automáticamente en UserEcho (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con UserEcho, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a UserEcho habilitada para inicio de sesión único


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de UserEcho desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de UserEcho desde la galería
Para configurar la integración de UserEcho en Azure AD, deberá agregar UserEcho desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar UserEcho desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **UserEcho**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)<br>
7. En el panel de resultados, seleccione **UserEcho** y luego haga clic en **Completar** para agregar la aplicación. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)<br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con UserEcho con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo en UserEcho de un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de UserEcho.<br> Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en UserEcho.
 
Para configurar y probar el inicio de sesión único de Azure AD con UserEcho, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de UserEcho](#creating-a-userecho-test-user)**: para tener un homólogo de Britta Simon en UserEcho que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación UserEcho.




**Para configurar el inicio de sesión único de Azure AD con UserEcho, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **UserEcho**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en UserEcho?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) <br>

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) <br>

    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios emplean para iniciar sesión en la aplicación UserEcho (p. ej.: **https://fabrikam.UserEcho.com/*)).

    b. Haga clic en **Siguiente**.
 
 
4. En la página **Configurar inicio de sesión único en UserEcho**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de UserEcho como administrador.

1. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Haga clic en **Integraciones**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) <br>


1. Haga clic en **Sitio web** y, después, haga clic en **Inicio de sesión único (SAML2)**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) <br>


1. Siga estos pasos en la página **Inicio de sesión único (SAML)**:<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) <br>

    a. En **Habilitado para SAML**, seleccione **Sí**.

    b. En el Portal de Azure clásico, en la página de diálogo Configurar inicio de sesión único en UserEcho, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único de SAML**.

    c. En el Portal de Azure clásico, en la página de diálogo Configurar inicio de sesión único en UserEcho, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión remoto**.

    d. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado X.509**.

    e. Haga clic en **Guardar**.


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png) <br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) <br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de UserEcho

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en UserEcho.

**Para crear una usuaria llamada Britta Simon en UserEcho, realice los pasos siguientes:**

1. Inicie sesión en el sitio de compañía de UserEcho como administrador.

1. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Haga clic en **Usuarios** para expandir la sección **Usuarios**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) <br>

1. Haga clic en **Usuarios**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) <br>

1. Haga clic en **Invitar a un nuevo usuario**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png) <br>


1. En el cuadro de diálogo **Invitar a un nuevo usuario**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta Simon**.

    b. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de Britta en el Portal de Azure clásico.

    c. Haga clic en **Invitar**.

Se envía una invitación a Britta que le permite empezar a usar UserEcho.



### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a UserEcho. <br><br>![Asignar usuario][200] <br>

**Para asignar a Britta Simon a UserEcho, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **UserEcho**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de UserEcho en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación UserEcho.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0302_2016-->