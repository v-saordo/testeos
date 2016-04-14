<properties 
   pageTitle="Notas de la versión de la actualización 0.2 de StorSimple 8000 | Microsoft Azure"
   description="Describe las nuevas características y soluciones, problemas abiertos y soluciones alternativas de la versión de Microsoft Azure StorSimple de enero de 2015 (Actualización 0.2)."
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
   ms.date="01/15/2016"
   ms.author="v-sharos" />


# Notas de la versión 0.2 de la actualización de la serie StorSimple 8000 - enero de 2015

## Información general

En las notas siguientes se identifican los problemas críticos por resolver de la versión de enero de 2015 de Microsoft Azure StorSimple. También contienen una lista de las actualizaciones de software y firmware de StorSimple incluidas en esta versión. Esta es la segunda versión que aparece después de que la versión de lanzamiento de la serie StorSimple 8000 Series se pusiera a disposición general en julio de 2014.
  
Esta actualización no cambia la versión de software del dispositivo físico de la actualización de octubre. Sigue siendo la versión 6.3.9600.17312. En esta versión, la imagen que usa la imagen del dispositivo virtual cambió. Por lo tanto, en todos los dispositivos virtuales nuevos creados después del 20/01/2015 se mostrará la versión 6.3.9600.17361.

Consulte la siguiente información de las notas de la versión referente a la actualización de enero de 2015.

> [AZURE.IMPORTANT]
>
>- Esta actualización no está disponible a través de Windows Update y no se puede instalar como otras actualizaciones. El dispositivo no recibirá esta actualización aunque haya aplicado las actualizaciones mediante el Portal de Azure clásico. Esta actualización solo se aplicará a los dispositivos virtuales creados después del 20 de enero de 2015. 
> 
>- La versión de enero de StorSimple no contiene actualizaciones para el dispositivo físico StorSimple. Puede aplicar cualquier actualización disponible de Windows en el dispositivo virtual, incluidas revisiones de seguridad recientes, pero no verá un cambio de versión en el dispositivo físico StorSimple.

## Novedades de la versión de enero

