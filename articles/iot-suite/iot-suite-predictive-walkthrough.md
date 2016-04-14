<properties
 pageTitle="Tutorial de mantenimiento predictivo | Microsoft Azure"
 description="Tutorial de la solución preconfigurada de mantenimiento predictivo de IoT de Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="araguila"/>

# Tutorial de la solución preconfigurada de mantenimiento predictivo

## Introducción

La solución preconfigurada de mantenimiento predictivo del Conjunto aplicaciones de IoT es una solución de un extremo a otro para un escenario empresarial, que predice el punto en el que es probable que se produzca un error. Puede aprovechar esta solución preconfigurada de forma proactiva para actividades como, por ejemplo, la optimización del mantenimiento. La solución combina servicios críticos del Conjunto de aplicaciones de IoT de Azure, incluida un área de trabajo de [Aprendizaje automático de Azure][lnk_machine_learning], con experimentos para predecir la vida útil restante (RUL) de un motor de avión basándose en un conjunto de datos de ejemplo público. La solución proporciona una implementación completa del escenario empresarial como punto de partida para planear e implementar este tipo de solución de IoT a fin de satisfacer sus requisitos empresariales específicos.

## Arquitectura lógica

El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![][img-architecture]

Los elementos de color azul son servicios de Azure que se aprovisionen en la ubicación que se seleccione al aprovisionar la solución preconfigurada. Puede aprovisionar la solución preconfigurada en la región Este de EE. UU., Norte de Europa o Asia oriental.

Algunos recursos no están disponibles en las regiones donde se ha aprovisionado la solución preconfigurada. Los elementos de color naranja del diagrama representan los servicios de Azure aprovisionados en la región más cercana disponible (Centro y sur de EE. UU., Oeste de Europa y Sudeste de Asia ) en función de la región seleccionada.

El elemento verde es un dispositivo simulado que representa un motor de avión. Puede obtener más información acerca de estos dispositivos simulados a continuación.

Los elementos de color gris representan los componentes que implementan capacidades de *administración de dispositivos*. La versión actual de la solución preconfigurada de mantenimiento predictivo no aprovisiona estos recursos. Para obtener más información acerca de la administración de dispositivos, consulte el [Tutorial de la solución preconfigurada de supervisión remota][lnk-remote-monitoring].

## Dispositivos simulados

En la solución preconfigurada, un dispositivo simulado representa un motor de avión. La solución se aprovisiona con dos motores que se asignan a un avión único. Cada motor emite cuatro tipos de telemetría: Sensor 9, Sensor 11, Sensor 14 y Sensor 15 que proporcionan los datos necesarios para el modelo de Aprendizaje automático a fin de calcular la vida útil restante de dicho motor. Cada dispositivo simulado envía los siguientes mensajes de telemetría al Centro de IoT:

*Recuento de ciclos*. Un ciclo representa un vuelo completado con una longitud variable entre 2-10 horas en el que se capturan datos de telemetría cada media hora durante la realización del vuelo.

*Telemetría*. Existen cuatro sensores que representan atributos de motor. Los sensores se etiquetan genéricamente Sensor 9, Sensor 11, Sensor 14 y Sensor 15. Estos cuatro sensores representan la telemetría suficiente para obtener resultados útiles del modelo de Aprendizaje automático en lo referente a la vida útil restante. Este modelo se crea a partir de un conjunto de datos público que incluye datos de sensor de un motor real. Para obtener más información sobre cómo se creó el modelo del conjunto de datos original, consulte [Predictive Maintenance Template en Cortana Analytics Gallery][lnk-cortana-analytics].

Los dispositivos simulados pueden controlar los siguientes comandos enviados desde un Centro de IoT:

| Comando | Descripción |
|---------|-------------|
| StartTelemetry | Controla el estado de la simulación.<br/>Inicia el envío de telemetría del dispositivo. |
| StopTelemetry | Controla el estado de la simulación.<br/>Detiene el envío de telemetría del dispositivo. |

