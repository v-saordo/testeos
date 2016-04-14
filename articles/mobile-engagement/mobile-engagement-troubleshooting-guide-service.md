<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement - Servicio" 
   description="Guías de solución de problemas de Azure Mobile Engagement" 
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

# Guía de solución de problemas de servicio

Los siguientes son posibles problemas que pueden producirse con cómo Azure Mobile Engagement se ejecuta.

## Interrupciones del servicio

### Problema
- Problemas que parecen deberse a interrupciones del servicio de Azure Mobile Engagement.

### Causas
- Los problemas que parecen deberse a interrupciones del servicio de Azure Mobile Engagement pueden deberse a diversos problemas:
    - Problemas aislados que originalmente parecen sistémicos de todo Azure Mobile Engagement
    - Problemas conocidos causados por interrupciones de los servidores (no siempre se muestran en el estado del servidor):
	- Retrasos de la programación, errores de orientación, problemas de actualización de distintivos, detención de la recopilación de estadísticas, detención del funcionamiento de las API, imposibilidad de crear nuevas aplicaciones o usuarios, errores de DNS y errores de tiempo de espera en la interfaz de usuario, en la API o en las aplicaciones de un dispositivo.
    - Interrupciones de dependencia en la nube [Estado del servicio de Azure](http://status.azure.com/)
    - Interrupciones de dependencia de los servicios de notificación de inserción (PNS)
    - Interrupciones de la tienda de aplicaciones

1) Para comprobar si el problema es sistémico, puede probar la misma función desde otro:
   
   - Aplicación integrada de Azure Mobile Engagement
   - Dispositivo de prueba
   - Versión del sistema operativo del dispositivo de prueba
   - Campaña
   - Cuenta de usuario administrador
   - Explorador (Internet Explorer, Firefox, Chrome, etc.)
   - Equipo

2) Para probar si el problema solo afecta a la interfaz de usuario o a la API:

   - Pruebe la misma función en la interfaz de usuario de Azure Mobile Engagement y en la API de Azure Mobile Engagement.

3) Para probar si el problema está relacionado con la red de telefonía móvil:

   - Realice la prueba mientras está conectado a Internet a través de Wi-Fi y mientras está conectado a través de la red de teléfono móvil 3G.
   - Confirme que el firewall no está bloqueando ninguna de las direcciones IP de Azure Mobile Engagement o ningún puerto.

4) Para probar si el problema está relacionado con el dispositivo:

   - Compruebe que el dispositivo puede conectarse a Azure Mobile Engagement con otra aplicación integrada en Azure Mobile Engagement.
   - Compruebe que puede generar eventos, trabajos y bloqueos desde el teléfono que se puede ver en la interfaz de usuario de Azure Mobile Engagement. 
   - Compruebe que puede enviar notificaciones push desde la interfaz de usuario de Azure Mobile Engagement al dispositivo según su identificador de dispositivo. 

5) Para probar si el problema está relacionado con la aplicación:

   - Instale y pruebe la aplicación desde un emulador en lugar de hacerlo desde un dispositivo físico:
   
6) Para probar si el problema está relacionado con las actualizaciones del sistema operativo de los dispositivos del usuario final, que requieren una actualización SDK para resolver:

   - Pruebe la aplicación en diferentes dispositivos con distintas versiones del sistema operativo.
   - Confirme que está utilizando la versión más reciente del SDK.
 
## Problemas de conectividad e información incorrecta

### Problema
- Problemas para iniciar sesión en la interfaz de usuario de Azure Mobile Engagement.
- Errores de conexión con la API de Azure Mobile Engagement.
- Problemas al cargar etiquetas de información de aplicación a través de la API del dispositivo.
- Problemas de descarga de registros o datos exportados desde Azure Mobile Engagement.
- Información incorrecta que se muestra en la interfaz de usuario de Azure Mobile Engagement.
- Información incorrecta que se muestra en los registros de Azure Mobile Engagement.

### Causas
* Confirme que su cuenta de usuario tiene permisos suficientes para realizar la tarea.
* Confirme que el problema no se limita a un equipo o a la red local.
* Confirme que el servicio de Azure Mobile Engagement no tiene interrupciones registradas.
* Confirme que los archivos de la etiqueta de información de la aplicación siguen todas estas reglas:
	- Use únicamente el juego de caracteres UTF8 (no se admite el juego de caracteres ANSI).
    - Use una coma "," como carácter separador (puede abrir una solicitud de servicio para solicitar cambiar el carácter separador de .csv de una coma "," a otro carácter como un punto y coma ";").
    - Use minúsculas para los valores booleanos "true" y "false".
    - Use un archivo que de tamaño inferior al tamaño máximo de archivo de 35 MB.
 

<!---HONumber=AcomDC_0302_2016-->