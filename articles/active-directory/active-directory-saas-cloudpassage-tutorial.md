<properties
	pageTitle="Tutorial: integración de Azure Active Directory con CloudPassage | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y CloudPassage."
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


# Tutorial: integración de Azure Active Directory con CloudPassage

El objetivo de este tutorial es mostrar cómo integrar CloudPassage con Azure Active Directory (Azure AD).<br>La integración de CloudPassage con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a CloudPassage. 
- Puede permitir que los usuarios inicien sesión automáticamente en CloudPassage (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, Azure Active Directory. 
- 

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con CloudPassage, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en CloudPassage


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una suscripción de prueba a Azure gratuita durante un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de CloudPassage desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de CloudPassage desde la galería
Para configurar la integración de CloudPassage en Azure AD, deberá agregar CloudPassage desde la galería a la lista de aplicaciones SaaS administradas.

### Para agregar CloudPassage desde la galería, realice los pasos siguientes:

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorios.<br><br>![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **CloudPassage**.<br><br>![Aplicaciones][5]
7. En el panel de resultados, seleccione **CloudPassage** y, a continuación, haga clic en **Completar** para agregar la aplicación.<br><br> ![Aplicaciones][6]



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con CloudPassage con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de CloudPassage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de CloudPassage.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en CloudPassage.
 
Para configurar y probar el inicio de sesión único de Azure AD con CloudPassage, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de CloudPassage](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en CloudPassage que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en una aplicación CloudPassage.<br> La aplicación CloudPassage espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla muestra un ejemplo. <br><br> ![Configurar inicio de sesión único][21]

**Para configurar el inicio de sesión único de Azure AD con CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **CloudPassage**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br> ![Configurar inicio de sesión único][7]

2. En la página **¿Cómo desea que los usuarios inicien sesión en CloudPassage?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.<br><br> ![Configurar inicio de sesión único][8]

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar las opciones de la aplicación][9]
 
     3\.1. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación CloudPassage (p. ej.: **https://portal.cloudpassage.com/saml/init/accountid*)).

     3\.2. En el cuadro de texto URL de respuesta, escriba su dirección URL de AssertionConsumerService (p. ej.: **https://portal.cloudpassage.com/saml/consume/accountid*)). <br> Para obtener el valor de este atributo, haga clic en **SSO Setup documentation** (Documentación de instalación de SSO) en la sección **Single Sign-on Settings** (Configuración de inicio de sesión único) del portal de CloudPassage. <br><br>![Configurar inicio de sesión único][10]

     3\.3. Haga clic en **Siguiente**.



4. En la página **Configuración de inicio de sesión único en CloudPassage**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente en el equipo. <br><br>![Configurar inicio de sesión único][11]

5. En otra ventana del explorador, inicie sesión en su sitio de la compañía de CloudPassage como administrador.

6. En el menú de la parte superior, haga clic en **Settings** (Configuración) y, a continuación, en **Site Administration** (Administración del sitio).<br><br> ![Configurar inicio de sesión único][12]

7. Haga clic en la pestaña **Authentication Settings** (Configuración de autenticación). <br><br> ![Configurar inicio de sesión único][13]


8. En la sección **Single Sign-on Settings** (Configuración del inicio de sesión único), siga estos pasos: <br><br> ![Configurar inicio de sesión único][14]


     8\.1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **SAML issuer URL** (URL del emisor de SAML).

     8\.2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **Extremo iniciado por el proveedor de servicios** y péguelo en el cuadro de texto **SAML endpoint URL** (URL de punto de conexión de SAML).

     8\.3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **URL de cierre de sesión** y péguelo en el cuadro de texto **Logout landing page** (Página de aterrizaje de cierre de sesión).

     8\.4. Cree un archivo codificado en **base 64** a partir del certificado descargado.
          
      >[AZURE.TIP] Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

     8\.5. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **certificado X.509**.

     8\.6. Haga clic en **Guardar**.


9. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br> ![Configurar inicio de sesión único][15]


10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Configurar inicio de sesión único][16]



11. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.<br><br> ![Configurar inicio de sesión único][17]

12. Para agregar los atributos de usuario necesarios en cada fila de la tabla siguiente, realice los pasos que se indican a continuación: <br>

| Nombre del atributo | Valor de atributo |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. En la barra de herramientas de la parte inferior, haga clic en **Aplicar cambios**. <br><br> ![Configurar inicio de sesión único][20]



### Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br><br> En la lista de usuarios, seleccione **Britta Simon**.<br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **crear**. <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:<br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   


  
 
### Creación de un usuario de prueba de CloudPassage

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en CloudPassage.

#### Para crear un usuario llamado Britta Simon en CloudPassage, realice los pasos siguientes:

1.	Inicie sesión en su sitio de la compañía de **CloudPassage** como administrador. 

2.	En la barra de herramientas de la parte superior, haga clic en **Settings** (Configuración) y luego en **Site Administration** (Administración del sitio). <br>![Creación de un usuario de prueba de CloudPassage][22]

3.	Haga clic en la pestaña **Users** (Usuarios) y, a continuación, en **Add New User** (Agregar nuevo usuario). <br>![Creación de un usuario de prueba de CloudPassage][23]
	
4.	En la sección **Add New User** (Agregar nuevo usuario), lleve a cabo estos pasos: <br>![Creación de un usuario de prueba de CloudPassage][24]

     4\.1. En el cuadro de texto **First Name** (Nombre), escriba Britta.

     4\.2. En el cuadro de texto **Last Name** (Apellidos), escriba Simon.

     4\.3. En el cuadro de texto **Username** (Nombre de usuario), el cuadro de texto **Email** (Correo electrónico) y el cuadro de texto **Retype Email** (Volver a escribir correo electrónico), escriba el nombre de usuario de Britta en Azure AD.

     4\.4. En **Access Type** (Tipo de acceso), seleccione **Enable Halo Portal Access** (Habilitar el acceso de Portal de Halo).

     4\.5. Haga clic en **Agregar**.










### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a CloudPassage.<br><br>![Asignar usuario][30]

**Para asignar un usuario llamado Britta Simon a CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **Aplicaciones** en el menú superior. <br> <br><br>![Asignar usuario][26]
2. En la lista de aplicaciones, seleccione **CloudPassage**. <br><br>![Asignar usuario][27]
1. En el menú de la parte superior, haga clic en **Usuarios**.<br> <br><br>![Asignar usuario][25]
1. En la lista de usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][29]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de CloudPassage en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de CloudPassage.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=AcomDC_0128_2016-->