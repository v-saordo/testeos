<properties
	pageTitle="Creación e implementación de un servicio en la nube | Microsoft Azure"
	description="Aprenda a crear e implementar un servicio en la nube con el método Creación rápida en Azure."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Creación e implementación de un servicio en la nube

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)
- [Azure classic portal](cloud-services-how-to-create-deploy.md)

El Portal de Azure clásico le ofrece dos formas de crear e implementar un servicio en la nube: **Creación rápida** y **Creación personalizada**.

En este tema se explica cómo usar el método Creación rápida para crear un nuevo servicio en la nube y, luego, usar **Cargar** para cargar e implementar un paquete de servicios en la nube en Azure. Cuando usa este método, el Portal de Azure clásico pone a su disposición los vínculos pertinentes para completar todos los requisitos que vaya necesitando sobre la marcha. Si está listo para implementar su servicio en la nube una vez creado, puede hacer las dos cosas a la vez usando **Creación personalizada**.

> [AZURE.NOTE] Si tiene pensado publicar su servicio en la nube desde Visual Studio Team Services (VSTS), use Creación rápida y, a continuación, configure la publicación VSTS desde **Creación rápida** o el panel. Para más información, consulte [Entrega continua a Azure con Visual Studio Team Services][TFSTutorialForCloudService] o la ayuda de la página **Inicio rápido**.

## Conceptos
se necesitan tres componentes para implementar una aplicación como servicio en la nube en Azure:

- **Definición de servicio** El archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluyendo el número de roles.

- **Configuración de servicio** El archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

- **Paquete de servicio** El paquete de servicio (.cspkg) contiene el código y las configuraciones de la aplicación y el archivo de definición de servicio.
  
Puede obtener más información acerca de estas y cómo crear un paquete [aquí](cloud-services-model-and-package.md).

