<properties 
	pageTitle="Configuración de un servicio en la nube | Microsoft Azure" 
	description="Aprenda a configurar servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio en la nube y configurar el acceso remoto en instancias de rol." 
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

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de Azure clásico. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

El Portal de Azure clásico también le permite [habilitar la conexión a escritorio remoto para un rol de servicios en la nube de Azure](cloud-services-role-enable-remote-desktop.md).

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## Cambiar un servicio en la nube

1. En el [Portal de Azure clásico](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Configurar**.

    ![Página de configuración](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    En la página **Configurar**, puede configurar la supervisión, actualizar la configuración de roles y seleccionar el sistema operativo invitado y la familia para las instancias de rol.

2. En **supervisión**, establezca el nivel de supervisión en Detallado o Mínimo y configure las cadenas de conexión del diagnóstico que se requieren para la supervisión detallada.

3. Para los roles de servicio (agrupados por rol), puede actualizar la siguiente configuración:
    
    >**Configuración** Modifique los valores de los ajustes de la configuración diversa que se especifican en los elementos *ConfigurationSettings* del archivo de configuración del servicio (.cscfg).
    >
    >**Certificados** Cambie la huella digital del certificado que se está usando en el cifrado SSL para un rol. Para cambiar un certificado, debe primero cargar el certificado nuevo (en la página **Certificados**). Posteriormente, actualice la huella digital en la cadena de certificado que aparece en la configuración de roles.

4. En **sistema operativo**, puede cambiar la familia o la versión del sistema operativo para las instancias de rol o seleccionar **Automático** para habilitar las actualizaciones automáticas de la versión del sistema operativo actual. Esta configuración del sistema operativo se aplica a roles web y roles de trabajo, pero no afecta a Máquinas virtuales.

    Durante la implementación, se instala la versión más reciente del sistema operativo en todas las instancias de rol y los sistemas operativos se actualizan automáticamente de manera predeterminada.
    
    Si debido a los requisitos de compatibilidad en su código necesita una versión diferente del sistema operativo para que su servicio en la nube pueda ejecutarse, puede seleccionar una familia y versión del sistema operativo. Al elegir una versión específica del sistema operativo, se suspenden las actualizaciones automáticas del sistema operativo para el servicio en la nube. Deberá asegurarse de que los sistemas operativos reciban las actualizaciones.
    
    Si soluciona todos los problemas de compatibilidad que tienen sus aplicaciones con la versión más reciente del sistema operativo, puede habilitar las actualizaciones automáticas del sistema operativo al establecer la versión del sistema operativo en **Automático**.
    
    ![Configuración del SO](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para guardar los ajustes de configuración y transmitirlos a las instancias de rol, haga clic en **Guardar**. (Haga clic en **Descartar** para cancelar los cambios). **Guardar** y **Descartar** se agregan a la barra de comandos después de cambiar un parámetro de configuración.

## Actualizar un archivo de configuración del servicio en la nube

1. Descargue un archivo de configuración del servicio en la nube (.cscfg) con la configuración actual. En la página **Configurar** del servicio en la nube, haga clic en **Descargar**. A continuación, haga clic en **Guardar** o en **Guardar como** para guardar el archivo.

2. Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

    1. En la página **Configurar**, haga clic en **Cargar**.
    
        ![Cargar configuración](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. En **Archivo de configuración**, use **Examinar** para seleccionar el archivo .cscfg actualizado.
    
    3. Si su servicio en la nube contiene cualquier rol que tiene una sola instancia, active la casilla **Aplicar la configuración aunque una o más reglas contenga una instancia única** para permitir que continúen las actualizaciones de configuración para los roles.
    
        A menos que defina como mínimo dos instancias de cada rol, Azure no puede garantizar una disponibilidad de su servicio en la nube de al menos un 99,95 % durante las actualizaciones de la configuración del servicio. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).
    
    4. Haga clic en **Aceptar** (marca de verificación).


## Pasos siguientes

* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage.md).
* [Habilitar la conexión a Escritorio remoto para un rol de servicios en la nube de Azure](cloud-services-role-enable-remote-desktop.md)
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate.md).

<!---HONumber=AcomDC_0128_2016-->