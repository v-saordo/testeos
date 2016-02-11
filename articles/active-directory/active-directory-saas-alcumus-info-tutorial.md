<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Alcumus Info Exchange | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Alcumus Info Exchange."
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


# Tutorial: Integración de Azure Active Directory con Alcumus Info Exchange

El objetivo de este tutorial es mostrar cómo integrar Alcumus Info Exchange con Azure Active Directory (Azure AD).<br>La integración de Alcumus Info Exchange con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Alcumus Info Exchange. 
- Puede permitir que los usuarios inicien sesión automáticamente en Alcumus Info Exchange (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Alcumus Info Exchange, necesita los siguientes elementos:

- Una suscripción de [Azure AD](https://azure.microsoft.com/)
- Una suscripción habilitada para inicio de sesión único en [Alcumus Info Exchange](http://www.alcumusgroup.com/)


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Alcumus Info Exchange de la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Alcumus Info Exchange de la galería
Para configurar la integración de Alcumus Info Exchange en Azure AD, deberá agregar Alcumus Info Exchange desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Alcumus Info Exchange desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**. <br><br>![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorio. <br><br>![Aplicaciones][2]<br>

4. Haga clic en **Agregar** en la parte inferior de la página. <br><br>![Aplicaciones][3]<br>

5. En el cuadro de diálogo **Qué desea hacer**, haga clic en **Agregar una aplicación de la galería**. <br><br>![Aplicaciones][4]<br>

6. En el cuadro de búsqueda, escriba **Alcumus Info Exchange**. <br><br>![Aplicaciones][5]<br>

7. En el panel de resultados, seleccione **Alcumus Info Exchange** y después haga clic en **Completar** para agregar la aplicación. <br><br>![Aplicaciones][400]<br>



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Alcumus Info Exchange con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Alcumus Info Exchange para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Alcumus Info Exchange.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Alcumus Info Exchange.
 
Para configurar y probar el inicio de sesión único de Azure AD con Alcumus Info Exchange, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)**: para tener un homólogo de Britta Simon en Alcumus Info Exchange que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD y configurar el inicio de sesión único en la aplicación Alcumus Info Exchange.<br>

**Para configurar el inicio de sesión único de Azure AD con Alcumus Info Exchange, realice los pasos siguientes:**

1. En el Portal de Azure AD, en la página de integración de aplicaciones de **Alcumus Info Exchange**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.<br><br> ![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Alcumus Info Exchange?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y después haga clic en **Siguiente**.<br><br> ![Inicio de sesión único de Azure AD][7]

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Inicio de sesión único de Azure AD][8]<br>
 
     3\.1 En el cuadro de texto **URL de respuesta**, escriba la dirección URL de consumidor que configuró el equipo de soporte técnico de Alcumus Info Exchange.

     >[AZURE.NOTE] Si desconoce el valor correcto, póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

     3\.2. Haga clic en **Siguiente**.
 
4. En la página **Configuración de inicio de sesión único en Alcumus Info Exchange**, haga clic en **Descargar metadatos** y después guarde el archivo de metadatos localmente en el equipo.<br><br>![Qué es Azure AD Connect][9]

5. Póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), proporcióneles el archivo de metadatos y hágales saber que deberían habilitar el SSO.


6. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Qué es Azure AD Connect][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Qué es Azure AD Connect][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en **Active Directory**.<br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **crear**. <br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:<br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

  
 
### Creación de un usuario de prueba de Alcumus Info Exchange

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en Alcumus Info Exchange.

**Para crear una usuaria llamada Britta Simon en Alcumus Info Exchange, realice los pasos siguientes:**

1. Póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Alcumus Info Exchange. <br><br>![Asignar usuario][200]

**Para asignar a una usuaria llamada Britta Simon a Alcumus Info Exchange, realice los pasos siguientes:**

1. En el Portal de Azure, abra la vista de aplicaciones; para ello, en la vista de directorio, haga clic en **Aplicaciones** en el menú de la parte superior. <br><br><br>![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Alcumus Info Exchange**. <br><br>![Asignar usuario][202]
1. En el menú de la parte superior, haga clic en **Usuarios**.<br> <br><br>![Asignar usuario][203]
1. En la lista de usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el Panel de acceso.<br> Al hacer clic en el icono de Alcumus Info Exchange en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Alcumus Info Exchange.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=AcomDC_0128_2016-->