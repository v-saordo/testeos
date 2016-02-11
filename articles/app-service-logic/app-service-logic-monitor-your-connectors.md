<properties 
	pageTitle="Administración y supervisión de las aplicaciones de API y los conectores en el Servicio de aplicaciones | Microsoft Azure" 
	description="Visualización del rendimiento de las aplicaciones de API y los conectores en el Servicio de aplicaciones de Azure; arquitectura de microservicios" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="mandia"/>

# Administración y supervisión de las aplicaciones de API y los conectores integrados

Ha creado una aplicación de API integrada. ¿Y ahora qué?

En Azure, cada aplicación de API es un sitio web independiente que se hospeda en Azure. Como resultado, puede ver fácilmente el número de solicitudes que se realizan y la cantidad de datos que está usando el conector. También puede realizar una copia de seguridad de la aplicación de API, crear alertas, habilitar Tinfoil Security y agregar usuarios y roles.

En este tema se describen algunas de las distintas opciones para administrar la aplicación de API.

Para ver estas características integradas, abra su aplicación de API en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si la aplicación de API se encuentra en el panel de inicio, selecciónela para abrir las propiedades. También puede seleccionar sucesivamente **Examinar**, **Aplicaciones de API** y luego su aplicación de API:

![][browse]

## Visualización de las propiedades que ha escrito

Cuando abre la aplicación de API, hay varias características y tareas disponibles:

![][settings]

Puede:

- **Configuración** muestra información específica sobre la aplicación de API, como los detalles de la suscripción, e indica los usuarios que tienen acceso a ella. También puede aumentar o reducir el número de instancias de la aplicación de API con la característica de escala; entre otras características.
- Use los botones **Iniciar** y **Detener** para controlar la aplicación de API.
- Cuando se realizan actualizaciones de productos en los archivos subyacentes usados por la aplicación de API, puede hacer clic en **Actualizar** para obtener las versiones más recientes. Por ejemplo, si existe una corrección o una actualización de seguridad publicadas por Microsoft, al hacer clic en **Actualizar** se actualiza automáticamente la aplicación de API para incluir esta corrección. 
- Seleccione **Plan de cambios** para actualizar o cambiar a una versión anterior en función del uso de datos de la aplicación de API. También puede usar esta característica para ver el uso de datos.
- Al crear un conector que tiene tablas, como el conector SQL, tiene la opción de escribir un nombre de tabla al que conectarse. Automáticamente se crea un esquema basado en la tabla que está disponible al hacer clic en **Descargar esquemas**. Luego, puede usar este esquema descargado para crear una transformación o una asignación. 

## Cambio de los valores de configuración del conector o de la aplicación de API que ha especificado

Después de configurar o crear su conector integrado, puede cambiar los valores especificados. Por ejemplo, si ha configurado el conector SQL y desea cambiar el nombre de tabla o nombre de SQL Server o el nombre de la tabla, puede hacerlo en la hoja Aplicación de API del conector.

Los pasos incluyen:

1. Abra el conector o la aplicación de API. Cuando lo haga, se abre la hoja Aplicación de API.
2. En **Essentials**, haga clic en el hipervínculo bajo la propiedad Host. El hipervínculo tiene un nombre parecido a *slackconnector* o *microsoftsqlconnector123*:

	![][apiapphost]

3. En la hoja Host de aplicaciones API, seleccione **Configuración**. En la hoja Configuración, seleccione **Configuración de la aplicación**. Los valores de configuración se muestran en **Configuración de la aplicación**:
	
	![][hostsettings]

4. Haga clic en el valor que quiera cambiar, escriba el nuevo valor y haga clic en **Guardar** para guardar los cambios.


## Instalación del Administrador de conexiones híbridas (opcional)

![][hcsetup]

El Administrador de conexiones híbridas permite conectarse a un sistema local, como SQL Server o SAP. Esta conectividad híbrida usa el Bus de servicio de Azure para conectarse y controlar la seguridad entre los recursos de Azure y los recursos locales.

Consulte [Uso del Administrador de conexiones híbridas del Servicio de aplicaciones de Azure](app-service-logic-hybrid-connection-manager.md)

