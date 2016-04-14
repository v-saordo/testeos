En esta sección agregará dos nuevos usuarios a su directorio junto con el nuevo grupo de ventas. Uno de los usuarios se incluirá en el grupo de ventas y el otro no.

### Creación de usuarios


1. En el [Portal de Azure clásico](https://manage.windowsazure.com), vaya hasta el directorio que configuró previamente para la autenticación cuando completó el tutorial para agregar la autenticación a una aplicación.
2. Haga clic en **Usuarios** en la parte superior de la página. A continuación, haga clic en el botón **Agregar usuario** de la parte inferior. 
3. Complete los cuadros de diálogo de nuevo usuario para crear un usuario llamado **Roberto**. Anote la contraseña temporal de ese usuario. 
4. Cree otro usuario llamado **David**. Anote la contraseña temporal de ese usuario.
5. Los nuevos usuarios tendrán un aspecto similar al que se muestra a continuación.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)


### Creación del grupo de ventas


1. En la página de directorios, haga clic en **Grupos** en la parte superior de la página. A continuación, haga clic en el botón **Agregar grupo** de la parte inferior. 
2. Escriba **Ventas** como nombre del grupo y presione el botón de finalizar en el cuadro de diálogo para crear el grupo. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Incorporación de usuarios al grupo de ventas.


1. Haga clic en **Grupos** en la parte superior de la página de directorios. A continuación, haga clic en el grupo **Ventas** para ir a la página del grupo de ventas. 
2. En la página del grupo de ventas, haga clic en **Agregar miembros**. Agregue el usuario llamado **Roberto** al grupo de ventas. El usuario llamado **David** no será miembro del grupo.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. En la página Grupo de ventas, haga clic en **Propiedades** y copie el **Id. de objeto** para el grupo de ventas en la parte inferior de la página.

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Vuelva a la página de configuración de su servicio móvil y agregue el Id. de objeto en forma de valor de la aplicación llamado **AAD\_SALES\_GROUP\_ID**. Este tutorial usa el Id. de objeto del grupo como un valor de la aplicación, en lugar de buscar el Id. basándose en el nombre del grupo. Esto se debe a que el nombre del grupo puede cambiar, mientras que el Id. no varía.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!---HONumber=AcomDC_1203_2015-->