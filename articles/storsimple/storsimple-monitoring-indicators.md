<properties 
    pageTitle="Indicadores de supervisión de StorSimple | Microsoft Azure" 
    description="Describe los diodos emisores de luz (LED) y alarmas audibles que se usan para supervisar el estado del dispositivo StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="01/05/2016"
    ms.author="v-sharos" />

# Usar indicadores de supervisión de StorSimple para administrar su dispositivo   

## Información general

El dispositivo StorSimple incluye diodos emisores de luz (LED) y alarmas que sirven para supervisar los módulos y el estado general del dispositivo StorSimple. Los indicadores de supervisión pueden estar en los componentes de hardware del revestimiento principal del dispositivo y del revestimiento de EBOD. Estos indicadores de supervisión pueden ser LED o alarmas audibles.

Existen tres estados de LED para indicar el estado de un módulo: verde, intermitencia entre verde y rojo-ámbar o rojo-ámbar.

- Los LED verdes señalan un estado operativo correcto.  
- Los LED con intermitencia entre verde y rojo-ámbar señalan la existencia de condiciones no críticas que pueden precisar de la intervención del usuario.  
- Los LED rojo-ámbar señalan que hay un error crítico en el módulo.  

En lo que resta de artículo se describen los distintos indicadores LED de supervisión, sus ubicaciones en el dispositivo StorSimple, el estado del dispositivo en función de los estados de LED y las alarmas audibles asociadas.

## Indicadores LED en el panel frontal

El panel frontal, también conocido como *panel de operaciones*, muestra el estado global de todos los módulos del sistema. El panel frontal es idéntico en el gabinete EBOD y el gabinete principal de StorSimple, tal como se ilustra a continuación.

   ![Panel frontal del dispositivo][1]
 
El panel frontal contiene los siguientes indicadores:

