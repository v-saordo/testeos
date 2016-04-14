<properties 
   pageTitle="Obtenga información acerca de los últimos lanzamientos de SO invitado de Azure | Microsoft Azure" 
   description="Noticias sobre los lanzamientos más recientes y compatibilidad con el SO invitado de Servicios en la nube de Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="02/22/2016"
   ms.author="yuemlu"/>

# Matriz de compatibilidad del SDK y versiones del SO invitado de Azure
Proporciona información actualizada sobre los lanzamientos del SO invitado de Azure más recientes para Servicios en la nube. Esta información le ayudará a planear su ruta de actualización antes de que se deshabilite un SO invitado.

> [AZURE.IMPORTANT] Esta página se aplica a los roles web y de trabajo de Servicios en la nube, que se ejecutan en un sistema operativo invitado. No se aplica a las máquinas virtuales de IaaS. Si configura los roles para utilizar actualizaciones automáticas del SO invitado como se describe en [Configuración de actualización del SO invitado de Azure][], no es fundamental que lea esta página.

<!-- -->
<br />

> [AZURE.TIP] Suscríbase a la [Fuente RSS de actualización del SO invitado][rss] para recibir la notificación más puntual en todos los cambios del sistema operativo invitado. Los cambios mencionados en la fuente se integrarán en esta página cada semana aproximadamente.



## Actualizaciones de noticias

###### **22 de febrero de 2016**
La implementación del SO invitado de febrero comienza el 22 de febrero de 2016 y está previsto que se publique el 9 de marzo de 2016.

###### **18 de enero de 2016**
La implementación del SO invitado de enero comienza el 18 de enero de 2016 y está previsto que se publique el 12 de febrero de 2016.

###### **4 de enero de 2016**
El SO invitado 201511-02 de noviembre se publicó el 4 de enero de 2016 para la implementación. Esta versión del SO no está establecida como el SO predeterminado para actualización automática, por lo que el tiempo de aprovisionamiento de la implementación del SO invitado a la versión de SO 201511-02 de noviembre sería un poco más largo.

###### **18 de diciembre de 2015**
La fecha de lanzamiento del SO invitado 201511-02 de noviembre se ha pospuesto del 16 de diciembre de 2015 al 4 de enero de 2016.

###### **9 de diciembre de 2015**
La implementación del SO invitado de diciembre comienza hoy, 10 de diciembre de 2015, y está previsto que se publique el 8 de enero de 2016.

###### **12 de noviembre de 2015**  
El 7 de agosto de 2014, Microsoft anunció que el soporte para .NET Framework 4, 4.5 y 4.5.1 finalizará el 12 de enero de 2016. Se recomienda que, para el 12 de enero de 2016, los clientes y desarrolladores completen la actualización en contexto a .NET Framework 4.5.2 para seguir recibiendo soporte técnico y actualizaciones de seguridad. Visite la directiva de ciclo de vida de soporte técnico de Microsoft .NET Framework para obtener más detalles.

El 27 de octubre, anunciamos que, Azure actualizará .NET Framework en la familia 2.x, 3.x y 4.x del sistema operativo invitado de Azure (SO invitado) a .NET Framework 4.5.2 en la próxima versión del SO invitado de noviembre. Desde entonces, hemos recibido comentarios de clientes para posponer la actualización automática a una versión de SO con .NET 4.5.2 y proporcionar una imagen con .NET 4.5.2 para validación de pruebas.

Para contemplar los requisitos de los clientes y proporcionar una actualización sin problemas a .NET 4.5.2, Azure actualizará .NET Framework en la familia 2.x, 3.x y 4.x del sistema operativo invitado de Azure (SO invitado) a .NET Framework 4.5.2 en la versión del SO invitado de enero de 2016. Los servicios en la nube que se ejecutan en la familia 2.x, 3.x y 4.x del SO invitado con las actualizaciones automáticas habilitadas se actualizarán al SO invitado de enero de 2016 con .NET Framework 4.5.2. En noviembre, el programa .NET Framework instalado en el SO predeterminado no se cambiará. Con el fin de ayudar a los clientes a validar su servicio en la nube con .NET 4.5.2, Azure proporcionará un segundo conjunto de versiones de SO de noviembre 201511-02 para .NET 4.5.2 para implementación manual.

En la tabla siguiente se muestran los cambios que se aplicarán.
 
| Familia del SO invitado | .NET Framework instalado antes que la versión de SO invitado 201511-02 | .NET Framework instalado en la versión de SO invitado 201511-02 | .NET Framework instalado en la versión de SO invitado 201512-01 | .NET Framework instalado en la versión de SO invitado 201601-01 o posterior |
| --------------- | ---------------- | ---------------- | ---------------- | ---------------- |
| Familia 2.x del SO basada en Windows Server 2008 R2 | .NET 3.5 y .NET 4.0 | .NET 3.5 y .NET 4.5.2 | .NET 3.5 y .NET 4.0 | .NET 3.5 y .NET 4.5.2 |
| Familia 3.x del SO basada en Windows Server 2012 | .NET 4.5 | .NET 4.5.2 | .NET 4.5 | .NET 4.5.2 | 
| Familia 4.x del SO basada en Windows Server 2012 R2 | .NET 4.5.1 | .NET 4.5.2 | .NET 4.5.1 | .NET 4.5.2 | 
 
