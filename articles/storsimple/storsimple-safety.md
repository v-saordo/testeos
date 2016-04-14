<properties 
   pageTitle="Seguridad del dispositivo StorSimple | Microsoft Azure"
   description="Describe las consideraciones, directrices y convenciones de seguridad y explica cómo instalar y operar el dispositivo StorSimple de forma segura."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/20/2016"
   ms.author="alkohli" />

# Instalar y operar el dispositivo StorSimple de forma segura

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Leer el aviso de seguridad](./media/storsimple-safety/IC740885.png)**LEER LA INFORMACIÓN DE SEGURIDAD Y SALUD**

Lea toda la información de seguridad y salud de esta guía que sea aplicable a su dispositivo de Microsoft Azure StorSimple Conserve todas las guías impresas entregadas con su dispositivo de StorSimple para futuras referencias. Si no se siguen las instrucciones y se configura, usa y cuida adecuadamente este producto, puede aumentar el riesgo de lesiones graves o muerte, o bien de daños en el dispositivo o dispositivos. También hay disponible una [versión descargable de esta guía](http://www.microsoft.com/download/details.aspx?id=44233).

## Convenciones de iconos de seguridad

Estos son los iconos que encontrará cuando revise las precauciones de seguridad que se deben observar al configurar y ejecutar el dispositivo de Microsoft Azure StorSimple.

| Icono | Descripción |
|:------|:-------------| 
|![Icono Peligro](./media/storsimple-safety/IC740879.png)**PELIGRO**|Indica una situación peligrosa que, si no se evita, causará la muerte o lesiones graves. Esta palabra de aviso se debe limitar a las situaciones más extremas.| 
|![Icono Advertencia](./media/storsimple-safety/IC740879.png) **ADVERTENCIA**|Indica una situación peligrosa que, si no se evita, podría causar la muerte o lesiones graves.|
|![Icono Advertencia](./media/storsimple-safety/IC740879.png)**PRECAUCIÓN**|Indica una situación peligrosa que, si no se evita, podría causar lesiones leves o graves.|
|![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**|Indica información considerada importante, pero no relacionada con la peligrosidad.|
|![Icono Descarga eléctrica](./media/storsimple-safety/IC740882.png)**Peligro de descarga eléctrica** |Alta tensión|
|![Icono Peso pesado](./media/storsimple-safety/IC740883.png)**Peso pesado**| |
|![Icono No contiene piezas reparables por el usuario](./media/storsimple-safety/IC740879.png)**No contiene piezas reparables por el usuario**|No acceder sin la formación adecuada.|
|![Icono Leer el aviso de seguridad](./media/storsimple-safety/IC740885.png)**Lea todas las instrucciones primero**| |
|![Icono Sugerencia peligrosa](./media/storsimple-safety/IC740886.png)**Sugerencia peligrosa**| |


## Precauciones en la manipulación

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Peso pesado](./media/storsimple-safety/IC740883.png)**ADVERTENCIA**


Para reducir el riesgo de lesiones:

- Un alojamiento completamente configurado puede pesar hasta 32 kg (70 lbs); no intente levantarlo sin ayuda de otra persona.
- Antes de mover el alojamiento, asegúrese siempre de que sean dos personas las que levanten su peso. Tenga en cuenta que si una persona sola intenta levantar su peso, podría lesionarse.
- No levante el alojamiento por las asas de los módulos de alimentación y refrigeración (PCM) ubicadas en la parte posterior de la unidad. No están diseñadas para soportar el peso.

## Precauciones en la conexión

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Descarga eléctrica](./media/storsimple-safety/IC740882.png)**ADVERTENCIA**

Para reducir la probabilidad de lesiones, descargas eléctricas o muerte:

- Cuando se usen varias fuentes de CA, desconéctelas todas para conseguir un aislamiento completo.

- Desenchufe de forma definitiva la unidad antes de moverla o si piensa que se ha dañado de algún modo.

- Proporcione una conexión eléctrica a tierra segura para los cables de la fuente de alimentación. Compruebe que la toma de tierra del alojamiento cumple con los requisitos nacionales y locales antes encenderlo.

- Asegúrese siempre de que la conexión de alimentación está desconectada antes de extraer un PCM del alojamiento.

- Dado que el enchufe del cable de la fuente de alimentación es el dispositivo de desconexión principal, asegúrese de que los enchufes están ubicados cerca del equipo y que se pueden acceder con facilidad.

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Descarga eléctrica](./media/storsimple-safety/IC740882.png)**ADVERTENCIA**

Para reducir la probabilidad de sobrecalentamiento o de incendio de las conexiones eléctricas:

- Proporcione una fuente de alimentación adecuada con protección frente a sobrecargas eléctricas para cumplir con los requisitos detallados en las especificaciones técnicas.

- No use cables de alimentación ramificados (cables en “Y”).

- Para cumplir con los requisitos de seguridad, de emisiones y térmicos aplicables, no se debe retirar ninguna cubierta y todas las bahías deben estar llenas con módulos de complemento o unidad de relleno.

- Asegúrese de que el equipo se usa de la manera especificada por el fabricante. Si el equipo se usa de manera diferente a la especificada por el fabricante, la protección proporcionada por el equipo puede verse afectada.

![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**

Para una operación apropiada del equipo y evitar daños en el producto:

- Los puertos RJ45 en la parte posterior del dispositivo solo son para conexión Ethernet. No se deben conectar a una red de telecomunicaciones.

- Asegúrese de instalar el dispositivo en un bastidor que pueda acomodar un diseño de refrigeración que vaya desde la parte frontal a la trasera.

- Todos los módulos de complemento y las placas de relleno forman parte del alojamiento del sistema. Solo deben extraerse cuando pueda agregarse inmediatamente su reemplazo. El sistema no debe ejecutarse sin todos los módulos o rellenos en su lugar.

## Precauciones del sistema en bastidor

Se deben tener en cuenta los requisitos de seguridad siguientes al montar el dispositivo en un gabinete de bastidor.

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Sugerencia peligrosa](./media/storsimple-safety/IC740886.png)**ADVERTENCIA**
 
