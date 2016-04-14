<properties
	pageTitle="Información general del Portal de Microsoft Azure"
	description="Aprenda a usar el Portal de Microsoft Azure."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="12/16/2015"
	ms.author="dwrede"/>

# Información general del Portal de Microsoft Azure

El Portal de Microsoft Azure es una ubicación central desde donde se pueden aprovisionar y administrar los recursos de Azure. Este tutorial le permitirá familiarizarse con el portal y le mostrará cómo usar algunas de estas funcionalidades clave: - Un **marketplace completo** que le permite examinar miles de elementos de Microsoft y otros proveedores que se pueden comprar y/o aprovisionar. - Una **experiencia de exploración unificada y escalable** que facilita encontrar los recursos que le interesan y realizar diversas operaciones de administración. - **Páginas de administración coherente** (u hojas) que le permiten administrar la amplia variedad de servicios de Azure a través de una forma coherente de exponer configuraciones, acciones, información de facturación, seguimiento de estado y datos de uso, etc. - Una **experiencia personal** que le permite crear una pantalla de inicio personalizada que muestra la información que desea ver cada vez que inicia sesión. También puede personalizar cualquiera de las hojas de administración que contienen mosaicos.

 ![Orientación sobre la interfaz de usuario del Portal de Azure][UIOrientation]

## Antes de comenzar

Necesitará una suscripción válida a Azure para continuar con este tutorial. Si no cuenta con una, [suscríbase a una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) hoy mismo. Una vez que tenga una suscripción, puede tener acceso al portal en [https://portal.azure.com].

## Creación de un recurso

Azure tiene un marketplace con miles de elementos que se pueden crear desde un solo lugar. Supongamos que desea crear una máquina virtual de Windows Server 2012 nueva. El concentrador +NUEVO es el punto de entrada a un conjunto protegido de categorías destacada desde el marketplace. Cada categoría cuenta con un pequeño conjunto de elementos destacados junto con un vínculo al marketplace completo que muestra todas las categorías y la búsqueda. Para crear esa nueva máquina virtual de Windows Server 2012, realice las siguientes acciones:

1.	Windows Server 2012 está incluido, por lo que puede seleccionarlo en la categoría Proceso.  
2.	Rellene algunas entradas básicas de un formulario.
3.	Haga clic en "Crear" y la máquina virtual comenzará el aprovisionamiento de inmediato.

El centro de notificaciones le enviará una alerta cuando se cree el recurso y se abrirá una hoja de administración (siempre puede navegar más adelante a los recursos).

![Categorías del portal][PortalCategories]


## Cómo buscar los recursos

Siempre tiene la posibilidad de anclar los recursos de acceso más frecuente en el panel de inicio, pero es posible que deba navegar a algún contenido al que no tiene acceso con frecuencia. El centro de exploración que se muestra a continuación es la forma que tiene para llegar a todos los recursos. Puede filtrar por suscripción, elegir columnas o cambiar su tamaño y navegar a las hojas de navegación con un clic en elementos individuales.

![Centro de exploración][BrowseHub]

## Cómo administrar y delegar acceso a un recurso

Desde esta hoja puede conectarse a la máquina virtual a través de Escritorio remoto, supervisar las métricas de rendimiento clave, controlar el acceso a esta máquina virtual mediante el uso de control de acceso basado en rol (RBAC), configurar la máquina virtual y realizar otras importantes tareas de administración. Delegar el acceso basado en rol resulta fundamental para la administración a escala. Haga clic [aquí](role-based-access-control-configure.md) para obtener más información al respecto. Para delegar el acceso a un recurso, realice las siguientes acciones:

1.	Vaya a su sitio.
2.	En la sección Essentials, haga clic en "Toda la configuración".
3.	Haga clic en "Usuarios" en la lista de configuraciones.
4.	Haga clic en "Agregar" en la barra de comandos.
5.	Elija un usuario y un rol.

![Administración de un recurso][ManageResource]

## Cómo personalizar una hoja de recursos

Azure configura previamente las hojas correspondientes a los recursos, pero los mosaicos de estas hojas quedan bajo su control. Puede personalizar fácilmente el modo para agregar, quitar, cambiar el tamaño o volver a organizar los mosaicos. Para personalizar una hoja, realice las siguientes acciones.

1.	Vaya a su sitio.
2.	Haga clic en "..." en la parte superior de la hoja que desea personalizar.
3.	Haga clic en "Agregar elementos".
4.	Comience a arrastrar y soltar elementos.  

![Personalización de hojas][CustomizeBlades]

## Cómo obtener ayuda

Estamos aquí para ayudarle en caso de tener problemas. El portal cuenta con una página de ayuda y soporte técnico que puede señalarle la dirección correcta. Dependiendo del [plan de soporte técnico](https://azure.microsoft.com/support/plans/) que tenga, también puede crear incidencias de soporte técnico directamente en el portal. Después de crear una incidencia de soporte técnico, puede administrar el ciclo de vida de la incidencia desde el portal. Para tener acceso a la página de ayuda y soporte técnico, navegue a Examinar > Ayuda y soporte técnico

![Ayuda y soporte técnico][HelpSupport]

## Resumen

Revisemos lo aprendido en este tutorial: - Aprendió a suscribirse, obtener una suscripción y navegar al portal. - Se le orientó respecto de la interfaz de usuario del portal y aprendió a crear y explorar recursos. - Aprendió a crear y explorar recursos. - Aprendió sobre la estructura o las hojas de administración y cómo administrar de manera coherente distintos tipos de recursos. - Aprendió a personalizar el portal para poner la información que le interesa al frente y al centro del mismo. - Aprendió a controlar el acceso a los recursos mediante el uso del acceso basado en rol (RBAC). - Aprendió a obtener ayuda y soporte técnico.

El Portal de Microsoft Azure simplifica radicalmente la compilación y administración de las aplicaciones en la nube. Eche un vistazo al [blog de administración](https://azure.microsoft.com/blog/topics/management/) para mantenerse al día, dado que constantemente [prestamos atención a los comentarios](https://feedback.azure.com/forums/223579-azure-preview-portal/) y realizamos mejoras. El [blog de ScottGu](http://weblogs.asp.net/scottgu) es otro excelente lugar para buscar todas las actualizaciones de Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

<!---HONumber=AcomDC_0128_2016-->