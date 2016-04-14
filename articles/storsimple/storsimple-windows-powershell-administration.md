<properties 
   pageTitle="PowerShell para la administración de dispositivos StorSimple | Microsoft Azure"
   description="Obtenga más información acerca de cómo usar Windows PowerShell para que StorSimple administre su dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/29/2016"
   ms.author="alkohli@microsoft.com" />

# Usar Windows PowerShell para StorSimple para administrar su dispositivos

## Información general

Windows PowerShell para StorSimple es una interfaz de línea de comandos que puede utilizar para administrar el dispositivo de StorSimple de Microsoft Azure. Como sugiere su nombre, es una interfaz de línea de comandos basada en Windows PowerShell, que se compila en un espacio de ejecución restringido. Desde la perspectiva del usuario en la línea de comandos, un espacio de ejecución restringido aparece como una versión de Windows PowerShell restringida. Además de conservar algunas de las capacidades básicas de Windows PowerShell, esta interfaz tiene cmdlets adicionales dedicados orientados a administrar el dispositivo StorSimple de Microsoft Azure.

Este artículo describe las características de Windows PowerShell para StorSimple, incluido cómo puede conectarse a esta interfaz y vínculos a procedimientos detallados de flujos de trabajo que se pueden realizar mediante esta interfaz. Los flujos de trabajo incluyen cómo registrar el dispositivo, configurar la interfaz de red en el dispositivo, instalar las actualizaciones que requieren el dispositivo en modo de mantenimiento, cambiar el estado del dispositivo y solucionar los problemas que puede experimentar.

Después de leer este artículo, aprenderá a:

- Conectar el dispositivo StorSimple con Windows PowerShell para StorSimple.

- Administrar el dispositivo StorSimple con Windows PowerShell para StorSimple.

- Obtener ayuda de Windows PowerShell para StorSimple.

>[AZURE.NOTE] 	