Esta actualización contiene una revisión relacionada con los volúmenes que se desconectan en el dispositivo virtual. (Consulte [Problemas corregidos en esta versión](#issues-fixed-in-the-january-release)).

La actualización no contiene características o funcionalidades nuevas.

## Problemas corregidos en la versión de enero

En la tabla siguiente se describe el problema que se corrigió en esta actualización.

|N.º|Característica|Problema|Se aplica a un dispositivo físico|Se aplica a un dispositivo virtual 
|---|-------|-----|--------------------------|-------------------------
|1|Volúmenes que se desconectan|Cuando las latencias altas de la nube persisten durante varios minutos, los volúmenes del dispositivo virtual StorSimple se desconectan en los hosts. Esta revisión aumenta el umbral de las latencias de la nube, lo que reduce las situaciones que hacen que los volúmenes se desconecten en los hosts.|No|Sí  

## Problemas conocidos de la versión de enero

En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.
 
|N.º|Característica|Problema|Comentarios/solución alternativa|Se aplica a un dispositivo físico|Se aplica a un dispositivo virtual 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1|	Restablecimiento de fábrica|	En algunos casos, al realizar un restablecimiento de fábrica, el dispositivo StorSimple puede bloquearse y mostrar este mensaje: **Restablecimiento de fábrica en curso (fase 8)**. Esto sucede si presiona CTRL+C mientras el cmdlet está en curso.|	No presione CTRL+C después de iniciar un restablecimiento de fábrica. Si ya está en este estado, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|Sí|No|
|2|Cuórum de disco|	En raras ocasiones, si se desconecta la mayoría de los discos en el revestimiento de EBOD de un dispositivo 8600 y no se produce un cuórum de disco, el bloque de almacenamiento se desconectará. Seguirá desconectado incluso si se vuelven a conectar los discos.|Necesitará reiniciar el dispositivo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|Sí |No
|3|Errores de instantánea en la nube|En raras ocasiones, una instantánea en la nube puede producir el error **Se ha alcanzado el límite máximo de copia de seguridad**. Esto ocurre si se superan los 255 clones en línea en el mismo dispositivo, procedentes del mismo volumen original eliminado.||Sí|Sí
|4|Identificador de controlador incorrecto|Cuando se realiza un reemplazo de controlador, el controlador 0 puede aparecer como controlador 1. Durante el reemplazo de controlador, cuando se carga la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el identificador del controlador del mismo nivel. En raras ocasiones, este comportamiento también puede aparecer después del reinicio del sistema.|No se requiere ninguna acción del usuario. La situación se solucionará una vez completado el reemplazo del controlador.|Sí|No 
|5|	Gráficos de supervisión de dispositivos|En el servicio de Administrador de StorSimple, los gráficos de supervisión de dispositivos no funcionan cuando está habilitada la autenticación básica o NTLM en la configuración del servidor proxy para el dispositivo.|Modifique la configuración de proxy web para el dispositivo registrado con el servicio de Administrador de StorSimple para que la autenticación se establezca en NONE. Para ello, ejecute el cmdlet Set-HcsWebProxy de Windows PowerShell para StorSimple.|Sí|Sí
|6|	Cuentas de almacenamiento|El uso del servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no admitido. Esto provocará una situación en la que no se pueden recuperar los datos de usuario.|| Sí |	Sí
|7|Conmutación por error del dispositivo|	No se admiten varias conmutaciones por error de un contenedor de volúmenes del mismo dispositivo de origen a diferentes dispositivos de destino.|	La conmutación por error de un único dispositivo inactivo a varios dispositivos hará que los contenedores de volúmenes del primer dispositivo conmutado por error pierdan la propiedad de los datos. Después de este tipo de conmutación por error, estos contenedores de volúmenes aparecerán o se comportarán de forma diferente cuando se visualicen en el Portal de Azure clásico.|Sí|No
|8|	Instalación|Durante la instalación del adaptador de StorSimple para SharePoint, deberá proporcionar una dirección IP de dispositivo para que la instalación finalice correctamente.||Sí|No
|9|	Proxy web|Si la configuración de proxy web tiene HTTPS como protocolo especificado, la comunicación de dispositivo a servicio se verá afectada y el dispositivo se desconectará. También se generarán paquetes de compatibilidad en el proceso, que consumen muchos recursos en el dispositivo.|Asegúrese de que la dirección URL del proxy web tiene HTTP como protocolo especificado. Obtenga más información sobre cómo [Configurar el proxy web del dispositivo](storsimple-configure-web-proxy.md).|Sí |No
|10|Proxy web|	Si configura y habilita el proxy web en un dispositivo registrado, será necesario reiniciar el controlador activo en el dispositivo.||	Sí |No
|11|Latencia alta de la nube y alta carga de trabajo de E/S|Cuando el dispositivo StorSimple encuentra una combinación de latencias muy altas de la nube (del orden de segundos) y alta carga de trabajo de E/S, los volúmenes del dispositivo pasan a un estado degradado y las operaciones de E/S pueden fallar con el error «el dispositivo no está listo».|Necesitará reiniciar los controladores de dispositivo de forma manual o realizar una conmutación por error del dispositivo para recuperarse de esta situación.|Sí|No

## Actualizaciones del dispositivo físico en la versión de enero

Esta actualización no contiene otros cambios en el dispositivo StorSimple.

## Controlador SCSI conectado en serie (SAS) y actualizaciones de firmware en la versión de enero

Esta versión no contiene las actualizaciones para el controlador SCSI conectado en serie (SAS) o el firmware. La actualización del controlador se introdujo en la versión de octubre de 2014.

## Actualizaciones del dispositivo virtual en la versión de enero

Esta versión contiene una imagen actualizada para el dispositivo virtual. Todos los dispositivos virtuales creados después del 20 de enero de 2015 mostrarán la versión del software como 6.3.9600.17361.

 

<!---HONumber=AcomDC_0121_2016-->