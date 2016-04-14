<properties
   pageTitle="Administración de configuraciones de servicio y perfiles | Microsoft Azure"
   description="Aprenda a trabajar con las configuraciones de servicio y los archivos de configuración de perfiles que almacenan la configuración de los entornos de implementación y la configuración de publicación para los servicios en la nube."
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

# Administración de configuraciones de servicio y perfiles

## Información general

Al publicar un servicio en la nube, Visual Studio almacena la información de configuración en dos tipos de archivos de configuración: configuraciones de servicio y perfiles. Las configuraciones de servicio (archivos .cscfg) almacenan valores para los entornos de implementación de un servicio en la nube de Azure. Azure emplea estos archivos de configuración para administrar sus servicios en la nube. Por otro lado, los perfiles (archivos .azurePubxml) almacenan la configuración de publicación para los servicios en la nube. Estos valores son un registro de las selecciones que realiza al usar el Asistente para la publicación y Visual Studio los usa localmente. En este tema se explica cómo trabajar con ambos tipos de archivos de configuración.

## Configuraciones de servicio

Puede crear varias configuraciones de servicio para cada uno de los entornos de implementación. Por ejemplo, puede crear una configuración de servicio para el entorno local que emplea para ejecutar y probar una aplicación de Azure y otra configuración de servicio para el entorno de producción.

Puede agregar, eliminar, cambiar el nombre y modificar las configuraciones de servicio según sus necesidades. Puede administrarlas desde Visual Studio, como se muestra en la siguiente ilustración.

