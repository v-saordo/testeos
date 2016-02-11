<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite | Microsoft Azure"
	description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y SilkRoad Life Suite."
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
	ms.date="01/14/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite

El objetivo de este tutorial es mostrar cómo integrar SilkRoad Life Suite con Azure Active Directory (Azure AD).<br>La integración de SilkRoad Life Suite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SilkRoad Life Suite. 
- Puede permitir que los usuarios inicien sesión automáticamente en SilkRoad Life Suite (inicio de sesión único) con sus cuentas de Azure AD.

Si quiere obtener más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con SilkRoad Life Suite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un inicio de sesión único de SilkRoad Life Suite en la suscripción habilitada


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SilkRoad Life Suite desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de SilkRoad Life Suite desde la galería
Para configurar la integración de SilkRoad Life Suite en Azure AD, deberá agregar SilkRoad Life Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SilkRoad Life Suite desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro Buscar, escriba **SilkRoad Life Suite**.<br><br> ![Aplicaciones][5]<br>
7. En el panel de resultados, seleccione **SilkRoad Life Suite** y luego haga clic en **Completar** para agregar la aplicación. <br><br>![Aplicaciones][50]<br>


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con SilkRoad Life Suite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SilkRoad Life Suite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SilkRoad Life Suite.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SilkRoad Life Suite.
 
Para configurar y probar el inicio de sesión único de Azure AD con SilkRoad Life Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)**: para tener un homólogo de Britta Simon en SilkRoad Life Suite que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD y configurar el inicio de sesión único en la aplicación SilkRoad Life Suite.<br>

**Para configurar el inicio de sesión único de Azure AD con SilkRoad Life Suite, realice los pasos siguientes:**

5. Inicie sesión en su sitio de la compañía de SilkRoad Life Suite como administrador. 


    > [AZURE.NOTE]Para obtener acceso a la aplicación de autenticación de SilkRoad Life Suite para configurar la federación con Microsoft Azure AD, póngase en contacto con el soporte técnico o el representante de servicios de SilkRoad.


6. Vaya a **Service Provider** (Proveedor de servicios) y luego haga clic en **Federation Details** (Detalles de federación). <br><br>![Inicio de sesión único de Azure AD][10] <br>


1. Haga clic en **Download Federation Metadata** (Descargar los metadatos de federación). A continuación, guarde el archivo de metadatos en el equipo. <br><br>![Inicio de sesión único de Azure AD][11] <br>

3. En el Portal de Azure AD, en la página de integración de aplicaciones de **SilkRoad Life Suite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en SilkRoad Life Suite?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.<br><br> ![Inicio de sesión único de Azure AD][7] <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Inicio de sesión único de Azure AD][8] <br>
 
    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de SilkRoad Life Suite (p. ej.: **https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*)).

    b. Abra el archivo de metadatos **Silkroad** descargado.

    c. Busque la etiqueta **AssertionConsumerService** y, a continuación, copie el atributo **Ubicación**. <br><br>![Inicio de sesión único de Azure AD][21] <br>
   
    d. Pegue el valor en el cuadro de texto **Dirección URL de respuesta**.
 
    e. Haga clic en **Siguiente**.
 
4. En la página **Configurar inicio de sesión único en SilkRoad Life Suite**, siga estos pasos:<br><br>![Inicio de sesión único de Azure AD][9] <br>

    a. Haga clic en Descargar certificado y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.




1. En la aplicación **SilkRoad**, haga clic en **Authentication Sources** (Fuentes de autenticación). <br><br>![Inicio de sesión único de Azure AD][12] <br>



1. Haga clic en **Add Authentication Source** (Agregar origen de autenticación). <br><br>![Inicio de sesión único de Azure AD][13] <br>



1. En la sección **Add Authentication Source** (Agregar origen de autenticación), realice los siguientes pasos: <br><br>![Inicio de sesión único de Azure AD][14] <br>

    a. En **Option 2 - Metadata File** (Opción 2 - Archivo de metadatos), haga clic en **Browse** (Examinar) para cargar el archivo de metadatos descargado.

    b. Haga clic en **Create Identity Provider using File Data** (Crear proveedor de identidades con los datos del archivo).



1. En la sección **Authentication Sources** (Orígenes de autenticación), haga clic en **Edit** (Editar). <br><br>![Inicio de sesión único de Azure AD][15] <br>


1. En el cuadro de diálogo **Edit Authentication Source** (Editar origen de autenticación), realice los siguientes pasos: <br><br>![Inicio de sesión único de Azure AD][16] <br>

    a. En **Enabled** (Habilitado), seleccione **Yes** (Sí).

    b. En el cuadro de texto **IdP Description** (Descripción de IdP), escriba una descripción para la configuración (por ejemplo, *SSO de Azure AD*).

    c. En el cuadro de texto **IdP Name** (Nombre de IdP), escriba un nombre que sea específico para su configuración (por ejemplo, *Azure SP*).

    d. Haga clic en **Guardar**.


6. Deshabilite todos los demás orígenes de autenticación. <br><br>![Inicio de sesión único de Azure AD][17]<br>

7. En el Portal de Azure AD, en la página **Confirmación del inicio de sesión único**, haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][18]

1. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Inicio de sesión único de Azure AD][19]


### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) <br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) <br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de SilkRoad Life Suite

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en SilkRoad Life Suite. Britta debe tener un Id. de SSO (a veces se conoce como *AuthParam*) que coincida con su valor de **emailaddress** en Azure AD.

**Para crear un usuario llamado Britta Simon en SilkRoad Life Suite, realice los pasos siguientes:**

1. Pida al equipo de soporte de SilkRoad Life Suite que cree un usuario que tenga como atributo **SSO ID** el mismo valor que el campo **emailaddress** de Britta Simon en Azure AD.



### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a SilkRoad Life Suite. <br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a SilkRoad Life Suite, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior. <br><br>![Asignar usuario][201] <br>
2. En la lista de aplicaciones, seleccione **SilkRoad Life Suite**. <br><br>![Asignar usuario][202] <br>
1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>
1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono SilkRoad Life Suite en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de SilkRoad Life Suite.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0121_2016-->