<properties 
   pageTitle="Encendido o apagado del dispositivo de StorSimple | Microsoft Azure"
   description="Explica cómo encender un dispositivo de StorSimple nuevo, encender un dispositivo apagado o que perdió energía y apagar un dispositivo activo."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Encendido o apagado del dispositivo de StorSimple 

## Información general

No se requiere apagar el dispositivo de Microsoft Azure StorSimple como parte del funcionamiento normal del sistema. Sin embargo, es posible que sea necesario encender un dispositivo nuevo o puede que se haya debido apagar un dispositivo. Por lo general, se requiere apagar un dispositivo en casos en que se debe reemplazar algún hardware con error, mover físicamente una unidad o quitar un dispositivo del servicio. En este tutorial, se describe el procedimiento necesario para encender y apagar el dispositivo de StorSimple en distintos escenarios.

En la tabla siguiente, se muestran varios escenarios en los cuales se debe activar y apagar el dispositivo de StorSimple y aparecen vínculos a los procedimientos adecuados.

|Escenario|Temas de referencia|
|:-------|:---------------|
|Activar un dispositivo nuevo|[Activar un dispositivo nuevo](#turn-on-a-new-device)<ul><li>[Dispositivo nuevo solo con gabinete principal](#new-device-with-primary-enclosure-only)</li><li>[Dispositivo nuevo con gabinete EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Activar un dispositivo después del apagado|[Activar un dispositivo después del apagado](#turn-on-a-device-after-shutdown)<ul><li>[Dispositivo solo con gabinete principal](#device-with-primary-enclosure-only)</li><li>[Dispositivo con gabinete EBOD](#device-with-ebod-enclosure)</li></ul>|
|Activar un dispositivo después de una pérdida de energía|[Activar un dispositivo después de una pérdida de energía](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo solo con gabinete principal](#8100)</li><li>[Dispositivo con gabinete EBOD](#8600)</li></ul>|
|Activar un dispositivo tras la pérdida de la conexión entre el gabinete principal y EBOD|[Activar un dispositivo tras la pérdida de la conexión entre el gabinete principal y EBOD](#turn-on-a-device-after-the-primary-and-EBOD-enclosure-connection-is-lost)|
|Apagar un dispositivo activo|[Apagar un dispositivo activo](#turn-off-a-running-device)<ul><li>[Dispositivo solo con gabinete principal](#8100a)</li><li>[Dispositivo con gabinete EBOD](#8600a)</li></ul>|

## Activar un dispositivo nuevo

Los pasos para activar por primera vez un dispositivo de Microsoft Azure StorSimple difieren si el modelo del dispositivo es 8100 u 8600. El modelo 8100 tiene un único gabinete principal, mientras que el modelo 8600 es un dispositivo de dos gabinetes con un gabinete principal y un gabinete EBOD. Las secciones siguientes abarcan los pasos detallados para ambos modelos.

- [Dispositivo nuevo solo con gabinete principal](#new-device-with-primary-enclosure-only)

- [Dispositivo nuevo con gabinete EBOD](#new-device-with-ebod-enclosure)

### Dispositivo nuevo solo con gabinete principal

El modelo StorSimple 8100 es un dispositivo con un único gabinete. El dispositivo incluye módulos de alimentación y de refrigeración (PCM) redundantes. Ambos PCM deben estar instalados y conectados a diferentes fuentes de alimentación para garantizar una alta disponibilidad.

Realice los pasos siguientes para pasar los cables de alimentación del dispositivo.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Si desea consultar las instrucciones de cableado y configuración del dispositivo, vaya a [Instalar el dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Asegúrese de seguir las instrucciones de manera exacta.

### Dispositivo nuevo con gabinete EBOD

El modelo StorSimple 8600 tiene un gabinete principal y un gabinete EBOD. Esto requiere que se realice el cableado de alimentación y conectividad de SCSI conectadas en serie (SAS) de las unidades en conjunto.

Cuando configure este dispositivo por primera vez, realice primero los pasos para el cableado de SAS y luego complete los pasos para el cableado de alimentación.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Si desea consultar las instrucciones de cableado y configuración del dispositivo, vaya a [Instalar el dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Asegúrese de seguir las instrucciones de manera exacta.

## Activar un dispositivo después del apagado

Los pasos para activar un dispositivo de Microsoft Azure StorSimple después del apagado son distintos si el modelo del dispositivo es 8100 u 8600. El modelo 8100 tiene un único gabinete principal, mientras que el modelo 8600 es un dispositivo de dos gabinetes con un gabinete principal y un gabinete EBOD.

- [Dispositivo solo con gabinete principal](#device-with-primary-enclosure-only)

- [Dispositivo con gabinete EBOD](#device-with-ebod-enclosure)

### Dispositivo solo con gabinete principal

Después del apagado, utilice el procedimiento siguiente para encender un dispositivo StorSimple con un gabinete principal y sin gabinete EBOD.

#### Para activar un dispositivo solo con gabinete principal

1. Asegúrese de que el interruptor de alimentación de cada uno de los módulos de alimentación y refrigeración (PCM) se encuentran en la posición de apagado. Si los interruptores no se encuentran en la posición de apagado, póngalos en esa posición y espere que se apaguen las luces.

2. Gire el interruptor de alimentación de ambos PCM a la posición de encendido para encender el dispositivo. El sistema se debería encender.

3. Revise lo siguiente para comprobar que el dispositivo esté completamente encendido:

    1. Las luces LED de actividad correcta de ambos módulos PCM aparecen en verde.

    2. Las luces LED de ambos controladores son fijas y aparecen en verde.

    3. La luz LED azul de uno de los controladores aparece intermitente, lo que indica que el controlador está activo.

    Si alguna de estas condiciones no se cumple, el dispositivo no funciona correctamente. [Póngase en contacto con el soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

### Dispositivo con gabinete EBOD

Después del apagado, utilice el procedimiento siguiente para encender un dispositivo StorSimple con un gabinete principal y un gabinete EBOD. Realice cada paso de la secuencia tal como se describe. De lo contrario, podría haber una pérdida de datos.

#### Para encender un dispositivo con un gabinete principal y un gabinete EBOD

1. Asegúrese de que el gabinete EBOD está conectado al gabinete principal. Para obtener más información, consulte [Instalar el dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Asegúrese de que los módulos de alimentación y refrigeración (PCM) del gabinete EBOD y del gabinete principal se encuentran en la posición de apagado. Si los interruptores no se encuentran en la posición de apagado, póngalos en esa posición y espere que se apaguen las luces.

3. Gire el interruptor de alimentación de ambos PCM a la posición de encendido para encender primero el gabinete EBOD. Las luces LED de los PCM deben aparecer en verde. Una luz LED verde en el controlador EBOD de esta unidad indica que el gabinete EBOD está encendido.

4. Gire el interruptor de alimentación de ambos PCM a la posición de encendido para encender el gabinete principal. Ahora todo el sistema debería estar encendido.

5. Compruebe que las luces LED de SAS son verdes, lo que garantiza que la conexión entre el gabinete EBOD y el gabinete principal funciona correctamente.

## Activar un dispositivo después de una pérdida de energía

Una interrupción o una pérdida de energía puede apagar un dispositivo de Microsoft Azure StorSimple. El corte de energía puede producirse en uno o en ambos sistemas de alimentación. Los pasos de recuperación son distintos si el modelo del dispositivo es 8100 u 8600. El modelo 8100 tiene un único gabinete principal, mientras que el modelo 8600 es un dispositivo de dos gabinetes con un gabinete principal y un gabinete EBOD. En esta sección se describe el procedimiento de recuperación para cada uno de los escenarios.

- [Dispositivo solo con gabinete principal](#8100)

- [Dispositivo con gabinete EBOD](#8600)

### Dispositivo solo con gabinete principal <a name="8100">

El sistema puede funcionar de manera normal si se produce una pérdida de energía en uno de sus sistemas de alimentación. Sin embargo, para garantizar la alta disponibilidad del dispositivo, restaure la energía del sistema de alimentación tan pronto como sea posible.

Si hay un corte o una interrupción en la energía en ambos sistemas de alimentación, el sistema se apagará de manera ordenada y controlada. Cuando se restaura la energía, el sistema se enciende automáticamente.

### Dispositivo con gabinete EBOD <a name="8600">

#### Pérdida de energía en un sistema de alimentación

El sistema puede funcionar de manera normal si se produce una pérdida de energía en el gabinete principal o en el gabinete EBOD. Sin embargo, para garantizar la alta disponibilidad del dispositivo, restaure la energía del sistema de alimentación tan pronto como sea posible.

#### Pérdida de energía en ambos sistemas de alimentación en el gabinete principal y el gabinete EBOD

Si hay un corte o una interrupción en la energía en ambos sistemas de alimentación, el gabinete EBOD se apagará inmediatamente y el gabinete principal se apagará de manera ordenada y controlada. Cuando se restaure la energía, el dispositivo se iniciará automáticamente.

Si la energía se apaga manualmente, haga lo siguiente para restaurar la energía del sistema.

1. Encienda el gabinete EBOD.

2. Una vez que encienda el gabinete EBOD, encienda el gabinete principal.

### Pérdida de energía en ambos sistemas de alimentación en el gabinete EBOD

Cuando instala los cables, debe asegurarse de que el gabinete EBOD nunca esté conectado solo a un PDU independiente. Si el gabinete EBOD y el gabinete principal presentan errores al mismo tiempo, el sistema se recuperará.

Si solo el gabinete EBOD presenta errores en ambos sistemas de alimentación, el sistema no se recuperará de manera automática. Haga lo siguiente para encender el sistema y restaurarlo a un estado correcto:

1. Si el gabinete principal está encendido, apague ambos módulos de alimentación y enfriamiento (PCM).

2. Espere unos minutos hasta que se apague el sistema.

3. Encienda el gabinete EBOD.

4. Una vez que encienda el gabinete EBOD, encienda el gabinete principal.

## Activar un dispositivo tras la pérdida de la conexión entre el gabinete principal y EBOD

Si se pierde la conexión entre el controlador en espera y el controlador EBOD correspondiente, el dispositivo sigue funcionando. Si se pierde la conexión entre el controlador activo del sistema y el controlador EBOD correspondiente, se producirá la conmutación por error y el dispositivo debería seguir funcionando de manera normal.

Cuando se quitan ambos cables SAS o se interrumpe la conexión entre el gabinete EBOD y el gabinete principal, el dispositivo dejará de funcionar. En este punto, haga lo siguiente.

### Para encender el dispositivo tras la pérdida de la conexión

1. Obtenga acceso a la parte posterior del dispositivo.

2. Si se interrumpe la conexión del cable SAS entre el gabinete EBOD y el gabinete principal, se apagarán todas las luces LED del canal de SAS del gabinete EBOD.

3. Apagué los módulos de alimentación y enfriamiento (PCM) del gabinete EBOD y del gabinete principal.

4. Espere hasta que se apaguen todas las luces que se encuentran en la parte posterior de ambos gabinetes.

5. Vuelva a insertar los cables SAS y asegúrese de que la conexión entre el gabinete EBOD y el gabinete principal sea correcta.

6. Encienda primero el gabinete EBOD; para ello, gire ambos interruptores del PCM a la posición de encendido.

7. Revise que la luz LED verde esté encendida para asegurarse de que el gabinete EBOD también lo está.

8. Encienda el gabinete principal.

9. Revise que la luz LED verde del controlador esté encendida para asegurarse de que el gabinete principal también lo está.

10. Revise que las luces LED del canal SAS (cuatro por cada controlador EBOD) estén todas encendidas para comprobar que la conexión del gabinete EBOD con el gabinete principal es correcta.

>[AZURE.IMPORTANT]Si los cables SAS son defectuosos o la conexión entre el gabinete EBOD y el gabinete principal no es buena, cuando encienda el sistema, entrará en el modo de recuperación. Si esto sucede, [póngase en contacto con el soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

## Apagar un dispositivo activo

Es posible que sea necesario apagar un dispositivo de Microsoft Azure StorSimple activo si debe moverse, quitarlo del servicio o si tiene un componente que no funciona correctamente y que se debe reemplazar. Los pasos son distintos si el modelo del dispositivo de Microsoft Azure StorSimple es 8100 u 8600. El modelo 8100 tiene un único gabinete principal, mientras que el modelo 8600 es un dispositivo de dos gabinetes con un gabinete principal y un gabinete EBOD. En esta sección, se detallan los pasos pagara apagar un dispositivo activo.

- [Dispositivo con gabinete principal](#8100a)

- [Dispositivo con gabinete EBOD](#8600a)

### Dispositivo con gabinete principal <a name="8100a"> 

Actualmente, no hay manera de apagar un dispositivo de StorSimple activo desde el Portal de Azure clásico. La única manera de apagarlo es mediante el uso de Windows PowerShell para StorSimple. Para apagar el dispositivo de manera ordenada y controlada, obtenga acceso a Windows PowerShell para StorSimple y siga los pasos siguientes.

>[AZURE.IMPORTANT]No apague un dispositivo activo con el botón de inicio/apagado que se encuentra en la parte posterior del dispositivo.
>
>Antes de apagar el dispositivo, asegúrese de que todos los componentes del mismo funcionen bien. En el Portal de Azure clásico, vaya a **Dispositivos** > **Mantenimiento** > **Estado del hardware** y compruebe que el color del estado de todos los componentes sea verde. Esto solo sucede si el sistema funciona correctamente. Si el dispositivo se apaga para reemplazar un componente que no funciona correctamente, verá una luz LED de estado de error (rojo) o degradado (amarillo) para el componente respectivo en el **Estado del hardware**.

Puede conectarse a Windows PowerShell para StorSimple mediante la consola serie del dispositivo o a través de la comunicación remota de Windows PowerShell. Después de obtener acceso a Windows PowerShell para StorSimple, haga lo siguiente para apagar un dispositivo activo.

#### Para apagar un dispositivo activo

1. Conéctese a la consola serie del dispositivo.

2. En el menú que aparece, compruebe que el controlador al que está conectado es el controlador **en espera**. Si no es así, desconéctese y conéctese al otro controlador.

3. En el menú de la consola serie, elija **Opción 1** para iniciar sesión en el controlador en espera con acceso total.

4. En el símbolo del sistema, escriba:

    `Stop-HCSController`

    Con esto se debería apagar el controlador en espera actual.

    >[AZURE.IMPORTANT]Espere hasta que el controlador se apague completamente antes de continuar con el paso siguiente.

5. Para comprobar que finalizó el apagado, revise la parte posterior del dispositivo. El LED de error del controlador debe ser fijo y aparecer en rojo.

6. Conéctese al controlador activo a través de la consola serie y siga los mismos pasos para apagarlo.

7. Una vez apagados por completo los dos controladores, los LED de estado de ambos controladores deben parpadear en rojo.

8. Si necesita apagar el dispositivo por completo en este momento, cambie los interruptores de alimentación de los módulos de alimentación y refrigeración (PCM) a la posición de apagado.

9. Para comprobar que el dispositivo esté completamente apagado, revise que todas las luces de la parte posterior del dispositivo estén apagadas.

### Dispositivo con gabinete EBOD <a name="8600a">

>[AZURE.IMPORTANT]Antes de apagar el gabinete principal y el gabinete EBOD, asegúrese de que todos los componentes del dispositivo funcionen bien. En el Portal de Azure clásico, vaya a **Dispositivos** > **Mantenimiento** > **Estado del hardware** y compruebe que todos los componentes estén en buen estado.

#### Para apagar un dispositivo activo con gabinete EBOD

1. Siga todos los pasos que aparecen en [Dispositivo solo con gabinete principal](#8100a).

2. Una vez que se apaga el gabinete principal, apague el gabinete EBOD; para ello, gire ambos interruptores del módulo de alimentación y enfriamiento (PCM) a la posición de apagado.

3. Para comprobar que el EBOD se apagó, revise que todas las luces de la parte posterior del gabinete EBOD están apagadas.

>[AZURE.NOTE]Los cables SAS que se utilizan para conectar el gabinete EBOD con el gabinete principal no deben quitarse hasta después que se apague el sistema.

## Pasos siguientes

Si encuentra problemas al encender o apagar un dispositivo de StorSimple, [póngase en contacto con el soporte técnico de Microsoft Support](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_1203_2015-->