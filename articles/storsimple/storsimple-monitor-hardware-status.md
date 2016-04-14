<properties 
   pageTitle="Componentes de hardware de StorSimple y su estado | Microsoft Azure"
   description="Obtenga información sobre cómo supervisar los componentes de hardware del dispositivo StorSimple a través del servicio Administrador de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/05/2016"
   ms.author="alkohli" />

# Usar el servicio de Administrador de StorSimple para supervisar componentes y estados de hardware

## Información general

En este artículo se describen los distintos componentes físicos y lógicos en el dispositivo StorSimple local. También se explica cómo supervisar el estado de los componentes del dispositivo mediante la página **Mantenimiento** del servicio Administrador de StorSimple.

En la página **Mantenimiento** aparece el estado del hardware de todos los componentes del dispositivo StorSimple.

En la lista de componentes para 8100, se describen tres secciones:

- **Componentes compartidos**: no forman parte de los controladores, como las unidades de disco, el revestimiento, los componentes del PCM y la temperatura del PCM, la tensión de línea y los sensores de corriente de línea.

- **Componentes del Controlador 0**: componentes que residen en el Controlador 0, por ejemplo, el controlador, el ampliador y el conector de SAS, los sensores de temperatura del controlador y las diversas interfaces de red.

- **Componentes del Controlador 1**: componentes que constituyen el Controlador 1, similares a los detallados en el Controlador 0.

Un dispositivo 8600 tiene componentes adicionales que se corresponden con el revestimiento de un grupo extendido de discos (EBOD). En la lista de componentes, hay cinco secciones. De estas, hay tres que contienen los componentes en el revestimiento principal y son idénticas a las que se describen para 8100. Hay dos secciones adicionales para el revestimiento de EBOD que describen lo siguiente:

- **Componentes compartidos del revestimiento de EBOD**: componentes presentes en el revestimiento de EBOD y el PCM que no forman parte del controlador de EBOD.

- **Componentes del Controlador 0 de EBOD**: componentes que residen en el revestimiento 0 de EBOD, como el controlador de EBOD, el ampliador y el conector de SAS y los sensores de temperatura del controlador.

- **Componentes del Controlador 1 de EBOD**: componentes que constituyen el revestimiento 1 de EBOD, similares a los detallados en el revestimiento 0 de EBOD.

>[AZURE.NOTE]**La sección del estado de hardware no está presente en la página Mantenimiento para un dispositivo virtual StorSimple (1100).**


## Supervisión del estado del hardware

Realice los pasos siguientes para ver el estado de hardware de un componente del dispositivo:

1. Vaya a **Dispositivos** y seleccione un dispositivo de StorSimple concreto. Haga clic en él para ir al menú de nivel de dispositivo y haga clic en **Mantenimiento**. 
2. Busque la sección **Estado de hardware** y elija entre los componentes disponibles (como se describió anteriormente). Simplemente haga clic en una flecha situada antes de la etiqueta del componente para expandir la lista y ver el estado de los distintos componentes del dispositivo. Consulte la [lista detallada de componentes del revestimiento principal](#component-list-for-primary-enclosure-of-storsimple-device) y la [lista detallada de componentes del revestimiento de EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

2. Use el siguiente esquema de codificación de color para interpretar el estado de los componentes:
	-  **Marca de verificación verde**: denota un componente **Correcto** u **OK**.
	-  **Amarillo**: denota un componente en estado **Advertencia**.
	-  **Signo de exclamación rojo**: denota un componente que tiene un estado de **Error** o **Necesita atención**.
	-  **Blanco con texto negro**: denota un componente que no está presente.

3. Si se encuentra un componente que no está en estado **Correcto**, póngase en contacto con el soporte técnico de Microsoft. Si las alertas están habilitadas en el dispositivo, recibirá una alerta por correo electrónico. Si necesita reemplazar un componente de hardware con errores, consulte la [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).


## Lista de componentes de la caja principal del dispositivo StorSimple

En la tabla siguiente se describen los componentes físicos y lógicos que contiene la caja principal del dispositivo StorSimple local.

|Componente|Módulo|Escriba|Ubicación|¿Unidad reemplazable en campo (FRU)?|Descripción|
|---|---|---|---|---|---|
|Unidad en la ranura [0-11]|Unidades de disco|Física|Compartido|Sí|Hay una línea preesnte para cada una de las unidades SSD o HDD en el revestimiento principal.|
|Sensor de temperatura ambiente|Revestimiento|Física|Compartido|No|Mide la temperatura del chasis.|
|Sensor de temperatura de plano medio|Revestimiento|Física|Compartido|No|Mide la temperatura del plano medio.|
|Alarma audible|Revestimiento|Física|Compartido|No|Indica si el subsistema de alarma audible del chasis es funcional.|
|Revestimiento|Revestimiento|Física|Compartido|Sí|Indica la presencia de un chasis.|
|Configuración del revestimiento|Revestimiento|Física|Compartido|No|Hace referencia al panel frontal del chasis.|
|Sensores de voltaje de línea|PCM|Física|Compartido|No|En numerosos sensores de tensión de línea se muestra el estado, que indica si la tensión medida está dentro de la tolerancia.|
|Sensores de corriente de línea|PCM|Física|Compartido|No|En numerosos sensores de corriente de línea se muestra el estado, que indica si la corriente medida está dentro de la tolerancia.|
|Sensores de temperatura en el PCM|PCM|Física|Compartido|No|En numerosos sensores de temperatura como los sensores Inlet y Hotspot se muestra el estado, que indica si la temperatura medida está dentro de la tolerancia.|
|Fuente de alimentación [0-1]|PCM|Física|Compartido|Sí|Hay una línea presente para cada fuente de alimentación en las dos PCM situadas en la parte posterior del dispositivo.|
|Refrigeración [0-1]|PCM|Física|Compartido|Sí|Hay una línea presente para cada uno de los cuatro ventiladores de refrigeración que residen en los dos PCM.|
|Batería [0-1]|PCM|Física|Compartido|Sí|Hay una línea presente para cada uno de los módulos de batería de reserva que están alojados en el PCM.|
|Metis|N/D|Lógicos|Compartido|N/D|Muestra el estado de las baterías: si necesitan recargarse o se acercan al fin de ciclo de vida.|
|Clúster|N/D|Lógicos|Compartido|N/D|Muestra el estado del clúster que se crea entre los dos módulos de controlador integrados.|
|Nodo de clúster|N/D|Lógicos|Compartido|N/D|Indica el estado del controlador como parte del clúster.|
|Quórum de clúster|N/D|Lógicos||N/D|Indica la presencia de la pertenencia al disco de mayoría en el grupo de almacenamiento de la unidad de disco duro.|
|Espacio de datos de la unidad de disco duro|N/D|Lógicos|Compartido|N/D|El espacio de almacenamiento que se usa para los datos en el grupo de almacenamiento de la unidad de disco duro (HDD).|
|Espacio de administración de la unidad de disco duro|N/D|Lógicos|Compartido|N/D|El espacio reservado en el grupo de almacenamiento de la unidad de disco duro para tareas de administración.|
|Espacio de quórum de la unidad de disco duro|N/D|Lógicos|Compartido|N/D|El espacio reservado en el grupo de almacenamiento de la unidad de disco duro para el quórum del clúster.|
|Espacio de reemplazo de la unidad de disco duro|N/D|Lógicos|Compartido|N/D|El espacio reservado en el grupo de almacenamiento de la unidad de disco duro para el reemplazo del controlador.|
|Espacio de datos de la unidad de estado sólido|N/D|Lógicos|Compartido|N/D|El espacio de almacenamiento para los datos en el grupo de almacenamiento de la unidad de estado sólido (SSD).|
|Espacio de NVRAM de la unidad de estado sólido|N/D|Lógicos|Compartido|N/D|El espacio de almacenamiento en el grupo de almacenamiento de la unidad de estado sólido dedicado para la lógica NVRAM.|
|Grupo de almacenamiento de la unidad de disco duro|N/D|Lógicos|Compartido|N/D|Muestra el estado del grupo de almacenamiento lógico que se crea desde las unidades de disco duro del dispositivo.|
|Grupo de almacenamiento de la unidad de estado sólido|N/D|Lógicos|Compartido|N/D|Muestra el estado del grupo de almacenamiento lógico que se crea desde las unidades de estado sólido del dispositivo.|
|Controller [0-1] [estado]|E/S|Física|Controller|Sí|Muestra el estado del controlador y si está en modo activo o en espera dentro del chasis.|
|Sensores de temperatura en el controlador|E/S|Física|Controller|No|En numerosos sensores de temperatura como los sensores del módulo de E/S, de temperatura de la CPU, de DIMM y de PCI se muestra el estado, que indica si la temperatura se encuentra dentro de la tolerancia.|
|Ampliador SAS|E/S|Física|Controller|No|Indica el estado del ampliador del SCSI acoplado en serie (SAS), que se usa para conectar el almacenamiento integrado al controlador.|
|Conector SAS [0-1]|E/S|Física|Controller|No|Indica el estado de cada conector SAS, que se usa para conectar el almacenamiento integrado al ampliador SAS.|
|Interconexión de plano medio de SBB|E/S|Física|Controller|No|Indica el estado del conector de plano medio, que se usa para conectar cada controlador al plano medio.|
|Núcleo del procesador|E/S|Física|Controller|No|Indica el estado de los núcleos del procesador en cada controlador.|
|Potencia de la electrónica del revestimiento|E/S|Física|Controller|No|Indica el estado del sistema de alimentación que usa el revestimiento.|
|Diagnóstico de la electrónica del revestimiento|E/S|Física|Controller|No|Indica el estado de los subsistemas de diagnóstico que proporciona el controlador.|
|Controlador de administración de placa base (BMC)|E/S|Física|Controller|No|Indica el estado del controlador de administración de placa base (BMC), que es un procesador de servicios especializado que supervisa el dispositivo de hardware a través de sensores y se comunica con el administrador del sistema a través de una conexión independiente.|
|Ethernet|E/S|Física|Controller|No|Indica el estado de cada una de las interfaces de red, es decir, la administración y los puertos de datos proporcionados en el controlador.|
|NVRAM|E/S|Física|Controller|No|Indica el estado de la NVRAM, una memoria de acceso aleatorio no volátil respaldada por la batería que sirve para conservar la información crítica para la aplicación en caso de error de alimentación.|

## Lista de componentes de la caja EBOD del dispositivo StorSimple

En la tabla siguiente se describen los componentes físicos y lógicos que contiene la caja EBOD del dispositivo StorSimple local.

|Componente|Módulo|Escriba|Ubicación|¿FRU?|Descripción|
|---|---|---|---|---|---|
|Unidad en la ranura [0-11]|Unidades de disco|Física|Compartido|Sí|Hay una línea presente para cada una de las unidades HDD en el revestimiento de EBOD.|
|Sensor de temperatura ambiente|Revestimiento|Física|Compartido|No|Mide la temperatura del chasis.|
|Sensor de temperatura de plano medio|Revestimiento|Física|Compartido|No|Mide la temperatura del plano medio.|
|Alarma audible|Revestimiento|Física|Compartido|No|Indica si el subsistema de alarma audible del chasis es funcional.|
|Revestimiento|Revestimiento|Física|Compartido|Sí|Indica la presencia de un chasis.|
|Configuración del revestimiento|Revestimiento|Física|Compartido|No|Hace referencia al OPS o panel frontal del chasis.|
|Sensores de voltaje de línea|PCM|Física|Compartido|No|En numerosos sensores de tensión de línea se muestra el estado, que indica si la tensión medida está dentro de la tolerancia.|
|Sensores de corriente de línea|PCM|Física|Compartido|No|En numerosos sensores de corriente de línea se muestra el estado, que indica si la corriente medida está dentro de la tolerancia.|
|Sensores de temperatura en el PCM|PCM|Física|Compartido|No|En numerosos sensores de temperatura como los sensores Inlet y Hotspot se muestra el estado, que indica si la temperatura medida está dentro de la tolerancia.|
|Fuente de alimentación [0-1]|PCM|Física|Compartido|Sí|Hay una línea presente para cada fuente de alimentación en las dos PCM situadas en la parte posterior del dispositivo.|
|Refrigeración [0-1]|PCM|Física|Compartido|Sí|Hay una línea presente para cada uno de los cuatro ventiladores de refrigeración que residen en los dos PCM.|
|Almacenamiento local [HDD]|N/D|Lógicos|Compartido|N/D|Muestra el estado del grupo de almacenamiento lógico que se crea desde las unidades de disco duro del dispositivo.|
|Controller [0-1] [estado]|E/S|Física|Controller|Sí|Muestra el estado de los controladores en el módulo EBOD.|
|Sensores de temperatura en el EBOD|E/S|Física|Controller|No|En numerosos sensores de temperatura de cada controlador se muestra el estado, que indica si la temperatura está dentro de la tolerancia.|
|Ampliador SAS|E/S|Física|Controller|No|Indica el estado del ampliador SAS, que se usa para conectar el almacenamiento integrado al controlador.|
|Conector SAS [0-2]|E/S|Física|Controller|No|Indica el estado de cada conector SAS, que se usa para conectar el almacenamiento integrado al ampliador SAS.|
|Interconexión de plano medio de SBB|E/S|Física|Controller|No|Indica el estado del conector de plano medio, que se usa para conectar cada controlador al plano medio.|
|Potencia de la electrónica del revestimiento|E/S|Física|Controller|No|Indica el estado del sistema de alimentación que usa el revestimiento.|
|Diagnóstico de la electrónica del revestimiento|E/S|Física|Controller|No|Indica el estado de los subsistemas de diagnóstico que proporciona el controlador.|
|Conexión al controlador del dispositivo|E/S|Física|Controller|No|Indica el estado de la conexión entre el módulo de E/S de EBOD y el controlador del dispositivo.|

## Pasos siguientes
- Para utilizar el servicio StorSimple Manager para administrar el dispositivo, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).
 
- Si necesita solucionar problemas de un componente del dispositivo que tiene un estado degradado o con error, vea [Indicadores de supervisión de StorSimple](storsimple-monitoring-indicators.md).

- Para reemplazar un componente de hardware con errores, vea [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

- Si sigue teniendo problemas con el dispositivo, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_0107_2016-->