1. Botón de encendido
2. Indicador LED de alimentación (verde/rojo-ámbar)
3. Indicador LED de error de módulo (encendido rojo-ámbar/apagado)
4. Indicador LED de error lógico (encendido rojo-ámbar/apagado
5. Pantalla de identificación de la unidad  

La principal diferencia entre los LED del panel frontal del dispositivo y los del revestimiento de EBOD estriba en el **número de identificación de la unidad de sistema** que aparece en la pantalla LED. El identificador de unidad predeterminado que se muestra en el dispositivo es **00**, mientras que el del revestimiento de EBOD es **01**. Esto permite diferenciar rápidamente entre el dispositivo y el revestimiento de EBOD cuando el dispositivo está encendido. Si el dispositivo está apagado, use la información recogida en [Activar un dispositivo nuevo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar el dispositivo del revestimiento de EBOD.

## Estado de los LED del panel frontal  

Use la siguiente tabla para conocer el estado indicado por los LED del panel frontal del dispositivo o del revestimiento de EBOD.

|Alimentación del sistema | Error de módulo | Error lógico | Alarma | Estado|
|-------------|---------------|-----------------|-------|-------|
|Rojo-ámbar | Apagado | Apagado | N/D | Pérdida de corriente alterna, funcionamiento con la alimentación de respaldo, o bien corriente alterna funcionando y módulos de controlador retirados.|
|Verde | Encendido | Encendido | N/D | Alimentación del panel de operaciones en estado de prueba (5 s)|
|Verde | Apagado | Apagado | N/D | Encendido, todas las funciones correctas|
|Verde | Encendido |N/D | LED de error del PCM, LED de error del ventilador | Cualquier error del PCM, error del ventilador, exceso o defecto de temperatura|
| Verde | Encendido | N/D | LED del módulo de E/S | Cualquier error del módulo de controlador|
| Verde | Encendido | N/D | N/D | Error lógico del revestimiento|
| Verde | Intermitente | N/D | LED de estado de módulo en módulo del controlador. LED de error del PCM, LED de error del ventilador | Tipo de módulo de controlador desconocido instalado, error de bus I2C, error de configuración de datos de producto vitales del módulo de controlador |

## Indicadores LED del módulo de refrigeración de alimentación (PCM)   

Los indicadores LED del módulo de refrigeración de alimentación (PCM) se encuentran en la parte posterior del revestimiento principal o del revestimiento de EBOD de cada módulo PCM. Aquí indicamos cómo interpretar los siguientes LED para controlar el estado del dispositivo StorSimple.

- LED de PCM del revestimiento principal
- LED de PCM del revestimiento de EBOD

## LED de PCM del revestimiento principal  

El dispositivo StorSimple tiene un módulo PCM de 764 W con una batería adicional. En la siguiente ilustración se muestra el panel de indicadores LED del dispositivo.

   ![LED de PCM del revestimiento principal][2]

Leyenda de LED:

1. Error de corriente alterna
2. Error del ventilador
3. Error de la batería
4. PCM correcto
5. Error de corriente continua
6. Batería correcta  

El estado del PCM se indica en el panel de LED. El panel de LED de PCM del dispositivo tiene seis LED. Cuatro de ellos indican el estado de la fuente de alimentación y el ventilador, mientras que los dos restantes señalan el estado del módulo de la batería de reserva en el PCM. Use las siguientes tablas para conocer el estado del PCM.

### Indicadores LED del PCM relativos a la fuente de alimentación y el ventilador
| Estado | PCM correcto (verde) | Error de corriente alterna (ámbar) | Error de ventilador (ámbar) | Error de corriente continua (ámbar) |
|--------|----------------|-----------------------|------------------|----------------------|
| No hay corriente alterna (en el revestimiento) | Apagado | Apagado | Apagado | Apagado|
| No hay corriente alterna (solo en este PCM) | Apagado | Encendido | Apagado | Encendido |
| Hay corriente alterna y el PCM está encendido: todo correcto | Encendido | Apagado | Apagado | Apagado |
| Error de PCM (error de ventilador) | Apagado | Apagado | Encendido | N/D |
| Error de PCM (exceso de amperaje, de voltaje o de corriente) | Apagado | Encendido | Encendido | Encendido |
| PCM (ventilador fuera de tolerancia) | Encendido | Apagado | Apagado | Encendido |
| Modo de espera | Intermitente | Apagado | Apagado | Apagado |
| Descarga del firmware del PCM | Apagado | Intermitente | Intermitente | Intermitente |

### Indicadores LED del PCM relativos a la batería de reserva  

| Estado | Batería correcta (verde) | Error de batería (ámbar) |
|--------|----------------------|-----------------------|
| No hay batería | Apagado | Apagado |
| Hay batería y está cargada | Encendido | Apagado |
| Batería en carga o en descarga de mantenimiento | Intermitente | Apagado |
| Error flexible de la batería (recuperable) | Apagado | Intermitente |
| Error severo de la batería (recuperable) | Apagado | Encendido |
| Batería desarmada | Intermitente | Apagado |

## LED de PCM del revestimiento de EBOD  

El revestimiento de EBOD tiene un PCM de 580 W sin batería adicional. El panel del PCM del revestimiento de EBOD tiene LED únicamente para las fuentes de alimentación y el ventilador. En la siguiente ilustración se muestran estos indicadores.

   ![LED de PCM del revestimiento de EBOD][3]
 
Use la siguiente tabla para conocer el estado del PCM.

| Estado | PCM correcto (verde) | Error de corriente alterna (ámbar) | Error de ventilador (ámbar) | Error de corriente continua (ámbar) |
|--------|---------------|------------------------|------------------|----------------------|
| No hay corriente alterna (en el revestimiento) | Apagado | Apagado | Apagado | Apagado |
| No hay corriente alterna (solo en este PCM) | Apagado | Encendido | Apagado | Encendido |
| Hay corriente alterna y el PCM está encendido: todo correcto | Encendido | Apagado | Apagado | Apagado |
| Error de PCM (error de ventilador) | Apagado | Apagado | Encendido | X |
| Error de PCM (exceso de amperaje, de voltaje o de corriente) | Apagado | Encendido | Encendido | Encendido |
| PCM (ventilador fuera de tolerancia) | Encendido | Apagado | Apagado | Encendido |
| Modo de espera | Intermitente | Apagado | Apagado | Apagado |
| Descarga del firmware del PCM | Apagado | Intermitente | Intermitente | Intermitente |

## Indicadores LED del módulo del controlador  

El dispositivo StorSimple dispone de LED relativos al controlador principal y a los módulos de controlador de EBOD.

### LED de supervisión del controlador principal
La siguiente ilustración sirve para identificar los LED en el controlador principal con mayor facilidad. (Se muestran todos los componentes a modo orientativo).

   ![LED de supervisión: controlador principal][4]
 
Use la siguiente tabla para saber si el módulo del controlador está funcionando correctamente.

### Indicadores LED de controlador  

| LED | Descripción                                                                            
|---- | ----------- |
| LED de identificación (azul) | Indica que el módulo se está identificando. Si el LED azul parpadea en un controlador en funcionamiento, quiere decir que es el controlador activo y el otro, el controlador en modo de espera. Para más información, consulte [Identificar el controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| LED de error (ámbar) | Indica un error en el controlador.        
| LED correcto (verde) | Un color verde fijo indica que el controlador funciona correctamente. La intermitencia en verde indica un error de configuración de VPD del controlador. |
| LED de actividad SAS (verde) | Un color verde fijo señala una conexión sin ninguna actividad actual. La intermitencia en verde indica que la conexión tiene una actividad en curso. |
| Indicadores LED de estado de Ethernet | El lado derecho indica la actividad de vínculo/red: (verde fijo) vínculo activo, (intermitencia en verde) actividad de red. El lado izquierdo indica la velocidad de la red: (amarillo) 1000 Mb/s, (verde) 100 Mb/s y (apagado) 10 Mb/s. Según el modelo del componente, esta luz puede parpadear aun cuando la interfaz de red no esté habilitada. |
| LED POST | Indica el progreso de inicio cuando el controlador está encendido. Si el dispositivo StorSimple no puede iniciarse, este LED servirá para que el equipo de soporte técnico de Microsoft pueda identificar el punto en el proceso de inicio en el que se produjo el error. |

>[AZURE.IMPORTANT]Si el LED de error está encendido, quiere decir que hay un problema con el módulo del controlador que podría resolverse reiniciando el controlador. Póngase en contacto con el equipo de soporte técnico de Microsoft si reiniciar el controlador no resuelve este problema.


### LED de supervisión del EBOD (revestimiento de EBOD)  

Cada uno de los controladores de EBOD SAS de 6 Gb/s tiene varios LED que indican su estado, como se muestra en la siguiente ilustración.

  ![LED de supervisión: revestimiento de EBOD][5]

Use la siguiente tabla para saber si el módulo del controlador EBOD funciona con normalidad.

### Indicadores LED del módulo de controlador de EBOD  

|Estado | Módulo de E/S correcto (verde) | Error del módulo de E/S (ámbar) | Actividad de puerto del host (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Módulo de controlador correcto | Encendido | Apagado | - |
| Error del módulo de controlador | Apagado | Encendido | - |
| Sin conexión de puerto de host externo | - | - | Apagado |
| Conexión de puerto de host externo: sin actividad | - | - | Encendido|
| Conexión de puerto de host externo: actividad | - | - | Intermitente |
| Error de metadatos del módulo de controlador | Intermitente | - | - |

## Indicadores LED de unidad de disco del revestimiento principal y el revestimiento de EBOD

El dispositivo StorSimple tiene unidades de disco en el revestimiento principal y en el revestimiento de EBOD. Cada una de ellas cuenta con varios indicadores LED de supervisión, como se describe en esta sección.

El estado de las unidades de disco se indica con un LED verde y un LED rojo-ámbar montados en la parte frontal de cada módulo portador de unidades. En la siguiente ilustración se muestran estos indicadores.

  ![LED de unidad de disco][6]
 
Use la siguiente tabla para conocer el estado de cada unidad de disco, lo que a su vez afecta al estado de los LED del panel frontal.

### Indicadores LED de unidad de disco del revestimiento de EBOD  

| Estado | LED de actividad correcta (verde) | LED de error (rojo-ámbar) | LED del panel de operaciones asociado |
|-------|--------------------------|----------------------|-------------------------|
| No hay unidades instaladas | Apagado | Apagado | Ninguno |
| Unidad instalada y operativa | Intermitencia con actividad | X | Ninguno |
| Conjunto de identidad de dispositivo de los servicios de SCSI Enclosure (SES) | Encendido | Intermitente 1 segundo sí/1 segundo no | Ninguno |
| Conjunto de bits de error de dispositivo SES | Encendido | Encendido | Error lógico (rojo) |
| Error de circuito de control de alimentación | Apagado | Encendido | Error del módulo (rojo) |

## Alarmas audibles  

Un dispositivo StorSimple cuenta con alarmas audibles asociadas al revestimiento principal y el revestimiento de EBOD. Las alarmas audibles se emiten desde el panel frontal (también conocido como panel de operaciones) de ambos revestimientos. Una alarma audible indica que hay una condición de error. Las siguientes condiciones activarán la alarma:

- Error o avería del ventilador
- Voltaje fuera del intervalo
- Condición de exceso o defecto de temperatura
- Saturación térmica
- Error del sistema
- Error lógico
- Error de la fuente de alimentación
- Eliminación de un módulo de refrigeración de alimentación (PCM)  

En la siguiente tabla se describen los distintos estados de alarma.

### Estados de alarma  

| Estado de alarma | Acción | Acción cuando el botón Silenciar está presionado |
|------------|---------|---------------------------------|
| S0 | Modo normal: silencioso | Pitido doble |
| S1 | Modo de error: 1 segundo activado/1 segundo desactivado | Transición a S2 o a S3 (ver notas) |
| S2 | Modo de recordatorio: pitido intermitente | Ninguna |
| S3 | Modo en silencio: silencioso | Ninguna |
| S4 | Modo de error crítico: alarma continua | No disponible: silencio no activo |

> [AZURE.NOTE]

>  - Si en el estado de alarma S1 no presiona Silenciar en los siguientes 2 minutos, pasará automáticamente a S2 o S3.  
>  - Los estados de alarma S1 a S4 vuelven a S0 una vez resuelta la condición de error.  
>  - Se puede pasar a un estado de error crítico S4 desde cualquier otro estado.  

Puede desactivar la alarma audible presionando el botón Silencio del panel de operaciones. La alarma se silenciará automáticamente transcurridos dos minutos si el botón de silencio no se acciona manualmente. Cuando la alarma está silenciada, seguirá sonando con pitidos intermitentes breves para indicar que aún existe un problema. La alarma se silenciará por completo cuando no haya más problemas.

En la siguiente tabla se describen las diversas condiciones de alarma.

### Condiciones de alarma  

| Estado | Gravedad | Alarma | LED del panel de operaciones |
|--------|---------|--------|----------------|
| Alerta de PCM: pérdida de corriente continua de un único PCM | Error: no hay pérdida de redundancia | S1 | Error de módulo|
|Alerta de PCM: pérdida de corriente continua de un único PCM | Error: pérdida de redundancia | S1 | Error de módulo |
| Error de ventilador del PCM | Error: pérdida de redundancia | S1 | Error de módulo |
| El módulo SBB detectó un error de PCM | Error | S1 | Error de módulo |
| PCM extraído | Error de configuración | Ninguna | Error de módulo |
| Error de configuración del revestimiento | Error: crítico | S1 | Error de módulo |
| Alerta de advertencia temperatura baja | Advertencia | S1 | Error de módulo |
| Alerta de advertencia temperatura alta | Advertencia | S1 | Error de módulo |
| Alarma de exceso de temperatura | Error: crítico | S1 | Error de módulo |
| Error de bus I2C | Error: pérdida de redundancia | S1 | Error de módulo |
| Error de comunicación del panel de operaciones (I2C) | Error: crítico | S1 | Error de módulo |
| Error de controlador | Error: crítico | S1 | Error de módulo |
| Error del módulo de la interfaz SBB | Error: crítico | S1 | Error de módulo |
| Error del módulo de la interfaz SBB: no quedan módulos en funcionamiento | Error: crítico | S4 | Error de módulo |
| Módulo de interfaz SBB extraído | Advertencia | Ninguna | Error de módulo |
| Error de control de alimentación de la unidad | Advertencia: sin pérdida de alimentación de la unidad | S1 | Error de módulo |
| Error de control de alimentación de la unidad | Error: crítico; pérdida de alimentación de la unidad | S1 | Error de módulo |
| Unidad extraída | Advertencia | Ninguna | Error de módulo |
| Alimentación insuficiente disponible | Advertencia | Ninguna | Error de módulo |

## Pasos siguientes

Obtenga más información sobre los [componentes de hardware de StorSimple y su estado](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 

<!---HONumber=AcomDC_0107_2016-->
