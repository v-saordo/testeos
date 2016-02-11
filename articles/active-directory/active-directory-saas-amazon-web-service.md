<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Amazon Web Service (AWS) | Microsoft Azure"
	description="Aprenda a usar Amazon Web Service (AWS) con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
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


# Tutorial: integración de Azure Active Directory con Amazon Web Service (AWS)

El objetivo de este tutorial es mostrar cómo integrar Amazon Web Service (AWS) con Azure Active Directory (Azure AD).<br>La integración de Amazon Web Service (AWS) con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Service (AWS). 
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Service (AWS) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Amazon Web Service (AWS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Amazon Web Service (AWS)


> [AZURE.NOTE]Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Amazon Web Service (AWS) desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Amazon Web Service (AWS) desde la galería
Para configurar la integración de Amazon Web Service (AWS) en Azure AD, es preciso agregar Amazon Web Service (AWS) desde la galería a la lista de aplicaciones SaaS administradas.

### Para agregar Amazon Web Service (AWS) desde la galería, realice los pasos siguientes:

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Active Directory][1]<br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en el menú superior de la vista de directorios , haga clic en **Applications**. <br><br>![Aplicaciones][2]<br>

4. Haga clic en **Agregar** en la parte inferior de la página. <br><br>![Aplicaciones][3]<br>

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Incorporación de una aplicación desde la galería**. <br><br>![Aplicaciones][4]<br>

6. En el cuadro de búsqueda, escriba **Amazon Web Service (AWS)**. <br><br>![Aplicaciones][5]<br>

7. En el panel de resultados, seleccione **Amazon Web Service (AWS)** y, a continuación, haga clic en **Completar** para agregar la aplicación. <br><br>![Aplicaciones][6]<br>



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Amazon Web Service (AWS) con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Service (AWS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Service (AWS).<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Amazon Web Service (AWS).
 
Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Service (AWS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en Amazon Web Service (AWS) que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el portal de Azure AD y configurar el inicio de sesión único en la aplicación de Amazon Web Service (AWS).<br> La aplicación Amazon Web Service (AWS) espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo de esto.


<br><br>![Configurar inicio de sesión único][27]<br>

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Service (AWS), realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **Amazon Web Service (AWS)**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br>![Configurar inicio de sesión único][7]<br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en Amazon Web Service (AWS)?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**. <br><br>![Configurar inicio de sesión único][8]<br>

3. En la página de diálogo **Configurar las opciones de la aplicación**, haga clic en Siguiente. <br><br>![Configurar las opciones de la aplicación][9]<br>
 
4. En la página **Configuración de inicio de sesión único en Amazon Web Service (AWS)**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo. <br><br>![Configurar inicio de sesión único][10]<br>

5. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Service (AWS) como administrador.

6. Haga clic en **Página principal de la consola**. <br><br>![Configurar inicio de sesión único][11]<br>

7. Haga clic en **Administración de identidades y acceso**. <br><br>![Configurar inicio de sesión único][12]<br>

8. Haga clic en **Identity Providers** (Proveedores de identidad) y luego en **Create Provider** (Crear proveedor). <br><br>![Configurar inicio de sesión único][13]<br>

9. En la página de diálogo **Configure Provider** (Configurar proveedor), realice los pasos siguientes: <br><br>![Configurar inicio de sesión único][14]<br>

     a. En **Tipo de proveedor**, seleccione **SAML**.

     b. En el cuadro de texto **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

     c. Haga clic en **Elegir archivo** para cargar el archivo de metadatos descargado.

     d. Haga clic en **Siguiente paso**.


10. En la página diálogo **Comprobar la información del proveedor**, haga clic en **Crear**. <br><br>![Configurar inicio de sesión único][15]<br>

11. Haga clic en **Roles** y luego en **Crear nuevo rol**. <br><br>![Configurar inicio de sesión único][16]<br>

12. En el cuadro de diálogo **Establecer nombre de rol**, realice los siguientes pasos: <br><br>![Configurar inicio de sesión único][17]<br>

     a. En el cuadro de texto **Nombre de rol**, escriba un nombre de role (p. ej.: *TestUser*).

     b. Haga clic en **Siguiente paso**.

13. En el cuadro de diálogo **Seleccionar tipo de rol**, realice los siguientes pasos: <br><br>![Configurar inicio de sesión único][18]<br>

     a. Seleccione **Rol de acceso del proveedor de identidades**.

     b. En la sección **Conceder acceso de inicio de sesión único web (WebSSO) a los proveedores de SAML**, haga clic en **Seleccionar**.


14. En el cuadro de diálogo **Establecer confianza**, realice los siguientes pasos: <br><br>![Configurar inicio de sesión único][19]<br>
     
     a. Como proveedor de SAML, seleccione el proveedor de SAML que ha creado antes (p. ej.: *WAAD*).

     b. Haga clic en **Siguiente paso**.


15. En el cuadro de diálogo **Comprobar la confianza del rol**, haga clic en **Siguiente paso**. <br><br>![Configurar inicio de sesión único][32]<br>


16. En el cuadro de diálogo **Adjuntar directiva**, haga clic en **Siguiente paso**. <br><br>![Configurar inicio de sesión único][33]<br>


17. En el cuadro de diálogo **Revisar**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único][34]<br>

     a. Copie el valor de **ARN de rol**.

     b. Copie el valor de ARN de **Entidades de confianza**.

     c. Haga clic en **Crear rol**.

18. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Qué es Azure AD Connect][20]<br>

