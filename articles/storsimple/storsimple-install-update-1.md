<properties 
   pageTitle="Instalación de la actualización 1.2 en el dispositivo StorSimple | Microsoft Azure"
   description="Explica cómo instalar la actualización 1.2 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
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
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Instalación la actualización 1.2 en el dispositivo StorSimple

## Información general

En este tutorial se explica cómo instalar la actualización 1.2 en un dispositivo StorSimple con una versión de software anterior a la actualización 1. El tutorial también trata los pasos adicionales necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple.

La actualización 1.2 incluye actualizaciones de software del dispositivo, actualizaciones de controladores LSI y actualizaciones del firmware del disco. Las actualizaciones del software y de los controladores LSI son actualizaciones que no provocan interrupciones y se pueden aplicar mediante el Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

En función de la versión que se está ejecutando en el dispositivo, puede determinar si se aplicará la actualización 1.2. Para comprobar la versión del software del dispositivo, desplácese hasta la sección **Vista rápida** del **Panel** de su dispositivo.

</br>

| Si ejecuta la versión del software... | ¿Qué sucede en el portal? |
|---------------------------------|--------------------------------------------------------------|
| Lanzamiento - GA | Si está ejecutando la versión de lanzamiento (GA), no aplique esta actualización. [Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo.|
| Actualización 0.1 | El portal aplica la actualización 1.2. |
| Actualización 0.2 | El portal aplica la actualización 1.2. |
| Actualización 0.3 | El portal aplica la actualización 1.2. |
| Actualización 1 | Esta actualización no estará disponible. |
| Actualización 1.1 | Esta actualización no estará disponible. |

</br>

> [AZURE.IMPORTANT]
 
> -  Es posible que no vea la actualización 1.2 de inmediato porque hacemos una implementación por fases de las actualizaciones. Busque actualizaciones de nuevo en unos días ya que estarán disponibles pronto.
> - Esta actualización incluye un conjunto de comprobaciones previas manuales y automáticas para determinar el estado del dispositivo en cuanto a la conectividad de la red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico. 
> - Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. Las actualizaciones pueden tardar 5-10 horas en instalarse (incluidas las actualizaciones de Windows). Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.

## Preparación de las actualizaciones
Deberá realizar los siguientes pasos antes de buscar y aplicar la actualización:

1. Tome una instantánea de la nube de los datos del dispositivo.

2. Las direcciones IP fijas del controlador son enrutables y se pueden conectar a Internet. Estas direcciones IP fijas se usarán para atender las actualizaciones en el dispositivo. Para comprobarlo, ejecute el siguiente cmdlet en cada controlador de la interfaz de Windows PowerShell del dispositivo:

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Salida de ejemplo para la conexión de prueba cuando los IP fijos pueden conectarse a Internet**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

Después de haber completado correctamente estas comprobaciones previas manuales, puede buscar e instalar las actualizaciones.

## Instalación de la actualización 1.2 mediante el Portal de Azure clásico 

Use este procedimiento solo si tiene una puerta de enlace configurada en la interfaz de red DATA 0 del dispositivo. Realice los pasos siguientes para actualizar el dispositivo.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]

## Instale la actualización 1.2 en un dispositivo que tenga una puerta de enlace configurada para una interfaz de red que no sea DATA 0 

