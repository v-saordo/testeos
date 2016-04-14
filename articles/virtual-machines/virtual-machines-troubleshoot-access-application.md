<properties
	pageTitle="Solución de problemas de acceso de aplicaciones de una máquina virtual | Microsoft Azure"
	description="Si no puede acceder a una aplicación que se ejecuta en una máquina virtual de Azure, siga estos pasos para aislar la causa del problema."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="dkshir"/>

# Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Si no tiene acceso a una aplicación que se ejecuta en una máquina virtual de Azure, en este artículo se describe un enfoque metódico para aislar la causa del problema y corregirlo.

> [AZURE.NOTE]  Para obtener ayuda para la conexión a una máquina virtual de Azure, consulte [Solución de problemas de conexiones de escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md) o [Solución de problemas de conexiones Secure Shell (SSH) a una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md).

Hay cuatro áreas principales para solucionar el acceso de una aplicación que se ejecuta en una máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	La aplicación que se ejecuta en la máquina virtual de Azure.
2.	La máquina virtual de Azure.
3.	Extremos de Azure para el servicio en la nube que contiene la máquina virtual (para las máquinas virtuales creadas con la API de administración de servicios), reglas NAT entrantes (para las máquinas virtuales creadas en el Administrador de recursos) y grupos de seguridad de red.
4.	El dispositivo perimetral de Internet.

Para los equipos cliente que tienen acceso a la aplicación a través de una conexión ExpressRoute o de VPN de sitio a sitio, las áreas principales que pueden causar problemas son la aplicación y la máquina virtual de Azure. Para determinar el origen del problema y su corrección, siga estos pasos.

## Paso 1: ¿Puede acceder a la aplicación desde la máquina virtual de destino?

Intente acceder a la aplicación con el programa de cliente apropiado de la máquina virtual en la que se ejecuta la aplicación, use el nombre de host local, la dirección IP local o la dirección de bucle invertido (127.0.0.1).

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

Por ejemplo, si la aplicación es un servidor web, ejecute un explorador en la máquina virtual e intente obtener acceso a una página web hospedada en la máquina virtual.

Si puede tener acceso a la aplicación, vaya al [paso 2](#step2).

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- La aplicación se ejecuta en la máquina virtual de destino.
- La aplicación está escuchando en los puertos TCP y UDP esperados.

En máquinas virtuales basadas en Windows y Linux, use el comando **netstat -a** para mostrar los puertos de escucha activos. Examine la salida para los puertos esperados en el que debe escuchar su aplicación. Reinicie la aplicación o configúrela para usar los puertos esperados según sea necesario.

## <a id="step2"></a>Paso 2: ¿Tiene acceso a la aplicación desde otra máquina virtual en la misma red virtual?

Intente acceder a la aplicación desde una máquina virtual diferente en la misma red virtual que la de la máquina virtual en el que se ejecuta la aplicación con el nombre de host de la máquina virtual o su dirección IP de proveedor, privada o pública asignada por Azure. Para las máquinas virtuales creadas con la API de administración de servicios, no use la dirección IP pública del servicio en la nube.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

Por ejemplo, si la aplicación es un servidor web, intente tener acceso a una página web desde un explorador en una máquina virtual diferente de la misma red virtual.

Si puede tener acceso a la aplicación, vaya al [paso 3](#step3).

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- El firewall del host de la máquina virtual de destino permite el tráfico de la solicitud entrante y el tráfico de respuesta saliente.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de destino permite el tráfico.
- Los grupos de seguridad de red permiten el tráfico.
- Un componente independiente que se ejecuta en la red virtual en la ruta de acceso entre la máquina virtual de prueba y la máquina virtual, como un equilibrador de carga o firewall, está permitiendo el tráfico.

En una máquina virtual basada en Windows, use el Firewall de Windows con seguridad avanzada para determinar si las reglas de firewall excluyen el tráfico entrante y saliente de la aplicación.

## <a id="step3"></a>Paso 3: ¿Tiene acceso a la aplicación desde un equipo que está fuera de la red virtual, pero no conectado a la misma red que el equipo?

Intente tener acceso a la aplicación desde un equipo situado fuera de la red virtual como la máquina virtual en la que se está ejecutando la aplicación, pero que no esté en la misma red que el equipo cliente original.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

Por ejemplo, si la aplicación es un servidor web, intente tener acceso a la página web desde un explorador de un equipo que no esté en la red virtual.

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- En las máquinas virtuales creadas con la API de administración de servicios, en las que la configuración del extremo de la máquina virtual permita el tráfico entrante, especialmente el protocolo (TCP o UDP) y los números de puerto público y privado. Para obtener más información, consulte [Cómo establecer extremos en una máquina virtual](virtual-machines-set-up-endpoints.md).
- Para las máquinas virtuales creadas con la API de administración de servicios que tienen acceso a las listas de control (ACL) del extremo no impidan el tráfico entrante desde Internet. Para obtener más información, consulte [Cómo establecer extremos en una máquina virtual](virtual-machines-set-up-endpoints.md).
- En las máquinas virtuales creadas en la Administrador de recursos, que la configuración de la regla NAT entrante de la máquina virtual esté permitiendo el tráfico entrante, especialmente el protocolo (TCP o UDP) y los números de puerto público y privado.
- Que los grupos de seguridad de red permitan la solicitud entrante y el tráfico de respuesta saliente. Para obtener más información, vea [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)

Si la máquina virtual o el extremo es un miembro de un conjunto con equilibrio de carga:

- Compruebe que el protocolo de sondeo (TCP o UDP) y el número de puerto son correctos.
- Si el protocolo de sondeo y el puerto es diferente del protocolo y puerto del conjunto con equilibrio de carga:
	- Compruebe que la aplicación está escuchando el protocolo de sondeo (TCP o UDP) y el número de puerto (use **netstat –a** en la máquina virtual de destino).
	- El firewall del host de la máquina virtual de destino permite la solicitud de sondeo entrante y el tráfico de respuesta de sondeo saliente.

Si puede tener acceso a la aplicación, asegúrese de que el dispositivo perimetral de Internet permita:

- El tráfico de solicitud saliente de aplicaciones desde el equipo cliente hasta la máquina virtual de Azure.
- El tráfico de respuesta de aplicación entrante desde la máquina virtual de Azure.

## Solucionar problemas de conectividad de punto de conexión

Si tiene problemas al conectar a un punto de conexión como puede ser el del escritorio remoto, puede intentar los siguientes pasos para solucionar varios problemas generales:

- Reiniciar la máquina virtual
- Volver a crear el punto de conexión
- Conectarse desde otra ubicación
- Cambiar el tamaño de la máquina virtual
- Volver a crear la máquina virtual

Para obtener más información, consulte [Solución de problemas con la conectividad del punto de conexión (RDP, SSH, HTTP u otros errores)](https://social.msdn.microsoft.com/Forums/azure/es-ES/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## Pasos siguientes

Si ha efectuado los pasos anteriores que se indican en este artículo y necesita ayuda adicional para corregir el problema, puede:

- Obtener ayuda de expertos de Azure de todo el mundo. Enviar su problema a los foros de MSDN Azure o de desbordamiento de pila. Para obtener más información, consulte [Foros de Microsoft Azure](https://azure.microsoft.com/support/forums/).
- Registrar un incidente de soporte técnico de Azure. Vaya al [Sitio del soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico** en **Soporte técnico y facturación**.

## Recursos adicionales

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows ](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=AcomDC_0204_2016-->