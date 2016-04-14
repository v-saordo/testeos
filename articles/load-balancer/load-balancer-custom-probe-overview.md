<properties 
   pageTitle="Sondeos personalizados del equilibrador de carga y supervisión de estado de mantenimiento | Microsoft Azure"
   description="Aprenda a usar sondeos personalizados para el Equilibrador de carga de Azure a fin de supervisar las instancias detrás de un equilibrador de carga."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Sondeos del equilibrador de carga 

El Equilibrador de carga de Azure ofrece la capacidad de supervisar el estado de las instancias del servidor mediante sondeos. Cuando un sondeo no responde, el Equilibrador de carga de Azure deja de enviar nuevas conexiones a las instancias en mal estado. Las conexiones existentes no resultan afectadas y las nuevas conexiones se envían a instancias en buen estado.

Deben configurarse sondeos TCP o HTTP personalizados al usar máquinas virtuales detrás de un equilibrador de carga. Los roles del servicio en la nube (los roles de trabajo y los roles web) son las únicas instancias de servidor con supervisión del sondeo del agente invitado.
 
## Descripción del número y el tiempo de espera de los sondeos

El comportamiento de los sondeos depende del número de sondeos completados correctamente o con error requeridos para marcar una instancia como activa o inactiva. Esto se calcula mediante la ecuación SuccessFailCount = Timeout (Tiempo de espera) / Frequency (Frecuencia). Para el portal, el tiempo de espera se establece en dos veces el valor de la frecuencia (tiempo de espera = frecuencia * 2).

La configuración de los sondeos de todas las instancias con carga equilibrada para un punto de conexión (conjunto de carga equilibrada) debe ser igual. Esto significa que no puede tener una configuración de sondeo distinta (es decir, puerto local, tiempo de espera, etc.) para cada instancia de rol o máquina virtual en el mismo servicio hospedado para una combinación determinada de puntos de conexión.


>[AZURE.IMPORTANT] Un sondeo del equilibrador de carga utiliza una dirección IP 168.63.129.16. Esta dirección IP pública se utiliza para facilitar un canal de comunicación a los recursos internos de plataforma para el escenario de Red virtual que permite especificar su propia IP. La dirección IP pública virtual 168.63.129.16 se utiliza en todas las regiones y no cambiará. Se recomienda para que la dirección IP se permita en las directivas de firewall locales. No se debe considerar un riesgo de seguridad, ya que la plataforma interna de Azure es la única que puede originar un mensaje procedente de esa dirección. Si no lo hace así, se producirá un comportamiento inesperado en varios escenarios, como la configuración del mismo intervalo de direcciones IP de 168.63.129.16 y el hecho de contar con direcciones IP duplicadas.


## Tipos de sondeos

### Sondeo de agente invitado

Solo para servicios en la nube. El Equilibrador de carga de Azure usa al agente invitado que hay dentro de la máquina virtual, escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está en estado preparado (es decir, no está en los estados ocupado, reciclando, deteniendo, etc.).

Consulte [configuring the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/jj151530.asp) (Configurar el archivo de definición de servicio (csdef) para las comprobaciones de mantenimiento) o [Introducción a la creación de un equilibrador de carga orientado a Internet para servicios en la nube](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services) para obtener más información.
 
### ¿Qué haría que un sondeo de agente invitado marcara una instancia como en mal estado?

Si el agente invitado no responde con HTTP 200 OK, el Equilibrador de carga de Azure marca la instancia como sin respuesta y deja de enviar tráfico a esa instancia. El Equilibrador de carga de Azure continúa haciendo ping a la instancia y, si el agente invitado responde con un HTTP 200, envía de nuevo el tráfico a esa instancia.

Al usar un rol web, el código de su sitio web se ejecuta normalmente en w3wp.exe. El agente invitado o el tejido de Azure no supervisan este código, lo que significa que los errores en w3wp.exe (por ejemplo, respuestas HTTP 500) no se notificarán al agente invitado y que el equilibrador de carga no sabrá excluir esa instancia de la rotación.