Debe usar este procedimiento solo si la comprobación de la puerta de enlace es incorrecta al intentar instalar las actualizaciones mediante el Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1. Si el dispositivo no tiene una puerta de enlace en una interfaz de red que no sea DATA 0, puede actualizar el dispositivo directamente desde el Portal de Azure clásico. Consulte [Instalación de la actualización 1.2 mediante el Portal de Azure clásico](#install-update-12-via-the-azure-portal).

Las versiones de software que se pueden actualizar con este método son actualización 0.1, actualización 0.2 y actualización 0.3.


> [AZURE.IMPORTANT]
> 
> - Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.
> - Este procedimiento debe realizarse una sola vez para aplicar la actualización 1.2. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.

Si el dispositivo ejecuta el software previo a la actualización 1 y tiene una puerta de enlace establecida para una interfaz de red que no sea DATA 0, la actualización 1.2 se puede aplicar de las dos maneras siguientes:

- **Opción 1**: Descargar la actualización y aplicarla mediante el cmdlet `Start-HcsHotfix` desde la interfaz de Windows PowerShell del dispositivo. Éste es el método recomendado. **No use este método para aplicar la actualización 1.2 si el dispositivo ejecuta la actualización 1.0 o la actualización 1.1.** 

- **Opción 2**: quitar la configuración de la puerta de enlace e instalar la actualización directamente desde el Portal de Azure clásico.


En las siguientes secciones, se proporcionan instrucciones detalladas de cada una de estas opciones.

## Opción 1: Use Windows PowerShell para StorSimple para aplicar la actualización 1.2 como una revisión

Solo debe usar este procedimiento si ejecuta la actualización 0.1, 0.2 y 0.3 y si la comprobación de la puerta de enlace produce un error al intentar instalar actualizaciones desde el Portal de Azure clásico. Si está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo.

Antes de utilizar este procedimiento para aplicar la actualización, asegúrese de que:

- Los dos controladores de dispositivo están en línea.

Realice los pasos siguientes para aplicar la actualización 1.2. **Las actualizaciones podrían tardar alrededor de 2 horas en completarse (aproximadamente 30 minutos para el software, 30 minutos para los controladores, 45 minutos para el firmware del disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Opción 2: use el Portal de Azure clásico para aplicar la actualización 1.2 después de quitar la configuración de la puerta de enlace

Este procedimiento solo se aplica a los dispositivos StorSimple que ejecutan una versión del software anterior a la actualización 1 y tienen una puerta de enlace establecida en una interfaz de red que no sea DATA 0. Deberá borrar la configuración de la puerta de enlace antes de aplicar la actualización.
 
La actualización puede tardar unas horas en completarse. Si los hosts se encuentran en subredes diferentes, la eliminación de la configuración de la puerta de enlace de las interfaces de iSCSI podría provocar un tiempo de inactividad. Para reducir el tiempo de inactividad, se recomienda configurar DATA 0 para el tráfico iSCSI.
 
Realice los pasos siguientes para deshabilitar la interfaz de red con la puerta de enlace y, a continuación, aplique la actualización.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Solución de errores en actualización

**¿Qué ocurre si ve una notificación que indica que se ha producido un error en las comprobaciones previas a la actualización?**

Si se produce un error en la comprobación previa, asegúrese de que ha examinado la barra de notificación detallada en la parte inferior de la página. Esto proporciona una guía para saber en qué comprobación previa se ha producido el error. La siguiente ilustración muestra una instancia en la que aparece dicha notificación. En este caso, ni la comprobación de mantenimiento del controlador ni la comprobación de mantenimiento de los componentes de hardware han resultado satisfactorias. En la sección **Estado del hardware**, puede ver que los componentes de **Controller 0** y **Controller 1** necesitan atención.
 
  ![Error de comprobación previa](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Es preciso asegurarse de que ambos controladores funcionan correctamente y están en línea. También necesitará asegurarse de que en la página de mantenimiento se muestra que todos los componentes de hardware del dispositivo StorSimple están en buen estado. A continuación, puede intentar instalar las actualizaciones. Si no puede corregir los problemas de los componentes de hardware, deberá ponerse en contacto con el servicio de soporte técnico de Microsoft para los pasos siguientes.

**¿Qué sucede si recibe el mensaje de error "Las actualizaciones no se pudieron instalar" y la recomendación es hacer referencia a la guía de solución de problemas de actualización para determinar la causa del error?**

Una causa probable podría ser que no tiene conectividad con los servidores de Microsoft Update. Esta es una comprobación manual que se debe realizar. Si se pierde la conexión con servidor de actualizaciones, se producirá un error en el trabajo de actualización. Para comprobar la conectividad, ejecute el siguiente cmdlet desde la interfaz de Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Ejecute el cmdlet en ambos controladores.
 
Si ha comprobado que existe conectividad y sigue apareciendo este problema, póngase en contacto con servicio de soporte técnico de Microsoft para los pasos siguientes.


## Pasos siguientes

Obtenga más información sobre el [lanzamiento de la actualización 1.2](storsimple-update1-release-notes.md).

<!---HONumber=AcomDC_0121_2016-->