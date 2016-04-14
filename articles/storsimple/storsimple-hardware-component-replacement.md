<properties 
   pageTitle="Reemplazo de los componentes de hardware de StorSimple | Microsoft Azure"
   description="Describe cómo reemplazar de forma segura los PCM, la batería, los módulos de controladores, los controladores EBOD, las unidades de disco y el chasis de un dispositivo de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/26/2016"
   ms.author="alkohli" />

# Reemplazo de los componentes de hardware de StorSimple

## Información general

En los tutoriales de reemplazo de componentes de hardware se describen los componentes de hardware del dispositivo Microsoft Azure StorSimple de la serie 8000 y los pasos necesarios para quitarlos y reemplazarlos. En este artículo se describen los iconos de seguridad, se proporcionan indicaciones sobre tutoriales detallados y se enumeran los componentes que se pueden reemplazar.

>[AZURE.IMPORTANT] Antes de intentar quitar o reemplazar cualquier componente de StorSimple, asegúrese de revisar las [convenciones de iconos de seguridad](#safety-icon-conventions) y otras [precauciones de seguridad](storsimple-safety.md).
 
### Convenciones de iconos de seguridad

En la tabla siguiente se describen los iconos de seguridad usados en estos tutoriales. Preste mucha atención a estos iconos de seguridad al avanzar por los pasos para quitar y cambiar los componentes del dispositivo.

| Icono | Texto | Información adicional |
|:---- |:---- |:-----------|
|![Icono Advertencia](./media/storsimple-hardware-component-replacement/Warning.png)| **PELIGRO** | Indica una situación peligrosa que, si no se evita, causará la muerte o lesiones graves. Esta palabra de aviso se debe limitar a las situaciones más extremas.|
|![Icono Advertencia](./media/storsimple-hardware-component-replacement/Warning.png)| **¡ADVERTENCIA** | Indica una situación peligrosa que, si no se evita, podría causar la muerte o lesiones graves.|
|![Icono Precaución](./media/storsimple-hardware-component-replacement/Caution.png)| **PRECAUCIÓN** |Indica una situación peligrosa que, si no se evita, podría causar lesiones leves o graves.|
|![Icono Aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **AVISO:** | Indica información considerada importante, pero no relacionada con la peligrosidad.|
|![Icono Descarga eléctrica](./media/storsimple-hardware-component-replacement/Electric.png) | **Peligro de descarga eléctrica** | Indica alto voltaje.|
|![Icono Peso pesado](./media/storsimple-hardware-component-replacement/Weight.png)| **Peso pesado**| |
|![Icono No contiene piezas reparables por el usuario](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **No contiene piezas reparables por el usuario** | No acceder sin la formación adecuada.|
|![Icono Leer instrucciones](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Lea todas las instrucciones primero**| |
|![Icono Sugerencia peligrosa](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Sugerencia peligrosa**| |

### Antes de empezar

Familiarícese con la información de seguridad acerca de su dispositivo y los iconos de seguridad utilizados en este tutorial. Vaya a [Instalar y operar el dispositivo StorSimple de forma segura](storsimple-safety.md) para obtener información completa. Asegúrese de revisar las [Precauciones de seguridad](storsimple-safety.md#handling-precautions) antes de manipular su dispositivo StorSimple.

Antes de intentar reemplazar un componente, considere la siguiente información.

![Icono Advertencia](./media/storsimple-hardware-component-replacement/Warning.png) ![Icono Descarga eléctrica](./media/storsimple-hardware-component-replacement/Electric.png) **ADVERTENCIA**

- Conecte una buena descarga a tierra en sí mismo mediante una descarga electrostática o alfombra antiestática al manipular los módulos y los componentes del dispositivo StorSimple.

- No toque los circuitos. Utilice las asas y las guías proporcionadas al manipular los componentes que pueden tener circuitos expuestos.

![Icono Advertencia](./media/storsimple-hardware-component-replacement/Warning.png) ![Icono Aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **AVISO:**

Al reemplazar un módulo, **NUNCA deje un compartimento vacío en la parte posterior del gabinete**. Obtenga un módulo de reemplazo o vacío antes de quitar la parte problemática.

## Procedimientos de reemplazo de los componentes de hardware

El dispositivo StorSimple de la serie 8000 consta de varios módulos de complementos en el gabinete principal y EBOD. El modelo 8100 tiene un único gabinete principal, mientras que el modelo 8600 es un dispositivo de dos gabinetes con un gabinete principal y un gabinete EBOD.

En las tablas siguientes se resumen los principales componentes de hardware del dispositivo. Haga clic en el vínculo en la columna **Procedimiento de reemplazo** para ir al tutorial asociado.

|Componentes|# Presente|¿Módulo de complemento?|Procedimiento de reemplazo
|:---------|:--------|:--------------|:---------------------|
| Chasis|1|No|[Reemplazar el chasis en el dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores principales|2|Sí| [Reemplazar un módulos de controladores en el dispositivo StorSimple](storsimple-controller-replacement.md) |
|Módulos de alimentación y refrigeración (PCM) de 764 W|2|Sí| [Reemplazar un Módulo de alimentación y de refrigeración en el dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Batería de reserva|2|Sí| [Reemplazar el módulo de baterías de reserva en el dispositivo StorSimple](storsimple-battery-replacement.md) |
|Unidades de disco|12|Sí| [Reemplazar un disco duro en el dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabla 1** Componentes de hardware en el gabinete principal

Los gabinetes principal y EBOD tienen módulos de I/O diferentes. Además, los PCM tienen diferente vataje. Los PCM en el gabinete principal son de 764 W, mientras que en el gabinete EBOD son de 580 W. Los PCM en el gabinete principal también contienen un módulo de baterías de reserva.

|Componentes|# Presente|¿Módulo de complemento?| Procedimiento de reemplazo
|:---------|:--------|:--------------|:---------------------|
|Chasis|1|No| [Reemplazar el chasis en el dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores EBOD|2|Sí| [Reemplazar un controlador EBOD en el dispositivo StorSimple](storsimple-EBOD-controller-replacement.md) |
|Módulos de alimentación y refrigeración (PCM) de 580 W|2|Sí| [Reemplazar un Módulo de alimentación y de refrigeración en el dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Unidades de disco|12|Sí| [Reemplazar un disco duro en el dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabla 2** Componentes de hardware en el gabinete EBOD

Los módulos de complementos en el dispositivo se resaltan en los siguientes diagramas de las partes frontal y posterior. Puede utilizar estos diagramas para determinar la ubicación de los distintos módulos de complementos si es necesario un reemplazo. El diagrama de la parte frontal muestra las unidades de disco y los diagramas de la parte posterior del gabinete EBOD y el gabinete principal muestran los módulos de complementos.

![Plano frontal de dispositivo con unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Parte frontal del dispositivo

|Etiqueta|Descripción|
|:----|:----------|
|0 - 11|Unidades de discos (total de 12)|

El gabinete principal y el gabinete EBOD tienen módulos transportadores de unidades. El chasis aloja doce unidades de disco de 3,5" organizadas en un formato de 3 por 4.

![Backplane de módulos del gabinete principal del dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Parte posterior del gabinete principal

|Etiqueta|Descripción|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador 0|
|4|Controlador 1|

![Backplane de módulos de complementos del gabinete EBOD del dispositivo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Parte posterior del gabinete EBOD

|Etiqueta|Descripción|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador EBOD 0|
|4|Controlador EBOD 1|

## Unidades reemplazables en campo

Las siguientes unidades reemplazables en campo (FRU) están disponibles para el dispositivo StorSimple:

- Chasis (incluido el panel de operaciones integradas)

- PCM de 764 W de CA

- PCM de 580 W de CA

- Unidad de disco duro con módulo transportador de unidades

- Módulos de controladores

- Módulos de controladores EBOD

- Módulo de batería de reserva

- Kit de guías de montaje en bastidor

[Póngase en contacto con el servicio técnico de Microsoft](storsimple-contact-microsoft-support.md) para solicitar cualquiera de estas unidades de reemplazo.

## Pasos siguientes

Revise toda [la información de seguridad](storsimple-safety.md) antes de intentar reemplazar un componente de hardware de StorSimple.

<!---HONumber=AcomDC_0128_2016-->