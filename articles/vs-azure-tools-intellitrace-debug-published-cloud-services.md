<properties 
   pageTitle="Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado | Microsoft Azure"
   description="Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="tarcher" />



# Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado

##Información general

Con IntelliTrace, puede registrar información de depuración amplia para una instancia de rol cuando se ejecuta en Azure. Si necesita encontrar la causa de un problema, puede utilizar los registros de IntelliTrace para recorrer su código desde Visual Studio como si se estuviera ejecutando en Azure. De hecho, IntelliTrace graba datos de entorno y ejecución de código de clave cuando su aplicación de Azure se ejecuta como servicio en la nube en Azure y le permite reproducir los datos grabados desde Visual Studio. Como alternativa, puede utilizar la depuración remota para conectarse directamente a un servicio en la nube que se ejecuta en Azure. Consulte [Depuración de Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT]IntelliTrace está pensado solo para escenarios de depuración y no debe usarse para una implementación de producción.

>[AZURE.NOTE]Puede utilizar IntelliTrace si tiene Visual Studio Enterprise instalado y su aplicación de Azure tiene como destino .NET Framework 4 o una versión posterior. IntelliTrace recopila información para sus roles de Azure. Las máquinas virtuales para estos roles siempre ejecutan sistemas operativos de 64 bits.

## Para configurar una aplicación de Azure para IntelliTrace

Para habilitar IntelliTrace para una aplicación de Azure, debe crear y publicar la aplicación desde un proyecto de Azure de Visual Studio. Debe configurar IntelliTrace para su aplicación de Azure antes de su publicación en Azure. Si publica su aplicación sin configurar IntelliTrace pero termina decidiendo que desea hacerlo, deberá publicar la aplicación de nuevo desde Visual Studio. Para obtener más información, consulte [Publicar un servicio en la nube mediante Azure Tools](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Cuando esté preparado para implementar su aplicación de Azure, compruebe que sus destinos de compilación del proyecto se establecen en **Depurar**.

1. Abra el menú contextual del proyecto de Azure en el Explorador de soluciones y elija **Publicar**.
 
    Aparecerá el asistente para publicar aplicación de Azure.

1. Para recopilar registros de IntelliTrace para su aplicación al publicarse en la nube, seleccione la casilla **Habilitar IntelliTrace**.

    >[AZURE.NOTE]Puede habilitar IntelliTrace o bien la generación de perfiles al publicar su aplicación de Azure. No puede habilitar ambas cosas.

1. Para personalizar la configuración de IntelliTrace básica, elija el hipervínculo **Configuración**.

    Aparece el cuadro de diálogo Configuración de IntelliTrace, como se muestra en la ilustración siguiente. Puede especificar qué eventos se van a registrar, si se va a recopilar información de llamada, para qué módulos y procesos se van a recopilar registros y cuánto espacio se va a asignar a la grabación. Para obtener más información sobre IntelliTrace, consulte [Depuración con IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

El registro de IntelliTrace es un archivo de registro circular del máximo tamaño especificado en la configuración de IntelliTrace (el tamaño predeterminado es de 250 MB). Los registros de IntelliTrace se recopilan en un archivo en el sistema de archivos de la máquina virtual. Al solicitar los registros, se toma una instantánea en ese momento en el tiempo y se descarga en su equipo local.

Después de publicarse la aplicación de Azure en Azure, puede determinar si se ha habilitado IntelliTrace desde el nodo de proceso de Azure en el Explorador de servidores, como se muestra en la siguiente imagen:

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## Descarga de registros de IntelliTrace para una instancia de rol

Puede descargar registros de IntelliTrace para una instancia de rol desde el nodo **Servicios en la nube** en **Explorador de servidores**. Expanda el nodo **Servicios en la nube** hasta que encuentre la instancia en la que está interesado, abra el menú contextual de esta instancia y elija **Ver registros de IntelliTrace**. Los registros de IntelliTrace se descargan en un archivo de un directorio de su equipo local. Cada vez que solicita los registros de IntelliTrace, se crea una nueva instantánea.

Cuando se descargan los registros, Visual Studio muestra el progreso de la operación en la ventana Registro de actividad de Azure. Como se muestra en la ilustración siguiente, puede expandir el artículo de línea de la operación para ver más detalles.

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Puede continuar trabajando en Visual Studio mientras se descargan los registros de IntelliTrace. Cuando el registro haya terminado la descarga, se abrirá automáticamente en Visual Studio.

>[AZURE.NOTE]Es posible que los registros de IntelliTrace contengan excepciones que el marco genera y maneja posteriormente. El código del marco interno genera estas excepciones como parte normal del inicio de un rol, por lo que puede omitirlas de forma segura.

## Otras referencias

[Depuración de Servicios en la nube](https://msdn.microsoft.com/library/ee405479.aspx)

<!---HONumber=AcomDC_1223_2015-->