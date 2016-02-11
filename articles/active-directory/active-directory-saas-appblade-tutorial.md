<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con AppBlade | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AppBlade."
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con AppBlade

El objetivo de este tutorial es mostrar cómo integrar AppBlade con Azure Active Directory (Azure AD).<br>La integración de AppBlade con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a AppBlade.
- Puede permitir que los usuarios inicien sesión automáticamente en AppBlade (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con AppBlade, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en AppBlade


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de AppBlade desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de AppBlade desde la galería
Para configurar la integración de AppBlade en Azure AD, deberá agregar AppBlade desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar AppBlade desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones** en el menú superior de la vista de directorios.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **AppBlade**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_01.png)<br>
7. En el panel de resultados, seleccione **AppBlade** y después haga clic en **Completar** para agregar la aplicación. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_02.png)<br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con AppBlade con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de AppBlade para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de AppBlade.<br> Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en AppBlade.

Para configurar y probar el inicio de sesión único de Azure AD con AppBlade, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de AppBlade](#creating-a-appblade-test-user)**: para tener un homólogo de Britta Simon en AppBlade que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación AppBlade.



**Para configurar el inicio de sesión único de Azure AD con AppBlade, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **AppBlade**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en AppBlade?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_04.png) <br>


    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación AppBlade con el siguiente patrón: **“https://companyname.appblade.com/saml/tenantid”**.

    b. Haga clic en **Siguiente**.


4. En la página **Configurar inicio de sesión único en AppBlade**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_05.png) <br>

    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para que se configure el SSO para la aplicación, póngase en contacto con su equipo de soporte técnico de AppBlade a través de ****support@appblade.com** y adjunte el archivo de metadatos descargado a su correo electrónico. Además, pídales que configuren la **dirección URL del emisor de SSO** como ****https://appblade.com/saml**. Esta configuración es necesaria para que el inicio de sesión único funcione.


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración del inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_07.png) <br>

8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de AppBlade

El objetivo de esta sección es crear un usuario llamado Britta Simon en AppBlade. AppBlade admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. **Asegúrese de que el nombre de dominio está configurado con AppBlade para el aprovisionamiento de usuarios. Después de eso solo el aprovisionamiento de usuarios Just-In-Time funcionará.**

Si el usuario tiene una dirección de correo electrónico que termina con el dominio configurado por AppBlade para la cuenta, dicho usuario se unirá automáticamente a la cuenta como un miembro con el nivel de permiso que especifique, que puede ser "Básico" (un usuario básico que solamente puede instalar aplicaciones), "Miembro del equipo" (un usuario que puede cargar nuevas versiones de aplicaciones y administrar proyectos) o "Administrador" (privilegios completos de administrador para la cuenta). Normalmente se elegiría Básico y después se promocionaría a los usuarios manualmente a través de un inicio de sesión del administrador (AppBlade debe configurar un inicio de sesión del administrador basado en correo electrónico por adelantado o promocionar a un usuario en nombre del cliente después de iniciar sesión).

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a AppBlade se creará un nuevo usuario, en caso de que no exista. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE]Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de AppBlade.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a AppBlade. <br><br>![Asignar usuario][200] <br>

**Para asignar a Britta Simon a AppBlade, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **AppBlade**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de AppBlade en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación AppBlade.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->