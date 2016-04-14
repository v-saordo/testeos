<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con HR2day by Merces | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y HR2day by Merces."
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
	ms.date="02/15/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con HR2day by Merces

El objetivo de este tutorial es mostrar cómo integrar HR2day by Merces con Azure Active Directory (Azure AD).<br>La integración de HR2day by Merces con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a HR2day by Merces.
- Puede permitir que los usuarios inicien sesión automáticamente en HR2day by Merces (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con HR2day by Merces, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en HR2day by Merces


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. <br> La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de HR2day by Merces desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de HR2day by Merces desde la galería
Para configurar la integración de HR2day by Merces en Azure AD, debe agregar HR2day by Merces desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar HR2day by Merces desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br> ![Active Directory][1]<br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, en la vista de directorios, haga clic en **Aplicaciones** en el menú superior.<br><br> ![Aplicaciones][2]<br>
4. Haga clic en **Agregar** en la parte inferior de la página.<br><br> ![Aplicaciones][3]<br>
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.<br><br> ![Aplicaciones][4]<br>
6. En el cuadro Buscar, escriba **HR2day by Merces**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)<br>
7. En el panel de resultados, seleccione **HR2day by Merces** y después haga clic en **Completar** para agregar la aplicación. <br><br>

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con HR2day by Merces con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de HR2day by Merces para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de HR2day by Merces.<br> Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en HR2day by Merces.

Para configurar y probar el inicio de sesión único de Azure AD con HR2day by Merces, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de HR2day by Merces](#creating-a-hr2day-by-merces-test-user)**: para tener un homólogo de Britta Simon en HR2day by Merces que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en HR2day by Merces con su cuenta de Azure AD usando la federación basada en el protocolo SAML.

La aplicación HR2day by Merces espera las aserciones SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de pantalla muestra un ejemplo. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) <br>

Antes de configurar la aserción SAML, debe ponerse en contacto con el equipo de soporte técnico de HR2day mediante [servicedesk@merces.nl](mailto:servicedesk@merces.nl) y solicitar el valor del atributo de identificador único para el inquilino. Necesita este valor para completar los pasos descritos en la sección siguiente.


**Para configurar el inicio de sesión único de Azure AD con HR2day by Merces, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **HR2day by Merces**, en el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) <br>

2. Para agregar las asignaciones de atributos necesarias, realice los pasos siguientes: <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) <br>


	 a. Haga clic en **agregar atributo de usuario**.

	 b. En el cuadro de texto **Nombre de atributo**, escriba **"ATTR\_LOGINCLAIM"**.

	 c. En la lista **Valor de atributo**, seleccione**Join()**.

	 d. En la lista **Cadena1**, seleccione **User.mail**.

	 e. En el cuadro de texto **Cadena2**, escriba el **identificador único** proporcionado por el equipo de HR2day.

	 f. En el cuadro de texto **Separador**, escriba **@**.

	 g. Haga clic en **Completo**.

  
3. Haga clic en **Aplicar cambios**.


1. En el menú de la parte superior, haga clic en **Inicio rápido** para abrir el cuadro de diálogo **Inicio rápido**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) <br>



1. Haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**. <br><br> ![Configurar inicio de sesión único][6]<br>

2. En la página **¿Cómo desea que los usuarios inicien sesión en HR2day by Merces?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) <br>

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) <br>


    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación HR2day by Merces con el siguiente patrón: **"https://<nombreDeInquilino>.force.com/<nombreDeInstancia>"**.


4. En la página **Configurar inicio de sesión único en HR2day by Merces**, siga estos pasos: <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) <br>

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para que se configure el SSO para la aplicación, póngase en contacto con su equipo de soporte técnico de HR2day by Merces a través de [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) y adjunte el archivo de certificado descargado a su correo electrónico. Además, proporcione la dirección URL de inicio de sesión único de SAML, la dirección URL de cierre de sesión y la dirección URL del emisor para que se puedan configurar para la integración de SSO.


> [AZURE.NOTE] Explique al equipo de Merces que esta integración necesita que el id. de entidad se establezca con este patrón ****https://hr2day.force.com/INSTANCENAME**.



6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. <br><br>![Inicio de sesión único de Azure AD][10]<br>

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. <br><br>![Inicio de sesión único de Azure AD][11]



### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.<br> En la lista Usuarios, seleccione **Britta Simon**.<br><br>![Creación de un usuario de Azure AD][20]<br>

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) <br>

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.<br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) <br>

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) <br>

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes: <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) <br>

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) <br>

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**. <br><br> ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) <br>

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes: <br><br>![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) <br>

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de HR2day by Merces

El objetivo de esta sección es crear una usuaria de prueba llamado Britta Simon en HR2day by Merces. Trabaje con el equipo de soporte técnico de HR2day by Merces para agregar los usuarios en la cuenta de HR2day.


> [AZURE.NOTE] Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de HR2day by Merces.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a HR2day by Merces. <br><br>![Asignar usuario][200] <br>

**Para asignar Britta Simon a HR2day by Merces, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior. <br><br>![Asignar usuario][201] <br>

2. En la lista de aplicaciones, seleccione **HR2day by Merces**. <br><br>![Configurar inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) <br>

1. En el menú de la parte superior, haga clic en **Usuarios**. <br><br>![Asignar usuario][203] <br>

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**. <br><br>![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.<br> Al hacer clic en el icono de HR2day by Merces en el panel de acceso, debería iniciar sesión automáticamente en su aplicación HR2day by Merces.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0218_2016-->