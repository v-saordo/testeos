<properties 
   pageTitle="Asistente para publicar aplicación de Azure | Microsoft Azure"
   description="Asistente Publicar aplicaciones de Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Asistente Publicar aplicaciones de Azure.

## Información general

Después de desarrollar una aplicación web en Visual Studio, puede publicarla más fácilmente en un servicio en la nube de Azure mediante el asistente **Publicar aplicación de Azure**. En la primera sección se explican los pasos que se deben completar antes de usar el asistente y en las secciones restantes se explican las características del asistente.

>[AZURE.NOTE] Este tema explica la implementación en servicios en la nube, no en sitios web. Para obtener más información sobre la implementación en sitios web, consulte [Implementación de un sitio web de Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## Requisitos previos

Antes de publicar la aplicación web en Azure, debe tener una cuenta de Microsoft y una suscripción de Azure y asociar la aplicación web con un servicio en la nube de Azure. Si ya completó estas tareas, puede omitir la sección siguiente.

1. Obtenga una cuenta de Microsoft y una suscripción de Azure. Puede probar [aquí](https://azure.microsoft.com/pricing/free-trial/) una suscripción a Azure gratuita durante un mes.

1. Cree un servicio en la nube y una cuenta de almacenamiento de Azure. Puede hacer esto desde el Explorador de servidores en Visual Studio o mediante el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Habilite la aplicación web para Azure. Para habilitar la aplicación web para que se publique en Azure desde Visual Studio, deberá asociarla a un proyecto de servicio en la nube de Azure en Visual Studio. Para crear el proyecto de servicio en la nube asociado, abra el menú contextual del proyecto para la aplicación web y, luego, elija Convertir, **Convertir en proyecto de servicio en la nube de Azure**.

1. Cuando el proyecto del servicio en la nube se agregue a la solución, vuelva a abrir el mismo menú contextual y, luego, elija **Publicar**. Para obtener más información sobre cómo habilitar aplicaciones para Azure, consulte [Procedimiento: para migrar y publicar una aplicación web en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Asegúrese de iniciar Visual Studio con credenciales de administrador (Ejecutar como administrador).

1. Cuando esté preparado para publicar la aplicación, abra el menú contextual del proyecto del servicio en la nube de Azure y elija **Publicar**. Los pasos siguientes muestran el asistente Publicar aplicación de Azure.

## Elegir la suscripción

### Para elegir una suscripción

1. Antes de usar al asistente por primera vez, debe iniciar sesión. Elija el vínculo **Iniciar sesión**. Inicie sesión en el Portal de Azure cuando se le solicite y proporcione su nombre de usuario y contraseña de Azure. 

    ![Esta es una de las pantallas del Asistente para publicación](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    La lista de suscripciones se rellena con las suscripciones asociadas a su cuenta. Es posible que vea también suscripciones de otros archivos de suscripción que importó previamente.

1. En la lista **Elija una suscripción**, elija la suscripción que desee usar para esta implementación.

   Si elige **<Administrar…>**, aparece el cuadro de diálogo **Administrar suscripciones**, en el que puede elegir la suscripción y la cuenta de usuario que desea usar. En la pestaña **Cuentas** se muestran todas las cuentas del usuario y en la pestaña **Suscripciones** se muestran todas las suscripciones asociadas a las cuentas. También puede elegir una región desde la que se vayan a usar los recursos de Azure, así como crear o importar certificados para su suscripción desde el portal de Azure. Si importó suscripciones de un archivo de suscripción, los certificados asociados aparecerán en la pestaña **Certificados**. Cuando termine, elija el botón **Cerrar**.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. Elija el botón **Siguiente** para continuar. 

    Si no hay servicios en la nube en su suscripción, deberá crear un servicio en la nube en Azure para hospedar el proyecto. Aparece el cuadro de diálogo **Crear servicio en la nube y cuenta de almacenamiento**.

    Especifique un nombre nuevo para el servicio en la nube. El nombre debe ser único en Azure. Especifique una región o un grupo de afinidad para un centro de datos que esté cerca de usted o de la mayoría de los clientes. Este nombre también se usa para una nueva cuenta de almacenamiento que Azure crea para su servicio en la nube.

1. Modifique los valores deseados para esta implementación y, a continuación, publíquela mediante el botón **Publicar** (en la sección siguiente se proporcionan más detalles sobre las distintas configuraciones). Para revisar la configuración antes de publicar, elija el botón **Siguiente**.

    >[AZURE.NOTE] Si eligió Publicar en este paso, puede supervisar el estado de esta implementación en Visual Studio.

Puede modificar la configuración avanzada y la configuración común para una implementación mediante el asistente **Publicar aplicación de Azure**. Por ejemplo, puede elegir una configuración para implementar la aplicación en un entorno de prueba antes de liberarla. La siguiente ilustración muestra la pestaña **Configuración común** para una implementación de Azure.

![Configuración común](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## Configuración de la configuración de publicación

### Para configurar la configuración de publicación

1. En la lista **Servicio en la nube**, realice uno de los siguientes conjuntos de pasos:

   1. En el cuadro de lista desplegable, elija un servicio en la nube existente. Aparece la ubicación de centro de datos para este servicio. Debe anotar esta ubicación y asegurarse de que la ubicación de la cuenta de almacenamiento está en el mismo centro de datos.

    1. Elija **Crear nuevo** para crear un servicio en la nube que hospeda Azure. En el cuadro de diálogo **Crear servicio en la nube**, proporcione un nombre para el servicio y, luego, especifique una región o un grupo de afinidad para especificar la ubicación del centro de datos en el que desea hospedar este servicio en la nube. El nombre debe ser único en Azure.

1. En la lista **Entorno**, elija **Producción** o **Ensayo**. Elija el entorno de ensayo si desea implementar la aplicación en un entorno de prueba. Puede mover la aplicación al entorno de producción más tarde.

1. En la lista **Configuración de compilación**, elija **Depurar** o **Liberar**.

1. En la lista **Configuración de servicio**, elija **Nube** o **Local**.

    Active la casilla **Habilitar Escritorio remoto para todos los roles** si desea poder conectarse de forma remota al servicio. Esta opción se emplea principalmente para la solución de problemas. Al activar esta casilla, aparece el cuadro de diálogo **Configuración de Escritorio remoto**. Elija el vínculo Configuración para cambiar la configuración.

    Active la casilla **Habilitar Web Deploy para todos los roles web** para habilitar la implementación web para el servicio. Debe habilitar Escritorio remoto para poder usar esta característica. Para obtener más información, consulte [[Publicar un servicio en la nube mediante Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Para obtener más información sobre Web Deploy, consulte [[Publicar un servicio en la nube mediante Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Elija la pestaña **Configuración avanzada**. En el campo **Etiqueta de implementación**, acepte el nombre predeterminado o especifique un nombre de su elección. Para anexar la fecha a la etiqueta de implementación, deje activada la casilla.

    ![Tercera pantalla del Asistente para publicación](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. En la lista **Cuenta de almacenamiento**, elija la cuenta de almacenamiento que desee usar para esta implementación. Compare las ubicaciones de los centros de datos para el servicio en la nube y la cuenta de almacenamiento. Idealmente, estas ubicaciones deben ser iguales.

    >[AZURE.NOTE] La cuenta de almacenamiento de Azure almacena el paquete para la implementación de la aplicación. Una vez implementada la aplicación, el paquete se quita de la cuenta de almacenamiento.

1. Active la casilla **Actualización de implementación** si desea implementar solo los componentes actualizados. Este tipo de implementación puede ser más rápido que la implementación completa. Elija el vínculo **Configuración** para abrir el cuadro de diálogo **Configuración de actualización de implementación**, que se muestra en la ilustración siguiente.

    ![Configuración de implementación](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Puede elegir cualquiera de las dos opciones para actualizar la implementación, incremental o simultánea. Una implementación incremental actualiza una instancia implementada cada vez, de manera que la aplicación permanece en línea y disponible para los usuarios. Una implementación simultánea actualiza todas las instancias implementadas a la vez. Una actualización simultánea es más rápida que una actualización incremental pero, si se elige esta opción, la aplicación podría no estar disponible durante el proceso de actualización.

    Debe seleccionar la casilla Si no se puede actualizar la implementación, realizar una implementación completa si desea que la implementación completa tenga lugar automáticamente si se produce un error en una actualización de la implementación. Una implementación completa restablece la dirección IP virtual (VIP) para el servicio en la nube. Para obtener más información, consulte [Procedimiento: Cómo conservar una constante de la dirección IP virtual para un servicio en la nube](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Para depurar el servicio, active la casilla **Habilitar IntelliTrace** o bien, si va a implementar una configuración de **depuración** y desea depurar el servicio en la nube en Azure, active la casilla **Habilitar Depurador remoto para todos los roles** para implementar los servicios de depuración remota.

2. Para generar el perfil de esta aplicación, active la casilla **Habilitar generación de perfiles** y, a continuación, elija el vínculo **Configuración** para mostrar las opciones de generación de perfiles.


    >[AZURE.NOTE] Debe usar Visual Studio Ultimate para habilitar IntelliTrace o la generación de perfiles de interacción de capa (TIP), pero no puede habilitar ambos al mismo tiempo.

    Para obtener más información, consulte [Depurar con IntelliTrace y Visual Studio un servicio en la nube](https://msdn.microsoft.com/library/azure/ff683671.aspx) y [Probar el rendimiento de un servicio en la nube](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Elija **Siguiente** para ver la página de resumen de la aplicación.

## Publicación de la aplicación

1. Puede optar por crear un perfil de publicación a partir de la configuración que eligió. Por ejemplo, puede crear un perfil para un entorno de pruebas y otro para producción. Para guardar este perfil, elija el icono **Guardar**. El asistente creará el perfil y lo guardará en el proyecto de Visual Studio. Para modificar el nombre del perfil, abra la lista **Perfil de destino** y, a continuación, elija **<Administrar...>**.

    ![Pantalla de resumen del Asistente para publicación](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] El perfil de publicación aparecerá en el Explorador de soluciones en Visual Studio y su configuración se escribirá en un archivo con la extensión .azurePubxml. La configuración se guarda como atributos de etiquetas XML.

1. Elija **Publicar** para publicar la aplicación. Puede supervisar el estado del proceso en la ventana **Resultados** de Visual Studio.

## Otras referencias

[Procedimiento: para migrar y publicar una aplicación web en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publicar un servicio en la nube mediante Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Probar el rendimiento de un servicio en la nube](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=AcomDC_0204_2016-->