>- Los cmdlets de Windows PowerShell para StorSimple permiten administrar el dispositivo StorSimple desde una consola en serie o de forma remota a través de la conexión remota de Windows PowerShell. Para obtener más información acerca de cada uno de los cmdlets individuales que pueden utilizarse en esta interfaz, vaya a [Referencia de cmdlets de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Los cmdlets de Azure PowerShell StorSimple son un conjunto de cmdlets de Windows PowerShell que permiten automatizar las tareas de nivel de servicio y de migración desde la línea de comandos. Para obtener más información sobre los cmdlets de Azure PowerShell, consulte la [Referencia de los cmdlets de Azure StorSimple](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Puede tener acceso a Windows PowerShell para StorSimple mediante uno de los métodos siguientes:

- [Conectarse a la consola en serie del dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Conectarse de modo remoto a StorSimple mediante Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
	

## Conéctese a Windows PowerShell para StorSimple mediante la consola serie del dispositivo

Para conectarse a Windows PowerShell para StorSimple, puede [descargar PuTTY](http://www.putty.org/) o software de emulación de terminales similar. Necesitará configurar PuTTY específicamente para acceder al dispositivo de StorSimple de Microsoft Azure. Los temas siguientes contienen pasos detallados acerca de cómo configurar PuTTy y conectarse al dispositivo. También se explican varias opciones de menú de la consola en serie.

### Configuración de PuTTY

Asegúrese de usar la siguiente configuración de PuTTY para conectarse a la interfaz de Windows PowerShell desde la consola en serie.

#### Para configurar PuTTY

1. En el cuadro de diálogo **Reconfiguración** de PuTTY, en el panel **Categoría**, seleccione **Teclado**.

2. Asegúrese de que estén activadas las siguientes opciones (constituyen la configuración predeterminada cuando se inicia una nueva sesión).

 	|Elemento de teclado|Seleccionar|
 	|---|---|
 	|Tecla retroceso|Control-? (127)|
	|Teclas de inicio y fin|Standard|
	|Teclas de función y teclado numérico|ESC[n~|
	|Estado inicial de las teclas de dirección|Normal|
	|Estado inicial del teclado numérico|Normal|
	|Habilitar las características de teclado adicionales|Control-Alt es diferente de AltGr|

	![Configuración de Putty compatible](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Haga clic en **Apply**.

4. En el panel **Categoría**, seleccione **Traducción**.

5. En el cuadro de lista **Conjunto de caracteres remotos**, seleccione **UTF-8**.

6. En **Control de caracteres de dibujo de líneas**, seleccione **Usar puntos de código de dibujo de líneas Unicode**. La siguiente ilustración muestra las selecciones de PuTTY correctas.

	![Configuración de Putty de UTF](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Haga clic en **Apply**.


Ahora puede usar PuTTY para conectarse a la consola en serie del dispositivo realizando los pasos siguientes.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### Información acerca de la consola serie

Cuando tiene acceso a la interfaz de Windows PowerShell del dispositivo StorSimple a través de la consola en serie, un mensaje de banner se presenta, seguido de las opciones de menú.

El mensaje de banner contiene información básica de dispositivo StorSimple como el modelo, el nombre, la versión del software instalado y el estado del controlador al que obtiene acceso. La imagen siguiente muestra un ejemplo de un mensaje de banner.

![Mensaje de banner en serie](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Puede usar el mensaje de banner para identificar si el controlador que está conectado es Activo o Pasivo.

La imagen siguiente muestra las distintas opciones de espacio de ejecución que están disponibles en el menú de la consola en serie.

![Registrar el dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Puede elegir entre las siguientes opciones:

1. **Iniciar sesión con acceso completo**: esta opción le permite conectarse (con las credenciales apropiadas) al espacio de ejecución **SSAdminConsole** en el controlador local. (El controlador local es el controlador al que tiene acceso actualmente a través de la consola en serie del dispositivo StorSimple). Esta opción también puede utilizarse para permitir que el servicio técnico de Microsoft tenga acceso al espacio de ejecución no restringido (sesión de soporte técnico) para solucionar los problemas de dispositivo posibles. Después de utilizar la opción 1 para iniciar sesión, puede permitir que el ingeniero del servicio técnico de Microsoft tenga acceso a un espacio de ejecución sin restricciones mediante la ejecución de un cmdlet específico. Para obtener más información, consulte [Iniciar una sesión de soporte](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Iniciar sesión en el controlador del mismo nivel con acceso completo**: esta opción es igual a la opción 1, excepto que permite la conexión (con las credenciales apropiadas) al espacio de ejecución **SSAdminConsole** en el controlador del mismo nivel. Dado que el dispositivo StorSimple es un dispositivo de alta disponibilidad con dos controladores en una configuración activo/pasivo, el mismo nivel hace referencia al otro controlador en el dispositivo al que accede a través de la consola en serie). Similar a la opción 1, esta opción puede utilizarse para permitir que el servicio técnico de Microsoft tenga acceso a un espacio de ejecución sin restricciones en un controlador del mismo nivel.

3. **Conectarse con acceso limitado**: esta opción se utiliza para tener acceso a la interfaz de Windows PowerShell en modo limitado. No se le solicitarán las credenciales de acceso. Esta opción se conecta a un espacio de ejecución más restringido en comparación con las opciones 1 y 2. Algunas de las tareas que están disponibles a través de la opción 1 que no se pueden realizar en este espacio de ejecución son:

	- Restablecer la configuración de fábrica
	- Cambiar la contraseña
	- Habilitar o deshabilitar el acceso de soporte
	- Aplicar actualizaciones
	- Instalar revisiones 
												

	>[AZURE.NOTE] **Esta es la opción preferida si ha olvidado la contraseña del administrador de dispositivos y no se puede conectar a través de la opción 1 o 2.**

4. **Cambiar idioma**: esta opción le permite cambiar el idioma para mostrar en la interfaz de Windows PowerShell. Los idiomas admitidos son inglés, japonés, ruso, francés, coreano de Corea del Sur, español, italiano, alemán, chino y portugués de Brasil.


## Conectarse remotamente a StorSimple con Windows PowerShell para StorSimple

Puede usar la conexión remota de Windows PowerShell para conectarse al dispositivo StorSimple. Cuando se conecta de este modo, no verá un menú. (Verá un menú solo si utiliza la consola en serie del dispositivo para conectarse. La conexión remota remite directamente al equivalente de "opción 1 – acceso total” en la consola en serie). Con la conexión remota de Windows PowerShell, se conecta a un espacio de ejecución específico. También puede especificar el idioma de visualización.

El idioma de visualización es independiente del idioma que se configura mediante la opción **Cambiar idioma** en el menú de la consola en serie. Remote PowerShell seleccionará automáticamente la configuración regional del dispositivo desde el que se conecta si no es especifica uno.

>[AZURE.NOTE] Si está trabajando con hosts virtuales de Microsoft Azure y los dispositivos virtuales StorSimple, puede utilizar la conexión remota de Windows PowerShell y el host virtual para conectarse al dispositivo virtual. Si ha configurado una ubicación de recurso compartido en el host en el que se va a guardar la información de la sesión de Windows PowerShell, debe tener en cuenta que la entidad de seguridad incluye solo los usuarios autenticados. Por lo tanto, si ha configurado el recurso compartido para permitir el acceso a todos los usuarios y conectarse sin especificar las credenciales, se usará la entidad de seguridad anónima no autenticada y aparecerá un error. Para solucionar este problema, en el recurso compartido de host, debe habilitar la cuenta de invitado y, a continuación, proporcionar al invitado acceso de cuenta completa al recurso compartido o se deben especificar credenciales válidas junto con el cmdlet de Windows PowerShell.

Puede utilizar HTTP o HTTPS para conectarse a través de la conexión remota de Windows PowerShell. Siga las instrucciones de los siguientes tutoriales:

- [Conectarse de forma remota mediante http](storsimple-remote-connect.md#connect-through-http)
- [Conectarse de forma remota mediante https](storsimple-remote-connect.md#connect-through-https)

## Consideraciones de seguridad de conexión

Cuando decida cómo conectarse a Windows PowerShell para StorSimple, tenga en cuenta lo siguiente:

- Es seguro conectarse directamente a la consola en serie del dispositivo, pero la conexión a la consola en serie a través conmutadores de red no lo es. Tenga presente los riesgos de seguridad al conectarse al dispositivo en serie a través de los conmutadores de red.

- La conexión a través de una sesión HTTP puede ofrecer más seguridad que conectarse a través de la consola en serie a través de la red. Aunque este no es el método más seguro, es aceptable en redes de confianza.

- La conexión a través de una sesión HTTPS es la opción más segura y la recomendada.


## Administrar el dispositivo StorSimple con Windows PowerShell para StorSimple
En la siguiente tabla se muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden llevarse a cabo en la interfaz de Windows PowerShell del dispositivo de StorSimple. Para obtener más información sobre cada flujo de trabajo, haga clic en la entrada adecuada en la tabla.

#### Flujos de trabajo de Windows PowerShell para StorSimple

|Si desea hacer esto...|Utilice este procedimiento.|
|---|---|
|Registrar el dispositivo|[Configurar y registrar el dispositivo con Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurar el proxy web </br>Ver la configuración de proxy web|[Configurar el proxy web para el dispositivo StorSimple](storsimple-configure-web-proxy.md)|
|Modificar la configuración de interfaz de red de DATA 0 en el dispositivo|[Modificar la configuración de interfaz de red de DATA 0 en el dispositivo](storsimple-modify-data-0.md)|
|Detener un controlador </br> Reiniciar o apagar un controlador </br> Apagar un dispositivo</br>Restablecer el dispositivo a los valores predeterminados de fábrica|[Administrar controladores de dispositivo](storsimple-manage-device-controller.md)|
|Instalar revisiones y actualizaciones de modo de mantenimiento|[Actualizar su dispositivo](storsimple-update-device.md)|
|Acceder al modo de mantenimiento </br>Salir del modo de mantenimiento|[Modos del dispositivo StorSimple](storsimple-device-modes.md)|
|Crear un paquete de soporte</br>Descifrar y editar un paquete de soporte|[Crear y administrar paquetes de soporte técnico](storsimple-create-manage-support-package.md)|
|Iniciar una sesión de soporte</br>|[Iniciar una sesión de soporte en Windows PowerShell para StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## Obtener ayuda de Windows PowerShell para StorSimple

En Windows PowerShell para StorSimple, la Ayuda del cmdlet está disponible. Una versión actualizada en línea de esta Ayuda también está disponible, que puede utilizar para actualizar la Ayuda en el sistema.

Obtener ayuda en esta interfaz es similar a la de Windows PowerShell y funcionará la mayoría de los cmdlets relacionados con la Ayuda. Puede encontrar ayuda para Windows PowerShell en línea en la biblioteca de TechNet: [Scripting con Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

La siguiente es una breve descripción de los tipos de ayuda para esta interfaz de Windows PowerShell, incluso cómo actualizar la Ayuda.

#### Para obtener ayuda sobre un cmdlet

- Para obtener ayuda para cualquier cmdlet o función, utilice el siguiente comando: `Get-Help <cmdlet-name>`

- Para obtener ayuda en línea para cualquier cmdlet, use el cmdlet anterior con el parámetro `-Online`: `Get-Help <cmdlet-name> -Online`

- Para obtener ayuda completa, puede usar el parámetro –Full y, para ejemplos, use el parámetro `–Examples`.

#### Para actualizar la Ayuda

Puede actualizar fácilmente la Ayuda en la interfaz de Windows PowerShell. Realice los pasos siguientes para actualizar la Ayuda en el sistema.

#### Para actualizar la Ayuda del cmdlet

1. Inicie Windows PowerShell con la opción **Ejecutar como administrador**.

1. En el símbolo del sistema, escriba: `Update-Help`

1. Se instalarán los archivos de Ayuda actualizados.

1. Una vez instalados los archivos de ayuda, escriba: `Get-Help Get-Command`. Esto mostrará una lista de cmdlets para que la Ayuda está disponible.


>[AZURE.NOTE] Para obtener una lista de todos los cmdlets disponibles en cualquiera de los espacios de ejecución, inicie sesión en la opción de menú correspondiente y ejecute el cmdlet `Get-Command`.

## Pasos siguientes
Si experimenta problemas con el dispositivo StorSimple al realizar uno de los flujos de trabajo anteriores, vea [Herramientas para solucionar problemas en implementaciones de StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

<!---HONumber=AcomDC_0204_2016-->