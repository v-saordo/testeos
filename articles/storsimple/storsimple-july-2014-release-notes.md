<properties 
   pageTitle="Notas de la versión de StorSimple 8000 | Microsoft Azure"
   description="Describe las nuevas características, problemas y soluciones alternativas de la versión de Microsoft Azure StorSimple de julio de 2014."
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

# Notas de la versión de StorSimple 8000 Series - julio de 2014 

## Información general

Las notas siguientes identifican los problemas críticos por resolver de la versión de disponibilidad general (GA) de julio de 2014 de StorSimple 8000 Series de Microsoft Azure StorSimple. Esta versión se corresponde con la versión de software 6.3.9600.17215.

A menos que se especifique lo contrario, estas notas de la versión se aplican a todos los dispositivos de StorSimple. Las notas se actualizan continuamente y, a medida que se descubren, se van agregando problemas críticos que requieren una solución alternativa. Antes de implementar la solución de Microsoft Azure StorSimple, tenga en cuenta la siguiente información.

## Problemas conocidos en esta versión
En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.
 
| N.º | Característica | Problema | Comentarios/solución alternativa | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Restablecimiento de fábrica | En algunos casos, al realizar un restablecimiento de fábrica, el dispositivo StorSimple puede bloquearse y mostrar este mensaje: **Restablecimiento de fábrica en curso (fase 8)**. Esto sucede si presiona CTRL+C mientras el cmdlet está en curso. | No presione CTRL+C después de iniciar un restablecimiento de fábrica. Si ya está en este estado, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. | Sí | No |
| 2 | Cuórum de disco | En raras ocasiones, si se desconecta la mayoría de los discos en el revestimiento de EBOD de un dispositivo 8600 y no se produce un cuórum de disco, el bloque de almacenamiento se desconectará. Seguirá desconectado incluso si se vuelven a conectar los discos. | Necesitará reiniciar el dispositivo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. | Sí | No |
| 3 | Errores de instantánea en la nube | En raras ocasiones, una instantánea en la nube puede producir el error **Se ha alcanzado el límite máximo de copia de seguridad**. Esto ocurre si se superan los 255 clones en línea en el mismo dispositivo, procedentes del mismo volumen original eliminado. | | Sí | Sí |
| 4 | Identificador de controlador incorrecto | Cuando se realiza un reemplazo de controlador, el controlador 0 puede aparecer como controlador 1. Durante el reemplazo de controlador, cuando se carga la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el identificador del controlador del mismo nivel. En raras ocasiones, este comportamiento también puede aparecer después del reinicio del sistema. | No se requiere ninguna acción del usuario. La situación se solucionará una vez completado el reemplazo del controlador. | Sí | No |
| 5 | Gráficos de supervisión de dispositivos | En el servicio de Administrador de StorSimple, los gráficos de supervisión de dispositivos no funcionan cuando está habilitada la autenticación básica o NTLM en la configuración del servidor proxy para el dispositivo. | Modifique la configuración de proxy web para el dispositivo registrado con el servicio de Administrador de StorSimple para que la autenticación se establezca en NONE. Para ello, ejecute el cmdlet Set-HcsWebProxy de Windows PowerShell para StorSimple. | Sí | Sí |
| 6 | Cuentas de almacenamiento | El uso del servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no admitido. Esto provocará una situación en la que no se pueden recuperar los datos de usuario. | | Sí | Sí |
| 7 | Conmutación por recuperación | No se admite una conmutación por recuperación durante las 24 horas siguientes a la recuperación ante desastres (DR). | | Sí | No |
| 8 | Conmutación por error del dispositivo | No se admiten varias conmutaciones por error de un contenedor de volúmenes del mismo dispositivo de origen a diferentes dispositivos de destino. La conmutación por error de un único dispositivo inactivo a varios dispositivos hará que los contenedores de volúmenes del primer dispositivo conmutado por error pierdan la propiedad de los datos. Después de este tipo de conmutación por error, estos contenedores de volúmenes aparecerán o se comportarán de forma diferente cuando se visualicen en el Portal de Azure clásico. | | Sí | No |
| 9 | Instalación | Durante la instalación del adaptador de StorSimple para SharePoint, deberá proporcionar una dirección IP de dispositivo para que la instalación finalice correctamente. | | Sí | No |
| 10 | Interfaces de red | Las interfaces de red DATA 2 y DATA 3 se intercambiaron en el software. | Póngase en contacto con el soporte técnico de Microsoft si necesita configurar estas interfaces. | Sí | No |


 

<!---HONumber=AcomDC_1203_2015-->