<properties 
   pageTitle="Solución de problemas de un dispositivo StorSimple implementado | Microsoft Azure"
   description="Describe cómo diagnosticar y corregir los errores que se producen en un dispositivo de StorSimple que está implementado y operativo actualmente."
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
   ms.date="01/29/2016"
   ms.author="v-sharos" />

# Solución de problemas de un dispositivo de StorSimple operativo

## Información general

En este artículo se proporcionan instrucciones útiles para solucionar problemas de configuración que se pueden encontrar una vez que el dispositivo de StorSimple está implementado y operativo. Se describen los problemas comunes, las causas posibles y los pasos recomendados para ayudarle a resolver los problemas que pueden producirse al ejecutar Microsoft Azure StorSimple. Esta información se aplica tanto al dispositivo físico local de StorSimple como al dispositivo virtual.

Al final de este artículo, verá una lista de códigos de error que pueden surgir durante la operación de Microsoft Azure StorSimple, así como los pasos que puede seguir para resolver los errores.

## Proceso del Asistente de instalación para dispositivos operativos

Use el Asistente para instalación ([Invoke-HcsSetupWizard][1]) para comprobar la configuración del dispositivo y tomar medidas correctivas si es necesario.

Al ejecutar el Asistente para instalación en un dispositivo previamente configurado y operativo, el flujo del proceso es diferente. Solo se pueden cambiar las siguientes entradas:

- Dirección IP, máscara de subred y puerta de enlace
- Servidor DNS principal
- Servidor NTP principal
- Configuración de proxy web opcional

El Asistente para instalación no lleva a cabo las operaciones relacionadas con la recopilación de contraseñas y el registro del dispositivo.

## Errores que se producen durante las ejecuciones posteriores del Asistente para instalación

En la tabla siguiente se describen los errores que pueden surgir al ejecutar el Asistente para instalación en un dispositivo operativo, las posibles causas y las acciones recomendadas para resolverlos.

| Nº | Mensaje o condición de error | Causas posibles | Acción recomendada |
|:--- |:-------------------------- |:--------------- |:------------------ |
| 1 | Error 350032: El dispositivo ya se ha desactivado. | Verá este error si ejecuta al Asistente para instalación en un dispositivo que está desactivado. | [Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para conocer los pasos siguientes. No se puede poner en servicio un dispositivo desactivado. Puede ser necesario un restablecimiento de fábrica para que el dispositivo pueda volver a activarse. |
| 2 | Invoke-HcsSetupWizard: ERROR\_INVALID\_FUNCTION (excepción de HRESULT: 0x80070001) | Se produce un error en la actualización del servidor DNS. La configuración de DNS es global y se aplica en todas las interfaces de red habilitadas. | Habilite la interfaz y vuelva a aplicar la configuración de DNS. Esto puede interrumpir la red en otras interfaces habilitadas, ya que esta configuración es global. |
| 3 | El dispositivo parece estar en línea en el portal de servicios de StorSimple Manager, pero al intentar completar la instalación mínima y guardar la configuración, se produce un error en la operación. | Durante la instalación inicial, no se configuró el proxy web, aunque había un servidor proxy local. | Use el [cmdlet Test-HcsmConnection][2] para localizar el error. [Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) si no puede corregir el problema. |
| 4 | Invoke-HcsSetupWizard: El valor no está dentro del intervalo esperado. | Este error se debe a una máscara de subred incorrecta. Las posibles causas son: <ul><li> La máscara de subred falta o está vacía.</li><li>El formato de prefijo de Ipv6 es incorrecto.</li><li>La interfaz está habilitada para la nube, pero la puerta de enlace falta o es incorrecta.</li></ul>Tenga en cuenta que DATA 0 se habilita automáticamente para la nube si se configura mediante el Asistente para instalación. | Para determinar el problema, utilice la subred 0.0.0.0 o 256.256.256.256 y, a continuación, examine la salida. Escriba los valores correctos para la máscara de subred, la puerta de enlace y el prefijo de Ipv6, según sea necesario. |
 
## Códigos de error

Los errores se muestran en orden numérico.

|Número de error|Texto o descripción del error|Acción del usuario recomendada|
|:---|:---|:---|
|10502|Se encontró un error al obtener acceso a la cuenta de almacenamiento.|Espere unos minutos y vuelva a intentarlo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|
|40017|No se puede resolver un disco en un conjunto de copia de seguridad.|Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|
|40018|No se puede resolver ninguno de los discos en un conjunto de copia de seguridad.|Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|
|390061|El sistema está ocupado o no está disponible.|Espere unos minutos y vuelva a intentarlo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|
|390143|Se produjo un error con el código de error 390143. (Error desconocido).|Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes.|

## Pasos siguientes

Si no puede resolver el problema, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.


[1]: https://technet.microsoft.com/es-ES/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/es-ES/%5Clibrary/Dn715782(v=WPS.630).aspx

<!---HONumber=AcomDC_0204_2016-->