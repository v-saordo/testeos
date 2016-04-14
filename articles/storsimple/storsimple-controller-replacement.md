<properties 
   pageTitle="Reemplazar un controladores de dispositivo StorSimple | Microsoft Azure"
   description="Explica cómo quitar y reemplazar módulos de uno o dos controladores en su dispositivo StorSimple."
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
   ms.date="01/14/2016"
   ms.author="alkohli" />

# Reemplazar un módulos de controladores en el dispositivo StorSimple

## Información general

Este tutorial explica cómo quitar y reemplazar uno o ambos módulos de controladores en un dispositivo StorSimple. También se explica la lógica subyacente para los escenarios de reemplazo de uno o dos controladores.

>[AZURE.NOTE] Antes de realizar un reemplazo de controlador, se recomienda actualizar siempre el firmware del controlador a la versión más reciente.
>
>Para evitar daños en el dispositivo StorSimple, no expulse el controlador hasta que los LED muestren uno de los siguientes:
>
>- Todas las luces están apagadas.
>- LED 3, ![Icono de marca de verificación verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) y ![Icono de cruz roja](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) están parpadeando, y LED 0 y 7 están **encendidos**.

En la tabla siguiente se muestran los escenarios de reemplazo de controladores compatibles.

|Caso|Escenario de reemplazo|Procedimiento aplicable|
|:---|:-------------------|:-------------------|
|1|Un controlador está en estado de error, el otro controlador funciona y está activo.|[Reemplazo de un único controlador](#replace-a-single-controller), que describe la [lógica existente detrás del reemplazo de un único controlador](#single-controller-replacement-logic), así como los [pasos del reemplazo](#single-controller-replacement-steps).|
|2|Ambos controladores están defectuosos y requieren reemplazo. El chasis, los discos y el gabinete de discos funcionan.|[Reemplazo de dos controladores](#replace-both-controllers), que describe la [lógica existente detrás del reemplazo de dos controladores](#dual-controller-replacement-logic), así como los [pasos del reemplazo](#dual-controller-replacement-steps). |
|3|Se intercambian los controladores del mismo dispositivo o de dispositivos diferentes. El chasis, los discos y los gabinetes de discos funcionan.|Aparecerá un mensaje de alerta de error de falta de coincidencia de ranura.|
|4|Falta un controlador y se produce un error en el otro controlador.|[Reemplazo de dos controladores](#replace-both-controllers), que describe la [lógica existente detrás del reemplazo de dos controladores](#dual-controller-replacement-logic), así como los [pasos del reemplazo](#dual-controller-replacement-steps).|
|5|Error de uno o ambos controladores. No se puede acceder al dispositivo a través de la consola en serie o la conexión remota de Windows PowerShell.|[Póngase en contacto con el servicio técnico de Microsoft](storsimple-contact-microsoft-support.md) para un procedimiento de reemplazo manual de los controladores.|
|6|Los controladores tienen una versión de compilación diferente, que puede deberse a:<ul><li>Los controladores tienen una versión de software diferente.</li><li>Los controladores tienen una versión de firmware distinta.</li></ul>|Si las versiones de software del controlador son diferentes, la lógica de sustitución detecta y actualiza la versión del software en el controlador de reemplazo.<br><br>Si las versiones de firmware del controlador son diferentes y la versión de firmware anterior **no** puede actualizarse automáticamente, aparecerá un mensaje de alerta en el Portal de Azure clásico. Debe buscar actualizaciones e instalar las actualizaciones de firmware.</br></br>Si las versiones de firmware del controlador son diferentes y la antigua versión de firmware puede actualizarse automáticamente, la lógica de reemplazo de controladores lo detectará y después de iniciar el controlador, el firmware se actualizará automáticamente.|

Debe quitar un módulo del controladores si se ha producido un error. Uno o ambos módulos de controladores pueden producir un error, lo que puede dar lugar a un reemplazo de uno o dos controladores. Para obtener información sobre los procedimientos de reemplazo y la lógica existente detrás de ellos, vea lo siguiente:

- [Reemplazar un único controlador](#replace-a-single-controller)
- [Reemplazar ambos controladores](#replace-both-controllers)
- [Quitar un controlador](#remove-a-controller)
- [Insertar un controlador](#insert-a-controller)
- [Identificar el controlador activo en el dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Antes de quitar y reemplazar un controlador, revise la información de seguridad en [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## Reemplazar un único controlador

Cuando uno de los dos controladores en el dispositivo StorSimple de Microsoft Azure ha fallado, funciona mal o falta, necesitará reemplazar un único controlador.

### Lógica de reemplazo de un único controlador

Un reemplazo de un controlador único, primero debe quitar el controlador defectuoso. (El controlador restante en el dispositivo es el controlador activo). Cuando se inserta el controlador de reemplazo, se producen las siguientes acciones:

1. El controlador de reemplazo inicia inmediatamente la comunicación con el dispositivo StorSimple.

2. Se copia una instantánea del disco duro virtual (VHD) para el controlador activo en el controlador de reemplazo.

3. La instantánea se modifica para que cuando se inicia el controlador de reemplazo de este disco duro virtual, se reconocerá como un controlador en modo de espera.

4. Cuando se hayan completado las modificaciones, el controlador de reemplazo se iniciará como controlador de reserva.

5. Cuando se ejecutan ambos controladores, el clúster se conecta.

### Pasos de reemplazo de un único controlador

Complete los pasos siguientes si se produce un error en uno de los controladores del dispositivo StorSimple de Microsoft Azure. (El otro controlador debe estar activo y en funcionamiento. Si ambos controladores fallan o no funcionan, vaya a [Pasos de reemplazo de dos controladores](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Puede tardar entre 30 y 45 minutos para que el controlador se reinicie y recupere completamente del procedimiento de reemplazo de un único controlador. El tiempo total para el procedimiento completo, incluida la conexión de los cables, es de aproximadamente dos horas.

#### Para quitar un módulo de un solo controlador defectuoso

1. En el Portal de Azure clásico del servicio StorSimple Manager, haga clic en la pestaña **Dispositivos** y, a continuación, haga clic en el nombre del dispositivo que quiera supervisar.

2. Haga clic en la pestaña **Mantenimiento** y, a continuación, vaya a **Estado del hardware**. El estado del Controlador 0 o 1 debe ser rojo, que indica un error.

    >[AZURE.NOTE] El controlador defectuoso en un reemplazo de un único controlador siempre es un controlador en modo de espera.

3. Utilice la Figura 1 y la tabla siguiente para buscar el módulo del controlador defectuoso.

    ![Backplane de módulos del gabinete principal del dispositivo](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1** Parte posterior del dispositivo StorSimple

    |Etiqueta|Descripción|
    |:----|:----------|
    |1|PCM 0|
    |2|PCM 1|
    |3|Controlador 0|
    |4|Controlador 1|

4. En el controlador defectuoso, quite todos los cables de red conectados a los puertos de datos. Si utiliza un modelo 8600, quite también los cables SAS que conectan el controlador al controlador EBOD.

5. Siga los pasos en [Quitar un controlador](#remove-a-controller) para quitar el controlador defectuoso.

6. Instale el reemplazo de fábrica en la misma ranura de la que se quitó el controlador defectuoso. Esto desencadena la lógica de reemplazo de un único controlador. Para obtener más información, vea [Lógica de reemplazo de un único controlador](#single-controller-replacement-logic).

7. Mientras que la lógica de reemplazo de un único controlador progrese en segundo plano, vuelva a conectar los cables. Preste atención y conecte todos los cables exactamente del mismo modo en que estaban conectados antes del reemplazo.

8. Una vez reiniciado el controlador, compruebe el **Estado del controlador** y el **Estado del clúster** en el Portal de Azure clásico para comprobar que el controlador esté en un estado correcto y en modo de espera.

>[AZURE.NOTE] Si va a supervisar el dispositivo a través de la consola en serie, pueden producirse varios reinicios mientras se está recuperando el controlador del procedimiento de reemplazo. Cuando se presente el menú de la consola enserie, sabrá que el reemplazo está completo. Si el menú no aparece en un plazo de dos horas de iniciado el reemplazo del controlador, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

## Reemplazar ambos controladores

Cuando ambos controladores en el dispositivo StorSimple de Microsoft Azure están defectuosos, funcionan mal o faltan, necesitará reemplazar ambos controladores.

### Lógica de reemplazo de dos controladores

En un reemplazo de dos controladores, primero extraiga ambos controladores defectuoso y, a continuación, inserte reemplazos. Cuando se insertan los dos controladores de reemplazo, se producen las siguientes acciones:

1. El controlador de reemplazo en la ranura 0 comprueba lo siguiente:
 
   1. ¿Está usando las versiones actuales de firmware y software?

   2. ¿Es parte del clúster?

   3. ¿Se está ejecutando el controlador del mismo nivel y está en el clúster?
							
    Si ninguna de estas condiciones se cumple, el controlador busca la última copia de seguridad diaria más reciente (ubicada en **nonDOMstorage** de la unidad S). El controlador copia la instantánea más reciente del VHD de la copia de seguridad.

2. El controlador en la ranura 0 utiliza la instantánea para crear una imagen propia.

3. Mientras tanto, el controlador en la ranura 1 espera el controlador 0 para completar la creación de imágenes e iniciarse.

4. Una vez que se inicia el controlador 0, el controlador 1 detecta el clúster creado por el controlador 0, que activa la lógica de reemplazo de un único controlador. Para obtener más información, vea [Lógica de reemplazo de un único controlador](#single-controller-replacement-logic).

5. Después, se ejecutarán ambos controladores y el clúster se conectará.

>[AZURE.IMPORTANT] Después de un reemplazo de dos controladores, después de configurar el dispositivo StorSimple, es esencial que realice una copia de seguridad manual del dispositivo. Las copias de seguridad de configuración de dispositivo diarias no se producen hasta después de que hayan transcurrido 24 horas. Trabaje con [Servicio técnico de Microsoft](storsimple-contact-microsoft-support.md) para hacer una copia de seguridad manual del dispositivo.

### Pasos de reemplazo de dos controladores

Este flujo de trabajo es necesario cuando los dos controladores del dispositivo StorSimple de Microsoft Azure están defectuosos. Esto podría suceder en un centro de datos en el que el sistema de refrigeración deja de funcionar y, como resultado, ambos controladores se dañarán en muy poco tiempo. Según si el dispositivo StorSimple está desactivado o activado y si se utiliza un modelo 8600 u 8100, se requiere una serie diferente de pasos.

>[AZURE.IMPORTANT] Puede tardar de 45 minutos a 1 hora que el controlador se reinicie y recupere completamente de un procedimiento de reemplazo de dos controladores. El tiempo total para el procedimiento completo, incluida la conexión de los cables, es de aproximadamente 2,5 horas.

#### Para reemplazar los módulos de dos controladores

1. Si el dispositivo está apagado, omita este paso y continúe con el paso siguiente. Si el dispositivo está activado, desactive el dispositivo.
										
    1. Si utiliza un modelo de 8600, apague el gabinete principal primero y, a continuación, apague el gabinete EBOD.

    2. Espere hasta que el dispositivo se apague por completo. Todos los LED en la parte posterior del dispositivo estarán apagados.

2. Quite todos los cables de red que están conectados a los puertos de datos. Si utiliza un modelo 8600, quite también los cables SAS que conectan el gabinete principal al gabinete EBOD.

3. Quite ambos controladores del dispositivo StorSimple. Para obtener más información, vea [Quitar un controlador](#remove-a-controller).

4. Inserte primero el reemplazo de fábrica del Controlador 0 y, a continuación, instale el Controlador 1. Para obtener más información, vea [Insertar un controlador](#insert-a-controller). Esto desencadena la lógica de reemplazo de dos controladores. Para obtener más información, consulte [Lógica de reemplazo de dos controladores](#dual-controller-replacement-logic).

5. Mientras que la lógica de reemplazo del controlador progrese en segundo plano, vuelva a conectar los cables. Preste atención y conecte todos los cables exactamente del mismo modo en que estaban conectados antes del reemplazo. Consulte las instrucciones detalladas para el modelo en la sección Cableado del dispositivo de [Instalar el dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [Instalar el dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Encienda el dispositivo StorSimple. Si está usando un modelo 8600:

    1. Asegúrese de que el gabinete EBOD se active por primera vez.

    2. Espere hasta que se esté ejecutando el gabinete EBOD.

    3. Encienda el gabinete principal.

    4. Después de que el primer controlador se reinicie y se encuentre en un estado correcto, el sistema estará en ejecución.

    >[AZURE.NOTE] Si va a supervisar el dispositivo a través de la consola en serie, pueden producirse varios reinicios mientras se está recuperando el controlador del procedimiento de reemplazo. Cuando aparezca el menú de la consola en serie, sabrá que el reemplazo está completo. Si el menú no aparece en 2,5 horas después de haber iniciado el reemplazo del controlador, [póngase en contacto con el servicio técnico de Microsoft](storsimple-contact-microsoft-support.md).

## Quitar un controlador

Utilice el siguiente procedimiento para quitar un módulo defectuoso del controlador del dispositivo StorSimple.

>[AZURE.NOTE] Las ilustraciones siguientes se usan para el controlador 0. Para el controlador 1, estos se revertirán.

#### Para quitar un módulo del controlador

1. Sujete el pestillo del módulo entre el pulgar y el índice.

2. Apriete suavemente el pulgar y el índice juntos para liberar el pestillo del controlador.

    ![Liberación del pestillo del controlador](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2** Liberación del pestillo del controlador

2. Use el pestillo como asa para deslizar el controlador hacia afuera del chasis.

    ![Deslizamiento del controlador fuera del chasis](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3** Deslizamiento del controlador fuera del chasis

## Insertar un controlador

Utilice el siguiente procedimiento para instalar un módulo del controlador suministrado de fábrica después de quitar un módulo defectuoso del dispositivo StorSimple.

#### Para instalar un módulo del controlador

1. Compruebe si hay algún daño en los conectores de la interfaz. No instale el módulo si cualquiera de las clavijas del conector están dañadas o dobladas.

2. Deslice el módulo del controlador en el chasis mientras el pestillo está completamente liberado.

    ![Deslizamiento del controlador hacia el chasis](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4** Deslizamiento del controlador hacia el chasis

3. Con el módulo de controlador insertado, comience a cerrar el pestillo mientras continúa empujando el módulo del controlador hacia el chasis. Se enganchará el pestillo para guiar al controlador hacia su lugar.

    ![Cierre del pestillo del controlador](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5** Cierre del pestillo del controlador

4. Ha terminado cuando el pestillo encaje en su lugar. El LED **Aceptar** debe estar encendido ahora.

    >[AZURE.NOTE] Puede tardar hasta 5 minutos para que el controlador y el LED se activen.

5. Para comprobar que el reemplazo se realizó correctamente, en el Portal de Azure clásico, vaya a **Dispositivos** > **Mantenimiento** > **Estado del hardware** y asegúrese de que los controladores 0 y 1 funcionan correctamente (el estado es verde).

## Identificar el controlador activo en el dispositivo

Hay muchas situaciones, como el registro del dispositivo por primera vez o el reemplazo de controladores, que requieren que localice el controlador activo en un dispositivo StorSimple. El controlador activo procesa todas las operaciones de firmware y redes del disco. Puede utilizar cualquiera de los métodos siguientes para identificar el controlador activo:

- [Usar el Portal de Azure clásico para identificar el controlador activo](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Usar Windows PowerShell para StorSimple para identificar el controlador activo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Comprobar el dispositivo físico para identificar el controlador activo](#check-the-physical-device-to-identify-the-active-controller)

A continuación se describe cada uno de estos procedimientos.

### Usar el Portal de Azure clásico para identificar el controlador activo

En el Portal de Azure clásico, vaya a **Dispositivos** > **Mantenimiento** y desplácese hasta la sección **Controladores**. Aquí puede comprobar qué controlador está activo.

![Identificar el controlador activo en el Portal de Azure clásico](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** El Portal de Azure clásico muestra el controlador activo

### Usar Windows PowerShell para StorSimple para identificar el controlador activo

Al acceder al dispositivo a través de la consola en serie, se presenta un mensaje de banner. El mensaje de banner contiene información de dispositivos básicos como modelo, el nombre, la versión del software instalado y el estado del controlador al que accede. La imagen siguiente muestra un ejemplo de un mensaje de banner:

![Mensaje de banner en serie](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Mensaje de banner que muestra que el controlador 0 está Activo

Puede usar el mensaje de banner para determinar si el controlador que está conectado está activo o pasivo.

### Comprobar el dispositivo físico para identificar el controlador activo

Para identificar el controlador activo en el dispositivo, busque el LED azul sobre el puerto de datos 5 en la parte posterior del gabinete principal.

Si este LED parpadea, el controlador está activo y el otro controlador está en modo de espera. Use el diagrama y la tabla siguiente como ayuda.

![Plano anterior de la caja principal del dispositivo con los puertos de datos](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Parte posterior del gabinete principal con los puertos de datos y LED de supervisión

|Etiqueta|Descripción|
|:----|:----------|
|1-6|DATOS 0: 5 puertos de red|
|7|LED azul|


## Pasos siguientes

Obtenga más información sobre el [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0224_2016-->