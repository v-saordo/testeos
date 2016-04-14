<properties
	pageTitle="Recuperación del servicio móvil en caso de un desastre | Microsoft Azure"
	description="Obtenga información acerca de cómo recuperar su servicio móvil en caso de desastre."
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="christopheranderson"/>

# Recuperación del servicio móvil en caso de un desastre

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

Cuando utiliza los Servicios móviles de Azure para implementar una aplicación, puede utilizar las funciones integradas para garantizar la continuidad empresarial en caso de problemas de disponibilidad como, por ejemplo, errores de servidor, interrupciones de red, pérdida de datos y pérdida generalizada en instalaciones. Al implementar la aplicación mediante los Servicios móviles de Azure, saca partido de las múltiples capacidades de infraestructura y tolerancia a errores que tendría que diseñar, implementar y administrar si tuviera que implementar una solución tradicional local. Azure mitiga una gran cantidad de posibles errores a un coste reducido.

## <a name="prepare"></a> Preparación ante posibles desastres

Para facilitar la recuperación en caso de un problema de disponibilidad, puede prepararse por adelantado:

+ **Haga una copia de seguridad de los datos en la base de datos SQL de Servicios móviles de Azure** Los datos de la aplicación de Servicios móviles se almacenan en una Base de datos SQL de Azure. Le recomendamos que haga una copia de seguridad tal como se indica en [Continuidad empresarial de Base de datos SQL de Azure].
+ **Haga una copia de seguridad de los scripts de servicios móviles** Recomendamos que almacene los scripts de los servicios móviles en un sistema de control de código fuente como [Team Foundation Service] o [GitHub] y no depender únicamente de las copias del propio servicio móvil. Puede descargar los scripts en el Portal de Azure clásico, con la [función de control de código fuente] de los Servicios móviles o con la [interfaz de la línea de comandos de Azure]. Preste atención a las características etiquetadas como "vista previa" en el portal de Azure clásico, dado que la recuperación de dichos scripts no está garantizada y es posible que tenga que recuperarlos desde su propio control de código fuente original.
+ **Reserve un servicio móvil secundario** En caso de que surja algún problema de disponibilidad con el servicio móvil, es posible que tenga que volver a implementarlo en otra región de Azure. Para garantizar que la capacidad está disponible (por ejemplo, en aquellas circunstancias raras como la pérdida de una región completa), le recomendamos crear un servicio móvil secundario en una región alternativa y establecer el modo en un nivel igual o superior al modo de su servicio principal. (Si el servicio principal se encuentra en modo básico, puede hacer que el servicio secundario sea básico o estándar. Sin embargo, si el principal es estándar, el secundario también debe ser estándar).

## <a name="watch"></a>Inspección de indicios de problemas

Estas circunstancias indican un problema que podría necesitar una operación de recuperación:

+ Las aplicaciones que están conectadas a su servicio móvil no pueden comunicarse con él durante un prolongado período de tiempo.
+ El estado del servicio móvil se muestra como **Incorrecto** en el [Portal de Azure clásico].
+ Se muestra un banner **Incorrecto** en la parte superior de cada pestaña del servicio móvil en el portal de Azure clásico y las operaciones de administración originan mensajes de error.
+ El [Panel de servicios de Azure] indica un problema de disponibilidad.

## <a name="recover"></a>Recuperación de un desastre

Cuando surja un problema, utilice el Panel de servicios para obtener ayuda y actualizaciones.

Si se lo indica el Panel de servicios, ejecute los pasos siguientes para restaurar el servicio móvil a un estado de ejecución en otra región de Azure. Si ya ha creado un servicio secundario, su capacidad se utilizará para restaurar el servicio principal. Como la dirección URL y la clave de aplicación del servicio principal no se han cambiado después de la recuperación, no tiene que actualizar ninguna aplicación que haga referencia al servicio.

Para recuperar el servicio móvil después de una interrupción del servicio:

1. En el portal de Azure clásico, asegúrese de que el estado del servicio se indica como **Incorrecto**.

2. Si ya ha reservado un servicio móvil secundario, puede omitir este paso.

   Si todavía no ha reservado un servicio móvil secundario, cree uno ahora en otra región de Azure. Defina su modo de escala igual al modo del servicio principal.

