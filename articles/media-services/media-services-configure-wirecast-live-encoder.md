<properties 
	pageTitle="Uso del codificador Telestream Wirecast para enviar una transmisión por secuencias en directo de velocidad de bits única" 
	description="En este tema se muestra cómo configurar el codificador en directo Wirecast para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="juliako"/>

#Uso del codificador Wirecast para enviar una transmisión por secuencias en directo de velocidad de bits única

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

En este tema se muestra cómo configurar el codificador en directo [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo. Para obtener más información, consulte [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).

En este tutorial se muestra cómo administrar Servicios multimedia de Azure (AMS) con la herramienta Explorador de Servicios multimedia de Azure (AMSE). Esta herramienta solo se ejecuta en Windows PC. Si se encuentra en Mac o Linux, use el Portal de Azure clásico para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y [programas](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program).


##Requisitos previos

- [Creación de una cuenta de Servicios multimedia de Azure](media-services-create-account.md)
- Asegúrese de que haya un extremo de streaming en ejecución que tenga asignada al menos una unidad de streaming. Para obtener más información, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md).
- Debe instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Inicie la herramienta y conéctese a la cuenta de AMS.

##Sugerencias

- Siempre que sea posible, use una conexión a Internet por cable.
- Una buena regla general al determinar los requisitos de ancho de banda consiste en duplicar las velocidades de bits de streaming. Aunque no se trata de un requisito obligatorio, contribuirá a mitigar el impacto de la congestión de la red.
- Cuando se usen codificadores por software, cierre todos los programas innecesarios.


## Crear un canal

1.  En la herramienta AMSE, navegue a la pestaña **Directo** y haga clic con el botón derecho dentro del área de canales. Seleccione **Crear canal...** en el menú.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique un nombre de canal (el campo de descripción es opcional). En Configuración de canal, seleccione **Estándar** para la opción de codificación en directo, con el protocolo de entrada establecido en **RTMP**. Puede dejar todas las demás opciones como están.


Asegúrese de que la opción **Iniciar el nuevo canal ahora** esté seleccionada.

3. Haga clic en **Crear canal**. ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] El canal puede tardar hasta 20 minutos en iniciarse.

