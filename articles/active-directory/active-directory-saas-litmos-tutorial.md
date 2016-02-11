<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Litmos | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Litmos."
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


# Tutorial: Integración de Azure Active Directory con Litmos

El objetivo de este tutorial es mostrar cómo integrar Litmos con Azure Active Directory (Azure AD).<br>La integración de Litmos con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Litmos 
- Puede permitir que los usuarios inicien sesión automáticamente en Litmos (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, Azure Active Directory. 

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Litmos, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Litmos


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Litmos desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Litmos desde la galería
Para configurar la integración de Litmos en Azure AD, deberá agregar Litmos desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Litmos desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro de búsqueda, escriba **Litmos**.<br><br> ![Aplicaciones][5]<br>
7. En el panel de resultados, seleccione **Litmos** y, a continuación, haga clic en **Completar** para agregar la aplicación. <br><br>![Aplicaciones][500]<br>


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Litmos con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Litmos para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Litmos.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Litmos.
 
Para configurar y probar el inicio de sesión único de Azure AD con Litmos, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Litmos](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en Litmos que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación Litmos.<br> Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Como parte de la configuración, debe personalizar la **atributos de Token SAML** para la aplicación Litmos. <br><br> ![Inicio de sesión único de Azure AD][17] <br>

**Para configurar el inicio de sesión único de Azure AD con Litmos, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **Litmos**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6] <br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en Litmos?**, seleccione **Inicio de sesión único de Azure AD** y, a continuación, haga clic en **Siguiente**. <br><br> ![Inicio de sesión único de Azure AD][7] <br>


1. Inicie sesión en su sitio de la compañía de Litmos como administrador (p. ej.: **https://azureapptest.litmos.com/account/Login*) como administrador. <br><br> ![Inicio de sesión único de Azure AD][21] <br>


1. En la barra de navegación del lado izquierdo, haga clic en el icono de **cuentas**. <br><br> ![Inicio de sesión único de Azure AD][22] <br>


1. Haga clic en la pestaña **Integrations** (Integraciones). <br><br> ![Inicio de sesión único de Azure AD][23] <br>


1. En la pestaña **Integrations** (Integraciones), desplácese hacia abajo hasta **3rd Party Integrations** (Integraciones de terceros) y, a continuación, haga clic en la pestaña **SAML 2.0**. <br><br> ![Inicio de sesión único de Azure AD][24] <br>

1. Copie el valor de **The SAML endoiint for litmos is:** (El extremo de SAML para limos es). <br><br> ![Inicio de sesión único de Azure AD][26] <br>


3. En el Portal de Azure clásico, en la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Inicio de sesión único de Azure AD][8] <br>
 
    a. En el cuadro de texto **Identificador**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación Litmos (p. ej.: **https://azureapptest.litmos.com/account/Login*)).
     
    b. En el cuadro de texto **URL de respuesta**, pegue el valor que ha copiado de la aplicación Litmos en el paso anterior.

    c. Haga clic en **Siguiente**.
 
4. En la página **Configurar inicio de sesión único en Litmos**, siga estos pasos: <br><br>![Inicio de sesión único de Azure AD][2] <br>

    a. Haga clic en Descargar certificado y después guarde el archivo en el equipo.


1. En la aplicación **Litmos**, realice los pasos siguientes: <br><br>![Inicio de sesión único de Azure AD][25] <br>

    a. Haga clic en **Enable SAML** (Habilitar SAML).

    b. Cree un archivo **codificado en base 64** a partir del certificado descargado.

    >[AZURE.TIP] Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    c. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.509 de SAML**.

    d. Haga clic en **Guardar cambios**.


6. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completa**. <br><br>![Inicio de sesión único de Azure AD][11]


20. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**. <br><br>![Configurar inicio de sesión único][12]<br>


24. En el cuadro de diálogo **Agregar atributo de usuario**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único][14]<br>

    | Nombre del atributo | Valor de atributo |
    | ---            | ---             |
    | Email | user.mail |
    | Nombre | user.givenname |
    | Lastname | user.surname |

    En cada fila de datos de la tabla anterior, realice los pasos siguientes:
   
    a. Haga clic en **agregar atributo de usuario**. <br><br>![Configurar inicio de sesión único][15]<br>


    a. En el cuadro de texto **Nombre de atributo**, escriba el **nombre de atributo** que se muestra en dicha fila.

    b. Seleccione el **valor de atributo** que se muestra en dicha fila.

    c. Haga clic en **Completar** para cerrar el cuadro de diálogo **Agregar atributo de usuario**.


25. Haga clic en **Aplicar cambios**. <br><br>![Configurar inicio de sesión único][16]<br>




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)<br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br>

    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba en Litmos

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Litmos.<br> La aplicación Litmos admite el aprovisionamiento Just-in-Time. Esto significa que, si es necesario, se crea automáticamente una cuenta de usuario durante un intento de acceso a la aplicación mediante el panel de acceso.

**Para crear un usuario llamado Britta Simon en Litmos, realice los pasos siguientes:**


1. Inicie sesión en su sitio de la compañía de Litmos como administrador (p. ej.: **https://azureapptest.litmos.com/account/Login*) como administrador. <br><br> ![Inicio de sesión único de Azure AD][21] <br>


1. En la barra de navegación del lado izquierdo, haga clic en el icono de **cuentas**. <br><br> ![Inicio de sesión único de Azure AD][22] <br>


1. Haga clic en la pestaña **Integrations** (Integraciones). <br><br> ![Inicio de sesión único de Azure AD][23] <br>


1. En la pestaña **Integrations** (Integraciones), desplácese hacia abajo hasta **3rd Party Integrations** (Integraciones de terceros) y, a continuación, haga clic en la pestaña **SAML 2.0**. <br><br> ![Inicio de sesión único de Azure AD][24] <br>

1. Seleccione **Autogenerate Users:** (Generar usuarios automáticamente). <br><br> ![Inicio de sesión único de Azure AD][27] <br>


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Litmos. <br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a Litmos, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>
2. En la lista de aplicaciones, seleccione **Litmos**. <br><br>![Asignar usuario][202] <br>
1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>
1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de Litmos en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Litmos.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=AcomDC_0128_2016-->