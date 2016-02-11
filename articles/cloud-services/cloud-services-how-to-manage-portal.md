<properties 
	pageTitle="Tareas de administración de servicios en la nube comunes | Microsoft Azure" 
	description="Vea cómo administrar servicios en la nube en el Portal de Azure. Estos ejemplos usan el Portal de Azure." 
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
	ms.date="01/20/2016"
	ms.author="adegeo"/>


# Administración de servicios en la nube

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-manage.md)
- [Azure portal](cloud-services-how-to-manage-portal.md)

En el área **Servicios en la nube** del Portal de Azure, puede actualizar un rol de servicio o una implementación, pasar su servicio en la nube de ensayo a producción, vincular recursos con su servicio en la nube de modo que pueda ver las dependencias de los recursos y escalar los recursos juntos, además de eliminar un servicio en la nube o una implementación.


## Actualización del rol de servicio en la nube o implementación

Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Actualizar** en la hoja del servicio en la nube. Puede actualizar un solo rol o todos los roles. Necesitará cargar un paquete de servicio nuevo y el archivo de configuración de servicio.

1. En el [Portal de Azure][], seleccione el servicio en la nube que desee actualizar. Esto abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, haga clic en el botón **Actualizar**.

    ![Botón Actualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Actualice la implementación con un nuevo archivo de paquete de servicio (.cspkg) y un archivo de configuración de servicio (.cscfg).

    ![Implementación de actualizaciones](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionalmente** puede actualizar la etiqueta de implementación y la cuenta de almacenamiento.

5. Si la actualización cambia el número de roles o el tamaño de algún rol, active la casilla **Permitir actualizar si cambian los tamaños de rol o el número de roles** para que la actualización continúe.

	>[AZURE.WARNING] Tenga presente que si cambia el tamaño de un rol (es decir, el tamaño de una máquina virtual que hospeda una instancia de rol) o la cantidad de roles, se debe volver a crear una imagen de la instancia de rol (máquina virtual) y se perderán todos los datos locales.

6. Si algún rol de servicio tiene solo una instancia de rol, active la casilla **Actualizar aunque uno o más roles contengan una única instancia** para permitir que la actualización continúe.

	Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtuales). Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza.

8. Haga clic en **Aceptar** para iniciar la actualización del servicio.



## Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción

Use **Intercambiar** para pasar una implementación de ensayo de un servicio en la nube a producción Cuando decide implementar una nueva versión de un servicio en la nube, puede ensayar y probar la nueva versión en su entorno de ensayo del servicio en la nube mientras los clientes usan la versión actual en la producción. Cuando esté listo para promover la nueva versión de la producción, puede usar **Intercambiar** para cambiar las direcciones URL que dirigen a las dos implementaciones.

Puede intercambiar implementaciones desde la página **Servicios en la nube** o el panel.

1. En el [Portal de Azure][], seleccione el servicio en la nube que desee actualizar. Esto abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, haga clic en el botón **Intercambiar**.

    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Se abre la siguiente solicitud de confirmación.

	![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Después de verificar la información de la implementación, haga clic en **Aceptar** para intercambiar las implementaciones.

	El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.

	Para ahorrar en los costes de proceso, puede eliminar la implementación en el entorno de ensayo cuando esté seguro de que la nueva implementación de producción se realiza según lo esperado.

## Vinculación de un recurso a un servicio en la nube

El Portal de Azure no vincula recursos entre sí como el Portal de Azure clásico actual. En su lugar, debe implementar recursos adicionales en el mismo grupo de recursos que está usando el servicio en la nube.

## Eliminación de implementaciones y un servicio en la nube

Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costes de proceso, puede eliminar la implementación de ensayo después de verificar que la implementación de la producción esté funcionando según lo esperado. Se le cobrarán los costes de proceso de las instancias de rol aunque un servicio en la nube no esté funcionando.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube.

1. En el [Portal de Azure][], seleccione el servicio en la nube que desee eliminar. Esto abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, haga clic en el botón **Eliminar**.

    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Puede eliminar el servicio en la nube completo seleccionando **Servicio en la nube y sus implementaciones** o elija **Implementación de producción** o **Implementación de almacenamiento provisional**.

    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Haga clic en el botón **Eliminar** situado en la parte inferior.

5. Para eliminar el servicio en la nube, haga clic en **Eliminar servicio en la nube**. Luego, haga clic en **Sí** en la solicitud de confirmación.

> [AZURE.NOTE]
Si se configura una supervisión detallada para su servicio en la nube, Azure no elimina los datos de supervisión de la cuenta de almacenamiento al eliminar el servicio en la nube. Tendrá que eliminar los datos manualmente. Para obtener información sobre dónde buscar las tablas métricas, consulte [este](cloud-services-how-to-monitor.md) artículo.

[Portal de Azure]: https://portal.azure.com

## Pasos siguientes

* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->