## Preparación de la aplicación
Antes de implementar un servicio en la nube, debe crear el paquete de servicio en la nube (.cspkg) desde su código de aplicación y un archivo de configuración de servicio en la nube (.cscfg). El SDK de Azure proporciona herramientas para preparar estos archivos de implementación necesarios. Puede instalar el SDK desde la página [Descargas de Azure](https://azure.microsoft.com/downloads/), en el idioma en que prefiera implementar su código de aplicación.

Hay tres características del servicio en la nube que requieren configuraciones especiales antes de exportar un paquete de servicio:

- Si desea implementar un servicio en la nube que utilice Capa de sockets seguros (SSL) para el cifrado de datos, [configure la aplicación](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) para SSL.

- Si desea configurar una conexión de Escritorio remoto a instancias de rol, [configure los roles](cloud-services-role-enable-remote-desktop.md) para Escritorio remoto.

- Si desea configurar una supervisión exhaustiva para su servicio en la nube, habilite Diagnósticos de Azure para el servicio en la nube. *Supervisión mínima* (nivel predeterminado de supervisión) usa contadores de rendimiento recopilados de los sistemas operativos host para las instancias de rol (máquinas virtuales). "La supervisión exhaustiva* recopila métricas adicionales basadas en los datos del rendimiento dentro de las instancias de rol para permitir un análisis más profundo de los problemas que se producen durante el procesamiento de la aplicación. Para obtener información acerca de cómo habilitar Diagnósticos de Azure, consulte [Habilitación de Diagnósticos en Azure](cloud-services-dotnet-diagnostics.md).

Para crear un servicio en la nube con implementaciones de roles web o de trabajo, debe [crear el paquete de servicio](cloud-services-model-and-package.md#servicepackagecspkg).

## Antes de empezar

- Si no ha instalado el SDK de Azure, haga clic en **Instalación de SDK de Azure** para abrir la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y, a continuación, descargue el SDK en el idioma en que prefiera desarrollar el código. (Tendrá la oportunidad de hacer esto más tarde).

- Si hay instancias de rol que necesitan certificados, créelos. Los servicios en la nube requieren un archivo .pfx con una clave privada. Puede [cargar los certificados en Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) mientras crea e implementa el servicio en la nube.

- Si tiene pensado implementar el servicio en la nube en un grupo de afinidad, cree dicho grupo. Puede usar un grupo de afinidad para implementar su servicio en la nube y otros servicios de Azure en la misma ubicación de una región. Puede crear el grupo de afinidad en el área **Redes** del Portal de Azure clásico, en la página **Grupos de afinidad**.


## Creación de un servicio en la nube usando Creación rápida

1. En el [Portal de Azure clásico](http://manage.windowsazure.com/), haga clic en **Nuevo** > **Proceso** > **Servicio en la nube** > **Creación rápida**.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. En **URL**, escriba el nombre del subdominio que vaya a usar en la URL pública para obtener acceso a su servicio en la nube en las implementaciones de producción. El formato de la URL para las implementaciones de producción es el siguiente: http://*myURL*.cloudapp.net.

3. En **Región o grupo de afinidad**, seleccione la zona geográfica o el grupo de afinidad en el que desea implementar el servicio en la nube. Seleccione un grupo de afinidad si desea implementar un servicio en la nube en la misma ubicación que los otros servicios de Azure de una región.

4. Haga clic en **Crear servicio en la nube**.

	![CloudServices\_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	Puede supervisar el estado del proceso en el área de mensajes situada en parte inferior de la ventana.

	El área **Servicios en la nube** se abre y en ella se muestran los servicios. Si el estado cambia a "Created", significa que la creación de servicios en la nube se ha completado correctamente.

	![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## Carga de un certificado para un servicio en la nube

1. En el [Portal de Azure clásico](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Certificados**.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Haga clic en **Cargar un certificado** o en **Cargar**.

3. En **Archivo**, use **Examinar** para seleccionar el certificado (archivo .pfx).

4. En **Contraseña**, escriba la clave privada del certificado.

5. Haga clic en **Aceptar** (marca de verificación).

	![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	Puede ver el progreso de carga en el área de mensajes que se muestra a continuación. Cuando la carga termine, el certificado se agregará a la tabla. En el área de mensajes, haga clic en OK para cerrar el mensaje.

	![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## Implementación de un servicio en la nube

1. En el [Portal de Azure clásico](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Panel**.

2. Haga clic en **Cargar una nueva implementación de producción** o **Cargar**.

3. En **Etiqueta de implementación**, escriba un nombre para la nueva implementación; por ejemplo, MyCloudServicev4.

3. En **Paquete**, use **Examinar** para seleccionar el archivo de paquete de servicio (.cspkg) que quiera usar.

4. En **Configuración**, use **Examinar** para seleccionar el archivo de configuración de servicio (.cscfg) que quiera usar.

5. Si el servicio en la nube incluye roles con una sola instancia, active la casilla **Implementar aunque uno o varios roles contengan una sola instancia** para habilitar la implementación y continuar.

    Azure solo puede garantizar el 99,95 % de acceso al servicio en la nube durante el mantenimiento y las actualizaciones de servicio si cada rol tiene dos instancias como mínimo. Si procede, puede agregar instancias de rol adicionales en la página **Escalar** después de implementar el servicio en la nube. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

6. Haga clic en **Aceptar** (marca de verificación) para iniciar la implementación del servicio en la nube.

	![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

	Puede supervisar el estado de la implementación en el área de mensajes. Haga clic en OK para ocultar el mensaje.

	![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## Compruebe que la implementación se haya completado correctamente.

1. Haga clic en **Panel**.

	El estado debería mostrar que el servicio está **En ejecución**.

2. Debajo de **Vista rápida**, haga clic en la URL del sitio para abrir su servicio en la nube en un explorador web.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796
 
## Pasos siguientes

* [Configuración general de su servicio en la nube](cloud-services-how-to-configure.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate.md).

<!---HONumber=AcomDC_0128_2016-->