Centro de IoT proporciona la confirmación de los comandos del dispositivo.

## Trabajo de Análisis de transmisiones de Azure

**Trabajo: telemetría** opera en la transmisión entrante de la telemetría del dispositivo mediante dos instrucciones. La primera selecciona todos los datos de telemetría de los dispositivos y los envía al Almacenamiento de blobs desde donde se visualizan en la aplicación web. La segunda calcula los valores medios del sensor con una ventana deslizante de dos minutos y envía estos datos a través del centro de eventos a un **procesador de eventos**.

## Procesador de eventos

El **procesador de eventos** toma los valores medios del sensor de un ciclo completado y los pasa a una API que expone el modelo entrenado de Aprendizaje automático para calcular la vida útil restante de un motor.

## Aprendizaje automático de Azure

Para obtener más información sobre cómo se creó el modelo del conjunto de datos original, consulte [Predictive Maintenance Template en Cortana Analytics Gallery][lnk-cortana-analytics].

## Primeros pasos

Esta sección le guiará por los componentes de la solución, describe el caso práctico previsto y ofrece ejemplos.

### Panel de mantenimiento predictivo

Esta página de la aplicación web utiliza controles de Power BI JavaScript (consulte el [repositorio de imágenes de Power BI][lnk-powerbi]) para ver:

- Los datos de salida de los trabajos de Análisis de transmisiones en el Almacenamiento de blobs.
- El recuento de ciclos y la vida útil restante por cada motor de avión.

### Observación del comportamiento de la solución en la nube

Puede ver los recursos aprovisionados consultando el Portal de Azure y desplazándose luego al grupo de recursos con el nombre de la solución elegida.

![][img-resource-group]

Al aprovisionar la solución preconfigurada, recibirá un mensaje de correo electrónico con un vínculo al área de trabajo de Aprendizaje automático. También puede navegar a esta área de trabajo desde la página [azureiotsuite.com][lnk-azureiotsuite] de la solución de aprovisionamiento cuando se encuentra en el estado **Listo**.

![][img-machine-learning]

En el portal de la solución, puede ver que el ejemplo se aprovisiona con cuatro dispositivos simulados que representan dos aviones con dos motores por avión y cuatro sensores por motor. Cuando va al portal de la solución por primera vez, se detiene la simulación.

![][img-simulation-stopped]

Haga clic en **Iniciar simulación** para iniciar la simulación en que la que verá cómo el historial del sensor, la vida útil restante, los ciclos y el historial de vida útil restante rellenan con datos el panel.

![][img-simulation-running]

Cuando la vida útil restante sea inferior a 160 (un umbral arbitrario elegido para fines de demostración), el portal de la solución muestra un símbolo de advertencia junto a la presentación de la vida útil restante y colorea de amarillo el motor del avión de la imagen. Observará que los valores de la vida útil restante presentan una tendencia descendente en general pero tienden a rebotar hacia arriba y hacia abajo. Esto es consecuencia de las longitudes del ciclo que varían y de la precisión del modelo.

![][img-simulation-warning]

La simulación completa tarda alrededor de 35 minutos en finalizar 148 ciclos. Se alcanza el umbral de vida útil restante de 160 por primera vez en unos 5 minutos y ambos motores alcanzan el umbral en aproximadamente unos 8 minutos.

La simulación ejecuta el conjunto de datos completo para 148 ciclos y se establece en los valores finales de la vida útil existente y de ciclos.

Puede detener la simulación en cualquier punto, pero al hacer clic en **Iniciar simulación** se reproduce la simulación desde el principio del conjunto de datos.

## Pasos siguientes

Ahora que ha ejecutado la solución preconfigurada de mantenimiento predictivo, es posible que desee modificarla; consulte para ello [Personalización de soluciones preconfiguradas][lnk-customize].

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0309_2016-->