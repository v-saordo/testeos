<properties 
	pageTitle="Configuración de un servicio en la nube | Microsoft Azure" 
	description="Aprenda a configurar servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio en la nube y configurar el acceso remoto en instancias de rol. Estos ejemplos usan el Portal de Azure." 
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




# Configuración de servicios en la nube

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-configure-portal.md)
- [Azure classic portal](cloud-services-how-to-configure.md)

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

También puede habilitar una conexión de Escritorio remoto con uno o todos los roles que se ejecutan en su servicio en la nube. Escritorio remoto le permite tener acceso al escritorio de su aplicación mientras se ejecuta, además de solucionar y diagnosticar problemas. Puede habilitar una conexión de Escritorio remoto con su rol incluso si no configuró el archivo de definición de servicio (.csdef) para el Escritorio remoto durante el desarrollo de la aplicación. No es necesario volver a implementar su aplicación para habilitar una conexión de Escritorio remoto.

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## Cambiar un servicio en la nube

1. En el [Portal de Azure](https://portal.azure.com/), vaya al servicio en la nube.

2. Haga clic en el icono **Configuración** o el vínculo **Conceptos básicos/Toda la configuración** para abrir la hoja **Configuración**.

    ![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    Desde aquí puede ver las **Propiedades**, cambiar la **Configuración**, administrar los **Certificados** y administrar los **Usuarios** que tienen acceso a este servicio en la nube.

2. En la sección **Supervisión** puede hacer clic en cualquier icono para configurar alertas.

    ![Supervisión de servicios en la nube](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. En la sección **Roles e instancias** puede hacer clic en cualquier rol de servicio en la nube para administrar la instancia.

    ![Instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    Puede conectarse de forma remota para reiniciar o restablecer la imagen inicial del servicio en la nube desde aquí.
    
    ![Botones de instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]
No se puede cambiar el sistema operativo usado para el servicio en la nube mediante el **Portal de Azure**, solo puede cambiar esta configuración mediante el [Portal de Azure clásico](http://manage.windowsazure.com/). Esto se detalla [aquí](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Actualizar un archivo de configuración del servicio en la nube

1. En primer lugar, descargue el archivo de configuración del servicio en la nube existente (.cscfg).

    1. En el [Portal de Azure](https://portal.azure.com/), vaya al servicio en la nube.

    2. Haga clic en el icono **Configuración** o el vínculo **Conceptos básicos/Toda la configuración** para abrir la hoja **Configuración**.

        ![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. Haga clic en el elemento **Configuración**.

        ![Hoja de configuración](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. Haga clic en el botón **Descargar**.

        ![Descargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

    1. Siga los tres primeros pasos anteriores para abrir la hoja **Configuración** para el servicio en la nube.
    
    2. Haga clic en el botón **Cargar**.

        ![Cargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. Seleccione el archivo .cscfg y haga clic en **Aceptar**.

## acceso remoto a las instancias de rol

El acceso remoto no se puede configurar mediante el **Portal de Azure**, solo puede cambiar esta configuración mediante el [Portal de Azure clásico](http://manage.windowsazure.com/). Este procedimiento se describe [aquí](cloud-services-role-enable-remote-desktop.md).
			
## Pasos siguientes

* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->