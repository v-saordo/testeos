<!--author=SharS last changed: 12/01/15-->

### Paso 1: Autorizar que un dispositivo cambie la clave de cifrado de datos del servicio en el Portal de Azure clásico.

Normalmente, el administrador de dispositivos solicitará que el administrador de servicios autorice que un dispositivo cambie las claves de cifrado de datos del servicio. A continuación, el administrador de servicios autorizará que el dispositivo cambie la clave.

Este paso se realiza en el Portal de Azure clásico. El administrador de servicios puede seleccionar un dispositivo en una lista de los dispositivos que se pueden autorizar. A continuación, se autoriza que el dispositivo inicie el proceso de cambio de las claves de cifrado de datos del servicio.

#### ¿Qué dispositivos se pueden autorizar para que cambien las claves de cifrado de datos del servicio?

Para poder autorizar que un dispositivo inicie los cambios de las claves de cifrado de datos del servicio debe cumplir los siguientes criterios:

- Para que sea válido para la autorización de cambio de claves de cifrado de datos del servicio, el dispositivo debe estar en línea.

- Si el cambio de claves no se ha iniciado, es posible autorizar el mismo dispositivo 30 minutos después.

- Se puede autorizar otro dispositivo, siempre que el dispositivo autorizado anteriormente no haya iniciado el cambio de claves. Una vez que se haya autorizado el nuevo dispositivo, el anterior no puede iniciar el cambio.

- No se puede autorizar un dispositivo mientras la sustitución de la clave de cifrado de datos del servicio esté en curso.

- Se puede autorizar un dispositivo cuando algunos de los dispositivos registrados en el servicio hayan sustituido el cifrado, mientras que otros no lo hayan hecho. En tales casos, los dispositivos aptos son los que hayan completado el cambio de claves de cifrado de datos del servicio.

> [AZURE.NOTE]
En el Portal de Azure clásico, los dispositivos virtuales de StorSimple no se muestran en la lista de dispositivos que se pueden autorizar para iniciar el cambio de claves.

Realice los pasos siguientes para seleccionar un dispositivo y autorizarlo para que inicie el cambio de claves de cifrado de datos del servicio.

#### Para autorizar que un dispositivo cambie la clave

1. En la página Panel del servicio, haga clic en **Cambiar clave de cifrado de datos del servicio**.

    ![Cambiar clave de cifrado del servicio](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. En el cuadro de diálogo **Cambiar clave de cifrado de datos del servicio**, seleccione un dispositivo y autorícelo para que inicie el cambio de claves de cifrado de datos del servicio. La lista desplegable tiene todos los dispositivos que se pueden autorizar.

3. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### Paso 2: usar Windows PowerShell para StorSimple para iniciar el cambio de claves de cifrado de datos del servicio

Este paso se realiza en la interfaz de Windows PowerShell para StorSimple del dispositivo de StorSimple autorizado.

> [AZURE.NOTE] Hasta que se complete la sustitución de claves no se pueden realizar operaciones en el Portal de Azure clásico del servicio StorSimple Manager.

Si utiliza la consola serie del dispositivo para conectarse a la interfaz de Windows PowerShell, realice los pasos siguientes.

#### Para iniciar el cambio de claves de cifrado de datos del servicio

1. Seleccione la opción 1 para iniciar sesión con acceso total.

2. En el símbolo del sistema, escriba:

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Una vez que se haya completado correctamente el cmdlet, obtendrá una nueva clave de cifrado de datos del servicio. Copie y guarde esta clave para usarla en el paso 3 de este proceso. Esta clave se utilizará para actualizar todos los dispositivos restantes registrados en el servicio StorSimple Manager.

    > [AZURE.NOTE] Este proceso debe iniciarse en las cuatro horas siguientes a la autorización de un dispositivo de StorSimple.

   A continuación, esta nueva clave se envía al servicio de inserción en todos los dispositivos que están registrados en el servicio. Seguidamente, aparecerá una alerta en el panel del servicio. El servicio deshabilitará todas las operaciones en los dispositivos registrados y, a continuación, el administrador de dispositivos tendrá que actualizar la clave de cifrado de datos del servicio en el resto de dispositivos. Sin embargo, las E/S (hosts que envían datos a la nube) no se interrumpirán.

   Si tiene un único dispositivo registrado en el servicio, el proceso de sustitución habrá finalizado y puede omitir el paso siguiente. Si tiene varios dispositivos registrados en el servicio, vaya al paso 3.

### Paso 3: actualizar la clave de cifrado de datos del servicio en otros dispositivos de StorSimple

Estos pasos deben realizarse en la interfaz de Windows PowerShell del dispositivo de StorSimple si tiene varios dispositivos registrados en el servicio StorSimple Manager. La clave que obtuvo en el paso 2: usar Windows PowerShell para StorSimple para iniciar el cambio de claves de cifrado de datos del servicio debe utilizarse para actualizar todo el dispositivo de StorSimple restante registrado en el servicio StorSimple Manager.

Realice los pasos siguientes para actualizar el cifrado de datos del servicio en el dispositivo.

#### Para actualizar la clave de cifrado de datos del servicio

1. Use Windows PowerShell para StorSimple para conectarse a la consola. Seleccione la opción 1 para iniciar sesión con acceso total.

2. En el símbolo del sistema, escriba:

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. Proporcione la clave de cifrado de datos del servicio que obtuvo en [Paso 2: usar Windows PowerShell para StorSimple para iniciar el cambio de claves de cifrado de datos del servicio](#to-initiate-the-service-data-encryption-key-change).

<!---HONumber=AcomDC_0128_2016-->