<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Flatter Files | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Flatter Files."
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con Flatter Files

El objetivo de este tutorial es mostrar cómo integrar Flatter Files con Azure Active Directory (Azure AD).<br>La integración de Flatter Files con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Flatter Files. 
- Puede permitir que los usuarios inicien sesión automáticamente en Flatter Files (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Flatter Files, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Flatter Files


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Flatter Files desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Flatter Files desde la galería
Para configurar la integración de Flatter Files en Azure AD, deberá agregar Flatter Files desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Flatter Files desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **Flatter Files**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)<br>
7. En el panel de resultados, seleccione **Flatter Files** y luego, haga clic en **Completar** para agregar la aplicación<br><br>.![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)<br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Flatter Files con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Flatter Files para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Flatter Files.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Flatter Files.
 
Para configurar y probar el inicio de sesión único de Azure AD con Flatter Files, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Flatter Files](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en CS Stars que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación Flatter Files. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar el inicio de sesión único para Flatter Files, se necesita un dominio registrado. Si no dispone de un dominio registrado, póngase en contacto con el equipo de soporte técnico de Flatter Files a través de [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Para configurar el inicio de sesión único de Azure AD con Flatter Files, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **Flatter Files**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en Flatter Files?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.<br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) <br>

3. En la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **Siguiente**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) <br>

    > [AZURE.NOTE] Flatter Files usa la dirección URL de inicio de sesión SSO para todos los clientes: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
 
 
4. En la página **Configurar inicio de sesión único en Flatter Files**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


1. Inicie sesión en su aplicación de Flatter Files como administrador.

2. Haga clic en Panel.<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png) <br>



2. Click **Configuración** y después ejecute los pasos siguientes en la pestaña **Compañía**: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png) <br>

    a. Seleccione **Usar SAML 2.0 para autenticación**.

    b. Haga clic en **Configurar SAML**.



2. En el cuadro de diálogo **Configuración de SAML**, realice los siguientes pasos:<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png) <br>

    a. En el cuadro de texto Dominio, escriba el dominio registrado.

    > [AZURE.NOTE] Si no dispone de un dominio registrado, póngase en contacto con el equipo de soporte técnico de Flatter Files a través de [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. En el Portal de Azure clásico, en el cuadro de diálogo Configurar inicio de sesión único en Flatter Files, copie el valor de la Dirección URL del servicio de inicio de sesión único y péguelo en el cuadro de texto Identity Provider URL (URL de proveedor de identidades).

    c. Cree un archivo **codificado en base 64** a partir del certificado descargado.

    >[AZURE.TIP] Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    d. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado de proveedor de identidades de Flatter Files**.

    e. Haga clic en **Actualizar**.

6. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completa**. <br><br>![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) <br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) <br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de Flatter Files

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Flatter Files.

**Para crear una usuaria llamada Britta Simon en Flatter Files, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de **Flatter Files** como administrador.

2. En el panel de navegación izquierdo, haga clic en **Configuración** y luego en la pestaña **Usuarios**. <br><br>![Creación de un usuario de Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)<br>

3. Haga clic en **Agregar usuario**.

4. En el cuadro de diálogo **Agregar usuario**, realice los pasos siguientes: <br><br>![Creación de un usuario de Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)<br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de Britta en el Portal de Azure clásico.

    c. Haga clic en **Enviar**.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Flatter Files. <br><br>![Asignar usuario][200] <br>

**Para asignar a Britta Simon a Flatter Files, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>
2. En la lista de aplicaciones, seleccione **Flatter Files**. <br><br>![Asignar usuario](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) <br>
1. En el menú de la parte superior, haga clic en **Usuarios**.<br> <br>![Asignar usuario][203]<br>
1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de Flatter Files en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Flatter Files.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0128_2016-->