![Administrar configuraciones de servicio](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

También puede abrir el cuadro de diálogo **Administrar configuraciones** en las páginas de propiedades del rol. Para abrir las propiedades de un rol en un proyecto de Azure, abra el menú contextual para ese rol y, a continuación, elija **propiedades**. En la pestaña **Configuración** expanda la lista **Configuración de servicio** y, a continuación, haga clic en **Administrar** para abrir el cuadro de diálogo **Administrar configuraciones**.

### Para agregar una configuración de servicio

1. En el Explorador de soluciones, abra el menú contextual del proyecto de Azure y, a continuación, elija **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar configuraciones de servicio**.

1. Para agregar una configuración de servicio, tiene que crear una copia de una configuración existente. Para ello, elija la configuración que desea copiar en la lista Nombre y, a continuación, haga clic en **Crear copia**.

1. (Opcional) Para dar un nombre diferente a la configuración del servicio, elija la nueva configuración de servicio en la lista Nombre y, a continuación, haga clic en **Cambiar nombre**. En el cuadro de texto **Nombre**, escriba el nombre que desea usar para esta configuración de servicio y, a continuación, haga clic en **Aceptar**.

    Se agrega un nuevo archivo de configuración de servicio denominado ServiceConfiguration.[Nuevo nombre].cscfg al proyecto de Azure en el Explorador de soluciones.


### Para eliminar una configuración de servicio

1. En el Explorador de soluciones, abra el menú contextual del proyecto de Azure y luego haga clic en **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar configuraciones de servicio**.

1. Para eliminar una configuración de servicio, elija la configuración que quiera eliminar en la lista **Nombre** y luego haga clic en **Quitar**. Aparece un cuadro de diálogo para verificar que desea eliminar esta configuración.

1. Hacer clic en **Eliminar**.

     Se quita el archivo de configuración de servicio del proyecto de Azure en el Explorador de soluciones.


### Para cambiar el nombre de una configuración de servicio

1. En el Explorador de soluciones, abra el menú contextual del proyecto de Azure y luego haga clic en **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar configuraciones de servicio**.

1. Para dar un nombre diferente a la configuración de servicio, elija la nueva configuración de servicio en la lista **Nombre** y luego haga clic en **Cambiar nombre**. En el cuadro de texto **Nombre**, escriba el nombre que quiera usar para esta configuración de servicio y luego haga clic en **Aceptar**.

    Se cambia el nombre del archivo de configuración de servicio del proyecto de Azure en el Explorador de soluciones.

### Para cambiar una configuración de servicio

- Si desea cambiar una configuración del servicio, abra el menú contextual del rol concreto que desea cambiar en el proyecto de Azure y, a continuación, haga clic en Propiedades. Para más información vea [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx).

## Uso de los perfiles para realizar diferentes combinaciones de valores

Mediante el uso de un perfil, puede rellenar automáticamente el **Asistente para publicación** con diferentes combinaciones de configuración para distintos fines. Por ejemplo, puede tener un perfil para depuración y otro para de compilaciones de versión. En ese caso, su perfil de **depuración** tendría **IntelliTrace** habilitado y la configuración de **depuración** seleccionada y su perfil de **versión** tendría **IntelliTrace** deshabilitado y la configuración de **versión** seleccionada. También puede usar varios perfiles para implementar un servicio con una cuenta de almacenamiento diferente.

Al ejecutar el asistente por primera vez, se crea un perfil predeterminado. Visual Studio almacena el perfil en un archivo con una extensión .azurePubXml, que se agrega al proyecto de Azure en la carpeta **Perfiles**. Si especifica manualmente diferentes opciones al ejecutar el asistente más adelante, el archivo se actualiza automáticamente. Antes de ejecutar el siguiente procedimiento, tendría que haber publicado ya su servicio en la nube al menos una vez.

### Para agregar un perfil

1. Abra el menú contextual del proyecto de Azure y haga clic en **Publicar**.

1. Junto a la lista **Perfil de destino**, haga clic en el botón **Guardar perfil** , como se muestra en la ilustración siguiente. Con esto se crea un perfil.

    ![Crear un nuevo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Después de crea el perfil, haga clic en **<Administrar...>** en la lista **Perfil de destino**.

    Aparece el cuadro de diálogo **Administrar perfiles**, como se muestra en la ilustración siguiente.

    ![Administrar diálogo de perfiles](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. En la lista **Nombre**, elija un perfil y luego haga clic en **Crear copia**.

1. Elija el botón **Cerrar**.

    El nuevo perfil aparece en la lista de perfiles de destino.

1. En la lista **Perfil de destino** , haga clic en el perfil que acaba de crear. La configuración del Asistente para publicación se rellena con las opciones del perfil seleccionado.

1. Haga clic en los botones **Anterior** y **Siguiente** para mostrar cada página del Asistente para publicación y luego personalice la configuración para este perfil. Para más información vea [Asistente para publicar aplicación de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085).

1. Cuando termine de personalizar la configuración, haga clic en **Siguiente** para volver a la página de configuración. El perfil se guarda cuando se publica el servicio usando estos valores o si hace clic en **Guardar** junto a la lista de perfiles.

### Para cambiar el nombre de un perfil o eliminarlo

1. Abra el menú contextual del proyecto de Azure y haga clic en **Publicar**.

1. En la lista **Perfil de destino**, haga clic en **Administrar**.

1. En el cuadro de diálogo **Administrar perfiles**, haga clic en el perfil que quiere eliminar y luego haga clic en **Quitar**.

1. En el cuadro de diálogo de confirmación que aparece, haga clic en **Aceptar**.

1. Haga clic en **Cerrar**.

### Para cambiar un perfil

1. Abra el menú contextual del proyecto de Azure y haga clic en **Publicar**.

1. En la lista **Perfil de destino** , haga clic en el perfil que quiera cambiar.

1. Haga clic en los botones **Anterior** y **Siguiente** para mostrar cada página del Asistente para publicación y luego cambie los valores de configuración que quiera. Para más información vea [Asistente para publicar aplicación de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085).

1. Cuando termine de cambiar la configuración, haga clic en **Siguiente** para volver a la página de **configuración**.

1. (Opcional) Haga clic en **Publicar** para publicar el servicio en la nube con los nuevos valores. Si no desea publicar su servicio en la nube en este momento y cierra al Asistente para publicación, Visual Studio le pregunta si desea guardar los cambios en el perfil.

## Pasos siguientes

Para información sobre cómo configurar otras partes de su proyecto de Azure desde Visual Studio, vea [Configurar un proyecto de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075).

<!---HONumber=AcomDC_1223_2015-->