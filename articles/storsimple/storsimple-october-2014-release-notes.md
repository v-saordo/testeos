<properties 
    pageTitle="Notas de la versión de la actualización 0.1 de StorSimple 8000 | Microsoft Azure"
    description="Describe las nuevas características y soluciones, problemas abiertos y soluciones alternativas de la versión de Microsoft Azure StorSimple de octubre de 2014 (Actualización 0.1)."
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
    ms.date="12/01/2015"
    ms.author="v-sharos" />

# Notas de la versión 0.1 de la actualización de la serie StorSimple 8000: octubre de 2014  

## Información general

Las siguientes notas de la versión identifican los problemas críticos abiertos de la actualización 0.1 de la serie StorSimple 8000 publicada en octubre de 2014. También contienen una lista de las actualizaciones de software y firmware de StorSimple incluidas en esta versión. Esta es la primera versión que aparece después de que la versión de lanzamiento de la serie StorSimple 8000 Series se pusiera a disposición general en julio de 2014 y corresponde a la versión de software 6.3.9600.17312.

Se recomienda buscar y aplicar las actualizaciones disponibles inmediatamente después de instalar el dispositivo. También puede activar las actualizaciones automáticas para descargar e instalar actualizaciones de alta prioridad de Microsoft en cuanto se publiquen. Para obtener más información, consulte [Actualización del dispositivo StorSimple](storsimple-update-device.md).

Revise la información contenida en las notas de la versión antes de implementar las actualizaciones de la solución de StorSimple.

>[AZURE.IMPORTANT]
> 
-	Para instalar las actualizaciones de octubre, use el servicio de Administrador de StorSimple y no Windows PowerShell para StorSimple.  
-	Las actualizaciones suelen tardar unas 3 horas en completarse.  
-	La versión de octubre de StorSimple no contiene actualizaciones para el dispositivo virtual de StorSimple. Puede aplicar cualquier actualización disponible de Windows, incluidas revisiones de seguridad recientes, pero no verá un cambio de versión en el dispositivo virtual.  

Asegúrese de que se cumplen los siguientes requisitos previos antes de actualizar el dispositivo StorSimple.

