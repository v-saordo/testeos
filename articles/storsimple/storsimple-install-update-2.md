<properties 
   pageTitle="Instalar la actualización 2 en el dispositivo de StorSimple | Microsoft Azure"
   description="Se explica cómo instalar la actualización 2 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
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
   ms.date="01/27/2016"
   ms.author="alkohli" />

# Instalar la actualización 2 en el dispositivo de StorSimple

## Información general

En este tutorial se explica cómo instalar la actualización 2 en un dispositivo de StorSimple ejecutando una versión anterior del software mediante el Portal de Azure clásico. En él también se tratan los pasos necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo de StorSimple y está intentando actualizar desde una versión de software anterior a la actualización 1.

En la actualización 2 se incluyen actualizaciones de software del dispositivo, actualizaciones de controladores LSI y actualizaciones del firmware del disco. Las actualizaciones del software y de los controladores LSI no provocan interrupciones y se pueden aplicar a través del Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]
 
> -  Es posible que no vea la actualización 2 de inmediato porque hacemos una implementación por fases de las actualizaciones. Busque actualizaciones de nuevo en unos días ya que estarán disponibles pronto.
> - En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a conectividad de la red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico. 
> - Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. La instalación de las actualizaciones puede tardar de 4 a 7 horas (incluidas las actualizaciones de Windows Update). Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.
> - Si ejecuta StorSimple Snapshot Manager opcional, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión d Snapshot Manager a la actualización 2.

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

## Instalación de la actualización 2 a través del Portal de Azure clásico 

Este es el procedimiento recomendado para actualizar el dispositivo. Lleve a cabo los siguiente pasos.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Instalar la actualización 2 como una revisión 

Debe usar este procedimiento solo si la comprobación de la puerta de enlace es incorrecta al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1.

Las versiones de software que se pueden actualizar con el método de revisión son la actualización 0.1, actualización 0.2, actualización 0.3, actualización 1, actualización 1.1 y actualización 1.2. El método de revisión implica los tres pasos siguientes:

- Descargar las revisiones desde el catálogo de Microsoft Update.
- Instalar y comprobar las revisiones de modo normal.
- Instalar y comprobar la revisión del modo de mantenimiento.

Las revisiones que se aplican a través de este método son como se indican a continuación:

| Orden | KB | Nombre | Tipo de actualización |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Actualización de software | Normal |
| 2 | KB3121900 | Controlador LSI | Normal |
| 3 | KB3080728 | Corrección de Storport | Normal |
| 4 | KB3090322 | Corrección de Spaceport | Normal |
| 5 | KB3121899 | Firmware del disco | Mantenimiento |


> [AZURE.IMPORTANT] 
> 
> - Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.
> - Este procedimiento debe realizarse una sola vez para aplicar la actualización 2. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> - Cada instalación de la revisión puede tardar unos 20 minutos en completarse. El tiempo total de instalación es casi 2 horas. 
> - Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.

Realice los pasos siguientes para aplicar la actualización 2.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## Solución de errores en actualización

**¿Qué ocurre si ve una notificación que indica que se ha producido un error en las comprobaciones previas a la actualización?**

Si se produce un error en la comprobación previa, asegúrese de que ha examinado la barra de notificación detallada en la parte inferior de la página. Esto proporciona una guía para saber en qué comprobación previa se ha producido el error. La siguiente ilustración muestra una instancia en la que aparece dicha notificación. En este caso, ni la comprobación de mantenimiento del controlador ni la comprobación de mantenimiento de los componentes de hardware han resultado satisfactorias. En la sección **Estado del hardware**, puede ver que los componentes de **Controller 0** y **Controller 1** necesitan atención.
 
  ![Error de comprobación previa](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

Es preciso asegurarse de que ambos controladores funcionan correctamente y están en línea. También necesitará asegurarse de que en la página de mantenimiento se muestra que todos los componentes de hardware del dispositivo StorSimple están en buen estado. A continuación, puede intentar instalar las actualizaciones. Si no puede corregir los problemas de los componentes de hardware, deberá ponerse en contacto con el servicio de soporte técnico de Microsoft para los pasos siguientes.

**¿Qué sucede si recibe el mensaje de error "Las actualizaciones no se pudieron instalar" y la recomendación es hacer referencia a la guía de solución de problemas de actualización para determinar la causa del error?**

Una causa probable podría ser que no tiene conectividad con los servidores de Microsoft Update. Esta es una comprobación manual que se debe realizar. Si se pierde la conexión con servidor de actualizaciones, se producirá un error en el trabajo de actualización. Para comprobar la conectividad, ejecute el siguiente cmdlet desde la interfaz de Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Ejecute el cmdlet en ambos controladores.
 
Si ha comprobado que existe conectividad y sigue apareciendo este problema, póngase en contacto con servicio de soporte técnico de Microsoft para los pasos siguientes.


## Pasos siguientes

Obtenga más información sobre el [lanzamiento de la actualización 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0128_2016-->