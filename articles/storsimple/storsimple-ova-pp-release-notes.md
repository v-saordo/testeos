<properties 
   pageTitle="Notas de la versión de la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe los problemas críticos y las soluciones de la matriz virtual de StorSimple."
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
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Notas de la versión de la matriz virtual de StorSimple (vista previa) 

## Información general

Las siguientes notas de la versión identifican los problemas críticos de la versión de vista previa de diciembre de 2015 de la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple).

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar el dispositivo virtual de StorSimple, le recomendamos que lea detenidamente la información que encontrará en las notas de la versión.

>[AZURE.IMPORTANT]
>
>- La matriz virtual de StorSimple se encuentra en versión preliminar y está pensada con fines de evaluación y planificación de implementación. No se admite la instalación de esta versión preliminar en un entorno de producción. 
>
>- Si experimenta problemas con la matriz virtual de StorSimple, publique los problemas en el [foro de MSDN de StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.

| N.º| Característica | Problema | Soluciones alternativas o comentarios |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Actualizaciones | Los dispositivos virtuales que se crean en la versión preliminar no se pueden actualizar a una versión que sea compatible con la versión de disponibilidad general.| Debe conmutar por error estos dispositivos virtuales a la versión de disponibilidad general mediante un flujo de trabajo de recuperación ante desastres (DR).|
| **2.** | Volúmenes o recursos compartidos en niveles | El bloqueo del intervalo de bytes de las aplicaciones que funcionan con volúmenes de StorSimple no se admite. Si tiene habilitado un bloqueo de intervalo de bytes, la organización en niveles de StorSimple no funcionará. | Medidas recomendadas, entre otras:<ul><li>desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.</li><li> Guarde los datos de esta aplicación en volúmenes que estén anclados localmente en lugar de volúmenes organizados en niveles.</li>*Advertencia*: si usa volúmenes anclados localmente y tiene el bloqueo del intervalo de bytes habilitado, tenga en cuenta que el volumen anclado localmente puede estar en línea incluso antes de que se complete la restauración. En tal caso, si hay una restauración en curso, debe esperar a que esta se complete.|
| **3.** | Recursos compartidos organizados en niveles | Si trabaja con archivos de gran tamaño, estos podrían ocasionar que la organización en niveles se desarrolle lentamente. | Cuando trabaje con esta clase de archivos, asegúrese de que el archivo de mayor tamaño no ocupa más del 3 % del tamaño recurso compartido.|
| **4.** | Volúmenes o recursos compartidos anclados localmente | La capacidad de un volumen o recurso compartido anclado localmente y que pueda usar, corresponde al 85-90 % de la capacidad aprovisionada. El intervalo del 10-15 % está reservado a los metadatos. | Debe aprovisionar un volumen de mayor tamaño para obtener la capacidad utilizable deseada. Por ejemplo, para una capacidad utilizable de 1 TB, debe aprovisionar un volumen de 1,15 TB.|
| **5.** | Interfaz de usuario web local. | Si tiene las características de seguridad mejorada habilitadas en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario web local, tales como **Solución de problemas ** o **Mantenimiento**, no funcionen correctamente. Asimismo, cabe la posibilidad de que los botones de estas páginas tampoco funcionen. | Desactive las características de seguridad mejorada de Internet Explorer.|
| **6.** | Interfaz de usuario web local. | Esta versión no admite la localización de la interfaz de usuario web local. | Esta característica se implementará en una versión posterior.|
| **7.** | Interfaz de usuario web local. | En una máquina virtual de Hyper-V, las interfaces de red que se encuentran en la interfaz de usuario web se muestran como interfaces de 10 Gbps. | Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra los adaptadores de red virtual a 10 Gbps.|                                                                                                 
| **8.** | Recuperación ante desastres | Solo puede realizar la recuperación ante desastres de un servidor de archivos en el mismo dominio que el del dispositivo de origen. Con esta versión no se puede realizar la recuperación ante desastres en el dispositivo de destino de otro dominio. | Esta característica se implementará en una versión posterior.|
| **9.**| Capacidad de recursos compartidos usada| Si no hay datos en el recurso compartido, es posible que vea cierto consumo del recurso compartido. Esto ocurre porque la capacidad que se usa para los recursos compartidos incluye los metadatos. | |                                                                                                                                                                                                                                                         
| **10.** | Configuración de la zona horaria| Si se cambia la zona horaria de un dispositivo registrado, el cambio no se refleja en las pruebas de diagnóstico que se ejecutan a través de la interfaz de usuario web local. | Este problema se abordará en una versión posterior.|
| **11.** | Configuración de red | Cuando se configura una dirección IP estática para una interfaz de red a través de la interfaz de usuario web, se produce el cambio de IP. Sin embargo, también se modifica la dirección IP del servidor DNS a una dirección local de un sitio IPv6. | Este problema se abordará en una versión posterior.|
| **12.** | Azure PowerShell | En esta versión no se pueden administrar los dispositivos virtuales de StorSimple a través de Azure PowerShell. | Toda la administración de los dispositivos virtuales debe realizarse mediante el portal de Azure clásico y la interfaz de usuario web local.|
| **13.** | Trabajos | El progreso del trabajo no está pormenorizado. Es más, el progreso del trabajo puede pasar de 0 a 100 % directamente. | Esta situación se abordará en una versión posterior.|
| **14.** | Disco de datos aprovisionado | Una vez se haya aprovisionado un disco de datos de un determinado tamaño y cree el correspondiente dispositivo virtual de StorSimple, no debe expandir o reducir el disco de datos. Si intentar hacer esto, es posible que pierda todos los datos de los niveles locales del dispositivo. | |
| **15.** | Ayuda | Los temas de ayuda no estarán disponibles.| Tiene toda la documentación disponible en el Centro de descarga de Microsoft y el sitio de documentación de Azure. |   

<!---HONumber=AcomDC_0121_2016-->