> [AZURE.NOTE]El Administrador de conexiones híbridas solo es necesario si va a conectarse a un recurso local detrás del firewall. Si no se va a conectar a un sistema local, puede que el Administrador de conexiones híbridas no aparezca en la hoja del conector.

## Supervisión del rendimiento
Las métricas de rendimiento son características integradas y se incluyen con cada aplicación de API que se crea. Estas métricas son específicas de la aplicación de API hospedada en Azure. Métricas de ejemplo:

![][monitoring]

Puede:

- Seleccione **Errores y solicitudes** para agregar distintas métricas de rendimiento, incluidos códigos de error HTTP conocidos, como los códigos de estado 200, 400 o 500 HTTP. También puede ver los tiempos de respuesta, cuántas solicitudes se realizan a la aplicación de API, así como la cantidad de datos que entran y salen. En función de las métricas de rendimiento, puede crear alertas de correo electrónico si una métrica supera un umbral de su elección. 
- En **Uso**, puede ver la cantidad de **CPU** que usa la aplicación de API, revisar la **Cuota de uso** actual en MB y ver el uso de datos máximo según el nivel de costo. La opción **Gasto estimado** le puede ayudar a determinar los costos potenciales de ejecutar su aplicación de API.
- Seleccione **Procesos** para abrir el Explorador de procesos. Esta acción muestra las instancias web y sus propiedades, incluidos el uso de memoria y el número de subprocesos.

Con estas herramientas, puede determinar si el plan de servicio de aplicaciones debe escalarse o reducirse verticalmente, según sus necesidades empresariales. Estas características están integradas en el portal y no se necesitan herramientas adicionales.

## Control de la seguridad

Las aplicaciones de API usan seguridad basada en roles. Estos roles se aplican a toda la experiencia de Azure, incluidas las aplicaciones de API y otros recursos de Azure. Los roles incluyen:

Rol | Descripción
--- | ---
Propietario | Tiene acceso total a la experiencia de administración y puede proporcionar acceso a otros usuarios o grupos.
Colaborador | Tiene acceso total a la experiencia de administración. No puede conceder acceso a otros usuarios o grupos.
Lector | Puede ver todos los recursos excepto los secretos.
Administrador de acceso de usuario | Puede ver todos los recursos, crear y administrar roles y crear y administrar incidencias de soporte técnico.

[Control de acceso basado en rol en el Portal de Microsoft Azure](role-based-access-control-configure.md)

Puede agregar usuarios de forma fácil y asignarles roles específicos para su aplicación de API. El portal muestra los usuarios que tienen acceso y su rol asignado:

![][access]

- Seleccione **Usuarios** para agregar un usuario, asignar un rol y quitar un usuario.
- Seleccione **Roles** para ver todos los usuarios de un rol determinado, agregar un usuario a un rol y quitar un usuario de un rol. 


## Más cosas buenas
- Seleccione **Definición de API** para abrir el archivo de Swagger creado automáticamente para la aplicación de API específica.
- Seleccione **Dependencias** para ver los archivos que necesita la aplicación de API. Por ejemplo, si usa el conector SAP, debe instalar algunos archivos adicionales en el Administrador de conexiones de híbridas local. Estas dependencias se muestran en la hoja Aplicación de API. 

> [AZURE.IMPORTANT]Cuando abra las propiedades de la aplicación de API y mire en **Essentials**, verá los vínculos **Host** y **Puerta de enlace** que abren nuevas hojas:
> 
> ![][host]
> 
> Estas propiedades son específicas del sitio web que hospeda la aplicación de API. Cuando se usa una aplicación de API App o un conector integrados, la mayoría de estas propiedades no se aplican realmente y se recomienda que no las actualice. Si creó su propia aplicación de API en Visual Studio y la implementó en su suscripción a Azure, puede usar las hojas Host y Puerta de enlace. En [Administración de aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md) se proporciona más información sobre lo que puede hacer en estas hojas con su aplicación de API creada de forma personalizada. 



>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


## Más información.

[Supervisión de las aplicaciones lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Lista de aplicaciones de API y conectores del Servicio de aplicaciones](app-service-logic-connectors-list.md)<br/>
[Control de acceso basado en roles en el Portal de Microsoft Azure](role-based-access-control-configure.md)<br/>
[Uso del Administrador de conexiones híbridas en el Servicio de aplicaciones de Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_1210_2015-->