- Asegúrese de que ambos controladores de dispositivo se están ejecutando antes de buscar actualizaciones. Si no se está ejecutando uno de los controladores, se producirá un error en la búsqueda. Para comprobar que los controladores están en buen estado, vaya a **Estado del Hardware** en la página **Mantenimiento**. Si algún componente **Requiere atención**, póngase en contacto con el soporte técnico de Microsoft antes de continuar.  
- Asegúrese de que las IP fijas del Controlador 0 y el Controlador 1 sean enrutables y puedan conectarse a Internet, ya que se usan para el mantenimiento de las actualizaciones del dispositivo. Puede usar el [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) para hacer ping a una dirección conocida fuera de la red, como outlook.com, para comprobar que el controlador tiene conectividad a la red externa.  
- Asegúrese de que los puertos 80 y 443 están disponibles en el dispositivo StorSimple para la comunicación saliente. Para obtener más información, consulte [Requisitos de red para el dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Si la versión del software de dispositivo es anterior a 6.3.9600.17312 (actualización de octubre de 2014), deshabilite los puertos Data 2 y Data 3, en caso de que estén habilitados, antes de iniciar la actualización. Si deja los puertos Data 2 o Data 3 habilitados durante la aplicación de la actualización, el controlador del dispositivo podría entrar en modo de recuperación. Tenga en cuenta que, al deshabilitar las interfaces de red, todos los volúmenes asociados se desconectarán y se interrumpirá la E/S mientras dure la actualización.  

## Novedades de la versión de octubre

Esta actualización incluye las mejoras siguientes:

- Ahora puede usar el interfaz de usuario del servicio de Administrador de StorSimple para administrar los controladores del dispositivo. Las acciones de administración incluyen reiniciar, apagar o activar un controlador. Para obtener más información, vaya a [Administrar controladores de dispositivo StorSimple](storsimple-manage-device-controller.md).  
- Puede programar la asignación de ancho de banda WAN según las combinaciones del día de la semana y la hora del día. Esto permite hacer un mejor uso del ancho de banda WAN fuera de horas pico. Se admiten plantillas diferentes de ancho de banda para distintos contenedores de volúmenes. Para obtener más información, vaya a [Administrar las plantillas de ancho de banda de StorSimple](storsimple-manage-bandwidth-templates.md).  
- Puede configurar notificaciones de correo electrónico para informar a los administradores y a otras personas sobre los problemas existentes o que podrían producirse. Para obtener más información, vaya a [Configurar las alertas](storsimple-manage-alerts.md#configure-alert-settings).  

## Problemas corregidos en la versión de octubre


La tabla siguiente contiene un resumen de los problemas corregidos en esta actualización.

| N.º | Característica | Problema | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces de red | En la versión anterior, las interfaces de red DATA 2 y DATA 3 se intercambiaron en el software. Esto se corrigió en esta actualización. Borre la configuración y deshabilite estas interfaces de red antes de instalar la actualización. Después de instalar la actualización, tendrá que volver a configurar estas interfaces. | Sí | No |
| 2 | Paquete de soporte | En la versión anterior, si se ejecutaba el cmdlet **Export-HcsSupportPackage** de Windows PowerShell para recuperar los registros del controlador de administración de placa base (BMC), la operación producía un error con la siguiente advertencia: «La operación se realizó correctamente en este controlador, pero no se pudo realizar en el controlador del mismo nivel debido al siguiente error. Compruebe si el controlador del mismo nivel está en buen estado y si el nodo actual puede conectar con él». Este problema está corregido. | Sí | No |
| 3 | Conmutación por error del dispositivo | En la versión anterior, existía la posibilidad de que se produjese una incoherencia en los datos si un trabajo para **detectar la copia de seguridad** producía un error durante una conmutación por error del dispositivo. Este problema está corregido. | Sí | No |
| 4 | Conmutación por error del dispositivo | En la versión anterior, después de una conmutación por error del dispositivo, las copias de seguridad estaban visibles, pero el contenedor de volúmenes asociado no estaba presente en el dispositivo de destino. Este problema está corregido. | Sí | No |
| 5 | Conmutación por error del dispositivo | En la versión anterior, se producía un error en la enumeración de las copias de seguridad en la nube durante la operación de restauración del Registro que podía provocar una incoherencia en los datos si había problemas de conectividad en la nube. | Sí | No |
| 6 | Actualización de firmware | En la versión anterior, el trabajo de actualización de firmware del dispositivo mostraba un error que indicaba que los cmdlets no eran reconocibles y que, como resultado, la actualización no se había realizado correctamente. A continuación, el controlador entraba en modo de recuperación. Este problema está corregido. | Sí | No |
| 7 | Instalación | Se corrigieron los errores causados por la incorrecta digitalización del dispositivo durante la instalación. | Sí | No |
| 8 | Restablecimiento de fábrica | Ahora, si lo desea, puede omitir la comprobación de firmware para el restablecimiento de fábrica. Se trata de un cambio respecto de la versión anterior. | Sí | No |
| 9 | Restablecimiento de fábrica | En la versión anterior, cuando se ejecutaba un cmdlet de restablecimiento de fábrica, solo se realizaban comprobaciones de la versión del firmware para algunos componentes de hardware. Las comprobaciones del firmware adicionales se realizaban después del primer reinicio en el proceso, lo que podía provocar un error en el restablecimiento. Esta corrección garantiza que todas las comprobaciones del firmware se realizan cuando se ejecuta el cmdlet de restablecimiento de fábrica y antes del primer reinicio del sistema. | Sí | No |
| 10 | Rotación de claves de cuentas de almacenamiento | El cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** que se usa para rotar las claves de la cuenta de almacenamiento ahora le solicita al usuario que escriba la clave de cifrado de datos de servicio. Se trata de un cambio respecto de la versión anterior, donde la clave de cifrado de datos de servicio se pasaba como un parámetro en línea. | Sí | No |
| 11 | Conmutación por recuperación en 24 horas | Durante la recuperación ante desastres, la limpieza del dispositivo de origen no se realizó correctamente, lo que hizo que la conmutación por recuperación produjese un error. Esto se corrigió en esta versión. | Sí | No |

## Problemas conocidos de la versión de octubre

En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.

| N.º | Característica | Problema | Comentarios/solución alternativa | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Restablecimiento de fábrica | En algunos casos, al realizar un restablecimiento de fábrica, el dispositivo StorSimple puede bloquearse y mostrar este mensaje: **Restablecimiento de fábrica en curso (fase 8)**. Esto sucede si presiona CTRL+C mientras el cmdlet está en curso. | No presione CTRL+C después de iniciar un restablecimiento de fábrica. Si ya está en este estado, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. | Sí | No |
| 2 | Restablecimiento de fábrica | No efectúe un restablecimiento de fábrica en un dispositivo StorSimple actualizado de GA a la versión de octubre de 2014. | Esta operación solo funcionará si se instala una revisión. Póngase en contacto con el soporte técnico de Microsoft para obtener dicha revisión. | Sí | No |	
| 3 | Cuórum de disco | En raras ocasiones, si se desconecta la mayoría de los discos en el revestimiento de EBOD de un dispositivo 8600 y no se produce un cuórum de disco, el bloque de almacenamiento se desconectará. Seguirá desconectado incluso si se vuelven a conectar los discos. | Necesitará reiniciar el dispositivo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. | Sí | No |
| 4 | Errores de instantánea en la nube | En raras ocasiones, una instantánea en la nube puede producir el error **Se ha alcanzado el límite máximo de copia de seguridad**. Esto ocurre si se superan los 255 clones en línea en el mismo dispositivo, procedentes del mismo volumen original eliminado. | | Sí | Sí |
| 5 | Identificador de controlador incorrecto | Cuando se realiza un reemplazo de controlador, el controlador 0 puede aparecer como controlador 1. Durante el reemplazo de controlador, cuando se carga la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el identificador del controlador del mismo nivel. En raras ocasiones, este comportamiento también puede aparecer después del reinicio del sistema. |No se requiere ninguna acción del usuario. La situación se solucionará una vez completado el reemplazo del controlador. | Sí | No |
| 6 | Gráficos de supervisión de dispositivos | En el servicio de Administrador de StorSimple, los gráficos de supervisión de dispositivos no funcionan cuando está habilitada la autenticación básica o NTLM en la configuración del servidor proxy para el dispositivo. | Modifique la configuración de proxy web para el dispositivo registrado con el servicio de Administrador de StorSimple para que la autenticación se establezca en NONE. Para ello, ejecute el cmdlet Set-HcsWebProxy de Windows PowerShell para StorSimple. | Sí | Sí |
| 7 | Cuentas de almacenamiento | El uso del servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no admitido. Esto provocará una situación en la que no se pueden recuperar los datos de usuario. | | Sí | Sí |
| 8 | Conmutación por error del dispositivo | No se admiten varias conmutaciones por error de un contenedor de volúmenes del mismo dispositivo de origen a diferentes dispositivos de destino. | La conmutación por error de un único dispositivo inactivo a varios dispositivos hará que los contenedores de volúmenes del primer dispositivo conmutado por error pierdan la propiedad de los datos. Después de este tipo de conmutación por error, estos contenedores de volúmenes aparecerán o se comportarán de forma diferente cuando se visualicen en el Portal de Azure clásico. | Sí | No |
| 9 | Instalación | Durante la instalación del adaptador de StorSimple para SharePoint, deberá proporcionar una dirección IP de dispositivo para que la instalación finalice correctamente. | | Sí | No |
| 10 | Proxy web | Si la configuración de proxy web tiene HTTPS como protocolo especificado, la comunicación de dispositivo a servicio se verá afectada y el dispositivo se desconectará. También se generarán paquetes de compatibilidad en el proceso, que consumen muchos recursos en el dispositivo. | Asegúrese de que la dirección URL del proxy web tiene HTTP como protocolo especificado. Obtenga más información sobre cómo [Configurar el proxy web para el dispositivo](storsimple-configure-web-proxy.md). | Sí | No |
| 11 | Proxy web | Si configura y habilita el proxy web en un dispositivo registrado, será necesario reiniciar el controlador activo en el dispositivo. | | Sí | No |
| 12 | Latencia alta de la nube y alta carga de trabajo de E/S | Cuando el dispositivo StorSimple encuentra una combinación de latencias muy altas de la nube (del orden de segundos) y alta carga de trabajo de E/S, los volúmenes del dispositivo pasan a un estado degradado y las operaciones de E/S pueden fallar con el error «el dispositivo no está listo». | Necesitará reiniciar los controladores de dispositivo de forma manual o realizar una conmutación por error del dispositivo para recuperarse de esta situación. | Sí | No |

## Actualizaciones del dispositivo físico en la versión de octubre

Cuando estas actualizaciones se aplican a un dispositivo físico, la versión del software cambiará a 6.3.9600.17312. A menos que se especifique lo contrario, estas notas de la versión se aplican a todos los dispositivos de StorSimple. Para obtener más información sobre estas actualizaciones, consulte [Actualización de software del dispositivo físico de octubre de 2014 para el dispositivo de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2986997).

## Controlador SCSI conectado en serie (SAS) y actualizaciones de firmware en la versión de octubre

Esta versión actualiza el controlador y el firmware del controlador SAS del dispositivo físico. Para obtener más información sobre la actualización del controlador SAS, vea [Actualización de octubre de 2014 para los controladores SAS LSI del dispositivo de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987020).

Esta versión también aplica una actualización de firmware acumulativa que soluciona problemas de confiabilidad de los componentes de hardware del dispositivo. Para obtener más información sobre la actualización de firmware, consulte [Actualización de firmware de octubre de 2014 para el dispositivo de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987015).

## Actualizaciones del dispositivo virtual en la versión de octubre

Esta versión no contiene actualizaciones para el dispositivo virtual. La aplicación de esta actualización no cambiará la versión de software de un dispositivo virtual.
 

<!---HONumber=AcomDC_1203_2015-->