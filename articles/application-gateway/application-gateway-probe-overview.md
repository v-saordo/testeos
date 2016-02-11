

<properties
   pageTitle="Información general sobre la supervisión de estado para la puerta de enlace de aplicaciones de Azure | Microsoft Azure"
   description="Aprenda sobre las funciones de supervisión de la puerta de enlace de aplicaciones de Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones


La puerta de enlace de aplicaciones de Azure supervisa de forma predeterminada el estado de todos los recursos de su grupo de back-end y elimina automáticamente del grupo los recursos que se considera que están en mal estado. Además, continúa supervisando las instancias en mal estado y las agrega de nuevo al grupo de back-end en buen estado, una vez que están disponibles y responden a los sondeos de estado.

Aparte del uso de la supervisión del sondeo de estado, también puede personalizar el sondeo de estado para adaptarlo a las necesidades de su aplicación. En este artículo trataremos ambos sondeos de estado: el personalizado y el predeterminado.

## Sondeo de estado predeterminado

Una puerta de enlace de aplicaciones configura automáticamente un sondeo de estado predeterminado cuando no hay ningún sondeo personalizado configurado. El comportamiento de supervisión consiste en realizar una solicitud HTTP a las direcciones IP configuradas para el grupo de back-end.

Por ejemplo: configura la puerta de enlace de aplicaciones para usar los servidores back-end A, B y C para recibir el tráfico de red HTTP en el puerto 80. La supervisión de estado predeterminada comprueba los tres servidores cada 30 segundos para ver que la respuesta de HTTP es correcta. Una respuesta HTTP correcta tiene un [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 y 399.

Si la comprobación de sondeo predeterminado da error en el servidor A, la puerta de enlace de aplicaciones lo elimina de su grupo de back-end y el tráfico de red deja de fluir a este servidor. El sondeo predeterminado sigue comprobando el servidor A cada 30 segundos. Cuando el servidor A responde correctamente a una solicitud de un sondeo de estado predeterminado, se considera que está en buen estado y se agrega de nuevo al grupo de back-end; el tráfico comienza a fluir de nuevo a él.

El sondeo predeterminado solo examina http://127.0.0.1:<port> para determinar el estado de mantenimiento. Si necesita configurar el sondeo de estado para ir a una dirección URL personalizada o modificar alguna otra configuración, debe usar sondeos personalizada tal como se describe a continuación.

### Configuración de sondeo de estado predeterminado

|Propiedad de sondeo | Valor | Descripción|
|---|---|---|
| Dirección URL de sondeo| http://127.0.0.1/ | Ruta de acceso URL |
| Intervalo | 30 | Intervalo de sondeo en segundos |
| Tiempo de espera | 30 | Tiempo de espera del sondeo en segundos |
| Umbral incorrecto | 3 | Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |


## Sondeo de estado personalizado

Los sondeos personalizados permiten un control más específico sobre la supervisión de estado. Cuando se usan sondeos personalizados, puede configurar el intervalo de sondeo, la dirección URL y la ruta de acceso a la comprobación y cuántas respuestas erróneas se aceptan antes de marcar la instancia del grupo de back-end como en mal estado.


### Configuración de sondeo de estado personalizado

|Propiedad de sondeo| Descripción|
|---|---|
| Nombre | Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP de back-end. |
| Protocolo | Protocolo usado para enviar el sondeo. HTTP es el único protocolo válido. |
| Host | Nombre de host para enviar el sondeo. |
| Ruta de acceso | Ruta de acceso relativa del sondeo. La ruta de acceso válida se inicia desde '/'. El sondeo se envía a <protocol>://<host>:<port><path> |
| Intervalo | Intervalo de sondeo en segundos. Es el intervalo de tiempo entre dos sondeos consecutivos.|
| Tiempo de espera | Tiempo de espera del sondeo en segundos. El sondeo se marca como error si no se recibe una respuesta válida dentro de este período de tiempo de espera. |
| Umbral incorrecto | Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

## Pasos siguientes

Como ya ha aprendido sobre la supervisión de estado de la puerta de enlace de aplicaciones, puede configurar un [sondeo de estado personalizado](application-gateway-create-probe-ps.md) para el Administrador de recursos de Azure o un [sondeo de estado personalizado](application-gateway-create-probe-classic-ps.md) para el modelo de implementación clásica de Azure.

<!---HONumber=AcomDC_0114_2016-->