3. Configure la interfaz de la línea de comandos de Azure (CLI de Azure) para que actúe con la suscripción, tal como se describe en [Automatización de servicios móviles con CLI de Azure].

4. Ahora puede utilizar el servicio secundario para recuperar el principal.

	> [AZURE.IMPORTANT] Además de migrar los archivos, el comando migrate también actualiza el nombre de host del servicio principal para señalar el servicio secundario, para que no sea necesario actualizar las aplicaciones de ese cliente. Sin embargo, el nombre de host demorará hasta 30 minutos en resolver el nuevo servicio. Por este motivo, se recomienda usar el comando migrate solo en escenarios de recuperación ante desastres.

	> [AZURE.IMPORTANT] Cuando ejecuta el comando en este paso, el servicio secundario se elimina, de tal forma que su capacidad se puede utilizar para recuperar el servicio principal. Le recomendamos que haga una copia de seguridad de los scripts y configuraciones antes de ejecutar el comando, si desea conservarlos.

	Cuando esté preparado, ejecute el comando siguiente:

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE] Pueden pasar unos minutos después de la finalización del comando hasta que pueda ver los cambios en el portal de Azure clásico.

5. Compruebe que todos los scripts se han recuperado correctamente comparándolos con los originales en el control de código fuente. En la mayoría de los casos, los scripts se recuperan automáticamente sin pérdida de datos, pero si encuentra una discrepancia, puede recuperar dicho script manualmente.

6. Asegúrese de que el servicio recuperado se comunica con la Base de datos SQL de Azure. El comando de recuperación recupera el servicio móvil, pero conserva la conexión con la base de datos original. Si el problema de la región principal de Azure también afecta a la base de datos, es posible que el servicio recuperado no se esté ejecutando correctamente. Puede utilizar el Panel de servicios de Azure para examinar el estado de la base de datos de una región específica. Si la base de datos original no está funcionando, puede recuperarla:
	+ Recupere la Base de datos SQL de Azure en la región de Azure donde acaba de recuperar el servicio móvil, tal como se describe en la [Guía sobre continuidad empresarial de Base de datos SQL de Azure].
	+ En el Portal de Azure clásico, en la pestaña **"Configurar"** del servicio móvil, elija "Cambiar base de datos" y seleccione la base de datos recientemente recuperada.

7. El servicio móvil ahora se hospeda en una ubicación física distinta. Deberá actualizar sus credenciales de publicación y/o de git para permitir la actualización del sitio en ejecución.

	+ Si usa un **back-end de .NET**, vuelva a configurar su perfil de publicación, tal como se describe en [Publicación del servicio móvil](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md#publish-your-mobile-service). Con esto se actualizarán los detalles de publicación para que indiquen la nueva ubicación de servicio.
	+ Si usa un **back-end de JavaScript** y administra el servicio con el Portal, no es necesario que realice ninguna acción adicional.

	+ Si usa un **back-end de JavaScript** y administra el servicio con nodo, actualice el git remoto para que indique el repositorio nuevo. Para hacerlo, debe quitar la ruta de acceso al archivo .git del git remoto:

		1. Busque el remoto de origen actual:

				git remote -v
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (fetch)
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (push)

		3. Actualice el remoto con la misma dirección URL, pero sin la ruta de acceso del archivo .git final: git remote set-url origin https://myservice.scm.azure-mobile.net
		4. Extraiga del origen para comprobar que funciona correctamente.

Ahora debería encontrarse en un estado donde el servicio móvil se haya recuperado en una nueva región de Azure y esté aceptando tráfico de las aplicaciones de la Tienda usando su URL original.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Continuidad empresarial de Base de datos SQL de Azure]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Guía sobre continuidad empresarial de Base de datos SQL de Azure]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/
[Github]: https://github.com/
[función de control de código fuente]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[interfaz de la línea de comandos de Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Portal de Azure clásico]: http://manage.windowsazure.com/
[Panel de servicios de Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatización de servicios móviles con CLI de Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!---HONumber=AcomDC_0211_2016-->