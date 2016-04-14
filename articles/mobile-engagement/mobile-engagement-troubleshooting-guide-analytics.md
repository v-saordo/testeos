<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement - Análisis" 
   description="Solución de problemas de análisis, supervisión, segmentación y panel en Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/29/2016"
   ms.author="piyushjo"/>

# Guía de solución de problemas de análisis, supervisión, segmentación y panel

Los siguientes son posibles problemas que pueden producirse con cómo reúne Azure Mobile Engagement información acerca de sus aplicaciones, dispositivos y usuarios.

## Información no encontrada o retrasada

### Problema
- La información se retrasa en aparecer en el análisis, la segmentación o el panel.
- Falta información en la supervisión.
- Falta información de análisis, de la segmentación o del panel.
- Alcanzar límites de segmentación.

### Causas

- Puede utilizar la API de análisis, API de supervisión y la API de segmentos para ver si los datos que faltan en la interfaz de usuario están visibles a través de las API.
- Si el SDK de Azure Mobile Engagement no está integrado correctamente en la aplicación no podrá ver la información de análisis, segmentación, supervisión o paneles.
- Los segmentos no se pueden cambiar una vez creados, solo se pueden "clonar" (copiar) o "destruir" (eliminar). Los segmentos solo pueden contener 10 criterios.
- La mejor manera de probar la información que falta en la supervisión es configurar un dispositivo de prueba y desinstalar o volver a instalar la aplicación en el dispositivo de prueba.
- La información se actualiza cada 24 horas para analizarla, segmentarla o para los paneles.
- Es posible que la información de los segmentos nuevos no se muestre hasta 24 horas después de que se creen, aunque si el segmento se base en información anterior.
- Filtrar los datos de análisis de la interfaz de usuario mostrará todos los ejemplos de este tipo, independientemente de la versión de la aplicación (p. ej., "se bloquea" filtrado por nombre mostrará desde las versiones 1 y 2 de la aplicación).
- El período de tiempo de análisis se basa en la fecha de la configuración de dispositivo de los usuarios, por lo que un usuario cuyo teléfono tiene la fecha establecida incorrectamente podría aparecer en el período de tiempo incorrecto.
- No se registra ningún dato del servidor cuando utiliza el botón para "probar" inserciones, los datos solo se registran para campañas de inserción real.

## No puede buscar los elementos de la interfaz de usuario

### Problema
- No se pueden crear segmentos basados en determinados criterios de etiqueta de información de la aplicación personalizada o integrada.
- No se puede encontrar determinados criterios de etiquetas de información de aplicación personalizada o integrada en análisis, supervisión o paneles.
- No se pueden interpretar los datos de análisis, supervisión, segmentación o paneles.

### Causas

- Algunos elementos integrados y etiquetas de información de aplicación solo están disponibles como criterios de inserción, pero puede que no se agreguen a un segmento o no estén visibles desde el análisis, la supervisión o el panel. 
- Para los elementos integrados y las etiquetas de información de aplicación que no se pueden agregar a un segmento, necesitará configurar la lista de criterios de destinatarios en cada campaña para realizar la misma función que la orientación en función de un segmento.
- Consulte los menús contextuales en las secciones de análisis, supervisión, segmentación y paneles de la interfaz de usuario de Azure Mobile Engagement para obtener más ayuda e información práctica.

## Solución de problemas de bloqueos

### Problema
- Bloqueos de la aplicación que aparecen en el panel, supervisión o análisis.

### Causas

- Para solucionar problemas de bloqueos de aplicaciones vistos en el análisis, la supervisión o el panel, asegúrese de comprobar las notas de la versión para obtener información sobre problemas conocidos con las versiones anteriores del SDK.
- Para solucionar más bloqueos de la aplicación, realice un evento desde un dispositivo de prueba con la aplicación instalada y buscar el identificador del dispositivo en la sección "Monitor – eventos" de la interfaz de usuario de Azure Mobile Engagement. Después, realice el evento que causa que la aplicación se bloquee y busque información adicional en la sección "Supervisión – Bloqueo" de la interfaz de usuario de Azure Mobile Engagement. 

 

<!---HONumber=AcomDC_0302_2016-->