Si se encuentra en la actualización manual del SO invitado, habrá dos versiones del SO invitado publicadas para todas las familias del SO invitado en noviembre.

   • WA-GUEST-OS-4.26\_201511-01, WA-GUEST-OS-3.33\_201511-01 y WA-GUEST-OS-2.45\_201511-01 (predeterminado)

   Estas incluyen el MSRC de noviembre y los paquetes acumulativos de Windows de octubre.

   • WA-GUEST-OS-4.26\_201511-02, WA-GUEST-OS-3.33\_201511-02 y WA-GUEST-OS-2.45\_201511-02

   Estas incluyen el MSRC de noviembre, los paquetes acumulativos de Windows de octubre y .NET Framework actualizado a 4.5.2.



Antes de actualizar, estas son algunas de las opciones para ayudar a validar el servicio en la nube con .NET 4.5.2:

1. 	Implemente un rol de servicio en la nube de prueba en la versión 201511-02 y lleve a cabo la validación de compatibilidad de aplicaciones. 

2. 	Instale .NET 4.5.2 en un rol de servicio en la nube de prueba y lleve a cabo la validación de compatibilidad de aplicaciones. Vea [Instalación de .NET en un rol de servicio en la nube] para instrucciones.

3. 	Lleve a cabo la validación de compatibilidad de aplicaciones en una VM independiente, ya sea localmente o en una VM de Azure con .NET 4.5.2 instalado.




###### **15 de octubre de 2015**
La implementación del SO invitado de octubre comienza hoy, 15 de octubre de 2015, y está previsto que se lance el 13 de noviembre de 2015.

Las versiones del SO invitado 4.24, 3.31 y 2.43 se lanzaron el 1 de octubre de 2015.

###### **9 de septiembre de 2015**
La implementación del SO invitado de septiembre comienza hoy, 9 de septiembre de 2015, y está previsto que se lance el 8 de octubre de 2015.

Las versiones del SO invitado 4.23, 3.30 y 2.42 se lanzaron el 9 de septiembre de 2015.

###### **14 de agosto de 2015**
La implementación del SO invitado de agosto comienza hoy, 14 de agosto de 2015, y está previsto que se lance el 11 de septiembre de 2015.

###### **7 de agosto de 2015**
Las versiones del SO invitado 4.22, 3.29 y 2.41 se lanzaron el 7 de agosto de 2015.

###### **14 de julio de 2015**
La implementación del SO invitado de julio comienza hoy, 14 de julio de 2015, y está previsto que se lance el 14 de agosto de 2015.

Las versiones 4.21, 3.28 y 2.40 del SO invitado se han publicado el 9 de julio de 2015.

###### **15 de junio de 2015**
La implementación del SO invitado de junio comienza hoy, 15 de junio de 2015, y está previsto que se lance el 9 de julio de 2015.

Las versiones del SO invitado 4.20, 3.27 y 2.39 se lanzaron el 12 de junio de 2015.

###### **20 de mayo de 2015:**
La implementación del SO invitado de mayo comienza hoy, 20 de mayo de 2015, y está previsto que se lance el 12 de junio de 2015.

###### **17 de abril de 2015**
Las versiones del SO invitado 4.19, 3.26 y 2.38 se han lanzado hoy.

