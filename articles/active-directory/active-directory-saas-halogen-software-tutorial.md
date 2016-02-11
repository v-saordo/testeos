<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Halogen Software"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Halogen Software."
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


# Tutorial: Integración de Azure Active Directory con Halogen Software

El objetivo de este tutorial es mostrar cómo integrar Halogen Software con Azure Active Directory (Azure AD).<br>La integración de Halogen Software con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Halogen Software. 
- Puede permitir que los usuarios inicien sesión automáticamente en Halogen Software (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Halogen Software, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Halogen Software


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br>
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Halogen Software desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Agregar Halogen Software desde la galería
Para configurar la integración de Halogen Software en Azure AD, deberá agregar Halogen Software desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Halogen Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>
![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorios.<br><br>
![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br>
![Aplicaciones][3]
5. En el cuadro de diálogo **Qué desea hacer**, haga clic en **Agregar una aplicación de la galería**.<br><br>
![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **halogen software**.<br>
![Aplicaciones][5]
7. En el panel de resultados, seleccione **Halogen Software** y, luego, haga clic en **Completa** para agregar la aplicación.<br>



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Halogen Software según un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Halogen Software para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Halogen Software.<br>
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Halogen Software.
 
Para configurar y probar el inicio de sesión único de Azure AD con Halogen Software, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Halogen Software](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en Halogen Software que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el portal de Azure AD y configurar el inicio de sesión único en la aplicación de Halogen Software.<br>

**Para configurar el inicio de sesión único de Azure AD con Halogen Software, realice los pasos siguientes:**

1. En el portal de Azure AD, en la página de integración de aplicaciones de **Halogen Software**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br>
![Configurar inicio de sesión único][8]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Halogen Software?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.<br><br>
![Inicio de sesión único de Azure AD][9]

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar las opciones de la aplicación][10]
 
     3\.1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Halogen Software con el siguiente patrón: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     3\.2. Haga clic en **Siguiente**.
 
4. En la página **Configuración de inicio de sesión único en Halogen Software**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.<br><br>![Qué es Azure AD Connect][11]

5. En una ventana de explorador diferente, inicie sesión en su aplicación de **Halogen Software** como administrador.
6. Haga clic en la pestaña **Opciones**. <br><br>![Qué es Azure AD Connect][12]
7. En el panel de navegación izquierdo, haga clic en **Configuración de SAML**. <br><br>![Qué es Azure AD Connect][13]
8. En la página **Configuración de SAML**, realice los siguientes pasos: <br><br>![Qué es Azure AD Connect][14]

     8\.1. Como **identificador único**, seleccione **NameID**.

     8\.2. En **El identificador único se asigna a**, seleccione **nombre de usuario**.

     8\.3. Para cargar el archivo de metadatos descargado, haga clic en **Examinar** para seleccionar el archivo y, luego, en **Cargar archivo**.

     8\.4. Para probar la configuración, haga clic en **Ejecutar prueba**. 

> [AZURE.NOTE]Deberá esperar a que aparezca el mensaje "*La prueba de SAML está completa. Cierre esta ventana *". A continuación, cierre la ventana del explorador abierta. <br> La casilla de verificación **Habilitar SAML** solo está habilitada si se ha completado la prueba.

     8\.5. Seleccione **Habilitar SAML**.
    
     8\.6. Haga clic en **Guardar cambios**.


9. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.<br><br>![Qué es Azure AD Connect][15]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completa**.<br><br>![Qué es Azure AD Connect][16]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**.
<br><br>![Qué es Azure AD Connect][100] 
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
<br><br>![Qué es Azure AD Connect][101] 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
<br><br>![Qué es Azure AD Connect][102] 
5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
<br><br>![Qué es Azure AD Connect][103] 
  1. Como **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.
6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: 
<br><br>![Qué es Azure AD Connect][104] 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
<br><br>![Qué es Azure AD Connect][105]  
8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
<br><br>![Qué es Azure AD Connect][106]   
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   
  
 
### Creación de un usuario de prueba de Halogen Software

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en el Portal de Azure.

**Para crear un usuario llamado Britta Simon en Halogen Software, realice los pasos siguientes:**

1. Inicie sesión en su aplicación de **Halogen Software** como administrador.
2. Haga clic en la pestaña **Centro de usuarios** y, luego, haga clic en **Crear usuario**.
<br><br>![Qué es Azure AD Connect][300]  
3. En la página del cuadro de diálogo **Nuevo usuario**, realice los pasos siguientes:
<br><br>![Qué es Azure AD Connect][301]
  1. En el cuadro de texto **Nombre**, escriba **Britta**. 
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre de usuario**, escriba el **nombre de usuario de Britta Simon en el portal de Azure AD**.
  4. En el cuadro de texto **Contraseña**, escriba una contraseña para Britta.
  5. Haga clic en **Guardar**.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon utilice el inicio de sesión único de Azure concediéndole acceso a Halogen Software.
<br><br>![Qué es Azure AD Connect][200]

**Para asignar un usuario llamado Britta Simon a Halogen Software, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior.<br>
<br><br>![Qué es Azure AD Connect][201]
2. En la lista de aplicaciones, seleccione **Halogen Software**.
<br><br>![Qué es Azure AD Connect][202]
1. En el menú de la parte superior, haga clic en **Usuarios**. <br>
<br><br>![Qué es Azure AD Connect][203]
1. En la lista Usuarios, seleccione **Britta Simon**.
<br><br>![Qué es Azure AD Connect][204]
2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
<br><br>![Qué es Azure AD Connect][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br>
Al hacer clic en el icono de Halogen Software en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Halogen Software.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=AcomDC_1223_2015--->

