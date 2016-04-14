<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Novatus | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y LearnUpon."
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
	ms.date="02/11/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con LearnUpon

El objetivo de este tutorial es mostrar cómo integrar LearnUpon con Azure Active Directory (Azure AD).<br>La integración de LearnUpon con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a LearnUpon.
- Puede permitir que los usuarios inicien sesión automáticamente en LearnUpon (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory clásico. 
- 

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con LearnUpon, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LearnUpon


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de LearnUpon desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de LearnUpon desde la galería
Para configurar la integración de LearnUpon en Azure AD, es preciso agregar LearnUpon desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LearnUpon desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **LearnUpon**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)<br>
7. En el panel de resultados, seleccione **LearnUpon** y después haga clic en **Completar** para agregar la aplicación. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)<br>
##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con LearnUpon con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LearnUpon para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LearnUpon.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en LearnUpon.

Para configurar y probar el inicio de sesión único de Azure AD con LearnUpon, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de LearnUpon](#creating-a-learnupon-test-user)**: para tener un homólogo de Britta Simon en LearnUpon que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el portal de Azure AD y configurar el inicio de sesión único en la aplicación LearnUpon.



**Para configurar el inicio de sesión único de Azure AD con LearnUpon, siga estos pasos:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **LearnUpon**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en LearnUpon?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) <br>


    a. En el cuadro de texto URL de respuesta, escriba la URL del Servicio de consumidor de aserciones con el siguiente patrón: **"https://<nombreDeEmpresa>.learnupon.com/saml/consumer"**.


4. En la página **Configurar inicio de sesión único en LearnUpon**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo. Necesitaremos este certificado y las direcciones URL de metadatos (Id. de entidad, Dirección URL de inicio de sesión SSO y Dirección URL de cierre de sesión) para configurar el SSO en LearnUpon.

    b. Haga clic en **Siguiente**.


5. Abra otro inicio de sesión de explorador en la sesión de LearnUpon con el usuario administrador configurado en **Inicio de sesión único de SAML** en LearnUpon. Cuando haya iniciado sesión en LearnUpon, verá una pantalla similar a la siguiente. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) <br>

	a. Haga clic en la pestaña **configuración** para abrir la ventana de configuración.<br> b. Haga clic en **Inicio de sesión único - SAML**<br> c. Haga clic en **Configuración general** para configurar SAML. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) <br> d. Rellene el formulario de **Configuración general** del modo siguiente: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) <br> d1. Active la casilla **Habilitado** para habilitar SAML en este portal.<br> d2. Elija **versión 2.0**<br>. d3. Elija **Condiciones de no omitir**<br>. d4. **Nombre de parámetro POST de token de SAML** es el nombre del parámetro POST de solicitud para la dirección URL del consumidor de SAML indicada antes, que contiene la aserción SAML que se debe comprobar y autenticar; por ejemplo,**SAMLResponse**. <br> d5. **Formato de identificador de nombre** nos indica dónde reside el identificador de los usuarios (dirección de correo electrónico) en la aserción SAML; por ejemplo,**urn:oasis:names:tc:SAML:1.1:nameid- format:emailAddress**.<br> d6. **Identificar la ubicación del proveedor** es donde se enviará a los usuarios si hacen clic en el icono que ha cargado desde la pantalla de inicio de sesión del portal.<br> d7. Copie el valor de **Dirección URL del servicio de cierre de sesión único** en la pantalla de configuración de Azure a **Dirección URL de cierre de sesión**. <br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_09.png) <br> d8. Haga clic en el vínculo **Administrar huellas digitales** en Huellas digitales de certificado para cargar la huella digital del certificado. <br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_10.png) <br> d9. Haga clic en el botón Guardar. Haga clic en **Configuración de usuario** para configurar los valores del usuario SAML. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) <br> e1. En **Formato de identificador de nombre**, se indica en qué parte de la aserción SAML reside el nombre de los usuarios; por ejemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**. e2. En **Formato de identificador de apellido**, se indica en qué parte de la aserción SAML reside el apellido de los usuarios; por ejemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.


6. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br>![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) <br>

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de LearnUpon

El objetivo de esta sección es crear un usuario llamado Britta Simon en LearnUpon. LearnUpon admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a LearnUpon se creará un nuevo usuario, si aún no existe. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear un usuario manualmente, es preciso que se ponga contacto con el equipo de soporte técnico de LearnUpon.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a LearnUpon. <br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a LearnUpon, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **LearnUpon**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de LearnUpon en el panel de acceso, debería iniciar sesión automáticamente en su aplicación LearnUpon.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0218_2016-->