Para reducir la probabilidad de lesiones debido a un vuelco:

- El diseño en bastidor debe admitir el peso total de los alojamientos instalados y debe incorporar las características de estabilización adecuadas para prevenir que el bastidor se incline o se tumbe durante la instalación o uso normal.

- Al cargar un bastidor, llénelo de abajo hacia arriba y vacíelo de arriba hacia abajo.

- No deslice más de un alojamiento fuera del bastidor a la vez para evitar el peligro de que se caiga.

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Descarga eléctrica](./media/storsimple-safety/IC740882.png)**ADVERTENCIA**

Para reducir la probabilidad de lesiones, descargas eléctricas o muerte:

- El bastidor debe tener un sistema de distribución eléctrica seguro. Debe proporcionar protección frente a las sobrecargas de corriente para el alojamiento y no debe sobrecargarse por el número total de alojamientos instalados. Debe observarse la potencia nominal de consumo eléctrico que aparece en la placa de nombre.

- El sistema de distribución eléctrica debe proporcionar una toma de tierra de confianza para cada alojamiento del bastidor.

- El diseño del sistema de distribución eléctrica debe tomar en consideración la fuga a tierra total de todas las fuentes de alimentación de todos los alojamientos. Tenga en cuenta que cada fuente de alimentación de cada alojamiento tiene una corriente de fuga a tierra de 1,0 mA como máximo a 60 Hz, 264 voltios. El bastidor puede requerir el etiquetado de “CORRIENTE DE FUGA ALTA. Es fundamental que haya una conexión a tierra antes de realizar la conexión a una fuente”.

- El bastidor, cuando se configura con los alojamientos, debe cumplir los requisitos de seguridad de UL 60950-1 e IEC 60950-1/EN 60950-1.

![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**

Para la refrigeración adecuada del sistema en bastidor:

- Asegúrese de que el diseño del bastidor toma en consideración la temperatura ambiente máxima en operación del alojamiento de 35 grados Celsius (95 grados Fahrenheit).

- El sistema se opera con una instalación de salida de aire posterior, de baja presión (la presión posterior creada por las puertas del bastidor y obstáculos no debe exceder de 5 pascales [0,5 mm del indicador de nivel de agua]).

## Precauciones en el módulo de alimentación y refrigeración (PCM)

El dispositivo está diseñado para operar con dos PCM. Cada uno de los PCM tiene una fuente de alimentación y un ventilador de dos ejes. Durante una condición crítica, el sistema permite que se produzca un error en una fuente de alimentación mientras continúa con las operaciones normales. Dos PCM (y por consiguiente dos fuentes de alimentación) deben estar siempre instalados. Un solo PCM no proporciona alimentación redundante. Por lo tanto, un error en uno de los PCM puede ocasionar inactividad o una posible pérdida de datos.

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![Icono Descarga eléctrica](./media/storsimple-safety/IC740882.png)**ADVERTENCIA**

Para reducir la probabilidad de lesiones, descargas eléctricas o muerte:

- No quite las cubiertas del PCM. Existe peligro de descargas eléctricas en su interior. Para devolver el PCM y obtener un reemplazo, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**

Para una operación apropiada del equipo y evitar daños en el producto:

- Debe reemplazar el PCM erróneo dentro de un período de 24 horas. Después de extraer un PCM para su reemplazo, este debe completarse dentro de los 10 minutos posteriores a la extracción.

- No quite un PCM a menos que pueda instalar su reemplazo inmediatamente. El alojamiento no puede operarse sin todos los módulos en su lugar.

## Precauciones en cuanto a las descargas electrostáticas (ESD)

![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**

Observe las siguientes precauciones relacionadas con ESD.

- Asegúrese de que ha instalado y comprobado una muñequera o tobillera antiestática adecuada.

- Observe todas las precauciones de ESD convencionales al manipular los módulos y los componentes.

- Evite el contacto con componentes del plano posterior y los conectores del módulo.

- Los daños por ESD no están cubiertos por la garantía.

## Precauciones al desechar la batería

La fuente de alimentación usa una batería especial para proteger el contenido de la memoria durante interrupciones de alimentación temporales de corta duración. La batería está asentada en el PCM. Recuerde la siguiente información sobre la batería.

![Icono Advertencia](./media/storsimple-safety/IC740879.png) **ADVERTENCIA**

Para reducir el riesgo de cortocircuito, incendio, explosión, lesiones o muerte:

- Deseche las baterías usadas según las regulaciones nacionales/regionales.

- No la desensamble, rompa, caliente por encima de los 60 grados Celsius (140 grados Fahrenheit) ni incinere. Reemplace la batería del PCM solo por una batería suministrada. Si se usa otra batería, se puede presentar riesgo de incendio o explosión.

- Use topes finales protectores en las baterías si se han quitado de la fuente de alimentación.

![Icono Aviso](./media/storsimple-safety/IC740881.png)**AVISO:**

Cuando se envían o transportan las baterías por aire, siga el documento IATA Lithium Battery Guidance disponible en [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Después de revisar estos avisos de seguridad, el siguiente paso es desempaquetar, montar en bastidor y cablear su dispositivo.

## Pasos siguientes

- Para un dispositivo 8100, vaya a [Instalación del dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).

- Para un dispositivo 8600, vaya a [Instalación del dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

<!---HONumber=AcomDC_0121_2016-->