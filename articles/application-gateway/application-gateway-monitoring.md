<properties 
   pageTitle="Configuración de una puerta de enlace de aplicaciones para sondeos personalizados mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="En esta página se ofrecen instrucciones para configurar sondeos personalizados en la Puerta de enlace de aplicaciones mediante el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Supervisión de estado y sondeos personalizados 


La Puerta de enlace de aplicaciones de Azure supervisa el estado de todos los recursos de su grupo de back-end y elimina automáticamente del grupo aquellos que se considera que están en mal estado.

La configuración de la supervisión de estado se realiza mediante dos tipos de sondeos: sondeo de estado predeterminado y sondeo personalizado.

## Sondeo de estado predeterminado

Una puerta de enlace de aplicaciones configura automáticamente un sondeo de estado predeterminado cuando no hay ningún sondeo personalizado configurado. El comportamiento de supervisión funciona de la siguiente manera: se realiza una solicitud HTTP a las direcciones IP configuradas para el grupo de back-end y al puerto configurado en la opción HTTP del back-end para la puerta de enlace de aplicaciones.

Por ejemplo: configura la puerta de enlace de aplicaciones para usar los servidores back-end A, B y C para recibir el tráfico de red HTTP en el puerto 80. La supervisión de estado predeterminada comprueba los tres servidores cada 30 segundos para ver que la respuesta de HTTP es correcta. Una respuesta HTTP correcta tiene un [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 y 399.

Si la comprobación de sondeo predeterminado da error en el servidor A, la puerta de enlace de aplicaciones lo elimina de su grupo de back-end y el tráfico de red deja de fluir a este servidor. El sondeo predeterminado sigue comprobando el servidor A cada 30 segundos. Cuando el servidor A responde correctamente a una solicitud de un sondeo de estado predeterminado, se considera que está en buen estado y se agrega de nuevo al grupo de back-end; el tráfico comienza a fluir de nuevo a él.

El sondeo predeterminado usa solo las direcciones IP para comprobar el estado. Si quiere comprobar el estado probando la conectividad a una dirección URL, debe utilizar el sondeo personalizado.


## Sondeo personalizado 

Los sondeos personalizados permiten un control más específico sobre la supervisión de estado. Cuando se usan sondeos personalizados, puede configurar la comprobación del intervalo de sondeo, la dirección URL y la ruta de acceso a la prueba, así como el número de respuestas incorrectas que se aceptarán antes de declarar la instancia del grupo de back-end como en mal estado.


Configuración del sondeo personalizado:

- **Intervalo de sondeo**: configura las comprobaciones del intervalo de sondeo.
- **Tiempo de espera de sondeo**: define el tiempo de espera de sondeo de una comprobación de solicitud HTTP.
- **Umbral incorrecto**: especifica el número de solicitudes con error que es necesario para marcar la instancia como incorrecta.  
- **Nombre de host y ruta de acceso**: si su sitio web o granja de servidores web no tiene una respuesta HTTP para la dirección IP, debe configurar un nombre de host y una ruta de acceso de sondeo para obtener una respuesta HTTP correcta válida. Por ejemplo, tiene un sitio web http://contoso.com/ pero no produce una respuesta HTTP válida. Es necesario configurar un nombre de host y una ruta de acceso para proporcionar la respuesta HTTP correcta válida con el fin de validar que la instancia de servidor web es correcta. En este caso, el sondeo personalizado se puede configurar para "http://contoso.com/path/custompath.htm" de modo que las comprobaciones de sondeo tengan una respuesta HTTP correcta. 



>[AZURE.NOTE] Los sondeos personalizados solo se pueden configurar mediante PowerShell

<!---HONumber=AcomDC_0128_2016-->