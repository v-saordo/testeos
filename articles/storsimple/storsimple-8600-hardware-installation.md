<properties 
   pageTitle="Instalación del dispositivo StorSimple 8600 | Microsoft Azure"
   description="Describe cómo desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8600 antes de implementar y configurar el software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8600.

## Información general
Su Microsoft Azure StorSimple 8600 es un dispositivo de receptáculo dual que consta de un receptáculo principal y EBOD. En este tutorial se explica cómo desempaquetar, montar en bastidor y conectar los cables del hardware del dispositivo StorSimple 8600 antes de configurar el software de StorSimple.

## Desempaquete el dispositivo StorSimple 8600

En los pasos siguientes se proporcionan instrucciones claras y detalladas sobre cómo desempaquetar el dispositivo de almacenamiento StorSimple 8600. Este dispositivo se distribuye en dos cajas, una para el receptáculo principal y otra para el de EBOD. Después, estas dos cajas se colocan en un una sola caja.

### Preparación para desempaquetar el dispositivo

Antes de desempaquetar el dispositivo, revise la información siguiente.


![Icono Advertencia](./media/storsimple-safety/IC740879.png)![icono de peso elevado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **¡ADVERTENCIA!**

1. Asegúrese de que haya dos personas disponibles para administrar el peso del dispositivo si lo está manejando de forma manual. Un receptáculo totalmente montado puede pesar hasta 32 kg.
1. Coloque la caja en una superficie plana y nivelada.

A continuación, complete los pasos siguientes para desempaquetar el dispositivo.

#### Para desempaquetar el dispositivo

1. Compruebe si la caja y la espuma del embalaje presentan golpes, cortes, daños por agua o cualquier otro daño evidente. Si la caja o el embalaje están muy dañados, no abra la caja. Póngase en contacto con el [soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudarle a determinar si el dispositivo está en buen estado.

2. Abra la caja exterior y extraiga las dos cajas correspondiente a los receptáculos principal y EBOD. Ahora puede desempaquetar los receptáculos principal y EBOD. En la siguiente ilustración se muestra una vista desempaquetada de uno de los receptáculos.

    ![Desempaquetar el dispositivo de almacenamiento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
 
    **Vista del dispositivo de almacenamiento desempaquetado**

     Etiqueta | Descripción 
     ----- | -------------
     1 | Caja de embalaje
     2 | Cables SAS (en la bandeja de cables y accesorios)
     3 | Espuma inferior
     4 | Dispositivo
     5 | Espuma superior
     6 | Caja de accesorios

3. Después de desempaquetar las dos cajas, asegúrese de que dispone de:

  - 1 receptáculos principal (el receptáculos principal y EBOD se encuentran en dos cajas independientes) 
  - 1 receptáculos EBOD
  - 4 cables de alimentación, 2 en cada caja
  - 2 cables SAS (para conectar el receptáculos principal al EBOD)
  - 1 cable Ethernet cruzado
  - 2 cables de consola serie
  - 1 convertidor serie a USB para el acceso en serie
  - 4 adaptadores QSFP a SFP+ para su uso con interfaces de red de 10 GbE
  - 2 kits de montaje en bastidor (4 guías laterales con herramientas de montaje, 2 para el receptáculos y el EBOD), 1 en cada caja
  - Documentación de introducción

    Si no recibió alguno de los elementos enumerados anteriormente, póngase en contacto con el [soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

El paso siguiente es el montaje en bastidor del dispositivo.

## Montaje en bastidor del dispositivo StorSimple 8600

Siga los pasos siguientes para instalar el dispositivo de almacenamiento StorSimple 8600 en un bastidor de 19 pulgadas estándar con postes delanteros y traseros. Este dispositivo se suministra con dos receptáculos: un receptáculo principal y un EBOD. Ambos deben montarse en bastidor.

La instalación consta de varios pasos, cada uno de los cuales se explica en los procedimientos siguientes.

### Preparación del sitio

Los receptáculos deben instalarse en un bastidor estándar de 19 pulgadas con postes delanteros y traseros. Utilice el procedimiento siguiente para preparar la instalación en bastidor.

#### Para preparar el sitio para la instalación en bastidor

1. Asegúrese de colocar los receptáculos principal y EBOD de forma segura sobre una superficie de trabajo plana, estable y nivelada (o similar).

2. Compruebe que la ubicación en la que desea efectuar la instalación dispone de alimentación de CA estándar de una fuente independiente o una unidad de distribución de energía (PDU) en bastidor con una fuente de alimentación ininterrumpida (UPS).

3. Asegúrese de que haya disponible una ranura 4U (2 X 2U) en el bastidor en el que desea montar los receptáculos.

![Icono Advertencia](./media/storsimple-safety/IC740879.png)![icono de peso elevado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **¡ADVERTENCIA!**

 Asegúrese de que haya dos personas disponibles para administrar el peso del dispositivo si está instalando el dispositivo de forma manual. Un receptáculo totalmente montado puede pesar hasta 32 kg.

### Requisitos previos del bastidor

Los receptáculos están diseñados para instalarse en un armario de bastidor de 19 pulgadas estándar con:

- Una profundidad mínima de 27,84 pulgadas de poste a poste del bastidor
- Peso máximo de 32 kg para el dispositivo
- Contrapresión máxima de 5 pascales (medidor de agua de 0,5 mm)

### Kit de guías de montaje en bastidor

Se proporcionará un conjunto de guías de montaje para utilizar con el armario de bastidor de 19 pulgadas. Las guías se han probado para soportar el peso máximo del receptáculo. Estas guías también permitirán la instalación de varios receptáculos sin pérdida de espacio en el bastidor. Instale primero el alojamiento EBOD.

#### Para instalar el receptáculo EBOD en las guías

2. Realice este paso únicamente si las guías internas no están instaladas en el dispositivo. Normalmente, las guías internas vienen instaladas de fábrica. En caso contrario, instale las guías del lado izquierdo y derecho en los laterales del chasis del receptáculo. Estas se instalan mediante seis tornillos métricos en cada lado. Para ayudarle con la orientación, las guías disponen de las marcas **LH – Front** y **RH – Front**, y el extremo que se fija en la parte trasera del receptáculo tiene un extremo ahusado.

    ![Fijación de las guías al chasis del receptáculo](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fijación de guías a los laterales de la caja**

    Etiqueta | Descripción
    ----- | -----------
    1 | Tornillos de cabeza de botón M 3 x 4
    2 | Guías de chasis
 
3. Instale los ensamblajes de las guías del lado izquierdo y derecho en los miembros verticales del armario del bastidor. Los soportes presentan las marcas **LH**, **RH** y **This side up** para guiarle a la hora de efectuar la orientación correcta.

4. Busque los pasadores de la guía en la parte frontal y trasera del soporte de la guía. Extienda la guía para ajustarla entre los postes del bastidor e inserte los pasadores en los orificios del miembro vertical del poste del bastidor delantero y trasero. Asegúrese de que el soporte de la guía esté nivelado.

5. Use dos de los tornillos métricos suministrados para fijar el soporte de la guía en los miembros verticales del bastidor. Utilice un tornillo en la parte frontal y otro en la parte posterior.

6. Repita estos pasos con el otro soporte de la guía.

     ![Fijación de las guías al armario del chasis](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Fijación de soportes de guía en el bastidor**

     Etiqueta | Descripción
     ----- | -----------
     1 | Tornillo de fijación
     2 | Tornillo del poste del bastidor delantero de orificio cuadrado
     3 | Pasadores de ubicación de la guía delantera izquierda
     4 | Tornillo de fijación
     5 | Pasadores de ubicación de la guía trasera izquierda

### Montaje del receptáculo EBOD en el bastidor 

Con las guías de bastidor que acaba de instalar, realice los pasos siguientes para montar el receptáculo EBOD en el bastidor.

#### Para montar el receptáculo EBOD

1. Con la ayuda de otra persona, levante el receptáculo y alinéelo con las guías del bastidor. 

2. Inserte cuidadosamente el receptáculo en las guías y, a continuación, empújelo completamente hacia el armario del bastidor.

    ![Inserción del dispositivo en el bastidor](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montaje de la caja en el bastidor**

3. Quite los topes de las bridas delantera derecha e izquierda extrayendo los topes. Los topes de las bridas simplemente encajan en las bridas.

4. Fije el receptáculo en el bastidor mediante la instalación de un tornillo de cabeza compatible con un tornillo de cabeza Phillips proporcionado a través de cada brida, izquierda y derecha.

4. Presione los topes de las bridas en su posición para ajustarlos.

     ![Instalación de los topes de las bridas](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalación de los topes de las bridas**
 
     Etiqueta | Descripción
     ----- | -----------
     1 | Tornillo de fijación del receptáculo


### Montaje del receptáculo principal en el bastidor

Cuando haya terminado de montar el receptáculo EBOD, deberá montar el receptáculo principal siguiendo los mismos pasos.

> [AZURE.NOTE]
> 
> - Es posible tener unas ranuras vacías en el bastidor entre el receptáculo principal y EBOD. 
> - Utilice el cable SAS proporcionado de 2 m para conectar el receptáculo principal al receptáculo EBOD.
> - No hay ninguna restricción en la colocación relativa de la unidad principal en la unidad EBOD. Por lo tanto, el receptáculo principal puede colocarse en la ranura superior y el receptáculo EBOD debajo, o viceversa.

El siguiente paso es la colocación de los cables de alimentación, red y acceso de serie del dispositivo.

## Instalación de cables del dispositivo StorSimple 8600

En los siguientes procedimientos se explica cómo pasar los cables del dispositivo StorSimple 8600 de alimentación, red y conexiones en serie.

### Requisitos previos

Antes de comenzar a pasar los cables del dispositivo, necesitará:

- Desempaquetar por completo el receptáculo y EBOD.
- 4 cables de alimentación (2 para el receptáculo principal y EBOD) suministrados con el dispositivo
- 2 cables SAS proporcionados con el dispositivo para conectar el receptáculo EBOD al receptáculo principal
- Acceso a 2 unidades de distribución de energía (PDU) (recomendado).
- Cables de red
- Cables serie suministrados
- Convertidor USB serie con el controlador apropiado instalado en su equipo (si es necesario)
- Se proporcionan 4 adaptadores QSFP a SFP+ para su uso con interfaces de red de 10 GbE.
- [Hardware compatible para interfaces de red de 10 GbE en el dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md) 

### SAS y cables de alimentación

El dispositivo cuenta con una caja principal y una caja EBOD. Esto requiere que se realice el cableado de alimentación y conectividad de SCSI conectadas en serie (SAS) de las unidades en conjunto.

Cuando configure este dispositivo por primera vez, realice primero los pasos para el cableado de SAS y luego complete los pasos para el cableado de alimentación.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### Cables de red

El dispositivo está en una configuración de dispositivo activo/en espera: en un momento dado, un módulo de controlador está activo y procesando todas las operaciones de disco y red mientras el otro módulo del controlador está en espera. Si se produce un error en un controlador, el controlador en modo de espera se activa inmediatamente y continúa todas las operaciones de discos y de red.

Para admitir esta conmutación por error de controlador redundante, necesitará pasar los cables de red del dispositivo del modo mostrado en los pasos siguientes.

#### Colocación de cables de conexión de red

1. El dispositivo tiene seis interfaces de red en cada controlador: cuatro puertos Ethernet de 1 Gbps y dos de 10 Gbps. Consulte la ilustración siguiente para identificar los puertos de datos de la placa posterior del dispositivo.

     ![Panel posterior del dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Parte posterior del dispositivo en la que se muestran los puertos de datos**
 
     Etiqueta | Descripción
     ------- | -----------
     0,1,4,5 | Interfaces de red de 1 GbE
     2,3 | Interfaces de red de 10 GbE
     6 | Puertos serie



1. Consulte el siguiente diagrama de cableado de red. (La configuración de red mínima se muestra mediante líneas azules continuas. Para obtener un alto rendimiento y disponibilidad, la configuración adicional requerida se muestra mediante líneas de puntos).

![Colocación del cable de red del dispositivo 4U](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cables de red del dispositivo**

Etiqueta | Descripción
----- | -----------
Encontrará | LAN con acceso a Internet
B | Controlador 0
C | PCM 0
D | Controlador 1
E | PCM 1
F | Controlador EBOD 0
G | Controlador EBOD 1
H,I | Hosts (por ejemplo, servidores de archivos)
0-5 | Interfaces de red
6 | Receptáculo principal
7 | Receptáculo EBOD

Cuando se realiza el cableado del dispositivo, la configuración mínima requiere:


- Al menos dos interfaces de red conectadas en cada controlador con una para el acceso a la nube y otra para iSCSI. El puerto DATA 0 se habilita y configura automáticamente mediante la consola serie del dispositivo. Además del puerto DATA 0, también es necesario configurar otro puerto de datos a través del Portal de Azure clásico. En este caso, conecte el puerto DATA 0 a la LAN principal (red con acceso a Internet). Los demás puertos de datos pueden conectarse al segmento de la LAN SAN/iSCSI (VLAN) de la red, dependiendo del rol deseado.

- Interfaces idénticas en cada controlador conectadas a la misma red para garantizar la disponibilidad si se produce conmutación por error en un controlador. Por ejemplo, si decide conectar los puertos DATA 0 y DATA 3 para uno de los controladores, necesitará conectar los puertos DATA 0 y DATA 3 correspondientes en el otro controlador.
	
Tenga en cuenta lo siguiente para alta disponibilidad y rendimiento:


- Cuando sea posible, configure un par de interfaz de red para el acceso a la nube (1 GbE) y otro par para iSCSI (se recomiendan 10 GbE) en cada controlador. 

- Cuando sea posible, conecte las interfaces de red desde cada controlador a dos conmutadores diferentes para garantizar la disponibilidad frente al error de un conmutador. En la ilustración se muestran las dos interfaces de red de 10 GbE, DATA 2 y DATA 3, desde cada controlador conectado a dos conmutadores distintos. Para obtener más información, consulte las **interfaces de red** en los [Requisitos de alta disponibilidad para el dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si usa transceptores SFP+ con las interfaces de red de 10 GbE, use los adaptadores QSFP-SFP+ que se ofrecen. Puede encontrar más información en [Hardware compatible para interfaces de red de 10 GbE en el dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### Cableado del puerto serie

Realice los pasos siguientes para pasar el cable del puerto serie.

#### Colocación de los cables de conexión en serie

1. El dispositivo tiene un puerto serie en cada controlador que se identifica mediante un icono de una llave inglesa. Para buscar los puertos serie, consulte la ilustración que muestra los puertos de datos en la parte posterior del dispositivo.

2. Identifique el controlador activo en la placa posterior del dispositivo. Un LED que parpadeará en azul indica que el controlador está activo.

3. Utilice el cable serie proporcionado (si es necesario, el convertidor de USB a serie de su equipo portátil) y conecte la consola o el equipo (con la emulación de terminales en el dispositivo) al puerto serie del controlador activo.

4. Instale los controladores de serie a USB (incluidos con el dispositivo) en el equipo.

5. Configure la conexión serie del modo indicado a continuación:
   - 115\.200 baudios
   - Bits de datos: 8
   - Bit de parada: 1
   - Sin paridad
   - Control de flujo establecido en **Ninguno**

6. Presione INTRO en la consola para comprobar que la conexión funciona. Debería aparecer un menú de consola serie.

> [AZURE.NOTE] **Administración de Lights-Out**: cuando el dispositivo está instalado en un centro de datos remoto o en una sala de equipos con acceso limitado, asegúrese de que las conexiones serie a ambos controladores estén siempre conectadas a un conmutador de consola serie o un equipo similar. Esto permite el control remoto de fuera de banda y las operaciones de soporte si hay interrupciones de red o errores inesperados.

Ha completado el cableado de alimentación, acceso a la red y conexión en serie del dispositivo. El siguiente paso es configurar el software en el dispositivo.

## Pasos siguientes

Ahora está listo para [implementar y configurar el dispositivo StorSimple local](storsimple-deployment-walkthrough.md).
 

<!---HONumber=AcomDC_0224_2016-->