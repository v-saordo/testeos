<properties
   pageTitle="Requisitos del sistema de la matriz virtual de StorSimple"
   description="Obtenga más información sobre los requisitos de software y red de la matriz virtual de StorSimple"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Requisitos del sistema de la matriz virtual de StorSimple

## Información general

Este artículo le ofrece información importante acerca los requisitos de sistema de la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o como dispositivo virtual de StorSimple) y de los clientes de almacenamiento que obtengan acceso a la matriz. Es recomendable que revise cuidadosamente la siguiente información antes de implementar el sistema StorSimple y que luego la consulte según sea necesario durante la implementación y el funcionamiento posterior.

Los requisitos del sistema incluyen:

-   **Requisitos de software para clientes de almacenamiento**: describe las plataformas de virtualización compatibles, los exploradores web, los iniciadores iSCSI, los clientes SMB, los requisitos mínimos del dispositivo virtual y cualquier otro requisito adicional de esos sistemas operativos.

-   **Requisitos de red para el dispositivo StorSimple**: proporciona información acerca de los puertos que deben estar abiertos en el firewall para permitir el tráfico iSCSI, de nube o de administración.

La información acerca de los requisitos de sistema de StorSimple publicada en este artículo solo se aplica a las matrices virtuales de StorSimple.

- En cuanto a los dispositivos de la serie 8000, consulte [Requisitos de sistema de los dispositivos de la serie 8000 de StorSimple](storsimple-system-requirements.md).
 
- Para los dispositivos de la serie 7000, consulte [Requisitos de sistema de los dispositivos de la serie 5000-7000 de StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## Requisitos de software

Los requisitos de software incluyen información sobre los exploradores web compatibles, las versiones SMB, las plataformas de virtualización y los requisitos mínimos de los dispositivos virtuales.

### Plataformas de virtualización admitidas


| **Hipervisor** | **Versión** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 y posterior |
| VMware ESXi | Versión 5.5 y versiones posteriores. |

### Requisitos de los dispositivos virtuales

| **Componente** | **Requisito** |
|----------------------------------------------|----------------------------|
| Número mínimo de procesadores virtuales (núcleos) | 4 |
| Cantidad mínima de memoria (RAM) | 8 GB |
| Espacio en disco<sup>1</sup> | Disco de sistema operativo: 80 GB <br></br> Disco de datos: de 500 GB a 8 TB|
| Número mínimo de interfaces de red | 1 |
| Ancho de banda mínimo de Internet<sup>2</sup> | 5 Mbps |

<sup>1</sup>: con aprovisionamiento fino

<sup>2</sup>: los requisitos de red pueden variar según la tasa de cambio diaria de datos. Por ejemplo, si un dispositivo necesita realizar copias de seguridad de 10 GB o más de los cambios realizados durante un día, la copia de seguridad diaria que se lleva a cabo a través de una conexión a 5 Mbps se puede demorar hasta 4,25 horas (eso si no se pudieran comprimir o desduplicar los datos).

### Exploradores web compatibles

| **Componente** | **Versión** | **Requisitos/notas adicionales** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | La versión más reciente | |
| Internet Explorer | La versión más reciente | Probado con Internet Explorer 11 |
| Google Chrome | La versión más reciente | Probado con Chrome 46 |

### Versiones de SMB compatibles

| **Versión** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## Requisitos de red 

La siguiente tabla enumera los puertos que deben abrirse en el firewall para permitir el tráfico de administración, de nube, de SMB o de iSCSI. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, saliente a Internet.

| **Nº de puerto<sup>1</sup>** | **Dentro o fuera** | **Ámbito de puerto** | **Obligatorio** | **Notas** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | Fuera | WAN | No | El puerto de salida se usa para obtener acceso a Internet para así recuperar las actualizaciones. <br></br>El usuario puede configurar el proxy web de salida. |
| TCP 443 (HTTPS) | Fuera | WAN | Sí | El puerto de salida se usa para tener acceso a los datos en la nube. <br></br>El usuario puede configurar el proxy web de salida. |
| UDP 53 (DNS) | Fuera | WAN | En algunos casos; consulte las notas. | Este puerto es necesario solo si está utilizando un servidor DNS basado en Internet. <br></br> **Nota**: Si implementa un servidor de archivos, le recomendamos que use el servidor DNS local.|
| UDP 123 (NTP) | Fuera | WAN | En algunos casos; consulte las notas. | Este puerto solo es necesario si está utilizando un servidor DNS basado en Internet.<br></br> **Nota:** Si implementa un servidor de archivos, le recomendamos que sincronice la hora con los controladores de dominio de Active Directory. |
|TCP 9354 | Fuera | WAN | Sí | El dispositivo StorSimple usa el puerto de salida para comunicarse con el servicio StorSimple Manager.|
| TCP 80 (HTTP) | En el | LAN | Sí | Este es el puerto de entrada de la interfaz de usuario local para el dispositivo StorSimple de la administración local. <br></br> **Nota**: Si obtiene acceso a la interfaz de usuario local mediante HTTP, se le redireccionará automáticamente a HTTPS.|
| TCP 443 (HTTPS) | En el | LAN | Sí | Este es el puerto de entrada de la interfaz de usuario local para el dispositivo StorSimple de la administración local.|
| TCP 3260 (iSCSI) | En el | LAN | No | Este puerto se utiliza para tener acceso a datos a través de iSCSI.|

<sup>1</sup> Ningún puerto de entrada debe estar abierto en la red Internet pública.

## Paso siguiente

-   [Prepare el portal para implementar la matriz virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0302_2016-->