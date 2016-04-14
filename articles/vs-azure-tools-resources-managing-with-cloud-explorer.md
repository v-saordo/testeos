<properties 
   pageTitle="Administración de recursos de Azure con Cloud Explorer | Microsoft Azure"
   description="Obtenga más información sobre cómo usar Cloud Explorer para examinar y administrar recursos de Azure en Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Administración de recursos de Azure con Cloud Explorer

##Información general

Cloud Explorer está diseñado para permitirle examinar y administrar con mayor facilidad y rapidez los recursos de Azure en el IDE de Visual Studio. Por ejemplo, puede usarlo para abrir una aplicación web en el Portal de Azure o en un explorador, o adjuntarle un depurador. También puede ver las propiedades de un contenedor de blobs y abrirlo en el Editor de contenedor de blobs.

Cloud Explorer se basa en la pila del Administrador de recursos de Azure, igual que el Portal de vista previa de Microsoft Azure. Entiende de recursos como, grupos de recursos de Azure, y de servicios de Azure, como aplicaciones lógicas y de API. Además, admite [control de acceso basado en rol](../role-based-access-control-configure/) (RBAC). Para ver los recursos de Azure que se han agregado o cambiado, elija el botón **Actualizar** en la barra de herramientas de Cloud Explorer.

Cloud Explorer se instala como parte de Visual Studio Tools para Azure SDK 2.7.

## Requisitos previos

- Visual Studio 2015 RTM.

- Visual Studio Tools para Azure SDK.
- También debe tener una cuenta de Azure y haber iniciado sesión en ella para ver recursos de Azure en Cloud Explorer. Si no tiene ninguna, puede crear una cuenta en un par de minutos. Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). De lo contrario, consulte [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Si Cloud Explorer no está visible, puede mostrarlo; para ello, elija **Ver**, **Otras ventanas,** **Cloud Explorer** en la barra de menús.

## Administración de suscripciones y cuentas de Azure

Para ver los recursos de Azure en Cloud Explorer, deberá iniciar sesión en una cuenta de Azure con una o más suscripciones activas. Si tiene más de una cuenta de Azure, puede agregarlas en Cloud Explorer. A continuación, elija las suscripciones que desea incluir en la vista de recursos de Cloud Explorer.

Si no ha usado Azure antes, o no ha agregado las cuentas necesarias a Visual Studio, se le pedirá que lo haga.

## Para agregar cuentas de Azure a Cloud Explorer

1. Elija el icono de configuración de la barra de herramientas de Cloud Explorer.

1. Elija el vínculo **Agregar una cuenta**. Inicie sesión en la cuenta de Azure cuyos recursos desea examinar. La cuenta que acaba de agregar debería estar seleccionada en la lista desplegable de selección de cuenta. Las suscripciones para esa cuenta aparecen bajo la entrada de cuenta.

    ![Agregar suscripciones de Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Elegir suscripciones de Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Active las casillas de las suscripciones de cuenta que desea examinar y, luego, elija el botón **Aplicar**.

    Los recursos de Azure de las suscripciones seleccionadas aparecen en Cloud Explorer.

## Para eliminar una cuenta de Azure

1. Elija **Archivo**, **Configuración de la cuenta** en la barra de menús.

1. En la sección **Todas las cuentas de** del cuadro de diálogo **Configuración de la cuenta**, seleccione el comando **Quitar** junto a la cuenta que desea quitar. Tenga en cuenta que este comando solo quita la cuenta de Visual Studio, no tiene efecto sobre la cuenta propiamente dicha.

## Vista de tipos o grupos de recursos

Para ver los recursos de Azure, puede elegir la vista **Tipos de recursos** o **Grupos de recursos**.

![Lista desplegable de vistas de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- La vista **Tipos de recursos**, que es también la vista común usada en el Portal de Azure, muestra los recursos de Azure clasificados por su tipo, como aplicaciones web, cuentas de almacenamiento y máquinas virtuales. Se parece al modo en que los recursos de Azure aparecen en el Explorador de servidores.

- La vista Grupos de recursos clasifica los recursos de Azure por el grupo de recursos de Azure al que están asociados.

 
	Un grupo de recursos es una agrupación de recursos de Azure, normalmente usados por una aplicación específica. Para obtener más información sobre los grupos de recursos de Azure, consulte [Información general del Administrador de recursos de Azure](https://azure.microsoft.com/documentation/articles/resource-group-overview/).

## Visualización y navegación por los recursos

Para navegar a un recurso de Azure y ver su información en Cloud Explorer, expanda el tipo del elemento o el grupo de recursos asociado y, luego, elija el recurso. Cuando elige el recurso, aparece información en las dos pestañas situadas en la parte inferior de Cloud Explorer.

![Elegir una vista de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- La pestaña **Acciones** muestra las acciones que puede realizar en Cloud Explorer con el recurso seleccionado. También puede ver las acciones disponibles en el menú contextual del recurso.

- La pestaña **Propiedades** muestra las propiedades del recurso, como su tipo, configuración regional y grupo de recursos al que está asociado.

Cada recurso tiene la acción **Abrir en el portal**. Cuando se selecciona esta acción, Cloud Explorer muestra el recurso seleccionado en el Portal de Azure. Esta característica es especialmente útil para navegar a los recursos profundamente anidados.

También pueden aparecer acciones adicionales y valores de propiedad basados en el recurso de Azure. Por ejemplo, las aplicaciones web y lógicas también tienen las acciones **Abrir en el explorador** y **Adjuntar depurador**, además de **Abrir en el portal**. Cuando se elige un blob, una cola o una tabla de cuenta de almacenamiento, aparecen acciones para abrir editores. Las aplicaciones de Azure tienen las propiedades **URL** y **Status**, mientras que los recursos de almacenamiento tienen propiedades de cadena de conexión y de clave.

## Búsqueda de recursos

Para buscar recursos con un nombre específico en las suscripciones de cuenta de Azure, escriba el nombre en el cuadro de búsqueda en Cloud Explorer.

![Buscar recursos en Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

A medida que escribe caracteres en el cuadro de búsqueda, solo los recursos que coinciden con los caracteres aparecen en el árbol de recursos.

<!---HONumber=AcomDC_0128_2016-->