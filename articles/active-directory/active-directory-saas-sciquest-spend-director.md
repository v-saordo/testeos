<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con SciQuest Spend Director | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SciQuest Spend Director."
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
	ms.date="01/05/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con SciQuest Spend Director

El objetivo de este tutorial es mostrar cómo integrar SciQuest Spend Director con Azure Active Directory (Azure AD).<br>La integración de SciQuest Spend Director con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SciQuest Spend Director. 
- Puede permitir que los usuarios inicien sesión automáticamente en SciQuest Spend Director (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con SciQuest Spend Director, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de SciQuest Spend Director habilitada para el inicio de sesión único


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar SciQuest Spend Director desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Agregar SciQuest Spend Director desde la galería
Para configurar la integración de SciQuest Spend Director en Azure AD, deberá agregar SciQuest Spend Director desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SciQuest Spend Director desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br> ![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorios.<br><br>![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]
5. En el cuadro de diálogo **Qué desea hacer**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **SciQuest Spend Director**.<br>![Aplicaciones][5]
7. En el panel de resultados, seleccione **SciQuest Spend Director** y, luego, haga clic en **Completa** para agregar la aplicación.<br> ![Aplicaciones][6]


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con SciQuest Spend Director según un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SciQuest Spend Director para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SciQuest Spend Director.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SciQuest Spend Director.
 
Para configurar y probar el inicio de sesión único de Azure AD con SciQuest Spend Director, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de SciQuest Spend Director](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en SciQuest Spend Director que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el portal de Azure AD y configurar el inicio de sesión único en la aplicación de SciQuest Spend Director.<br>

**Para configurar el inicio de sesión único de Azure AD con SciQuest Spend Director, realice los pasos siguientes:**

1. En el portal de Azure AD, en la página de integración de aplicaciones de **SciQuest Spend Director**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br>![Configurar inicio de sesión único][8]

2. En la página **¿Cómo desea que los usuarios inicien sesión en SciQuest Spend Director?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.<br><br> ![Inicio de sesión único de Azure AD][9]

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar las opciones de la aplicación][10]
 
     3\.1. 3.1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de SciQuest Spend Director con el siguiente patrón: **https://.*sciquest.com/.**

     3\.2. En el cuadro de texto **URL de respuesta**, escriba el mismo valor que ha escrito en el cuadro de texto **URL de inicio de sesión**.

     3\.3. Haga clic en **Siguiente**.
 
4. En la página **Configuración de inicio de sesión único en SciQuest Spend Director**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.<br><br>![Qué es Azure AD Connect][11]

5. Póngase en contacto con el soporte técnico de SciQuest para habilitar este método de autenticación mediante los datos descargados anteriores.

6. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.<br><br>![Qué es Azure AD Connect][15]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completa**.<br><br>![¿Qué es Azure AD Connect?][16]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Qué es Azure AD Connect][100] 
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br>![Qué es Azure AD Connect][101] 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br>![Qué es Azure AD Connect][102] 
5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br>![Qué es Azure AD Connect][103] 
  1. Como **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.
6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Qué es Azure AD Connect][104] 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br>![Qué es Azure AD Connect][105]  
8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Qué es Azure AD Connect][106]   
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   
  
 
### Creación de un usuario de prueba de SciQuest Spend Director

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en SciQuest Spend Director.

Deberá ponerse en contacto con el equipo de soporte técnico de SciQuest Spend Director y proporcionarles los detalles acerca de la cuenta de prueba que se ha creado.

Como alternativa, también puede aprovechar el aprovisionamiento Just-In-Time, una característica de inicio de sesión único que es compatible con SciQuest Spend Director. <br> Si el aprovisionamiento Just-In-Time está habilitado, los usuarios se crean automáticamente en SciQuest Spend Director durante un intento de inicio de sesión único, si no existen. Esta característica elimina la necesidad de crear manualmente usuarios homólogos de inicio de sesión único.

Para habilitar el aprovisionamiento Just-In-Timed, deberá ponerse en contacto con el equipo de soporte técnico de SciQuest Spend Director.
  

### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon utilice el inicio de sesión único de Azure concediéndole acceso a SciQuest Spend Director. <br><br>![Qué es Azure AD Connect][200]

**Para asignar a Britta Simon a SciQuest Spend Director, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior.<br> <br><br>![Qué es Azure AD Connect][201]
2. En la lista de aplicaciones, seleccione **SciQuest gastar Director**. <br><br>![Qué es Azure AD Connect][202]
1. En el menú de la parte superior, haga clic en **Usuarios**.<br> <br><br>![Qué es Azure AD Connect][203]
1. En la lista Usuarios, seleccione **Britta Simon**. <br><br>![Qué es Azure AD Connect][204]
2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Qué es Azure AD Connect][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br> Al hacer clic en el icono de SciQuest Spend Director en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de SciQuest Spend Director.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!----HONumber=AcomDC_0107_2016-->