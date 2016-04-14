
<properties 
    pageTitle="Uso de la suscripción de Office 365 con Azure RemoteApp | Microsoft Azure"
	description="Aprenda a usar su suscripción de Office 365 en Azure RemoteApp para compartir aplicaciones de Office."
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/14/2015" 
    ms.author="elizapo" />



# Uso de la suscripción de Office 365 con Azure RemoteApp

¿Sabía que puede usar tu suscripción de Office 365 existente en Azure RemoteApp para compartir aplicaciones de Office desde la nube? Siga leyendo para obtener información sobre las opciones de Office 365 + Azure RemoteApp, incluidos vínculos a artículos sobre Office 365 que le ayudarán a sacar el máximo partido de su suscripción.

## ¿Cómo uso cuentas de Office 365 para Azure RemoteApp?
Consulte el nuevo artículo de Peter para toda la información: [Cómo usar Azure RemoteApp con cuentas de usuario de Office 365](remoteapp-o365user.md)

## ¿Puedo usar mi suscripción de Office 365 para ejecutar aplicaciones de Office en Azure RemoteApp?

Sí. De hecho, usar su suscripción de Office 365 es la única manera de traer sus aplicaciones de Office a Azure RemoteApp.

(Nota: si la implementación de Azure RemoteApp se realiza por un socio de hospedaje, es posible que este pueda proporcionarle licencias de Office basadas en un [contrato de licencia de proveedor de servicio](http://www.microsoft.com/es-ES/Licensing/licensing-programs/spla-program.aspx)).


Lo mejor de su suscripción de Office 365 es que permite usar la misma licencia de usuario en muchas plataformas y entorno diferentes, como la nube de Azure. Cuando usa las aplicaciones de Office en Azure RemoteApp no es necesario adquirir licencias adicionales ni configurar las licencias existentes de ninguna manera especial. Todo lo que necesita es una suscripción de Office 365 que incluya [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus permite la [activación en equipos compartidos](https://technet.microsoft.com/library/Dn782860.aspx); esta característica permite la activación temporal de usuarios en Office en entornos virtuales y en la nube, como Azure RemoteApp (y Servicios de Escritorio remoto).

¿Qué planes de Office 365 incluyen Office 365 ProPlus? Consulte la tabla de [disponibilidad de servicios dentro de cada plan](https://technet.microsoft.com/library/office-365-plan-options.aspx). Tenga en cuenta que no todos los planes incluyen Office 365 ProPlus (por ejemplo, el plan de negocios de Office 365). Si este es su caso, considere la posibilidad de actualizar a un plan que sí lo incluya (por ejemplo, Office 365 Education E3).

## De acuerdo, así que ¿cómo se usan mis licencias de Office 365 ProPlus con Azure RemoteApp?

Cada licencia de usuario de Office 365 ProPlus permite a un usuario activar las aplicaciones de Office hasta en cinco equipos, tabletas y teléfonos. Cada activación se registra con el usuario hasta que este desactiva Office en el dispositivo. (Los usuarios pueden administrar sus dispositivos en el [portal de Office 365](https://portal.office365.com/)).

Con Azure RemoteApp un único usuario podría iniciar sesión en varios equipos el mismo día sin darse cuenta. Esto se debe a que el servicio administra y escala automáticamente los recursos en la nube, mientras que el usuario solo ve las aplicaciones y los programas que se han compartido. En este escenario Office 365 ProPlus ofrece un modo de activación de equipo compartido; esto significa que el usuario no necesita realizar ninguna administración de licencias para tener acceso a esos recursos y que los equipos individuales no cuentan para el límite de activación de cinco equipos.

Mientras el administrador asigne licencias de Office 365 ProPlus a los usuarios, pueden usar Office en sus dispositivos personales, así como a través de la colección de Azure RemoteApp.

## ¿Qué aplicaciones de Office se pueden usar con Office 365 y Azure RemoteApp?

Puede usar la suscripción de Office 365 ProPlus para compartir Office 2013 y Office 2016 (después de que se publique). Azure RemoteApp no es compatible con versiones anteriores de Office.

## ¿Qué sucede con Visio Pro o Project Pro?

La imagen de Office 365 ProPlus incluida en su suscripción de RemoteApp incluye Visio Pro y Project Pro. Pero no puede usar su suscripción de Office 365 ProPlus para activar esos programas, cada uno tiene su propia licencia. Puede activarlos en el [portal de Office 365](https://portal.office365.com/).

No necesita activar la licencia de estos programas si no desea usarlos. Solo active los programas que vaya a usar y omita los demás. Aunque los seguirá viendo e la imagen, no podrá usarlos.

## ¿Cómo puedo comenzar con Office 365 y Azure RemoteApp?

Ahora que conoce los detalles de licencia de Office 365, vamos a comenzar a usarlo en Azure RemoteApp, es muy fácil:

Cuando cree la colección de Azure RemoteApp, use la imagen de **Office 365 ProPlus (suscripción necesaria)**.

![Imagen de Azure RemoteApp con Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Esta imagen contiene la versión más reciente de Windows Server y Office 365 ProPlus. Después de configurar la colección (incluidas las aplicaciones publicación), los usuarios simplemente inician sesión en Azure RemoteApp (mediante su cliente de RemoteApp) y proporcionan sus credenciales de Office 365 para las aplicaciones de Office. Las licencias se entregan automáticamente sin necesidad de ninguna configuración o administración.

## ¿Puedo crear una imagen personalizada con Office 365 ProPlus?

Puede crear una imagen personalizada de la colección que incluye Office 365 ProPlus. Hay 2 opciones: usar la imagen de la galería de Azure que se proporciona, o bien crear su propia imagen personalizada e instalar ahí Office 365 ProPlus.

### Uso de la imagen de la galería de Azure

La manera más fácil de implementar Office 365 ProPlus en una colección es [comenzar con una de las imágenes de la galería de Azure](remoteapp-image-on-azurevm.md) incluidas en su suscripción de Azure RemoteApp. Asegúrese de que elige la imagen **Host de sesión de Escritorio remoto de Windows Server con Office 365 ProPlus preinstalado**. A continuación, instale las demás aplicaciones que desee en esa imagen y ya está.

### Uso de una imagen personalizada

Siempre puede crear una imagen personalizada: puede crear una [máquina virtual de Azure](remoteapp-image-on-azurevm.md) o [crear la imagen localmente](remoteapp-create-custom-image.md) y cargarla en Azure. En cualquier caso, asegúrese de instalar Office 365 ProPlus mediante el nodo de activación en equipos compartidos. Use la [Herramienta de implementación de Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) y siga las [instrucciones](https://technet.microsoft.com/library/Dn782858.aspx) de instalación.

### Deshabilite las actualizaciones automáticas para Office 365 ProPlus en la imagen personalizada. IMPORTANTE:

La imagen personalizada se usa en Azure RemoteApp como una plantilla para agregar recursos adicionales a medida que aumenta la demanda de los usuarios. Para evitar retrasos y problemas de conexión, deshabilite la actualización automática de Office en la imagen. Si no lo hace, todos los recursos creados con dicha plantilla se actualizarán automáticamente cuando se inicie. En vez de ello, use el proceso estándar de Azure RemoteApp para actualizar la imagen personalizada. De este modo, actualiza las aplicaciones de Office una vez en la imagen de plantilla y, luego, deja que Azure RemoteApp se encargue de obtener las actualizaciones para los usuarios.

Para deshabilitar las actualizaciones automáticas, agregue lo siguiente al archivo de configuración de la Herramienta de implementación de Office:

		<Updates Enabled="FALSE" />

Ahora, el archivo de configuración debe contener estas líneas:
	
		<Display Level="NONE" AcceptEULA="TRUE" />
		<Property Name="SharedComputerLicensing" Value="1" />
		<Updates Enabled="FALSE" />

## Entonces, ¿cómo puedo actualizar una imagen con Office 365 ProPlus?

Hay muchas razones para actualizar la imagen en la colección. Estas son algunas:

- Obtener las actualizaciones de Windows más recientes 
- Obtener las actualizaciones de aplicaciones de Office 365 ProPlus más recientes
- Actualizar la aplicación personalizada
- Cambiar otras opciones de configuración en la propia imagen.

Para ver los pasos completos para actualizar la colección y así usar la imagen actualizada, vaya [aquí](remoteapp-update.md). Sin embargo, para obtener información sobre cómo actualizar la imagen y Office 365 ProPlus, consulte la siguiente información.

Tiene dos opciones para actualizar la imagen: reemplazar la imagen por una completamente nueva o actualizar manualmente la imagen existente.

### Sustitución de la imagen por la última imagen de la galería de Azure y adición de personalizaciones
Con esta opción, deja que Microsoft se ocupe de las actualizaciones de Windows Server y Office 365 ProPlus. En lugar de actualizar la imagen existente, creará una imagen completamente nueva basada en la última imagen de la galería. A continuación, repita los pasos que hizo antes de personalizar la imagen: instalar aplicaciones personalizadas, modificar la configuración de la imagen, etc.

Las imágenes de la galería se actualizan periódicamente, así que respire tranquilo ya que sabe que sus aplicaciones de Windows Server y Office 365 ProPlus están actualizadas. Por supuesto, el inconveniente es que tendrá que aplicar las personalizaciones cada vez que reciba una nueva imagen. Puede crear scripts para automatizar la configuración de las personalizaciones.

### Actualización manual de la imagen existente

Con esta opción, solo se usan las herramientas estándar de Windows para aplicar actualizaciones a la imagen. Para Office 365 ProPlus, use la Herramienta de implementación de Office para descargar e instalar las últimas actualizaciones o versiones de Office 365 ProPlus.

> [AZURE.IMPORTANT]No olvide deshabilitar las actualizaciones automáticas de Office 365 ProPlus.

¿Necesita más información sobre el uso de la Herramienta de implementación de Office para las actualizaciones?

- [Implementar Hacer clic y ejecutar para productos de Office 365 usando la Herramienta de implementación de Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Implementación y actualización de Office 365 ProPlus mediante la Herramienta de implementación de Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vídeo)
- [Configuración de las opciones de actualización de Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)

<!---HONumber=AcomDC_1217_2015-->