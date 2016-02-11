<properties 
    pageTitle="Tutorial: configuración de Workday para la sincronización de entrada | Microsoft Azure" 
    description="Aprenda a usar Workday como origen de datos de identidad para Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />


#Tutorial: configuración de Workday para la sincronización de entrada


El objetivo de este tutorial es mostrar los pasos que se deben realizar en Workday y Azure AD para importar contactos de Workday a Azure AD.

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción válida a Azure AD Premium
-   Un inquilino en Workday
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en Workday 


2. Creación de un usuario del sistema de integración


3. Creación de un grupo de seguridad


4. Asignación del usuario del sistema de integración al grupo de seguridad


5. Configuración de las opciones del grupo de seguridad


6. Activación de cambios en directiva de seguridad


7. Configuración de la importación de usuarios en Azure AD



##Habilitación de la integración de aplicaciones en Workday
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Workday.

###Siga estos pasos para habilitar la integración de aplicaciones para Workday:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Agregar aplicación")

  
5. En el cuadro de búsqueda, escriba **Workday**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Agregar una aplicación de la galería")

6. En el panel de resultados, seleccione Workday y luego haga clic en Completar para agregar la aplicación.

    ![Galería de aplicaciones](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galería de aplicaciones")





## Creación de un usuario del sistema de integración

1. En el **Workday Workbench** (Área de trabajo de Workbench), escriba create user (crear usuario) en el cuadro de búsqueda y luego haga clic en **Create Integration System User** (Crear usuario del sistema de integración). <br><br> ![Crear usuario](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Crear usuario")



2. Para completar la tarea **Create Integration System User** (Crear usuario del sistema de integración), especifique el nombre de usuario y la contraseña del nuevo usuario del sistema de integración. Deje la opción Require New Password at Next Sign In (Solicitar una nueva contraseña en el siguiente inicio de sesión) sin activar, ya que este usuario iniciará sesión mediante programación. <br> Deje Minutos de tiempo de espera de la sesión en 0 (su valor predeterminado), con el fin de evitar que las sesiones del usuario agoten el tiempo de espera de manera prematura. <br><br> ![Crear usuario de sistema de integración](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Crear usuario de sistema de integración")
 




## Creación de un grupo de seguridad

Para el escenario que se describe en este tutorial, es preciso crear un grupo de seguridad del sistema de integración sin restricciones y asignarle el usuario.


1. Escriba create security group (crear grupo de seguridad) en el cuadro de búsqueda y luego haga clic en **Create Security Group** (Crear grupo de seguridad). <br><br> ![Crear grupos de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Crear grupos de seguridad")
 

2. Complete la tarea Create Security Group (Crear grupo de seguridad). En la lista desplegable Tipo de grupo de seguridad con inquilinos, seleccione Grupo de seguridad del sistema de integración: sin restringir para crear un grupo de seguridad al que se agregarán los miembros de forma explícita. <br><br> ![Crear grupos de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Crear grupos de seguridad")
 



## Asignación del usuario del sistema de integración al grupo de seguridad

1. Escriba edit security group (editar grupo de seguridad) en el cuadro de búsqueda y luego haga clic en **Edit Security Group** (Editar grupo de seguridad). <br><br> ![Editar grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Editar grupo de seguridad")
 
 

2. Busque y seleccione el nuevo grupo de seguridad de integración por nombre. <br><br> ![Editar grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Editar grupo de seguridad") Editar grupo de seguridad

3. Agregue el nuevo usuario del sistema de integración al nuevo grupo de seguridad. <br><br> ![Grupo de seguridad del sistema](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Grupo de seguridad del sistema")




## Configuración de las opciones del grupo de seguridad

En este paso, se conceden al nuevo grupo de seguridad permisos para las operaciones **Get** y **Put** en los objetos protegidos por las siguientes directivas de seguridad del dominio:

- External Account Provisioning

- Worker Data: Public Worker Reports

- Worker Data: All Positions

- Worker Data: Current Staffing Information

- Worker Data: Business Title on Worker Profile
 
   

1. Escriba domain security policies (directivas de seguridad de dominio) en el cuadro de búsqueda y luego haga clic en el vínculo Domain Security Policies for Functional Area (Directivas de seguridad de dominio para área funcional). <br><br> ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Directivas de seguridad de dominio")  
 

2. Busque el sistema y seleccione el área funcional **Sistema**. Haga clic en **Aceptar**. <br><br> ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Directivas de seguridad de dominio")


3. En la lista de directivas de seguridad del área funcional Sistema, expanda Administración de seguridad y seleccione la directiva de seguridad de dominio, Aprovisionamiento de cuentas externas. <br><br> ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Directivas de seguridad de dominio")


4. Haga clic en **Edit Permissions** (Editar permisos) y luego, en la página de diálogo **Editar permisos**, agregue el nuevo grupo de seguridad a la lista de grupos de seguridad con permisos de integración de **Get** y **Put**. <br><br> ![Editar permisos](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Editar permisos")

 

5. Repita el paso 1 anterior para volver a la pantalla para seleccionar las áreas funcionales y, esta vez, busque personal, seleccione el área funcional Personal y haga clic en el botón **Aceptar**.<br><br> ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Directivas de seguridad de dominio")
 

6. En la lista de directivas de seguridad del área funcional Staffing (Personal), expanda Worker Data: Staffing y repita el paso 4 anterior en cada una de las restantes directivas de seguridad:

     - Worker Data: Public Worker Reports

     - Worker Data: All Positions

     - Worker Data: Current Staffing Information

     - Worker Data: Business Title on Worker Profile


<br><br> ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Directivas de seguridad de dominio")







## Activación de cambios en directiva de seguridad


1. Escriba activar en el cuadro de búsqueda y luego haga clic en el vínculo Activar cambios en la directiva de seguridad pendientes. <br><br> ![Activar](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activar") 
 

2. Inicie la tarea Activar cambios en la directiva de seguridad pendientes, para lo que debe escribir un comentario para fines de auditoría y luego hacer clic en el botón **Aceptar**. <br><br> ![Activar seguridad pendiente](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activar seguridad pendiente")
 

3. Complete la tarea que aparece en la pantalla siguiente; para ello, active la casilla etiquetada como Confirmar y luego haga clic en **Aceptar**. <br><br> ![Activar seguridad pendiente](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activar seguridad pendiente")





## Configuración de la importación de usuarios en Azure AD

El objetivo de esta sección es describir cómo configurar Azure AD para importar contactos desde Workday.


### Para configurar la importación de usuarios en Azure AD, siga estos pasos:


1. En la página de integración de aplicaciones de **Workday**, haga clic en **Configurar importación de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento**.


2\. En la página **Configuración y credenciales de administrador**, realice los pasos siguientes y luego haga clic en **Siguiente**: <br><br> ![Configuración y credenciales de administrador](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Configuración y credenciales de administrador")
 
     2.1. In the Workday admin user name textbox, type the name of the user you have created in the Creating an integration system user section.

     2.2. In the Workday admin password textbox, type the password of the user you have created in the Creating an integration system user section.

     2.3. In the Workday tenant URL textbox, type the URL or your Workday tenant.


3. En la página **Probar conexión**, haga clic en **Iniciar prueba** para confirmar la conectividad y luego haga clic en **Siguiente**. <br><br> ![Probar conexión](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Probar conexión")  
 

4. En la página **Opciones de aprovisionamiento**, haga clic en **Siguiente**. <br><br> ![Opciones de aprovisionamiento](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opciones de aprovisionamiento")


5. En el cuadro de diálogo **Iniciar aprovisionamiento**, haga clic en **Completar**. <br><br> ![Iniciar aprovisionamiento](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Iniciar aprovisionamiento")
 

Ahora puede ir a la sección **Usuarios** y comprobar si se importó el usuario de Workday.



## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0114_2016-->