Este lanzamiento contiene [MS15-034](https://technet.microsoft.com/library/security/MS15-034), una revisión **crítica** para servidores HTTP de Windows.

Las versiones del SO invitado 4.17, 3.24 y 2.36 se deshabilitarán el 17 de mayo de 2015.

###### **6 de abril de 2015**
Las versiones del SO invitado 4.18, 3.25 y 2.37 se lanzaron el 2 de abril de 2015.

Las versiones del SO invitado 4.16, 3.23 y 2.35 se deshabilitarán el 2 de mayo de 2015.


###### **11 de marzo de 2015**
Las versiones del SO invitado 4.17, 3.24 y 2.36 se lanzaron el 9 de marzo de 2015.

Las versiones del SO invitado 4.15, 3.22 y 2.34 tienen la fecha de deshabilitación establecida para el 9 de abril de 2015.


###### **29 de enero de 2015**
La fecha de deshabilitación de las versiones del SO invitado 4.14, 4.13, 3.21, 3.20, 2.33, 2.32 (lanzadas en noviembre) se ha desplazado. Se ha actualizado la siguiente matriz de lanzamientos de SO invitado.


###### **13 de enero de 2015, actualizada el 15 de enero de 2015**
El SO invitado de diciembre se lanzó el 14 de enero de 2015.


###### **13 de enero de 2015**
La implementación del SO invitado de enero está prevista para iniciarse a partir del 19 de enero de 2015 con la actualización de servicios en la nube que se ejecutan en la actualización automática. El SO invitado de enero se lanzará para la implementación en alguna fecha de febrero. Se deshabilitará SSL v3.0 de forma predeterminada, tal como se sugiere en la actualización de seguridad de enero. Este artículo se actualizará cuando haya disponible más información.

Como [se ha anunciado anteriormente][ssl3 announcement], la actualización de seguridad de enero al SO invitado de PaaS deshabilitará la compatibilidad con SSL v3.0 de forma predeterminada como se recomienda en el [Aviso de seguridad de Microsoft 3009008][]. Este cambio irá acompañado de las demás actualizaciones de seguridad mensuales. Los clientes de PaaS con actualizaciones automáticas habilitadas pueden comprobar si hay un impacto en la compatibilidad con las aplicaciones cuando se deshabilita SSL v3.0 ejecutando este [script Fix it][ssl3-fixit], que deshabilitará la compatibilidad con SSL v3.0 de manera proactiva.

###### **16 de diciembre de 2014. Actualizado el 7 de enero de 2015**
El lanzamiento del SO invitado de diciembre está previsto para iniciarse a partir del 9 de enero de 2015.




## Información de los lanzamientos del SO invitado

Esta sección enumera las versiones del SO invitado compatibles actualmente. Las versiones y familias del SO invitado tienen una fecha de lanzamiento, una fecha de deshabilitación y una fecha de expiración. A partir de la fecha de lanzamiento, una versión de SO invitado se puede seleccionar manualmente en el Portal de Azure clásico. Un SO invitado se quita del Portal de Azure clásico en la fecha en que se "deshabilita" o después. A continuación, está "en transición", pero se admite con capacidad limitada para actualizar una implementación. La fecha de expiración tiene lugar cuando una versión o familia está programada para quitarse del sistema de Azure completamente. Los servicios en la nube que se estén ejecutando en una versión cuando esta expire se detendrán, eliminarán o se forzará su actualización a una versión más reciente, como se detalla en la [directiva de compatibilidad y retirada del SO invitado de Azure][retirepolicy].

Microsoft es compatible con al menos dos versiones recientes de cada familia del SO invitado admitida. Puede mover la fecha de deshabilitación de una versión de SO invitado existente a una fecha posterior para asegurarse de que al menos dos versiones lanzadas estén habilitadas para la implementación.

> [AZURE.WARNING] La retirada de la familia 1 del SO invitado comenzó el 1 de junio de 2013 y está programada para finalizar pronto. No cree nuevas instalaciones y actualice las antiguas con esta familia del SO invitado. Para obtener más información, consulte [Información de retirada de la familia 1 del SO invitado][fam1retire]


### Explicación de la familia, la versión y el lanzamiento del SO invitado
Las familias del SO invitado se basan en versiones lanzadas de Microsoft Windows Server. El SO invitado es el sistema operativo subyacente en el que se ejecutan Servicios en la nube de Azure. Cada SO invitado tiene un número de familia, versión y lanzamiento.

La **familia del SO invitado** corresponde a una versión de sistema operativo de Windows Server en la que se basa un SO invitado. Por ejemplo, la familia 3 se basa en Windows Server 2012.

Una **"versión de SO invitado"** es la imagen de familia de SO más las revisiones relevantes del [Centro de respuestas de seguridad de Microsoft (MSRC)][msrc] disponibles en la fecha en que se genera la nueva versión del SO invitado. Es posible que no se incluyan todas las revisiones. Los números empiezan por 0 y se incrementan en 1 cada vez que se agrega un nuevo conjunto de actualizaciones. Solo se muestran los ceros finales si es importante. Es decir, la versión 2.10 es una versión mucho más posterior y diferente que la versión 2.1.

Una **"versión del SO invitado"** hace referencia a un lanzamiento de una versión del SO invitado. Un relanzamiento se produce si Microsoft encuentra problemas durante las pruebas que requieren cambios. El lanzamiento más reciente siempre reemplaza a los lanzamientos anteriores, ya sean públicos o no. El portal de Azure clásico solo permitirá a los usuarios elegir el lanzamiento más reciente de una versión concreta. Las implementaciones que se ejecutan en un lanzamiento anterior no suelen ser una actualización forzada, según la gravedad del error.

En el ejemplo siguiente, 2 es la familia, 12 es la versión y "rel2" es el lanzamiento.

**Lanzamiento del SO invitado**: 2.12 rel2

**Cadena de configuración para este lanzamiento** -WA-GUEST-OS-2. 12\_201208-02

La cadena de configuración para un SO invitado incluye esta misma información incrustada, junto con una fecha que muestra qué revisiones de MSRC se tuvieron en cuenta para ese lanzamiento. En este ejemplo, las revisiones de MSRC generadas para Windows Server 2008 R2 hasta e incluido agosto de 2012 se tuvieron en cuenta para su inclusión. Solo se incluyen las revisiones que se aplican específicamente a esa versión de Windows Server. Por ejemplo, si una revisión de MSRC se aplica a Microsoft Office, no se incluirá porque este producto no forma parte de la imagen base de Windows Server.

## Lanzamientos

## Lanzamientos de la familia 4
**Windows Server 2012 R2**

Admite .NET 4.0, 4.5, 4.5.1, 4.5.2 (Nota 2)

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 4\.29 | WA-GUEST-OS-4.29\_201602-01 | Previsto el 9 de marzo de 2016 | Se actualizará cuando se lance la versión 4.31 | TBD |
| 4\.28 | WA-GUEST-OS-4.28\_201601-01 | 12 de febrero de 2016 | Se actualizará cuando se publique la versión 4.30 | TBD |
| 4\.27 | WA-GUEST-OS-4.27\_201512-01 | 12 de enero de 2016 | Se actualizará cuando se publique la versión 4.29 | TBD |
| 4\.26 | WA-GUEST-OS-4.26\_201511-02 | 4 de enero de 2016 | 12 de marzo de 2016| TBD |
| 4\.26 | WA-GUEST-OS-4.26\_201511-01 | 10 de diciembre de 2015 | 12 de marzo de 2016| TBD |
| 4\.25 | WA-GUEST-OS-4.25\_201510-01 | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| 4\.24 | WA-GUEST-OS-4.24\_201509-01 | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| 4\.23 | WA-GUEST-OS-4.23\_201508-02 | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| 4\.22 | WA-GUEST-OS-4.22\_201507-02 | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| 4\.21 | WA-GUEST-OS-4.21\_201506-01 | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| 4\.20 | WA-GUEST-OS-4.20\_201505-02 | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| 4\.19 | WA-GUEST-OS-4.19\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 4\.18 | WA-GUEST-OS-4.18\_201503-01 | 2 de abril de 2015 | 12 de julio de 2015 | 14 de octubre de 2015 |
| 4\.17 | WA-GUEST-OS-4.17\_201502-01 | 9 de marzo de 2015 | 17 de mayo de 2015 | 14 de octubre de 2015 |
| 4\.16 | WA-GUEST-OS-4.16\_201501-01 | 29 de enero de 2015 | 2 de mayo de 2015 | 14 de octubre de 2015 |
| 4\.15 | WA-GUEST-OS-4.15\_201412-01 | 14 de enero de 2015 | 9 de abril de 2015 | 14 de octubre de 2015 |
| 4\.14 | WA-GUEST-OS-4.14\_201411-01 | 11 de noviembre de 2014 | 28 de febrero de 2015 | 14 de octubre de 2015 |
| 4\.13 | WA-GUEST-OS-4.13\_201410-01 | 3 de noviembre de 2014 | 14 de febrero de 2015 | 14 de octubre de 2015 |
| 4\.12 (Nota 1) | WA-GUEST-OS-4.12\_201409-02 | 6 de octubre de 2014 | 12 de octubre de 2014 | 23 de marzo de 2015 |
| 4\.11 (Nota 1) | WA-GUEST-OS-4.11\_201408-02 | 25 de agosto de 2014 | 11 de septiembre de 2014 | 23 de marzo de 2015 |
| 4\.10 | WA-GUEST-OS-4.10\_201407-01 | 18 de julio de 2014 | 1 de diciembre de 2014 | 23 de marzo de 2015 |
| 4\.9 | WA-GUEST-OS-4.9\_201406-01 | 16 de junio de 2014 | 10 de noviembre de 2014 | 23 de marzo de 2015 |
| 4\.8 | WA-GUEST-OS-4.8\_201405-01 | 1 de junio de 2014. | 1 de agosto de 2014 | 23 de marzo de 2015 |

## Lanzamientos de la familia 3

**Windows Server 2012**

Admite .NET 4.0, 4.5

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 3\.36 | WA-GUEST-OS-3.36\_201602-01 | Previsto el 9 de marzo de 2016 | Se actualizará cuando se lance la versión 3.38 | TBD |
| 3\.35 | WA-GUEST-OS-3.35\_201601-01 | 12 de febrero de 2016 | Se actualizará cuando se publique la versión 3.37 | TBD |
| 3\.34 | WA-GUEST-OS-3.34\_201512-01 | 12 de enero de 2016 | Se actualizará cuando se publique la versión 3.36 | TBD |
| 3\.33 | WA-GUEST-OS-3.33\_201511-02 | 4 de enero de 2016 | 12 de marzo de 2016| TBD |
| 3\.33 | WA-GUEST-OS-3.33\_201511-01 | 10 de diciembre de 2015 | 12 de marzo de 2016| TBD |
| 3\.32 | WA-GUEST-OS-3.32\_201510-01 | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| 3\.31 | WA-GUEST-OS-3.31\_201509-01 | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| 3\.30 | WA-GUEST-OS-3.30\_201508-02 | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| 3\.29 | WA-GUEST-OS-3.29\_201507-02 | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| 3\.28 | WA-GUEST-OS-3.28\_201506-01 | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| 3\.27 | WA-GUEST-OS-3.27\_201505-02 | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| 3\.26 | WA-GUEST-OS-3.26\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 3\.25 | WA-GUEST-OS-3.25\_201503-01 | 2 de abril de 2015 | 12 de julio de 2015 | 14 de octubre de 2015 |
| 3\.24 | WA-GUEST-OS-3.24\_201502-01 | 9 de marzo de 2015 | 17 de mayo de 2015 | 14 de octubre de 2015 |
| 3\.23 | WA-GUEST-OS-3.23\_201501-01 | 29 de enero de 2015 | 2 de mayo de 2015 | 14 de octubre de 2015 |
| 3\.22 | WA-GUEST-OS-3.22\_201412-01 | 14 de enero de 2015 | 9 de abril de 2015 | 14 de octubre de 2015 |
| 3\.21 | WA-GUEST-OS-3.21\_201411-01 | 11 de noviembre de 2014 | 28 de febrero de 2015 | 14 de octubre de 2015 |
| 3\.20 | WA-GUEST-OS-3.20\_201410-01 | 3 de noviembre de 2014 | 14 de febrero de 2015 | 14 de octubre de 2015 |
| 3\.19 (Nota 1) | WA-GUEST-OS-3.19\_201409-02 | 6 de octubre de 2014 | 12 de octubre de 2014 | 23 de marzo de 2015 |
| 3\.18 (nota 1) | WA-GUEST-OS-3.18\_201408-02 | 25 de agosto de 2014 | 11 de septiembre de 2014 | 23 de marzo de 2015 |
| 3\.17 | WA-GUEST-OS-3.17\_201407-01 | 18 de julio de 2014 | 1 de diciembre de 2014 | 23 de marzo de 2015 |
| 3\.16 | WA-GUEST-OS-3.16\_201406-01 | 16 de junio de 2014 | 10 de noviembre de 2014 | 23 de marzo de 2015 |
| 3\.15 | WA-GUEST-OS-3.15\_201405-01 | 1 de junio de 2014. | 1 de agosto de 2014 | 23 de marzo de 2015 |


## Lanzamientos de la familia 2

**Windows Server 2008 R2 SP1**

Admite .NET 3.5, 4.0

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 2\.48 | WA-GUEST-OS-2.48\_201602-01 | Previsto el 9 de marzo de 2016 | Se actualizará cuando se lance la versión 2.50 | TBD |
| 2\.47 | WA-GUEST-OS-2.47\_201601-01 | 12 de febrero de 2016 | Se actualizará cuando se publique la versión 2.49 | TBD |
| 2\.46 | WA-GUEST-OS-2.46\_201512-01 | 12 de enero de 2016 | Se actualizará cuando se publique la versión 2.48 | TBD |
| 2\.45 | WA-GUEST-OS-2.45\_201511-02 | 4 de enero de 2016 | 12 de marzo de 2016| TBD |
| 2\.45 | WA-GUEST-OS-2.45\_201511-01 | 10 de diciembre de 2015 | 12 de marzo de 2016| TBD |
| 2\.44 | WA-GUEST-OS-2.44\_201510-01 | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| 2\.43 | WA-GUEST-OS-2.43\_201509-01 | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| 2\.42 | WA-GUEST-OS-2.42\_201508-02 | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| 2\.41 | WA-GUEST-OS-2.41\_201507-02 | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| 2\.40 | WA-GUEST-OS-2.40\_201506-01 | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| 2\.39 | WA-GUEST-OS-2.39\_201505-02 | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| 2\.38 | WA-GUEST-OS-2.38\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 2\.37 | WA-GUEST-OS-2.37\_201503-01 | 2 de abril de 2015 | 12 de julio de 2015 | 14 de octubre de 2015 |
| 2\.36 | WA-GUEST-OS-2.36\_201502-01 | 9 de marzo de 2015 | 17 de mayo de 2015 | 14 de octubre de 2015 |
| 2\.35 | WA-GUEST-OS-2.35\_201501-01 | 29 de enero de 2015 | 2 de mayo de 2015 | 14 de octubre de 2015 |
| 2\.34 | WA-GUEST-OS-2.34\_201412-01 | 14 de enero de 2015 | 9 de abril de 2015 | 14 de octubre de 2015 |
| 2\.33 | WA-GUEST-OS-2.33\_201411-01 | 11 de noviembre de 2014 | 28 de febrero de 2015 | 14 de octubre de 2015 |
| 2\.32 | WA-GUEST-OS-2.32\_201410-01 | 3 de noviembre de 2014 | 14 de febrero de 2015 | 14 de octubre de 2015 |
| 2\.31 (Nota 1) | WA-GUEST-OS-2.31\_201409-02 | 6 de octubre de 2014 | 12 de octubre de 2014 | 23 de marzo de 2015 |
| 2\.30 (Nota 1) | WA-GUEST-OS-2.30\_201408-02 | 25 de agosto de 2014 | 11 de septiembre de 2014 | 23 de marzo de 2015 |
| 2\.29 | WA-GUEST-OS-2.29\_201407-01 | 18 de julio de 2014 | 1 de diciembre de 2014 | 23 de marzo de 2015 |
| 2\.28 | WA-GUEST-OS-2.28\_201406-01 | 16 de junio de 2014 | 10 de noviembre de 2014 | 23 de marzo de 2015 |
| 2\.27 | WA-GUEST-OS-2.27\_201405-01 | 1 de junio de 2014. | 1 de agosto de 2014 | 23 de marzo de 2015 |




### Lanzamientos de la familia 1
**La FAMILIA 1** se ha [retirado][fam1retire].

#### Nota 1
Los lanzamientos de agosto y septiembre de 2014 se implementaron parcialmente debido a problemas detectados con una [revisión de MSRC][MS14-046] a medio camino del lanzamiento. El problema no permite la instalación manual de .NET 3.5.1 en la familia 3 y 4. El lanzamiento se deshabilitó como medida de precaución de modo que los clientes no podían seleccionarlo manualmente.

#### Nota 2
En la fecha de 19 de septiembre de 2014, .NET 4.5.2 no se había probado específicamente en el SO invitado de Azure. Pero el SO invitado es esencialmente equivalente a Windows Server. Por consiguiente, las mismas reglas de compatibilidad que se aplican a los productos de Windows Server se aplican a las familias del SO invitado equivalente. Si se produce una excepción a esta directiva, póngase en contacto con el [soporte técnico de Azure][azuresupport]. Microsoft hará un esfuerzo comercialmente razonable para resolver el problema. [Paquete de instalación manual para .NET 4.5.2][net install pkg].

## Actualizaciones de MSRC incluidas en el SO invitado
La lista de revisiones que se incluyen con cada lanzamiento del SO invitado mensual está disponible [aquí][patches].

## Compatibilidad con SDK

Esta tabla muestra qué familia del SO invitado es compatible con las versiones del SDK de Azure. Para obtener información más allá de esta tabla, vea [Información de compatibilidad y retirada del SDK de Azure para .NET y API][retire policy sdk]. La información de esta lista reemplaza a la siguiente información.

> [AZURE.IMPORTANT] Para asegurarse de que el servicio funciona según lo previsto, debe implementarlo en la versión del SO invitado que sea compatible con la versión del SDK de Azure usada para desarrollar el servicio. Si no lo hace, el servicio implementado puede mostrar errores en la nube que no eran evidentes en el entorno de desarrollo.

| Familia del SO invitado | Versiones del SDK admitidas |
| --------------- | ---------------------- |
| 4 | Versión 2.1 y posteriores |
| 3 | Versión 1.8 y posteriores |
| 2 | Versión 1.3 y posteriores |
| 1 | Versión 1.0 y posteriores |


## Proceso de actualización del SO invitado
Esta página incluye información sobre los próximos lanzamientos del SO invitado. Los clientes han indicado que desean saber cuándo se produce un lanzamiento porque sus roles de servicio en la nube se reiniciarán si están establecidos en actualización "Automática". Las versiones del SO invitado suelen producirse al menos 5 días después de que tenga lugar la actualización de MSRC el segundo martes de cada mes. Los nuevos lanzamientos incluyen todas las revisiones de MSRC relevantes para cada familia del SO invitado.

Microsoft Azure publica actualizaciones constantemente. El SO invitado es solo una de estas actualizaciones en la canalización. Un lanzamiento puede verse afectado por una cantidad de factores tal que no se puede enumerar aquí. Además, Azure se ejecuta literalmente en cientos de miles de máquinas. Esto significa que es imposible especificar una fecha y hora exacta de reinicio de los roles. Actualizaremos la [fuente RSS del SO invitado][rss] con la información más reciente que tengamos, pero se debe considera la fecha como un período aproximado. Somos conscientes de que esto es problemático para los clientes y estamos trabajando en un plan limitar o regular los reinicios.

Cuando se publica un nuevo lanzamiento del SO invitado, puede tardar tiempo en propagarse completamente en Azure. A medida que los servicios se actualizan al nuevo SO invitado, estos se reinician respetando los dominios de actualización. Los servicios configurados para usar actualizaciones "Automáticas" serán los primeros en obtener el lanzamiento. Después de la actualización, verá la nueva versión del SO invitado para el servicio en el Portal de Azure clásico. Durante ese período se pueden producir relanzamientos. Algunas versiones se pueden implementar durante largos períodos de tiempo y puede que no se produzcan actualizaciones reinicios de actualizaciones automáticas durante muchas semanas después de la fecha de lanzamiento oficial. Una vez que un SO invitado está disponible, puede elegir esa versión explícitamente desde el portal o en el archivo de configuración.

Para una gran cantidad de información valiosa sobre los reinicios y punteros para obtener más información técnica detallada de las actualizaciones de SO invitado y de SO host, consulte la entrada de blog de MSDN titulada [Reinicios de instancias de rol debido a actualizaciones del SO][restarts].

Si actualiza manualmente el SO invitado, lea la [Directiva de retirada del SO invitado][retirepolicy].


## Directiva de compatibilidad y retirada del SO invitado
La directiva de compatibilidad y retirada del SO invitado se explica [aquí][retirepolicy].
 
## Archivo de noticias

###### **11 de noviembre de 2014**

El lanzamiento de noviembre (4.14, 3.21 y 2.33) se implementó el 11 de noviembre. Esta actualización se desplazó a una fecha anterior porque incluye la actualización de MSRC [Boletín de seguridad de Microsoft MS14-066: crítico][MS14-066]. Los roles de web y de trabajo en la actualización automática deben reiniciarse una vez en los próximos días y recibir esta corrección.

###### **10 de noviembre de 2014**
La fecha de deshabilitación del lanzamiento de octubre (4.13, 3.20 y 2.32) se ha actualizado en función de los comentarios de los clientes. La fecha de deshabilitación siempre será al menos dos meses a partir de la fecha de lanzamiento.

###### **4 de noviembre de 2014**
El lanzamiento de octubre (4.13, 3.20 y 2.32) se implementó el 4 de noviembre de 2014. Incluye la revisión de MSRC que provocó problemas con las versiones de agosto y septiembre. Para solucionar este problema, el lanzamiento de octubre incluye .NET 3.5 y 3.5.1 preinstalados pero deshabilitados. Los scripts que intenten instalar .NET 3.5 o 3.5.1 volverán a rehabilitarlos de manera efectiva y devolverán el mensaje de "correcta" para la instalación de .NET, además de evitar el problema de instalación completa creado por la revisión de MSRC.

**20 de octubre de 2014. Actualizado el 4 de noviembre de 2014** - El lanzamiento de septiembre (4.12, 3.19, 2.31 y 1.39) se implementó parcialmente debido a que la misma [revisión de MSRC MS14-046][MS14-046] provocaba errores para aquellos que intentaban instalar .NET 3.5 o 3.5.1 en la familia 3 o 4. .NET 3.5.x no es compatible oficialmente con ninguna de estas familias, pero Microsoft responde al cambio de comportamiento porque las instalaciones de algunos clientes dependen de esta versión y el cambio no se anunció. Las fechas de deshabilitación de los SO invitados anteriores (junio y julio) se retrasarán en consecuencia para que al menos dos SO invitados lanzados sean compatibles y estén disponibles. En el lanzamiento de octubre de 2014 se incluyó una solución para el problema de instalación de .NET.

El lanzamiento de octubre incluye .NET 3.5 y 3.5.1 preinstalado (pero está deshabilitado) y también incluye la revisión de MSRC indicada anteriormente. Los scripts que intenten instalar .NET 3.5 o 3.5.1 lo volverán a habilitar y devolverán el mensaje de "correcta" para la instalación de .NET, además de evitar el problema de instalación creado por la revisión de MSRC.

Debido a la implementación parcial de los dos últimos lanzamientos, los usuarios que elijan la actualización automática o hayan implementado nuevas instalaciones pueden estar en cualquiera de estos lanzamientos del SO invitado. La siguiente tabla enumera qué lanzamientos del SO invitado permiten instalar .NET 3.5 o 3.5.1 en la familia 3 y 4. Actualmente, si un lanzamiento permite la instalación, significa que no tiene instalada la revisión de MSRC MS14-046.

| Versión del SO. | Puede instalar .NET 3.5 | Incluye la revisión de MSRC [MS14-046][] |
| --- | --- | --- |
| Todas las versiones posteriores del SO invitado | Sí | Sí |
| WA-GUEST-OS-4.13\_201410-01 | Sí | Sí |
| WA-GUEST-OS-4.12\_201409-02 | No | Sí |
| WA-GUEST-OS-4.12\_201409-01 | No | Sí |
| WA-GUEST-OS-4.11\_201408-02 | Sí | No |
| WA-GUEST-OS-4.11\_201408-01 | No | Sí |
| WA-GUEST-OS-4.10\_201407-01 | Sí | No |

**20 de octubre de 2014** - Puesto que el lanzamiento de agosto y septiembre solo se implementó parcialmente, debe tener en cuenta lo siguiente:

1. Los cambios de cifrado indicados en Diferencias entre el SO invitado de Azure y Windows Server predeterminado no se han implementado en todo Azure. Los clientes que no disponen de los lanzamientos de agosto o septiembre recibirán estos cambios en el lanzamiento de octubre. 

2. Los SO invitados de agosto y septiembre se han deshabilitado en el Portal de Azure clásico. No se pueden elegir manualmente. Esto es para protegerle frente a problemas que pudieran surgir si selecciona esta versión del SO invitado.

3. Las fechas deshabilitadas de algunos lanzamientos anteriores se han adelantado. Esto sirve para garantizar la disponibilidad continua en el portal y admitir al menos dos versiones de SO invitado en cada familia.

## Archivo de lanzamiento

### Familia 4

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 4\.7 | WA-GUEST-OS-4.7\_201404-01 | 2 de mayo de 2014 | 2 de julio de 2014 | 18 de agosto de 2014 |
| 4\.6 | WA-GUEST-OS-4.6\_201403-01 | 28 de marzo de 2014 | 9 de junio de 2014. | 18 de agosto de 2014 |
| 4\.5. | WA-GUEST-OS-4.5\_201402-01 | 21 de marzo de 2014 | 21 de mayo de 2014 | 18 de agosto de 2014 |
| 4\.4. | WA-GUEST-OS-4.4\_201401-01 | 8 de febrero de 2014 | 8 de abril de 2014 | 14 de mayo de 2014 |
| 4\.3 | WA-GUEST-OS-4.3\_201312-01 | 6 de enero de 2014 | 6 de marzo de 2014 | 14 de mayo de 2014 |
| 4\.2 | WA-GUEST-OS-4.2\_201311-01 | 12 de diciembre de 2013 | 12 de febrero de 2014 | 14 de mayo de 2014 |
| 4\.1 | WA-GUEST-OS-4.1\_201310-01 | 29 de octubre de 2013 | N/D | 14 de mayo de 2014 |
| 4\.0 rel3 | WA-GUEST-OS-4.0\_201309-03 | 9 de octubre de 2013 Publicada el 18 de octubre. | N/D | 14 de mayo de 2014 |
 


### Familia 3

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 3\.14 | WA-GUEST-OS-3.14\_201404-01 | 2 de mayo de 2014 | 2 de julio de 2014 | 18 de agosto de 2014 |
| 3\.13 | WA-GUEST-OS-3.13\_201403-01 | 28 de marzo de 2014 | 9 de junio de 2014. | 18 de agosto de 2014 |
| 3\.12 | WA-GUEST-OS-3.12\_201402-01 | 21 de marzo de 2014 | 21 de mayo de 2014 | 18 de agosto de 2014 |
| 3\.11 | WA-GUEST-OS-3.11\_201401-01 | 8 de febrero de 2014 | 8 de abril de 2014 | 14 de mayo de 2014 |
| 3\.10 | WA-GUEST-OS-3.10\_201312-01 | 6 de enero de 2014 | 6 de marzo de 2014 | 14 de mayo de 2014 |
| 3\.9 | WA-GUEST-OS-3.9\_201311-01 | 12 de diciembre de 2013 | 12 de febrero de 2014 | 14 de mayo de 2014 |
| 3\.8 | WA-GUEST-OS-3.8\_201310-01 | 29 de octubre de 2013 | N/D | 14 de mayo de 2014 |
| 3\.7 rel3 | WA-GUEST-OS-3.7\_201309-03 | 9 de octubre de 2013 | N/D | 14 de mayo de 2014 |
| 3\.7 rel1 | WA-GUEST-OS-3.7\_201309-01 | 23 de septiembre de 2013 | N/D | 14 de mayo de 2014 |

### Familia 2

| Versión de SO invitado | Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 2\.26 | WA-GUEST-OS-2.26\_201404-01 | 2 de mayo de 2014 | 2 de julio de 2014 | 18 de agosto de 2014 |
| 2\.25 | WA-GUEST-OS-2.25\_201403-01 | 28 de marzo de 2014 | 9 de junio de 2014. | 18 de agosto de 2014 |
| 2\.24 | WA-GUEST-OS-2.24\_201402-01 | 21 de marzo de 2014 | 21 de mayo de 2014 | 18 de agosto de 2014 |
| 2\.23 | WA-GUEST-OS-2.23\_201401-01 | 8 de febrero de 2014 | 8 de abril de 2014 | 14 de mayo de 2014 |
| 2\.22 | WA-GUEST-OS-2.22\_201312-01 | 6 de enero de 2014 | 6 de marzo de 2014 | 14 de mayo de 2014 |
| 2\.21 | WA-GUEST-OS-2.21\_201311-01 | 12 de diciembre de 2013 | 12 de febrero de 2014 | 14 de mayo de 2014 |
| 2\.20 | WA-GUEST-OS-2.20\_201310-01 | 29 de octubre de 2013 | N/D | 14 de mayo de 2014 |
| 2\.19 rel3 | WA-GUEST-OS-2.19\_201309-03 | 9 de octubre de 2013 | N/D | 14 de mayo de 2014 |
| 2\.19 rel1 | WA-GUEST-OS-2.19\_201309-01 | 23 de septiembre de 2013 | N/D | 14 de mayo de 2014 |

[Instalación de .NET en un rol de servicio en la nube]: https://azure.microsoft.com/es-ES/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Configuración de actualización del SO invitado de Azure]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Aviso de seguridad de Microsoft 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
 

<!---HONumber=AcomDC_0224_2016-->