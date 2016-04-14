<properties 
	pageTitle="Configuración del codificador NewTek TriCaster para enviar una transmisión por secuencias en directo de velocidad de bits única" 
	description="En este tema se muestra cómo configurar el codificador en directo TriCaster para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin,anilmur" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="03/01/2016"   
	ms.author="juliako"/>

#Uso del codificador NewTek TriCaster para enviar una transmisión por secuencias en directo de velocidad de bits única

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

En este tema se muestra cómo configurar el codificador en directo [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo. Para obtener más información, consulte [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).

En este tutorial se muestra cómo administrar Servicios multimedia de Azure (AMS) con la herramienta Explorador de Servicios multimedia de Azure (AMSE). Esta herramienta solo se ejecuta en Windows PC. Si se encuentra en Mac o Linux, use el Portal de Azure clásico para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y [programas](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program).

>[AZURE.NOTE]Cuando se utiliza Tricaster para enviar una fuente de contribución a Servicios multimedia de Azure puede haber problemas de audio y vídeo en el evento en directo si utiliza determinadas características de Tricaster, como un corte rápido entre las fuentes o el cambio a/de caretas. El equipo de AMS está trabajando en la solución de estos problemas; hasta entonces, no es recomendable usar estas características.


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

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique un nombre de canal (el campo de descripción es opcional). En Configuración de canal, seleccione **Estándar** para la opción de codificación en directo, con el protocolo de entrada establecido en **RTMP**. Puede dejar todas las demás opciones como están.


Asegúrese de que la opción **Iniciar el nuevo canal ahora** esté seleccionada.

3. Haga clic en **Crear canal**. ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] El canal puede tardar hasta 20 minutos en iniciarse.


Mientras se inicia el canal puede [configurar el codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Tenga en cuenta que la facturación comienza tan pronto como el canal entra en un estado Listo. Para obtener más información, consulte [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Configuración del codificador NewTek TriCaster

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

1. Cree un nuevo proyecto de **NewTek TriCaster** según el origen de entrada de vídeo que se use. 
2. Una vez dentro de ese proyecto, busque el botón **Stream** (Transmitir) y haga clic en el icono de engranaje junto a él para acceder al menú de configuración de transmisiones.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Cuando se abra el menú, haga clic en **New** (Nueva) bajo el encabezado de la conexión. Cuando se le pregunte por el tipo de conexión, seleccione **Adobe Flash**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Haga clic en **Aceptar**.

5. Ahora se puede importar un perfil FMLE; para ello, haga clic en la flecha desplegable situada bajo **Streaming Profile** (Perfil de streaming) y vaya a **Browse** (Examinar).

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Vaya a la ubicación donde guardó el perfil FMLE configurado.
7. Selecciónelo y presione **OK** (Aceptar).

	Una vez cargado el perfil, continúe con el paso siguiente.

6. Obtenga la dirección URL de entrada del canal para asignarla al **punto de conexión de RTMP** de Tricaster.
	
	Navegue de nuevo a la herramienta AMSE y compruebe el estado de finalización del canal. Una vez que se ha cambiado el estado de **Iniciando** a **En ejecución**, puede obtener la dirección URL de entrada.
	  
	Mientras se ejecuta el canal, haga clic con el botón derecho en el nombre del canal, desplácese hacia abajo y mantenga el puntero sobre **Copiar dirección URL de entrada en el portapapeles** y seleccione **Dirección URL de entrada principal**.
	
	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Pegue esta información en el campo **Ubicación** en **Flash Server** dentro del proyecto de Tricaster. Además, asigne un nombre de transmisión en el campo **Id. de transmisión**.

	Si se ha agregado la información de la transmisión al perfil FMLE, también se puede importar en esta sección; para ello, haga clic en **Import Settings** (Importar configuración), desplácese hasta el perfil FMLE guardado y haga clic en **OK** (Aceptar). Se deben rellenar los campos correspondientes de Flash Server con la información de FMLE.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Cuando termine, haga clic en **OK** (Aceptar) en la parte inferior de la pantalla. Cuando las entradas de audio y vídeo en Tricaster estén preparadas, comience a transmitir a AMS haciendo clic en el botón **Stream** (Transmitir).

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT] Antes de hacer clic **Stream** (Transmitir), **debe** asegurarse de que el canal está listo. Además, asegúrese de no dejar el canal en un estado Listo sin una fuente de contribución de entrada durante más de 15 minutos.

##Reproducción de pruebas
  
1. Vaya a la herramienta AMSE y haga clic con el botón derecho en el canal que se va a probar. En el menú, mantenga el puntero sobre **Reproducir la vista previa** y seleccione **con el Reproductor multimedia de Azure**.  

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Si la transmisión aparece en el reproductor, entonces el codificador se configuró correctamente para conectarse a AMS.

Si se recibe un error, se deberá restablecer el canal y ajustar la configuración del codificador. Consulte el tema [Solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.

##Creación de un programa

1. Una vez confirmada la reproducción de canales, cree un programa. En la pestaña **Directo** de la herramienta AMSE, haga clic con el botón derecho dentro del área de programas y seleccione **Crear programa**.  

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

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

<!---HONumber=AcomDC_0302_2016-->