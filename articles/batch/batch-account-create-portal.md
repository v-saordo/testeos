<properties
	pageTitle="Creación de una cuenta de Lote de Azure | Microsoft Azure"
	description="Aprenda a crear una cuenta de Lote de Azure en el Portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="danlep"/>



# Creación y administración de una cuenta de Lote de Azure en el Portal de Azure

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

En este artículo se muestra cómo usar el [Portal de Azure](https://portal.azure.com) para crear y administrar la cuenta de Lote de Azure, así como sus valores de configuración, por ejemplo, las claves de cuenta. Para autenticar todas las solicitudes de la API de Lote necesita una dirección URL de cuenta de Lote y una clave de acceso asociada. Y, todos los recursos de Lote (como grupos, trabajos y tareas) de la carga de trabajo de Lote se asocian con una cuenta de Lote específica.

>[AZURE.NOTE]En la actualidad el Portal de vista previa admite características de administración de cuentas de Lote y visualización de algunos recursos de cuenta. Las características completas del servicio Lote están disponibles para los desarrolladores a través de las API de Lote.

## Crear una cuenta de lote

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Nuevo** > **Proceso** > **Servicio de Lote**.

	![Lote en Marketplace][marketplace_portal]

3. Revise la información y luego haga clic en **Crear**.

4. En la hoja **Nueva cuenta de Lote**, escriba la siguiente información:

	a. En **Nombre de la cuenta**, escriba un nombre único para usarlo en la dirección URL de la cuenta de Lote.

	>[AZURE.NOTE]El nombre de la cuenta de Lote debe ser único para Azure, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.

	b. Si tiene más de una suscripción, haga clic en **Suscripción** para seleccionar una suscripción disponible donde se creará la cuenta.

	c. Haga clic en **Grupo de recursos** para seleccionar un grupo de recursos existente para la cuenta o cree una nueva.

	d. En **Ubicación**, seleccione una región de Azure en la que esté disponible el servicio Lote.

	![Crear una cuenta de lote][account_portal]

5. Haga clic en **Crear** para completar la creación de la cuenta.

## Administración de la configuración de cuenta y claves de acceso
Después de crear la cuenta, la encontrará en el portal donde puede administrar las claves de acceso, los usuarios autorizados y otros valores de configuración.

La dirección URL de la cuenta de Lote aparece en **Essentials**. Es una dirección URL del formulario `https://<account_name>.<region>.batch.azure.com`.

Para ver y administrar las claves de acceso, haga clic en el icono de llave.

![Claves de la cuenta de Lote][account_keys]

## Otras cosas que hay que saber sobre la cuenta de Lote

* Otras formas de crear y administrar cuentas de Lote son los [cmdlets de PowerShell para Lote](batch-powershell-cmdlets-get-started.md) y la [biblioteca .NET de administración de Lote](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/).


* Azure no cobra por tener una cuenta de Lote. Solo se le cobrará por el uso de los recursos de proceso de Azure y otros servicios cuando se ejecuten las cargas de trabajo (consulte los [precios de Lote](https://azure.microsoft.com/pricing/details/batch/)).

* Puede ejecutar varias cargas de trabajo de Lote en una sola cuenta de Lote, o bien distribuir las cargas de trabajo entre cuentas de Lote situadas en diferentes regiones de Azure.

* Si va a ejecutar varias cargas de trabajo de Lote a gran escala, tenga en cuenta que se aplican algunas [cuotas y límites de servicio de Lote](batch-quota-limit.md) a la suscripción de Azure y a cada cuenta de Lote. Las cuotas actuales de una cuenta de Lote aparecen en el portal de vista previa en las propiedades de la cuenta.

## Pasos siguientes

* Para más información sobre los conceptos de Lote, vea [Información general de las características de Lote de Azure](batch-api-basics.md).

* Comience a desarrollar su primera aplicación con la [biblioteca de cliente .NET de Lote](batch-dotnet-get-started.md)

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0114_2016-->