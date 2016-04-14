<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Uso de la CLI de Azure con el Administrador de recursos de Azure (ARM)

Para poder usar la CLI de Azure con los comandos y plantillas del Administrador de recursos para implementar los recursos de Azure y las cargas de trabajo mediante grupos de recursos, necesitará una cuenta de Azure (por supuesto). Si no tiene una cuenta, puede obtener [aquí una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Si todavía no tiene una cuenta de Azure, pero dispone de una suscripción a MSDN, puede obtener créditos gratuitos de Azure mediante la activación de sus [ventajas de suscriptor de MSDN aquí](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o puede usar la cuenta gratuita. Con cualquiera de estas opciones podrá obtener acceso a Azure.

### Paso 1: Comprobación de la versión de CLI de Azure

Para usar la CLI de Azure para comandos imperativos y plantillas de ARM, debe tener al menos la versión 0.8.17. Para comprobar su versión, escriba `azure --version`. Debería ver algo parecido a lo siguiente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Si necesita actualizar la versión de la CLI de Azure, consulte [CLI de Azure](https://github.com/Azure/azure-xplat-cli).

### Paso 2: Comprobación de que utiliza una identidad profesional o educativa con Azure

Solo puede utilizar el modo de comandos de ARM si utiliza un [inquilino de Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) o un [nombre principal de servicio](https://msdn.microsoft.com/library/azure/dn132633.aspx). (También se denominan *identificadores de la organización*).

Para ver si tiene uno, inicie sesión escribiendo `azure login` y usando el nombre de usuario y contraseña profesional o educativa cuando se le solicite. Si tiene uno, debe ver algo parecido a lo siguiente:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Si no ve esta opción, debe crear un nuevo inquilino (o nombre principal de servicio) con la identidad de la cuenta de Microsoft. (Esto suele ser el caso de las suscripciones personales a MSDN o suscripciones de evaluación gratuita). Para crear un identificador profesional o educativo desde la cuenta de Azure con un identificador de Microsoft, consulte [Asociación de un directorio de Azure AD con una nueva suscripción de Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Si cree que debería tener un identificador de la organización, quizá tenga que hablar con la persona que creó la cuenta por usted.

### Paso 3: Elección de la suscripción de Azure

Si tiene solo una suscripción en su cuenta de Azure, la CLI de Azure se asocia a esa suscripción de manera predeterminada. Si tiene más de una suscripción, debe seleccionar la suscripción que desea usar y escriba `azure account set <subscription id or name> true` donde _identificador de suscripción o nombre_ es el identificador de suscripción o el nombre de la suscripción con la que desea trabajar en la sesión actual.

Debe ver algo parecido a lo siguiente:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### Paso 4: Ubicación de CLI de Azure en modo ARM

Para utilizar el modo de administración de recursos de Azure (ARM) con la CLI de Azure, escriba `azure config mode arm`. Debe ver algo parecido a lo siguiente:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Puede volver a utilizar los comandos de administración de servicio de Azure escribiendo `azure config mode asm`.

<!---HONumber=AcomDC_0128_2016-->