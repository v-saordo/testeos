<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con SD Elements | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SD Elements."
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
	ms.date="12/21/2015"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con SD Elements

El objetivo de este tutorial es mostrar cómo integrar SD Elements con Azure Active Directory (Azure AD).<br>La integración de SD Elements con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SD Elements.
- Puede permitir que los usuarios inicien sesión automáticamente en SD Elements (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, Azure Active Directory. 


Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con SD Elements, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SD Elements


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de SD Elements desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de SD Elements desde la galería
Para configurar la integración de SD Elements en Azure AD, deberá agregar SD Elements desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SD Elements desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorio.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro Buscar, escriba **SD Elements**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)<br>
7. En el panel de resultados, seleccione **SD Elements** y luego haga clic en **Completa** para agregar la aplicación. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)<br>


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con SD Elements con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SD Elements para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SD Elements.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SD Elements.

Para configurar y probar el inicio de sesión único de Azure AD con SD Elements, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de SD Elements](#creating-a-sd-elements-test-user)**: para tener un homólogo de Britta Simon en SD Elements que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación SD Elements.

La aplicación SD Elements espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo:

![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) <br>



**Para configurar el inicio de sesión único de Azure AD con SD Elements, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SD Elements**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en SD Elements?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) <br>


    a. En el cuadro de texto **Emisor**, escriba la dirección URL del emisor del inquilino con el siguiente patrón: *https://\<your tenant name>.sdelements.com/sso/saml2/metadata*
   
    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL de respuesta del inquilino con el siguiente patrón: *https://\<your tenant name>.sdelements.com/sso/saml2/acs/*

    > [AZURE.NOTE]Si necesita la dirección URL del emisor y la dirección URL de respuesta reales del inquilino, póngase en contacto con su [equipo de soporte técnico de SD Elements](mailto:support@sdelements.com).
      
    c. Haga clic en **Siguiente**.


4. En la página **Configurar inicio de sesión único en SD Elements**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


1. Para que le habiliten el inicio de sesión único, póngase en contacto con su [equipo de soporte técnico de SD Elements](mailto:support@sdelements.com) y proporcióneles el archivo de certificado descargado.


5. En una ventana de explorador diferente, inicie sesión en su inquilino de SD Elements como administrador.

6. En el menú de la parte superior, haga clic en Sistema y luego en Inicio de sesión único. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) <br>


7. En el cuadro de diálogo **Configuración de inicio de sesión único**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) <br>

    a. Como **Tipo de inicio de sesión único**, seleccione **SAML**.

    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SD Elements**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Id. de entidad del proveedor de identidades**.

    c. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SD Elements**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Servicio de inicio de sesión único del proveedor de identidades**.

    d. Haga clic en **Guardar**.

6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completa**. <br><br>![Inicio de sesión único de Azure AD][11]

1. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**. <br><br>![Configurar inicio de sesión único][21]<br>


2. Para cada fila de la siguiente tabla, realice los pasos siguientes:

    | Nombre del atributo | Valor de atributo |
    | ---            | ---             |
    | email | user.mail |
    | firstname | user.givenname |
    | lastname | user.surname |


    a. Haga clic en **Agregar atributo de usuario**. <br><br>![Configurar inicio de sesión único][23]<br>

    b. En el cuadro de texto **Nombre de atributo**, escriba el valor de **Nombre de atributo** y, en **Valor de atributo**, seleccione el valor del atributo que se muestra para esa fila. <br><br>![Configurar inicio de sesión único][22]<br>

    c. Haga clic en **Agregar atributo de usuario**. <br><br>![Configurar inicio de sesión único][23]<br>

1. Haga clic en **Aplicar cambios**. <br><br>![Configurar inicio de sesión único][24]<br>

### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) <br>

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) <br>

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de SD Elements

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en SD Elements. En el caso de SD Elements, la creación de usuarios de SD Elements es una tarea manual.

**Para crear a Britta Simon en SD Elements, realice los pasos siguientes:**

1.	En una ventana de explorador web, inicie sesión como administrador en el sitio de la empresa de SD Elements.

2.	En el menú de la parte superior, haga clic en User Management (Administración de usuarios) y luego en Users (Usuarios).
 
    ![Creación de un usuario de prueba de SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) <br>

3.	Haga clic en Add New User (Agregar nuevo usuario).
 
    ![Creación de un usuario de prueba de SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) <br>

4.	En el cuadro de diálogo Add New User (Agregar nuevo usuario), realice los pasos siguientes:

    ![Creación de un usuario de prueba de SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) <br>

    a. En el cuadro de texto **E-mail** (Correo electrónico), escriba la dirección de correo electrónico de Britta en Azure AD.

    b. En el cuadro de texto **First Name** (Nombre), escriba **Britta**.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba **Simon**.

    d. Como **Role** (Rol), seleccione **User** (Usuario).

    e. Haga clic en **Create User** (Crear usuario).




### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a SD Elements. <br><br>![Asignar usuario][200] <br>

**Para asignar a Britta Simon a SD Elements, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **SD Elements**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista **Usuarios**, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de SD Elements en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación SD Elements.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->