### Sondeo HTTP personalizado

El sondeo HTTP personalizado del equilibrador de carga invalida el sondeo del agente invitado predeterminado y le permite crear su propia lógica personalizada para determinar el estado de la instancia de rol. El equilibrador de carga sondea periódicamente el punto de conexión (de forma predeterminada cada 15 segundos) y la instancia se considera en la rotación del equilibrador de carga si responde con HTTP 200 dentro del período de tiempo de espera (31 segundos de forma predeterminada). Esto puede resultar útil para implementar su propia lógica con el fin de quitar instancias de la rotación del equilibrador de carga. Por ejemplo, si la CPU de la instancia está por encima del 90 %, se devolvería un estado diferente de 200. En el caso de los roles web que usan w3wp.exe, esto también significa que dispone de supervisión automática de su sitio web puesto que los errores en el código de su sitio web devolverán un estado diferente de 200 al sondeo del equilibrador de carga.

>[AZURE.NOTE] El sondeo personalizado HTTP admite solo rutas de acceso relativas y protocolo HTTP. HTTPS no es compatible.


### ¿Qué haría que un sondeo HTTP personalizado marcara una instancia como en mal estado? 

- La aplicación HTTP devuelve un código HTTP de respuesta distinto de 200 (es decir, 403, 404, 500, etc.). Se trata de una confirmación positiva de que la instancia de la aplicación desea desconectarse del servicio inmediatamente.

-  En el evento, el servidor HTTP no responde en absoluto después del período de tiempo de espera. Tenga en cuenta que, según el conjunto de valores de tiempo de espera, esto puede significar que varias solicitudes de sondeo deben quedarse sin respuesta antes de marcar el sondeo como inactivo (es decir, se envían los sondeos SuccessFailCount).
- 	Cuando el servidor cierra la conexión a través de un restablecimiento TCP.

### Sondeo TCP personalizado

Los sondeos TCP están iniciando una conexión mediante la realización de un enlace de tres vías al puerto definido.

### ¿Qué haría que un sondeo TCP personalizado marcara una instancia como en mal estado?

- En el evento, el servidor TCP no responde en absoluto después del período de tiempo de espera. Dependerá del número de solicitudes de sondeo con error que es necesario que se queden sin respuesta según la configuración antes de marcar el sondeo como inactivo.
- 	Recibe un restablecimiento TCP de la instancia de rol.

Consulte [Introducción a la creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer) para aprender a configurar un sondeo de estado HTTP o un sondeo TCP.

## Incorporación de instancias en buen estado de nuevo en el equilibrador de carga

Los sondeos TCP y HTTP se consideran en buen estado y marcan la instancia de rol como en buen estado en los casos siguientes:

- La primera vez que se inicia la máquina virtual y el Equilibrador de carga obtiene un sondeo positivo.
- El número SuccessFailCount (véase más arriba) define el valor de sondeos correctos necesarios para marcar la instancia de rol como en buen estado. Si se quitó una instancia de rol, SuccessFailCount en los sondeos correctos de una fila debe marcar la instancia de rol como UP.

>[AZURE.NOTE] Si el estado de una instancia de rol fluctúa, el Equilibrador de carga de Azure espera más tiempo antes de devolver la instancia de rol al estado correcto. Esto se hace mediante la directiva para proteger al usuario y a la infraestructura.

## Análisis de registros para el equilibrador de carga

Puede usar [el análisis de registros para el equilibrador de carga](load-balancer-monitor-log.md) para comprobar el estado de mantenimiento de un sondeo y el número de sondeos. El registro se puede utilizar con Power BI o con Operation Insights para proporcionar estadísticas del estado de mantenimiento del equilibrador de carga,
 

<!---HONumber=AcomDC_0302_2016-->