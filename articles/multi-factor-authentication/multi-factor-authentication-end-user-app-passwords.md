<properties 
	pageTitle="¿Qué son las contraseñas de aplicación en Azure MFA?" 
	description="Esta página ayudará a los usuarios a entender qué son las contraseñas de aplicación y para qué se utilizan con respecto a Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>



# ¿Qué son las contraseñas de aplicación en Azure Multi-Factor Authentication?

Determinadas aplicaciones sin explorador, como el cliente de correo electrónico nativo de Apple que usa Exchange Active Sync, actualmente no admiten la autenticación multifactor. La autenticación multifactor se habilita por usuario. Esto significa que si un usuario se ha habilitado para la autenticación multifactor y está intentando utilizar aplicaciones sin explorador, no podrá hacerlo. Una contraseña de aplicación permite que pueda hacerlo.

>[AZURE.NOTE] Autenticación moderna para los clientes de Office 2013
>
> Los clientes de Office 2013 (incluye Outlook) ahora admiten nuevos protocolos de autenticación que pueden habilitarse para admitir Multi-Factor Authentication. Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013. Para obtener más información, consulte [Anuncio de la vista previa pública de la autenticación moderna de Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Cómo utilizar las contraseñas de aplicación

Las siguientes son algunas cosas que es necesario recordar sobre el uso de contraseñas de aplicación.

- La contraseña real se genera automáticamente y no la proporciona el usuario. Esto es así porque una contraseña generada automáticamente es más difícil de adivinar y por tanto es más segura.
- Actualmente hay un límite de 40 contraseñas por usuario. Si intenta crear más una vez que haya alcanzado el límite, se le pedirá que elimine una de las contraseñas de aplicación existentes para poder crear una nueva.
- Se recomienda crear contraseñas de aplicación para cada dispositivo y no para cada aplicación. Por ejemplo, puede crear una contraseña de aplicación para su equipo portátil y utilizar esa contraseña de aplicación para todas las aplicaciones de ese equipo portátil.
- Lla primera vez que inicie sesión se le da una contraseña de aplicación. Si necesita otras adicionales, puede crearlas.
 
![Configuración](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Una vez que tenga una contraseña de aplicación, utilícela en lugar de la contraseña original para estas aplicaciones sin explorador. Por ejemplo, si está utilizando la autenticación multifactor y el cliente de correo electrónico nativo de Apple en su teléfono. Utilice la contraseña de aplicación para omitir la autenticación multifactor y poder seguir trabajando.

## Creación y eliminación de contraseñas de aplicación
Durante el inicio de sesión primero, se proporciona una contraseña de aplicación que puede utilizar. Además, también puede crear y eliminar contraseñas de aplicación más adelante. La forma de hacerlo depende de cómo use la autenticación multifactor. Elija la opción que le sea más aplicable.

Cómo usa la autenticación multifactor|Descripción
:------------- | :------------- | 
[La uso con Office 365](#creating-and-deleting-app-passwords-with-office-365)| Esto significa que tendrá que crear las contraseñas de aplicación a través del portal de Office 365.
[No lo sé](#creating-and-deleting-app-passwords-with-myapps-portal)|Esto significa que tendrá que crear las contraseñas de aplicación a través de [https://myapps.microsoft.com](https://myapps.microsoft.com)
[La uso con Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Esto significa que tendrá que crear las contraseñas de aplicación a través del Portal de Azure.

## Creación y eliminación de contraseñas de aplicación con Office 365 

Si usa la autenticación multifactor con Office 365, le interesará crear y eliminar contraseñas de aplicación a través del portal de Office 365.

### Creación de contraseñas de aplicación en el portal de Office 365
--------------------------------------------------------------------------------

1. Inicie sesión en el [Portal de Office 365](https://login.microsoftonline.com/).
2. En la esquina superior derecha, seleccione el widget y elija la configuración de Office 365.
3. Haga clic en Comprobación de seguridad adicional.
4. A la derecha, haga clic en el vínculo que dice **Actualizar los números de teléfono usados para la seguridad de cuenta.** ![Configuración](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Esto le llevará a la página que le permitirá cambiar la configuración. ![Nube](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En la parte superior, al lado de la comprobación de seguridad adicional, haga clic en **contraseñas de aplicación.**
7. Haga clic en **Crear**. ![Nube](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**. ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación. ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### Para eliminar contraseñas de aplicación en el portal de Office 365
--------------------------------------------------------------------------------


1. Inicie sesión en el [Portal de Office 365](https://login.microsoftonline.com/).
2. En la esquina superior derecha, seleccione el widget y elija la configuración de Office 365.
3. Haga clic en Comprobación de seguridad adicional.
4. A la derecha, haga clic en el vínculo que dice **Actualizar los números de teléfono usados para la seguridad de cuenta.** ![Configuración](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Esto le llevará a la página que le permitirá cambiar la configuración. ![Nube](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En la parte superior, al lado de la comprobación de seguridad adicional, haga clic en **contraseñas de aplicación.**
7. Junto a la contraseña de la aplicación que desea quitar, seleccione **Eliminar**. ![Eliminar una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Para confirmar la eliminación, haga clic en **Sí**. ![Confirmar la eliminación](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Una vez que la contraseña se elimina, puede hacer clic en **cerrar**. ![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Creación y eliminación de contraseñas de aplicación con el portal de Myapps
Si no está seguro de cómo usar la autenticación multifactor, siempre puede cambiar la configuración mediante el portal de Myapps.

### Para crear una contraseña de aplicación mediante el portal de Myapps

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. En la parte superior, seleccione el perfil.
3. Seleccione Comprobación de seguridad adicional. ![Nube](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Esto le llevará a la página que le permitirá cambiar la configuración. ![Configuración](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En la parte superior, al lado de la comprobación de seguridad adicional, haga clic en **contraseñas de aplicación.**
6. Haga clic en **Crear**. ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**. ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación. ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### Para eliminar una contraseña de aplicación mediante el portal de Myapps

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. En la parte superior, seleccione el perfil.
3. Seleccione Comprobación de seguridad adicional. ![Nube](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Esto le llevará a la página que le permitirá cambiar la configuración. ![Configuración](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En la parte superior, al lado de la comprobación de seguridad adicional, haga clic en **contraseñas de aplicación.**
6. Junto a la contraseña de la aplicación que desea quitar, seleccione **Eliminar**. ![Eliminar una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Para confirmar la eliminación, haga clic en **Sí**. ![Confirmar la eliminación](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Una vez que la contraseña se elimina, puede hacer clic en **cerrar**. ![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Creación de contraseñas de aplicación en el Portal de Azure

Si utiliza la autenticación multifactor con Azure, le interesará crear contraseñas de aplicación a través del Portal de Azure.

### Creación de contraseñas de aplicación en el Portal de Azure

1. Inicie sesión en el Portal de administración de Azure.
2. En la parte superior, haga clic con el botón derecho en su nombre de usuario y seleccione Comprobación de seguridad adicional.
3. En la página de proofup, en la parte superior, seleccione las contraseñas de aplicación
4. Haga clic en **Crear**.
5. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**.
6. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación. ![Nube](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### Para eliminar contraseñas de aplicación en el Portal de Azure

1. Inicie sesión en el Portal de administración de Azure.
2. En la parte superior, haga clic con el botón derecho en su nombre de usuario y seleccione Comprobación de seguridad adicional.
3. En la parte superior, al lado de la comprobación de seguridad adicional, haga clic en **contraseñas de aplicación.**
4. Junto a la contraseña de la aplicación que desea quitar, haga clic en **Eliminar**.
5. Para confirmar la eliminación, haga clic en **Sí**.
6. Una vez que la contraseña se elimina, puede hacer clic en **cerrar**. ![Cerrar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_0218_2016-->