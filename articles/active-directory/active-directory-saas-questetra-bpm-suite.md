<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Questetra BPM Suite| Microsoft Azure"
	description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Questetra BPM Suite."
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


# Tutorial: Integración de Azure Active Directory con Questetra BPM Suite

El objetivo de este tutorial es mostrar cómo integrar Questetra BPM Suite con Azure Active Directory (Azure AD).<br>La integración de Questetra BPM Suite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Questetra BPM Suite 
- Puede permitir que los usuarios inicien sesión automáticamente en Questetra BPM Suite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Questetra BPM Suite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un inicio de sesión único de [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) en la suscripción habilitada


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Questetra BPM Suite desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Questetra BPM Suite desde la galería
Para configurar la integración de Questetra BPM Suite en Azure AD, deberá agregar Questetra BPM Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Questetra BPM Suite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br> ![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en el menú superior de la vista de directorios , haga clic en **Applications**.<br><br> ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Incorporación de una aplicación desde la galería**.<br><br> ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Questetra BPM Suite**.<br> ![Aplicaciones][5]
7. En el panel de resultados, seleccione **Questetra BPM Suite** y, a continuación, haga clic en **Completar** para agregar la aplicación.<br>



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Questetra BPM Suite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Questetra BPM Suite.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Questetra BPM Suite.
 
Para configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)**: para tener un homólogo de Britta Simon en Questetra BPM Suite que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD y configurar el inicio de sesión único en la aplicación Questetra BPM Suite.<br>

**Para configurar el inicio de sesión único de Azure AD con Questetra BPM Suite, realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **Questetra BPM Suite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br> ![Configurar inicio de sesión único][8]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Questetra BPM Suite?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.<br><br> ![Inicio de sesión único de Azure AD][9]


3. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de **Questetra BPM Suite** como administrador.

4. En el menú de la parte superior, haga clic en **System Settings** (Configuración del sistema). <br><br> ![Inicio de sesión único de Azure AD][10]

5. Para abrir la página **SingleSignOnSAML**, haga clic en **SSO (SAML)**. <br><br> ![Inicio de sesión único de Azure AD][11]


6. En el Portal de Azure, en la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar las opciones de la aplicación][13]
 
    a. En el sitio de la compañía de **Questetra BPM Suite**, en la sección de información de SP, copie la **ACS URL** (URL de ACS) y, a continuación, péguela en el cuadro de texto **Sign On URL** (URL de inicio de sesión).

    b. En el sitio de la compañía **Questetra BPM Suite**, en la sección de información de SP, copie el valor de **Entity ID** (Id. de entidad) y, a continuación, péguelo en el cuadro de texto **Issuer URL** (URL del emisor).

    c. En el sitio de la compañía **Questetra BPM Suite**, en la sección de información de SP, copie el valor de **ACS URL** (URL de ACS) y, a continuación, péguelo en el cuadro de texto **Reply URL** (URL de respuesta).

    d. Haga clic en **Siguiente**.

 
7. En la página **Configure single sign-on at Questetra BPM Suite** (Configurar inicio de sesión único en Questetra BPM Suite), haga clic en **Download certificate** (Descargar certificado) y, a continuación, guarde el archivo de certificado localmente en el equipo.<br><br>![Configurar inicio de sesión único][14]


8. En el sitio de la compañía **Questetra BPM Suite**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único][15]

    a. Seleccione **Enable Single Sign-On** (Habilitar inicio de sesión único).
     
    b. En el Portal de Azure, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Id. de entidad**.

    c. En el Portal de Azure, copie el valor de **URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **URL de la página de inicio de sesión**.

    d. En el Portal de Azure, copie el valor de **URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión**.

    e. En el cuadro de texto **Formato de NameID**, escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.


    f. Cree un archivo codificado en base 64 a partir del certificado descargado.

    >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado de validación**.

    h. Haga clic en **Guardar**.


9. En el portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Qué es Azure AD Connect][17]


10. En la página **Confirmación de inicio de sesión único**, haga clic en **Completo**.<br><br>![Qué es Azure AD Connect][18]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD][100] 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br>![Creación de un usuario de prueba de Azure AD][101]

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.<br><br>![Creación de un usuario de prueba de Azure AD][102]

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD][103]
 
    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
  
    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en Siguiente.
6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD][104] 
  
    a. En el cuadro de texto **Nombre**, escriba **Britta**.
 
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br>![Creación de un usuario de prueba de Azure AD][105]

8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD][106]
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   
  
 
### Creación de un usuario de prueba de Questetra BPM Suite

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Questetra BPM Suite.

**Para crear un usuario llamado Britta Simon en Questetra BPM Suite, realice los pasos siguientes:**

1.	Inicie sesión en el sitio de la compañía de Questetra BPM Suite como administrador.
2.	Vaya a **System Settings > User List > New User** (Configuración del sistema > Lista de usuarios > Nuevo usuario). 
3.	En el cuadro de diálogo New User (Nuevo usuario), realice los pasos siguientes: <br><br>![Creación de un usuario de prueba][300] 

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de usuario de Britta en Azure AD.

    b. En el cuadro de texto **Email** (Correo electrónico), escriba el nombre de usuario de Britta en Azure AD.

    c. En el cuadro de texto **Password** (Contraseña), escriba una contraseña.

4.	Haga clic en **Add new user** (Agregar nuevo usuario).



### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure al concederle acceso a Questetra BPM Suite. <br><br>![Qué es Azure AD Connect][200]

**Para asignar Britta Simon a Questetra BPM Suite, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior.<br><br>![Qué es Azure AD Connect][201]
2. En la lista de aplicaciones, seleccione **Questetra BPM Suite**. <br><br>![Qué es Azure AD Connect][205]
1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Qué es Azure AD Connect][202]
1. En la lista Usuarios, seleccione **Britta Simon**. <br><br>![Qué es Azure AD Connect][203]
2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Qué es Azure AD Connect][204]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br> Al hacer clic en el icono Questetra BPM Suite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Questetra BPM Suite.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=AcomDC_1223_2015-->