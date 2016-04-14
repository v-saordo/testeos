<properties
   pageTitle="Integración de OneDrive para la Empresa y RemoteApp de Azure | Microsoft Azure"
   description="Aprenda a usar OneDrive para la Empresa con RemoteApp de Azure."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# Integración de OneDrive para la Empresa y RemoteApp de Azure

Puede usar OneDrive para la Empresa como repositorio de archivos con RemoteApp de Azure. OneDrive para la Empresa es una excelente manera de mantener los archivos sincronizados en todos los dispositivos y áreas de trabajo. El [disco de perfil de usuario](remoteapp-upd.md) (UPD) es un lugar donde los usuarios pueden almacenar sus archivos para las aplicaciones de RemoteApp de Azure, pero a estos archivos solo se puede acceder mediante RemoteApp de Azure. OneDrive para la Empresa, por otro lado, permite al usuario acceder a los archivos donde y cuando desee sin el requisito previo de pasar a través de RemoteApp de Azure. En este artículo se describen las versiones de OneDrive para la Empresa compatibles, así como las distintas formas en que los administradores pueden configurar OneDrive para la Empresa para RemoteApp de Azure.

## ¿Se admiten todas las versiones de OneDrive?

Hay dos versiones de OneDrive: OneDrive y OneDrive para la Empresa. Solo OneDrive para la Empresa es compatible con RemoteApp de Azure. Funciona el OneDrive personal pero no se admite oficialmente. Además, solo se admite la versión más reciente de OneDrive para la Empresa, también conocida como cliente de sincronización de próxima generación, en RemoteApp de Azure (y de servidores RDSH, Citrix y Terminal Server).

>[AZURE.NOTE]No se admite OneDrive (edición de consumidores o personal) en RemoteApp de Azure. No se admiten todas las versiones de OneDrive para la Empresa, porque aún no se han certificado para que funcionen en Windows Server. Aunque el nuevo cliente (cliente de sincronización de siguiente generación) y las versiones anteriores de Groove parece funcionar bien en RemoteApp de Azure, como se describe en [https://support.microsoft.com/es-es/kb/2965687](https://support.microsoft.com/kb/2965687), los motores de sincronización anteriores no tendrán una funcionalidad completa en Citrix y servidores de Terminal Server (Windows Server). Use el nuevo cliente de sincronización en RemoteApp de Azure (y otras implementaciones de Windows Server).

## ¿Cuáles son las diferentes opciones de instalación para OneDrive para la Empresa?

- **Instalación tradicional del motor de sincronización de OneDrive para la Empresa:** el cliente de sincronización de OneDrive para la Empresa puede instalarse en una SKU del servidor (Escritorio remoto así como sesión de RemoteApp y sesión de Terminal Server) y en carpetas seleccionadas para la sincronización en la sesión de RemoteApp, al igual que en una SKU de cliente de Windows. La ubicación predeterminada de los archivos de sincronización de OneDrive para la Empresa es la misma ubicación donde reside el disco de perfil de usuario usado para almacenar los datos de usuario y la configuración en RemoteApp de Azure, en C:\users <nombreDeUsuario>. Este disco seguirá al usuario a todas las máquinas virtuales en las que inicien sesión y, por tanto, los archivos de OneDrive para la Empresa también seguirán al usuario. La aplicación OneDrive para la Empresa debe ser publicada por el administrador a todos los usuarios y estos deben iniciarla en cada nueva sesión (o se puede automatizar el inicio con un script de inicio de sesión) para asegurarse de que el motor de sincronización está activado. OneDrive para la Empresa descargará el archivo completo en la máquina virtual donde se está ejecutando la sesión. Representa una gran carga de trabajo sincronizar todo (CPU/datos transferidos/almacenamiento ocupado) sincronizar el contenido del usuario, por lo que no está optimizado para máquinas terminales con un gran número de usuarios que inician sesión brevemente en cada máquina. Con la sincronización selectiva, se reducirá la carga de trabajo aunque el problema persiste.
- **"Virtualice" OneDrive para la Empresa o redireccionarlo desde el cliente grueso local en la sesión:** si está sincronizando OneDrive en una carpeta en el dispositivo cliente, en una unidad, puede elegir [redirigir](remoteapp-redirection.md) dicha unidad a RemoteApp de Azure. Esa unidad debe ser la misma en todos los clientes de los usuarios que deben disponer de OneDrive sincronizado con una carpeta en esa unidad. Si acceden a RemoteApp desde cualquier otro cliente, es posible que estos archivos no estén disponibles (solución: siempre pueden acceder a los archivos mediante la versión en línea de OneDrive). 
- **Ofrezca OneDrive para la Empresa como una unidad dentro del entorno de RemoteApp de Azure que no precisa de almacenamiento en caché ni sincronización de archivos:** (asigne la dirección URL http de OneDrive para la Empresa a una unidad de la máquina virtual) se admite la asignación de OneDrive para la Empresa en la unidad de red dentro del entorno RDSH. Situaciones en las que se puede utilizar: 
	- Cuando se usan clientes ligeros (sin almacenamiento local) para acceder a RemoteApp de Azure y la aplicación requiere los archivos almacenados en OneDrive para la Empresa, pero quieren "buscar" en local y el administrador no quiere sincronizar los archivos con una máquina virtual.
	- Cuando hay muchos archivos grandes en OneDrive para la Empresa seleccionados para la sincronización. Dada la carga de trabajo de sincronización, es posible que todos los archivos no se sincronicen cuando el usuario desee usarlos. Además, si el tamaño total de los archivos es superior a 50 GB, no se almacenarán en el disco de perfil de usuario.

En las situaciones anteriores, los administradores pueden optar por utilizar las opciones de asignación en una unidad en la máquina virtual a la dirección URL http de OneDrive para la Empresa. Estas son algunas opciones para habilitar esta opción:

- Tener archivos binarios de Office en la imagen y no activar el motor de sincronización de OneDrive para la Empresa.
- NO tener archivos binarios de Office en la imagen. En este caso hay un requisito previo: el paquete Experiencia de escritorio. En concreto, el servicio WebClient (también conocido como WebDAV) debe instalarse como parte del paquete Experiencia de escritorio. 

### Instalación del paquete Experiencia de escritorio en Windows Server 2012 R2
Para instalar el paquete Experiencia de escritorio:

1. En Administrador del servidor, haga clic en **Administrar -> Agregar roles y características**.
2. Haga clic en **Características** y después en **Infraestructura e interfaces de usuario -> Experiencia de escritorio**.

### Asignación de una unidad a la dirección URL de OneDrive para la Empresa

Siga las instrucciones del artículo de soporte técnico: [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712).
 
Un paso importante en el programa de instalación es asegurarse de que selecciona **Mantener la sesión iniciada**.

En un nivel superior, aquí encontrará las instrucciones:

1.	Busque la dirección URL de la unidad. La dirección URL usada para la asignación de unidad es la que obtiene cuando va a su directorio particular en OneDrive para la Empresa en línea. Por ejemplo:
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	Abra la dirección URL mediante un explorador en la sesión de RemoteApp y seleccione **Mantener la sesión iniciada** antes de iniciar sesión en la dirección URL de su cuenta.
3.	Abra Explorador de Windows y asigne una unidad a la dirección URL anterior. Si así no resuelve la dirección URL, puede usar la forma corta:
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

	Se crea inmediatamente la unidad asignada y su aspecto es similar al siguiente:
 
	![OneDrive para la Empresa como una unidad de red asignada](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->