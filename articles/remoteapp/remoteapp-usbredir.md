<properties 
    pageTitle="Redireccionamiento de los dispositivos USB en Azure RemoteApp | Microsoft Azure" 
    description="Más información acerca de cómo usar el redireccionamiento para dispositivos USB en Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/11/2016" 
    ms.author="elizapo" />



# Redireccionamiento de los dispositivos USB en Azure RemoteApp

El redireccionamiento de dispositivos permite a los usuarios emplear los dispositivos USB conectados a su equipo o tableta con las aplicaciones de Azure RemoteApp. Por ejemplo, si comparte Skype a través de Azure RemoteApp, los usuarios necesitan poder usar las cámaras de su dispositivo.

Antes de proseguir, asegúrese de leer la información de redireccionamiento de USB en [Uso del redireccionamiento de RemoteApp de Azure RemoteApp](remoteapp-redirection.md). Sin embargo, el nusbdevicestoredirect:s: * recomendado no funcionará para las cámaras web USB y puede que no funcione para algunas impresoras USB o los dispositivos USB multifuncionales. Por diseño y por motivos de seguridad, el Administrador de Azure RemoteApp tiene que habilitar el redireccionamiento por GUID de clase de dispositivo o por id. de instancia de dispositivo antes de que los usuarios pueden usar esos dispositivos.

Aunque en este artículo se trata del redireccionamiento de cámaras web, puede usar un enfoque similar para redirigir impresoras USB y otros dispositivos USB multifuncionales que no se redirigen mediante el comando **nusbdevicestoredirect:s:***.

## Opciones de redireccionamiento de dispositivos USB
Azure RemoteApp usa mecanismos muy similares para redirigir dispositivos USB como los que están disponibles para los Servicios de Escritorio remoto. La tecnología subyacente le permite elegir el método de redireccionamiento correcto para un dispositivo determinado, para obtener lo mejor del nivel alto y el redireccionamiento del dispositivo USB RemoteFX mediante el comando **usbdevicestoredirect:s:**. Hay cuatro elementos en este comando:

| Orden de procesamiento | Parámetro | Descripción |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | Selecciona todos los dispositivos que no se recogen mediante el redireccionamiento de alto nivel. Nota: por diseño, * no funciona para las cámaras web USB. |
| | {GUID de clase de dispositivo} | Selecciona todos los dispositivos que coinciden con la clase de configuración de dispositivo especificada. |
| | USB\\InstanceID | Selecciona un dispositivo USB especificado para el identificador de instancia determinado. |
| 2 | -USB\\Instance ID | Quita la configuración de redireccionamiento para el dispositivo especificado. |

## Redireccionamiento de un dispositivo USB mediante el GUID de clase de dispositivo
Hay dos formas de buscar el GUID de clase del dispositivo que se puede usar para el redireccionamiento.

La primera opción es usar las [Clases de configuración de dispositivos definidos por el sistema disponibles para proveedores](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Seleccione la clase que mejor coincida con el dispositivo conectado al equipo local. Para las cámaras digitales, podría ser una clase de dispositivo de creación de imágenes o una clase de dispositivo de captura de vídeo. Deberá realizar alguna experimentación con las clases de dispositivos para encontrar el GUID de clase correcto que funciona con el dispositivo USB conectado localmente (en nuestro caso, la cámara web).

Una forma mejor, o la segunda opción, es seguir estos pasos para encontrar el GUID de clase de dispositivo específico:

1. Abra el Administrador de dispositivos, busque el dispositivo que se redirigirá, haga doble clic en él y, a continuación, abra las propiedades. ![Abra el Administrador de dispositivos](./media/remoteapp-usbredir/ra-devicemanager.png)
2. En la pestaña **Detalles**, elija la propiedad **Guid de clase**. El valor que aparece es el GUID de clase de ese tipo de dispositivo. ![Propiedades de la cámara](./media/remoteapp-usbredir/ra-classguid.png)
3. Use el valor de Guid de clase para redirigir los dispositivos que coincidan con él.

Por ejemplo:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "usbdevicestoredirect:s:<Class Guid value>"

Puede combinar varios redireccionamientos de dispositivos en el mismo cmdlet. Por ejemplo: para redirigir el almacenamiento local y una cámara web USB, cmdlet tiene el siguiente aspecto:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`usbdevicestoredirect:s:<Class Guid value>"

Al establecer el redireccionamiento de dispositivos por GUID de clase se redirigen todos los dispositivos que coinciden con ese GUID de clase de la colección especificada. Por ejemplo, si hay varios equipos en la red local que tienen las mismas cámaras web USB, puede ejecutar un solo cmdlet para redirigir todas las cámaras web.

## Redireccionamiento de un dispositivo USB con el identificador de instancia del dispositivo

Si desea un control más preciso y desea controlar el redireccionamiento de dispositivos, puede usar el parámetro de redireccionamiento **USB\\InstanceID**.

La parte más difícil de este método es encontrar el identificador de instancia del dispositivo USB. Necesitará tener acceso al equipo y al dispositivo USB específico. A continuación, siga estos pasos:

1. Habilite el redireccionamiento de dispositivos en la sesión de Escritorio remoto, como se describe en [Uso de mis dispositivos y recursos en una sesión de Escritorio remoto?](http://windows.microsoft.com/es-ES/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Abra una conexión a Escritorio remoto y haga clic en **Mostrar opciones**.
3. Haga clic en **Guardar como** para guardar la configuración de conexión actual en un archivo RDP. ![Almacenamiento de la configuración como un archivo RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Elija un nombre de archivo y una ubicación, por ejemplo, "MyConnection.rdp" y "Este equipo\\Documentos" y guarde el archivo.
5. Abra el archivo MyConnection.rdp con un editor de texto y busque el identificador de instancia del dispositivo que desee redirigir.

Ahora puede usar el identificador de instancia en el siguiente cmdlet:

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "usbdevicestoredirect:s: USB<Device InstanceID value>"



### Permítanos ayudarle 
¿Sabía que, además de clasificar este artículo y realizar comentarios abajo, puede realizar cambios en el artículo? ¿Falta algo? ¿Algo no es correcto? ¿Algo de lo que he escrito es simplemente confuso? Desplácese hacia arriba y haga clic en **Editar en GitHub** para realizar cambios que nos llegarán para su revisión y, a continuación, una vez que los aprobemos, verá los cambios y mejoras aquí.

<!---HONumber=AcomDC_0218_2016-->