Mientras se inicia el canal puede [configurar el codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Tenga en cuenta que la facturación comienza tan pronto como el canal entra en un estado Listo. Para obtener más información, consulte [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configuración del codificador Telestream Wirecast

En este tutorial se usa la siguiente configuración de salida. En el resto de esta sección se describen los pasos de configuración con más detalle.

**Vídeo**:
 
- Codec (Códec): H.264 
- Profile (Perfil): High (Level 4.0) (Alto [Nivel 4.0]) 
- Bitrate (Velocidad de bits): 5000 kbps 
- Keyframe (Fotograma clave): 2 seconds (60 seconds) (2 segundos [60 segundos]) 
- Frame Rate (Velocidad de fotogramas): 30
 
**Audio**:

- Codec (Códec): AAC (LC) 
- Bitrate (Velocidad de bits): 192 kbps 
- Sample Rate (Frecuencia de muestreo): 44,1 kHz


###Pasos de configuración

1. Abra la aplicación de Telestream Wirecast en el equipo que está usando y configúrela para el streaming de RTMP.
2. Configure la salida, para ello, desplácese hasta la pestaña **Output** (Salida) y seleccione **Output Settings** (Configuración de salida).
	
	Asegúrese de que **Output Destination** (Destino de salida) está establecido en **RTMP Server** (Servidor RTMP).
3. Haga clic en **Aceptar**.
4. En la página de configuración, establezca el campo **Destination** (Destino) en **Servicios multimedia de Azure**.
 
	El perfil de codificación está previamente seleccionado como **Azure H.264 720 p 16:9 (1280 x 720)**. Para personalizar esta configuración, seleccione el icono de engranaje a la derecha de la lista desplegable y, luego, elija **New Preset** (Nuevo valor preestablecido).

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configure los valores preestablecidos del codificador.

	Asigne un nombre al valor preestablecido y busque la siguiente configuración recomendada:

	**Vídeo**
	
	- Encoder (Codificador): MainConcept H.264
	- Frames per Second (Fotogramas por segundo): 30
	- Bit Rate (Velocidad de bits): 5000 kbps/sec (seg.) (se puede ajustar en función de las limitaciones de red)
	- Profile (Perfil): Main (Principal)
	- Key frame every (Fotograma clave cada): 60 frames (fotogramas)

	**Audio**

	- Target bit rate (Velocidad de bits de destino): 192 kbits/sec (seg)
	- Sample Rate (Frecuencia de muestreo): 44,100 kHz
	 
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Presione **Save** (Guardar).

	El campo de codificación tiene ahora el perfil recién creado disponible para la selección.

	Asegúrese de que esté seleccionado el nuevo perfil.

7. Obtenga la dirección URL de entrada del canal para asignarla al **punto de conexión de RTMP** de Wirecast.
	
	Navegue de nuevo a la herramienta AMSE y compruebe el estado de finalización del canal. Una vez que se ha cambiado el estado de **Iniciando** a **En ejecución**, puede obtener la dirección URL de entrada.
	  
	Mientras se ejecuta el canal, haga clic con el botón derecho en el nombre del canal, desplácese hacia abajo y mantenga el puntero sobre **Copiar dirección URL de entrada en el portapapeles** y seleccione **Dirección URL de entrada principal**.
	
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. En la ventana **Output Settings** (Configuración de salida) de Wirecast, pegue esta información en el campo **Address** (Dirección) de la sección de salida y asigne un nombre de transmisión.


	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Seleccione **Aceptar**.

10. En la pantalla principal de **Wirecast**, confirme que los orígenes de audio y vídeo están listos y, luego, haga clic en **Stream** (Transmitir) en la esquina superior izquierda.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT] Antes de hacer clic **Stream** (Transmitir), **debe** asegurarse de que el canal está listo. Además, asegúrese de no dejar el canal en un estado Listo sin una fuente de contribución de entrada durante más de 15 minutos.

##Reproducción de pruebas
  
1. Vaya a la herramienta AMSE y haga clic con el botón derecho en el canal que se va a probar. En el menú, mantenga el puntero sobre **Reproducir la vista previa** y seleccione **con el Reproductor multimedia de Azure**.  

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Si la transmisión aparece en el reproductor, entonces el codificador se configuró correctamente para conectarse a AMS.

Si se recibe un error, se deberá restablecer el canal y ajustar la configuración del codificador. Consulte el tema [Solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.

##Creación de un programa

1. Una vez confirmada la reproducción de canales, cree un programa. En la pestaña **Directo** de la herramienta AMSE, haga clic con el botón derecho dentro del área de programas y seleccione **Crear programa**.  

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. Dé nombre al programa y, si es necesario, ajuste el valor de **Duración de la ventana de archivo** (que de forma predeterminada es 4 horas). También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.
3. Active la casilla **Iniciar el programa ahora**.
4. Haga clic en **Crear programa**.  
  
	Nota: la creación de programas tarda menos que la creación de canales.
 
5. Cuando el programa esté en ejecución, confirme la reproducción. Para ello, haga clic con el botón derecho en el programa y vaya a **Reproducir los programas**. Luego, seleccione **con el Reproductor multimedia de Azure**.
6. Una vez confirmada, haga clic con el botón derecho de nuevo en el programa y seleccione **Copiar la dirección URL de salida en el portapapeles** (o recupere esta información desde la opción **Información y configuración del programa** en el menú). 

La transmisión está ahora preparada para insertarse en un reproductor o distribuirse a una audiencia para su visualización en directo.


## Solución de problemas
 
Consulte el tema [Solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->