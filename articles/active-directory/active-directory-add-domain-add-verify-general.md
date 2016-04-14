<properties
	pageTitle="Incorporación y comprobación de un nombre de dominio personalizado en Azure Active Directory | Microsoft Azure"
	description="Cómo agregar los dominios existentes a Azure Active Directory como parte de la introducción a Azure AD. Configure el dominio personalizado para sincronizar la información de la cuenta del usuario con la infraestructura de identidad local."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Incorporación y comprobación de un nombre de dominio personalizado en Azure Active Directory

Para agregar un nombre de dominio personalizado y comprobarlo para su uso con Azure Active Directory, debe completar los pasos siguientes.

1.  Realice una de las operaciones siguientes:

    -   [Agregar un nombre de dominio personalizado que se federará con la instancia de Active Directory local](#add-a-custom-domain-name-that-will-be-federated)

    -   [Agregar un nombre de dominio personalizado que no se federará con la instancia de Active Directory local](#add-a-custom-domain-name-that-will-not-be-federated)

2.  Compruebe el nombre de dominio personalizado.

    -   Agregue las entradas DNS que utilizará Azure AD para comprobar que su organización posee el nombre de dominio personalizado en el registrador de nombres de dominio para su dominio.

    -   Compruebe el dominio en Azure AD.

## Incorporación de un nombre de dominio personalizado que se federará

Para obtener más información acerca de la integración de directorios locales mediante Azure AD Connect, consulte

**Para agregar un nombre de dominio personalizado a su directorio de Azure AD**

1.  Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta que tenga privilegios de administrador global en Azure AD.

2.  Seleccione Active Directory.

3.  Abra el directorio.

4.  Seleccione la pestaña **Dominios**.

5.  En la barra de comandos, haga clic en **Agregar**.

6.  Escriba el nombre del dominio personalizado. Asegúrese de incluir la extensión .com.

7.  Active la casilla **Pretendo configurar este dominio para el inicio de sesión único con mi Active Directory local**.

8.  Haga clic en **Agregar**.

## Incorporación de un nombre de dominio personalizado que no se federará

La mayoría de los nombres de dominio personalizado son dominios de segundo nivel "contoso.com".

**Para agregar el nombre de dominio personalizado a su directorio de Azure AD**

1.  Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta que tenga privilegios de administrador global en Azure AD.

2.  Seleccione Active Directory.

3.  Abra el directorio.

4.  Seleccione la pestaña **Dominios**.

5.  En la barra de comandos, haga clic en **Agregar**.

6.  Escriba el nombre del dominio personalizado. Asegúrese de incluir la extensión .com.

7.  Asegúrese de dejar desactivada la casilla **Pretendo configurar este dominio para el inicio de sesión único con mi Active Directory local**.

8.  Seleccione **Agregar**.

9.  Compruebe que el dominio se ha agregado al directorio.

## Comprobación de un dominio en cualquier registrador de nombres de dominio

Para comprobar el dominio, se crea un registro DNS en el registrador de nombres de dominio, o donde se hospede su DNS y, a continuación, Azure AD usa ese registro para confirmar que usted es el propietario del dominio. [Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

Si ya tiene el nombre de dominio registrado con un registrador de nombres de dominio, los registros DNS necesarios ya existen.

Si se ha agregado un dominio personalizado pero este aún no se ha comprobado, el estado se mostrará como **No comprobado**.

## Comprobación de un nombre de dominio personalizado que no se federará con un directorio local
Después de haber agregado correctamente todos los registros que ha creado para su dominio a través del sistema DNS en el registrador de dominios, haga lo siguiente:

1.  En Azure Active Directory, en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Dominios**.

2.  En la lista **Dominios**, busque el dominio que se va a comprobar y, a continuación, según el portal que utilice, haga clic en **Haga clic para comprobar el dominio** o en **Comprobar**.

3.  Siga las instrucciones para completar el proceso de comprobación.

    -   Si la comprobación del dominio se realiza correctamente, se le notificará que el dominio se ha agregado a su cuenta.

    -   Si se produce un error en la comprobación del dominio, los cambios que realizó en el registrador de dominios tardarán más tiempo en propagarse. Cancele la comprobación e inténtelo de nuevo más tarde.

Si han pasado más de 72 horas desde que hizo los cambios en su dominio, inicie sesión en el sitio web del registrador de dominios y compruebe que especificó correctamente la información del alias. Si escribió incorrectamente la información, debe quitar el registro DNS incorrecto y crear uno nuevo con la información correcta.

## Comprobación del dominio personalizado para la federación con su directorio local

1.  Descargue y ejecute Azure AD Connect. La herramienta Azure AD Connect le [pedirá que agregue las entradas DNS que le proporciona](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

## Nombres de dominio de tercer nivel

Puede usar dominios de tercer nivel, como "europe.contoso.com", con Azure AD. Para agregar y utilizar un dominio de tercer nivel:

1.  Agregue y compruebe el dominio de segundo nivel "contoso.com".

2.  Agregue subdominios, como "europe.contoso.com", a Azure AD. Cuando se agrega un subdominio de un dominio de segundo nivel comprobado, el dominio de tercer nivel se comprueba automáticamente por Azure AD. No es necesario agregar más entradas DNS.

Estos pasos pueden realizarse también mediante PowerShell y Graph.

Una vez comprobado el dominio, puede configurarlo para que funcione con sus cuentas.

## Pasos siguientes

- [Using custom domain names to simplify the sign-in experience for your users (Uso de nombres de dominio personalizado para simplificar la experiencia de inicio de sesión para los usuarios)](active-directory-add-domain.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Assign users to a custom domain (Asignación de usuarios a un dominio personalizado)](active-directory-add-domain-add-users.md)
- [Change the DNS registrar for your custom domain name (Cambio del registrador DNS para el nombre de dominio personalizado)](active-directory-add-domain-change-registrar.md)
- [Delete a custom domain in Azure Active Directory (Eliminación de un dominio personalizado en Azure Active Directory)](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->