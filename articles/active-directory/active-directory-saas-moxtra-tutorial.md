<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Moxtra | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Moxtra."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Moxtra

El objetivo de este tutorial es mostrar cómo integrar Moxtra con Azure Active Directory (Azure AD).<br>La integración de Moxtra con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Moxtra. 
- Puede permitir que los usuarios inicien sesión automáticamente en Moxtra (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Moxtra, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Moxtra


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br>
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Moxtra desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Moxtra desde la galería
Para configurar la integración de Moxtra en Azure AD, es preciso agregar Moxtra desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Moxtra desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>
![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorio.<br><br>
![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br>
![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br>
![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **Moxtra**.<br><br>
![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. En el panel de resultados, seleccione **Moxtra** y luego **Completar** para agregar la aplicación.
<br><br>
![Creating an Azure AD test user](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)<br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Moxtra con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Moxtra para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Moxtra.<br>
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Moxtra.
 
Para configurar y probar el inicio de sesión único de Azure AD con Moxtra, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Moxtra](#creating-a-moxtra-test-user)**: para tener un homólogo de Britta Simon en Moxtra que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD y configurar el inicio de sesión único en la aplicación Moxtra.

La aplicación Moxtra espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. En la siguiente captura de pantalla se muestra un ejemplo.
<br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**Para configurar el inicio de sesión único de Azure AD con Moxtra, realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **Moxtra**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
<br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en Moxtra?**, seleccione **Inicio de sesión único de Azure AD** y, luego, haga clic en **Siguiente**.
<br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la siguiente URL: **https://www.moxtra.com/service/#login**.

    b. Haga clic en **Siguiente**.
 
 
4. En la página **Configurar inicio de sesión único en Moxtra**, siga estos pasos:
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


1. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Moxtra como administrador.

1. En la barra de herramientas de la izquierda, haga clic en **Admin Console (Consola de administración) > SAML Single Sign-on (Inicio de sesión único de SAML)** y luego en **New** (Nuevo).
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. En la página **SAML**, realice los siguientes pasos:
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a. En el cuadro de texto **Nombre**, escriba el nombre de la configuración (por ejemplo, *SAML*).

    b. En el Portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Moxtra**, copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **Id. de entidad de IdP**.

    c. En el Portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Moxtra**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.

    d. En el cuadro de texto **AuthnContextClassRef**, escriba **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    e. En el Portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Moxtra**, copie el valor de **Formato de identificador de nombre** y péguelo en el cuadro de texto **Formato de id. de nombre**.

    f. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado**.

    g. En el cuadro de texto del dominio de correo electrónico SAML, escriba su dominio de correo electrónico SAML.
    > [AZURE.NOTE]Para ver los pasos para comprobar el dominio, haga clic en la "**i**" a continuación.


    h. Haga clic en **Actualizar**.


6. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
<br><br> ![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
<br><br>![Inicio de sesión único de Azure AD][11]

1. Para agregar asignaciones de atributos personalizados a la configuración de atributos de token SAML, en el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. En cada fila de datos de la tabla anterior, realice los pasos siguientes:

    | Nombre del atributo | Valor de atributo |
    | ---            | ---             |
    | firstname | givenname |
    | lastname | surname |
    | idpid | *< el valor de **Id. de entidad** del cuadro de diálogo **Configurar inicio de sesión único en Moxtra** en el Portal de Azure >* |

 
    a. Haga clic en agregar atributo de usuario <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. En el cuadro de diálogo **Agregar atributo de usuario**, escriba el nombre y el valor del atributo mostrados para esa fila en la tabla. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Haga clic en **Completo**.



1. Haga clic en **Aplicar cambios**.
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br>
En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**.
<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos:
<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. 
    Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de Moxtra

El objetivo de esta sección es crear un usuario llamado Britta Simon en Moxtra.

**Para crear un usuario llamado Britta Simon en Moxtra, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de Moxtra como administrador.

1. En la barra de herramientas de la izquierda, haga clic en **Admin Console (Consola de administración) > User Management (Administración de usuarios)** y luego en **Add User** (Agregar usuario).
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. En el cuadro de diálogo **Agregar usuario**, realice los pasos siguientes:

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de Britta en el Portal de Azure.

    d. En el cuadro de texto **Division** (División), escriba **Dev**.

    e. En el cuadro de texto **Department** (Departamento), escriba **IT**.

    f. Seleccione **Administrador**.

    g. Haga clic en **Agregar**.





### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Moxtra.
<br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a Moxtra, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior.
<br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **Moxtra**.
<br><br>![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**.
<br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
<br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br>
Al hacer clic en el icono de Moxtra en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Moxtra.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!----HONumber=AcomDC_1203_2015-->