19. En la página **Confirmación de inicio de sesión único**, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**. <br><br>![Qué es Azure AD Connect][22]<br>


20. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**. <br><br>![Configurar inicio de sesión único][21]<br>

21. Haga clic en **agregar atributo de usuario**. <br><br>![Configurar inicio de sesión único][23]<br>

22. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes. <br><br>![Configurar inicio de sesión único][24]<br>

     a. En el cuadro de texto **Nombre de atributo**, escriba ****https://aws.amazon.com/SAML/Attributes/Role**.

     b. En el cuadro de texto **Valor del atributo**, escriba **[el valor de ARN de rol], [el valor de ARN de la entidad de confianza]**.

     >[AZURE.TIP]Estos son los valores que se ha copiado del cuadro de diálogo de revisión cuando creó el rol.

     c. Haga clic en **Completar** para cerrar el cuadro de diálogo **Agregar atributo de usuario**.

23. Haga clic en **agregar atributo de usuario**. <br><br>![Configurar inicio de sesión único][23]<br>


24. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes. <br><br>![Configurar inicio de sesión único][25]<br>


     a. En el cuadro de texto **Nombre de atributo**, escriba ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. En el cuadro de texto **Nombre de atributo**, escriba **userprincipalname**.

     c. Haga clic en **Completar** para cerrar el cuadro de diálogo **Agregar atributo de usuario**.


25. Haga clic en **Aplicar cambios**. <br><br>![Configurar inicio de sesión único][26]<br>





### Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>

  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>

  1. En el cuadro de texto **Nombre**, escriba **Britta**.
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **crear**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>

  1. Anote el valor de **Nueva contraseña**.
  2. Haga clic en **Completo**.   
  
 
### Creación de un usuario de prueba de Amazon Web Service (AWS)

El objetivo de esta sección es crear un usuario llamado Britta Simon en Amazon Web Service (AWS).

### Para crear un usuario llamado Britta Simon en Amazon Web Service (AWS), realice los pasos siguientes:

1. Inicie sesión en su sitio de la compañía de **Amazon Web Service (AWS)** como administrador.

2. Haga clic en el icono de **Página principal de la consola**. <br><br>![Configurar inicio de sesión único][11]<br>

3. Haga clic en Administración de identidades y acceso. <br><br>![Configurar inicio de sesión único][28]<br>

4. En el Panel, haga clic en Usuarios y luego, en Crear nuevos usuarios. <br><br>![Configurar inicio de sesión único][29]<br>

5. En el cuadro de diálogo Crear usuario, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único][30]<br>

     a. En los cuadros de texto **Escribir nombres de usuario**, escriba el nombre de usuario de Brita Simon (userprincipalname) en Azure AD.

     b. Haga clic en **Crear**.




### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Amazon Web Service (AWS).

![Asignar usuario][31]

**Para asignar un usuario llamado Britta Simon a CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][26]<br>

2. En la lista de aplicaciones, seleccione **Amazon Web Service (AWS)**. <br><br>![Asignar usuario][27]<br>

1. En el menú superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][25]<br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][29]<br>

### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br> Al hacer clic en el icono de Amazon Web Service (AWS) en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Amazon Web Service (AWS).


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png

<!---HONumber=AcomDC_1223_2015--->