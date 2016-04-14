<properties
   pageTitle="Crear una identidad profesional o educativa en AAD | Microsoft Azure"
   description="Aprenda a crear una identidad profesional o educativa en Azure Active Directory para usar con el Administrador de recursos y los modelos de implementación clásica."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Crear una identidad profesional o educativa en Azure Active Directory

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si creó una cuenta de Azure personal o tiene una suscripción a MSDN personal y creó la cuenta de Azure para beneficiarse de los créditos de Azure de MSDN, usó una identidad de *cuenta Microsoft* para crearla. Muchas características excelentes de Azure (las [plantillas de grupo de recursos](../resource-group-overview.md) son un ejemplo) requieren una cuenta profesional o educativa (una identidad administrada por Azure Active Directory) para que funcione. Puede seguir las instrucciones que se indican a continuación para crear una cuenta profesional o educativa porque, afortunadamente, una de las ventajas de su cuenta de Azure personal es que se incluye con un dominio de Azure Active Directory predeterminado que se puede usar para crear una nueva cuenta profesional o educativa que puede usar con las características de Azure que lo requieran.

Sin embargo, los cambios recientes hacen posible la administración de su suscripción con cualquier tipo de cuenta de Azure mediante el método de inicio de sesión interactivo `azure login` que se describe [aquí](../xplat-cli-connect.md). Puede usar ese mecanismo o seguir las instrucciones que se indican a continuación.

> [AZURE.NOTE]Si se le asignó un nombre de usuario y una contraseña por un administrador, hay muchas posibilidades de que ya disponga de un id. profesional o educativo (también denominado en ocasiones *id. de organización*). Si es así, puede comenzar inmediatamente a usar su cuenta de Azure para acceder a recursos de Azure que lo requieran. Si descubre que no puede usar esos recursos, puede que necesite volver a este artículo para obtener ayuda. Para más información, vea [Cuentas que puede usar para iniciar sesión](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) y [Cómo se relaciona una suscripción de Azure con Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Los pasos son sencillos. Necesita encontrar la identidad con la que ha iniciado sesión en el portal de Azure clásico, descubrir su dominio de Azure Active Directory predeterminado y agregarle un nuevo usuario como coadministrador de Azure.

## Buscar el directorio predeterminado en el Portal de Azure clásico

Empiece por iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) con su identidad de la cuenta de Microsoft personal. Cuando haya iniciado sesión, desplácese hacia abajo en el panel azul del lado izquierdo y haga clic en **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Empecemos buscando información sobre su identidad en Azure. Debería ver algo parecido a lo siguiente en el panel principal, que muestra que tiene un directorio predeterminado.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

Vamos a averiguar más información sobre él. Haga clic en la fila de directorio predeterminada, lo que le lleva a las propiedades de directorio predeterminadas.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Para ver el nombre de dominio predeterminado, haga clic en **DOMINIOS**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Aquí debería ver que cuando se creó la cuenta de Azure, Azure Active Directory creó un dominio predeterminado personal que es un valor de hash (un número generado a partir de una cadena de texto) de su id. personal usado como subdominio de onmicrosoft.com. Ese es el dominio en el que va a agregar ahora un nuevo usuario.

## Creación de un nuevo usuario en el dominio predeterminado

Haga clic en **USUARIOS** y busque su cuenta personal única. Debería ver en la columna **CON ORIGEN EN** que es una **cuenta de Microsoft**. Queremos crear un usuario en su dominio .onmicrosoft.com de Azure Active Directory predeterminado.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Vamos a seguir [estas instrucciones](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) en los pasos siguientes, pero con un ejemplo concreto.

En la parte inferior de la página, haga clic en **+AGREGAR USUARIO**. En la página que aparece, escriba el nuevo nombre de usuario y haga del **Tipo de usuario** un **Nuevo usuario de la organización**. En este ejemplo, el nuevo nombre de usuario es `ahmet`. Seleccione el dominio predeterminado que detectó anteriormente como el dominio para la dirección de correo electrónico de ahmet. Haga clic en la siguiente flecha cuando haya terminado.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Agregue más detalles para Ahmet, pero asegúrese de seleccionar el valor de **ROL** adecuado. Es sencillo usar **Administrador global** para asegurarse de que todo funciona, pero si puede utilizar un rol menor, es una buena idea. En este ejemplo se usa el rol **Usuario**. (Obtenga más información en [Permisos del administrador por rol](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) No habilite la autenticación multifactor a menos que quiera usar la autenticación multifactor para cada registro de la operación. Haga clic en la siguiente flecha cuando haya terminado.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Haga clic en el botón **crear** para generar y mostrar una contraseña temporal para Ahmet.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Copie la dirección de correo electrónico del nombre de usuario o use **ENVIAR CONTRASEÑA EN CORREO ELECTRÓNICO**. Necesitará la información para iniciar sesión en breve.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

Ahora debe ver el nuevo usuario **Ahmet el desarrollador**, con origen en Azure Active Directory. Ha creado la nueva identidad profesional o educativa con Azure Active Directory. Sin embargo, esta identidad no tiene permisos todavía para usar recursos de Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

Si usa **ENVIAR CONTRASEÑA EN CORREO ELECTRÓNICO**, se enviará el siguiente tipo de correo electrónico.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Agregar derechos de coadministrador de Azure para suscripciones

Ahora debe agregar el nuevo usuario como coadministrador de su suscripción para que el nuevo usuario pueda iniciar sesión en el Portal de administración. Para ello, en el panel inferior izquierdo, haga clic en **Configuración**.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

En el área de configuración principal, haga clic en **ADMINISTRADORES** de la parte superior y solo debería ver su identidad de la cuenta Microsoft personal. En la parte inferior de la página, haga clic en **+ AGREGAR** para especificar un coadministrador. Aquí, escriba la dirección de correo electrónico del nuevo usuario que haya creado, incluido el dominio predeterminado. Como se muestra en la siguiente captura de pantalla, aparecerá una marca de verificación verde junto al usuario para el directorio predeterminado. Recuerde seleccionar todas las suscripciones que le gustaría que este usuario pudiera administrar.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

Cuando haya terminado, debería ahora ver dos usuarios, incluida la nueva identidad de coadministrador. Cierre sesión en el portal.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Inicio de sesión y cambio de la contraseña del nuevo usuario

Inicie sesión como el nuevo usuario que ha creado.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Se le pedirá inmediatamente que cree una nueva contraseña.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Al realizarse la operación correctamente, aparecerá algo similar a lo siguiente:

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Pasos siguientes

Ahora puede usar su nueva identidad de Azure Active Directory para usar [plantillas de grupo de recursos de Azure](../xplat-cli-azure-resource-manager.md).

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=AcomDC_1217_2015-->