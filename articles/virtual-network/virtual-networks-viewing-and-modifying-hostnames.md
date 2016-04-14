<properties 
   pageTitle="Ver y modificar los nombres de host | Microsoft Azure"
   description="Cómo ver y cambiar los nombres de host para máquinas virtuales de Azure, roles de trabajo y web para la resolución de nombres"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Ver y modificar los nombres de host

Para permitir que el nombre de host haga referencia a las instancias de rol, debe establecer el valor del nombre de host en el archivo de configuración de servicio de cada rol. Para hacer esto, agregue el nombre de host que quiera al atributo **vmName** del elemento **Rol**. El valor del atributo **vmName** se usa como base para el nombre de host de cada instancia de rol. Por ejemplo, si el atributo **vmName** es *webrole* y hay tres instancias de ese rol, los nombres de host de las instancias serán *webrole0*, *webrole1* y *webrole2*. No es necesario especificar un nombre de host para máquinas virtuales en el archivo de configuración, porque el nombre de host de una máquina virtual se rellena según el nombre de esa máquina virtual. Para obtener más información acerca de cómo configurar un servicio de Microsoft Azure, consulte [Esquema de configuración del servicio de Azure (archivo de .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## Ver nombres de host

Puede ver los nombres de host de máquinas virtuales e instancias de rol en un servicio en la nube mediante varias herramientas: el Portal de Azure, el archivo de configuración de servicios, el Escritorio remoto, y la [API de REST de Administración de servicios de Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Portal de Azure

Puede usar el Portal de Azure para ver los nombres de host de las máquinas virtuales en la página del panel de máquinas virtuales. Tenga en cuenta que el panel muestra un valor referente al **Nombre** y al **Nombre de host**. Aunque inicialmente son el mismo valor, si decide cambiar el nombre de host no cambiará el nombre de la máquina virtual o instancia de rol.

Además, también puede encontrar las instancias de rol en el Portal de Azure, pero al enumerar las instancias de un servicio en la nube, el nombre de host no se mostrará. Verá el nombre de cada instancia, pero recuerde que ese nombre no representa al nombre de host.

### Archivo de configuración de servicio

Puede descargar en el Portal de Azure el archivo de configuración de servicio de un servicio implementado desde la página **Configurar** del servicio. A continuación, puede buscar el atributo **vmName** del elemento **Nombre de rol** para ver el nombre de host. Tenga en cuenta que ese nombre de host se usa como base para cada nombre de host de cada instancia de rol. Por ejemplo, si el atributo **vmName** es *webrole* y hay tres instancias de ese rol, los nombres de host de las instancias serán *webrole0*, *webrole1* y *webrole2*.

### Escritorio remoto

Una vez haya habilitado el Escritorio remoto (Windows), la comunicación remota de Windows PowerShell (Windows) o las conexiones SSH (Linux y Windows) en las máquinas virtuales o instancias de rol, podrá ver el nombre de host de una conexión del Escritorio remoto activa de varias maneras:

- Escriba el nombre de host en el símbolo del sistema o en un terminal SSH.

- Escriba ipconfig/all en el símbolo del sistema (sólo Windows).

- Consulte el nombre del equipo en la configuración del sistema (sólo Windows).

### API de REST de Administración de servicios de Azure

Desde un cliente REST, siga estas instrucciones:

1. Asegúrese de que tiene un certificado de cliente para conectarse al Portal de Azure. Para obtener un certificado de cliente, siga los pasos que se indican en [Descarga e importación de la configuración de publicación y la información de suscripción](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Establezca una entrada de encabezado denominada x-ms-version con un valor de 2013-11-01.

1. Envíe una solicitud con el siguiente formato: https://management.core.windows.net/\<subscrition-id>/services/hostedservices/<service-name>?embed-detail=true

1. Busque el elemento **HostName** de cada elemento **RoleInstance**.

>[AZURE.WARNING] Asimismo, puede ver el sufijo de dominio interno del servicio en la nube desde la respuesta a la llamada REST comprobando el elemento **InternalDnsSuffix** o ejecutando ipconfig /all desde un símbolo del sistema en una sesión de Escritorio remoto (Windows) o cat /etc/resolv.conf desde un terminal SSH (Linux).

## Modificar un nombre de host

Puede modificar el nombre de host de cualquier máquina virtual o instancia de rol al cargar un archivo de configuración de servicio modificado o al cambiar el nombre del equipo desde una sesión de Escritorio remoto.

## Pasos siguientes

[resolución de nombres DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuración del servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuración de la red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especificación de la configuración de DNS usando los archivos de configuración de red](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

<!---HONumber=AcomDC_0204_2016-->