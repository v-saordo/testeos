<properties
	pageTitle="Creación e implementación de un servicio en la nube | Microsoft Azure"
	description="Aprenda a crear e implementar un servicio en la nube con el método Creación rápida en Azure. Estos ejemplos usan el Portal de Azure."
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

El Portal de Azure le ofrece dos formas de crear e implementar un servicio en la nube: *Creación rápida* y *Creación personalizada*.

En este artículo se explica cómo usar el método Creación rápida para crear un nuevo servicio en la nube y, a continuación, cómo usar **Cargar** para cargar e implementar un paquete de servicios en la nube en Azure. Cuando usa este método, el Portal de Azure pone a su disposición los vínculos pertinentes para completar todos los requisitos que vaya necesitando sobre la marcha. Si está listo para implementar su servicio en la nube una vez creado, puede hacer las dos cosas a la vez usando Creación personalizada.

> [AZURE.NOTE] Si tiene pensado publicar su servicio en la nube desde Visual Studio Team Services (VSTS), use Creación rápida y, a continuación, configure la publicación VSTS desde Creación rápida de Azure o el panel. Para más información, consulte [Entrega continua a Azure usando Visual Studio Team Services][TFSTutorialForCloudService] o la ayuda de la página **Inicio rápido**.

## Conceptos
Necesita tres componentes para implementar una aplicación como servicio en la nube en Azure:

- **Definición de servicio** El archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluyendo el número de roles.

- **Configuración de servicio** El archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

- **Paquete de servicio** El paquete de servicio (.cspkg) contiene el código y las configuraciones de la aplicación y el archivo de definición de servicio.

Puede obtener más información acerca de estas y cómo crear un paquete [aquí](cloud-services-model-and-package.md).

## Preparación de la aplicación
Antes de implementar un servicio en la nube, debe crear el paquete de servicio en la nube (.cspkg) desde su código de aplicación y un archivo de configuración de servicio en la nube (.cscfg). El SDK de Azure proporciona herramientas para preparar estos archivos de implementación necesarios. Puede instalar el SDK desde la página [Descargas de Azure](https://azure.microsoft.com/downloads/), en el idioma en que prefiera implementar su código de aplicación.

Hay tres características del servicio en la nube que requieren configuraciones especiales antes de exportar un paquete de servicio:

- Si desea implementar un servicio en la nube que utilice Capa de sockets seguros (SSL) para el cifrado de datos, [configure la aplicación](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.

- Si desea configurar una conexión de Escritorio remoto a instancias de rol, [configure los roles](cloud-services-role-enable-remote-desktop.md) para Escritorio remoto. Solo puede realizarse en el portal clásico.

- Si desea configurar una supervisión exhaustiva para su servicio en la nube, habilite Diagnósticos de Azure para el servicio en la nube. *Supervisión mínima* (nivel predeterminado de supervisión) usa contadores de rendimiento recopilados de los sistemas operativos host para las instancias de rol (máquinas virtuales). La *supervisión detallada* recopila métricas adicionales basadas en los datos del rendimiento dentro de las instancias de rol para permitir un análisis más profundo de los problemas que se producen durante el procesamiento de la aplicación. Para obtener información acerca de cómo habilitar Diagnósticos de Azure, consulte [Habilitación de Diagnósticos en Azure](cloud-services-dotnet-diagnostics.md).

Para crear un servicio en la nube con implementaciones de roles web o de trabajo, debe [crear el paquete de servicio](cloud-services-model-and-package.md#servicepackagecspkg).

## Antes de empezar

- Si no ha instalado el SDK de Azure, haga clic en **Instalación de SDK de Azure** para abrir la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y, a continuación, descargue el SDK en el idioma en que prefiera desarrollar el código. (Tendrá la oportunidad de hacer esto más tarde).

- Si hay instancias de rol que necesitan certificados, créelos. Los servicios en la nube requieren un archivo .pfx con una clave privada. [Puede cargar los certificados en Azure]() mientras crea e implementa el servicio en la nube.

- Si tiene pensado implementar el servicio en la nube en un grupo de afinidad, cree dicho grupo. Puede usar un grupo de afinidad para implementar su servicio en la nube y otros servicios de Azure en la misma ubicación de una región. Puede crear el grupo de afinidad en el área **Redes** del Portal de Azure clásico, en la página **Grupos de afinidad**.


## Paso 3: Crear un servicio en la nube y cargar el paquete de implementación

1. Inicie sesión en el [Portal de Azure][].
2. Haga clic en **Nuevo > Proceso** y luego desplácese hacia abajo y haga clic en **Servicio en la nube**.

    ![Publicación del servicio en la nube](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. En la parte inferior de la página de información que se muestra, haga clic en **Crear**.
4. En la nueva hoja **Servicio en la nube**, escriba un valor para **Nombre DNS**
5. Cree un nuevo **Grupo de recursos** o seleccione uno existente.
6. Seleccione una **ubicación**.
7. Seleccione **Paquete** y, en la hoja **Cargar un paquete**, rellene los campos obligatorios.  

     Si cualquiera de los roles contiene una sola instancia, asegúrese de que la casilla **Implementar aunque uno o varios roles contengan una sola instancia** esté seleccionada.

8. Asegúrese de que la opción **Iniciar implementación** esté seleccionada.
9. Haga clic en **Aceptar**.

    ![Publicación del servicio en la nube](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Carga de un certificado

Si el paquete de implementación se [configuró para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), puede cargar el certificado ahora.

1. Seleccione **Certificados** y, en la hoja **Agregar certificados**, seleccione el archivo .pfx del certificado SSL y proporcione la **contraseña** del certificado,
2. Haga clic en **Adjuntar certificado** y luego en **Aceptar** en la hoja **Agregar certificados**.
3. Haga clic en **Crear** en la hoja **Servicio en la nube**. Cuando la implementación haya llegado al estado **Listo**, puede continuar con los pasos siguientes.

    ![Publicación del servicio en la nube](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Compruebe que la implementación se haya completado correctamente.

1. Haga clic en la instancia de servicio en la nube.

	El estado debería mostrar que el servicio está **En ejecución**.

2. En **Essentials**, haga clic en la **URL del sitio** para abrir su servicio en la nube en un explorador web.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## Pasos siguientes

* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->