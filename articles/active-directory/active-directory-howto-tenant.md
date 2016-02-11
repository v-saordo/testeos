<properties
	pageTitle="Obtención de un inquilino de Azure AD | Microsoft Azure"
	description="Obtenga un inquilino de Azure Active Directory para el registro y la creación de aplicaciones."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Obtención de un inquilino de Azure Active Directory

En Azure Active Directory (Azure AD), un [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) es un representante de una organización. Se trata de una instancia dedicada del servicio de Azure AD que recibe una organización y que posee cuando registra un servicio en la nube de Microsoft, como Azure, Microsoft InTune u Office 365. Cada inquilino de Azure AD es distinto e independiente de los demás inquilinos de Azure AD.

Un inquilino aloja los usuarios de una empresa y la información sobre ellos: sus contraseñas, datos de perfil de usuario, permisos, etc. También contiene grupos, aplicaciones y otra información relativa a una organización y su seguridad.

Para permitir que los usuarios de Azure AD inicien sesión en su aplicación, debe registrar la aplicación en un inquilino que elija. La publicación de una aplicación en un inquilino de Azure AD es **totalmente gratis**. De hecho, la mayoría de programadores creará varios inquilinos y aplicaciones para fines de experimentación, desarrollo, almacenamiento provisional y prueba. Las organizaciones que se registren y consuman la aplicación pueden elegir opcionalmente adquirir licencias si desean aprovechar las características avanzadas de directorio.

Por lo tanto, ¿cómo obtiene un inquilino de Azure AD? El proceso puede ser poco diferente si:

- [Dispone de una suscripción de Office 365 existente](#use-an-existing-office-365-subscription)
- [Tiene una suscripción de Azure asociada a una cuenta Microsoft](#use-an-msa-azure-subscription)
- [Dispone de una suscripción de Azure existente asociada a una cuenta organizativa](#use-an-organizational-azure-subscription)
- [No dispone de ninguna de las opciones anteriores y desea comenzar desde el principio](#start-from-scratch)

## Uso de una suscripción de Office 365 existente
Si dispone de una suscripción de Office 365 existente, pero no tiene una suscripción de Azure (y no se puede iniciar sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)), siga [estas instrucciones](https://technet.microsoft.com/library/dn832618.aspx) para obtener acceso al inquilino de Azure AD.

## Uso de una suscripción de MSA Azure
Si se ha registrado anteriormente en una suscripción de Azure con una cuenta Microsoft individual, ya dispone de un inquilino. En el [Portal de administración de Azure](https://manage.windowsazure.com), debe encontrar un inquilino con el nombre "Inquilino predeterminado" en "Todos los elementos" y "Active Directory". Pueden usa este inquilino como considere oportuno, pero es posible que desee crear una cuenta de administrador organizativo.

Para hacerlo, siga estos pasos: También es posible que quiera crear a un nuevo inquilino y crear un administrador en ese inquilino siguiendo un proceso similar.

1.	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) con su cuenta individual.
2.	Vaya a la sección "Active Directory" del portal (que se encuentra en la barra de navegación izquierda).
3.	Seleccione la entrada "Directorio predeterminado" en la lista de directorios disponibles.
4.	Haga clic en el vínculo Usuarios en la parte superior de la página. Verá un único usuario en la lista con el valor "Cuenta de Microsoft" en la columna Con origen en.
5.	En la parte inferior de la página, haga clic en "Agregar usuario".
6.	En el formulario Agregar usuario, proporcione la siguiente información:
    - Tipo de usuario: usuario nuevo en su organización
    - Nombre de usuario: (elija un nombre de usuario para este administrador)
    - Nombre/Apellidos/Nombre para mostrar: (elija valores adecuados)
    - Rol: administrador global
    - Dirección de correo electrónico alternativa: (especifique valores adecuados)
    - Opcional: Habilitación de Multi-Factor Authentication
    - Por último, haga clic en el botón verde "CREAR" para finalizar la creación del usuario (y mostrar la contraseña temporal).
7.	Cuando haya completado el formulario de adición de usuario y reciba la contraseña temporal para el nuevo usuario administrativo, asegúrese de registrar esta contraseña, puesto que tendrá que iniciar sesión con este nuevo usuario para cambiar la contraseña. También puede enviar la contraseña directamente al usuario mediante un correo electrónico alternativo.
8.	Para cambiar la contraseña temporal, inicie sesión en https://login.microsoftonline.com con esta nueva cuenta de usuario y cambie la contraseña cuando se solicite.


## Uso de una suscripción de Azure organizativa
Si se ha registrado anteriormente en una suscripción de Azure con la cuenta organizativa, ya dispone de un inquilino. En el [Portal de administración de Azure](https://manage.windowsazure.com), debe encontrar un inquilino con el nombre "Todos los elementos" y "Active Directory". Pueden usar a este inquilino como considere oportuno. También puede crear a un nuevo inquilino mediante el botón "Nuevo" en la esquina inferior izquierda del portal.


## Comienzo desde cero
Si todo lo anterior es un galimatías para usted, no se preocupe. Simplemente visite [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para registrarse en Azure con una nueva organización. Cuando haya completado el proceso, tendrá su propio inquilino de Azure AD con el nombre de dominio que elija durante el registro. En el [Portal de administración de Azure](https://manage.windowsazure.com), puede encontrar el inquilino dirigiéndose a "Active Directory" en panel de navegación de la izquierda.

Como parte del proceso de registro en Azure, se le solicitará que proporcione la información de tarjeta de crédito. Puede continuar con confianza: no se le cobrará por publicar aplicaciones en Azure AD o crear nuevos inquilinos.

<!---HONumber=Oct15_HO3-->