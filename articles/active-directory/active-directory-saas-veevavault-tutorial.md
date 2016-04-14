<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Veeva Vault | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Veeva Vault."
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
	ms.date="02/22/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con Veeva Vault

El objetivo de este tutorial es mostrar cómo integrar Veeva Vault con Azure Active Directory (Azure AD).<br>La integración de Veeva Vault con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Veeva Vault.
- Puede permitir que los usuarios inicien sesión automáticamente en Veeva Vault (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.


Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Veeva Vault, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Veeva Vault


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Veeva Vault desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Veeva Vault desde la galería
Para configurar la integración de Veeva Vault en Azure AD, es preciso agregar Veeva Vault desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Veeva Vault desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **Veeva Vault**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_01.png)<br>
7. En el panel de resultados, seleccione **Veeva Vault** y luego haga clic en **Completar** para agregar la aplicación. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_02.png)<br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Veeva Vault con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Veeva Vault para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Veeva Vault.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Veeva Vault.

Para configurar y probar el inicio de sesión único de Azure AD con Veeva Vault, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Veeva Vault](#creating-a-veeva-vault-test-user)**: para tener un homólogo de Britta Simon en Veeva Vault que esté vinculado a la representación de esta en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Veeva Vault.



**Para configurar el inicio de sesión único de Azure AD con Veeva Vault, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Veeva Vault**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en Veeva Vault?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_04.png) <br>


    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL de respuesta para su aplicación Veeva Vault con el siguiente patrón: **"https://login.veevavault.com/auth/saml/consumer/<IdInicioSesiónÚnicoInquilino>"**.

    b. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en Veeva Vault**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte técnico de Veeva Vault y envíe tanto el archivo de certificado descargado adjunto como el inicio de sesión remoto y la dirección URL de cierre de sesión por correo electrónico para que puedan configurarse para la integración con el inicio de sesión único.


	> [AZURE.NOTE] Mencione al equipo de Veeva que tiene que configurar este inicio de sesión único en el **modo iniciado por proveedor de identidades** con el identificador de entidad como ****https://login.url.com/vault_azure**. Esta configuración es importante para que el inicio de sesión único funcione.


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Inicio de sesión único de Azure AD][11]



### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_07.png) <br>

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veevavault-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de Veeva Vault

El objetivo de esta sección es crear una usuaria llamada Britta Simon en Veeva Vault. Trabaje con el equipo de soporte técnico de Veeva Vault para agregar usuarios a la aplicación de Veeva Vault.


> [AZURE.NOTE] Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el equipo de soporte técnico de Veeva Vault.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a Veeva Vault. <br><br>![Asignar usuario][200] <br>

**Para asignar la usuaria Britta Simon a Veeva Vault, siga estos pasos:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **Veeva Vault**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-veevavault-tutorial/tutorial_veevavault_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de Veeva Vault en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Veeva Vault.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-veevavault-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0224_2016-->