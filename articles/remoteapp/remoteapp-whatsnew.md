
<properties
    pageTitle="Novedades de Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre los cambios y las mejoras realizados en RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/06/2016"
    ms.author="elizapo" />



# Novedades en RemoteApp de Azure

Una de las ventajas de Azure RemoteApp es que siempre trabajamos para mejorarlo. Cada vez que lo hagamos, anunciaremos aquí esos cambios.

## Actualizaciones futuras
Hola, ¿sabía que el equipo de Azure RemoteApp publica actualizaciones mensuales en el blog RDS? No solo encontrará las novedades de Azure RemoteApp, sino también más información acerca de cómo utilizar RDS. Para obtener información, visite el blog [Remote Desktop Services Blog](https://blogs.msdn.microsoft.com/rds/). Por ejemplo, hace un par de semanas, publicaron una entrada sobre [elevación y desplazamiento de cargas de trabajo con Azure RemoteApp y Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## Septiembre de 2015
- Se agregó InfoPath a la imagen de galería y plantilla de Microsoft Office 365. Si desea compartir InfoPath, asegúrese de actualizar las colecciones con la imagen más reciente.
- Actualizaciones de clientes:
	- Se actualizó el cliente de Windows para permitir que los usuarios compartan comentarios, especialmente en torno a problemas de conexión.
	- Se actualizó el cliente de iOS para corregir errores de mensajería y para corregir un problema por el cual las credenciales expiraban antes de lo esperado.
- Estamos trabajando en probar la compatibilidad de Office 2016. Una vez completada, busque las imágenes actualizadas.
- Se publicó un artículo nuevo sobre las [diferencias entre las colecciones híbridas y de la nube](remoteapp-collections.md), lo que le permitirá elegir el tipo de colección más adecuado para sus aplicaciones: solo en la nube, en la nube y red virtual o híbrida.
- ¿Desea compartir QuickBooks con Azure RemoteApp, pero no está seguro de los pasos? Revise el [nuevo artículo de Eric](remoteapp-quickbooks.md), donde le indica exactamente lo que tiene que hacer.

## Agosto de 2015
En agosto hubo cambios importantes. A continuación, presentamos los aspectos destacados:

- Ahora puede usar una red virtual de Azure con una colección en la nube. Revise las [instrucciones para la creación de la nube](remoteapp-create-cloud-deployment.md) para ver los pasos nuevos.
- Se hizo posible agregar aplicaciones al menú **Inicio** para el cliente de Windows RemoteApp. Las aplicaciones aparecerán en la lista de aplicaciones y puede anclarlas en el menú **Inicio ** de Windows.
- Se agregó una imagen nueva a la galería de máquinas virtuales de Azure - Host de sesión de Escritorio remoto de Windows Server con Microsoft Office 365 ProPlus.
- Se corrigió el cliente de Mac para que las aplicaciones con ventanas modales dejarán de inmovilizarse.
- Se documentó la forma en que puede usar la [suscripción a Office 365 ProPlus](remoteapp-officesubscription.md) con Azure RemoteApp.
- Se detalló la forma en que puede [proteger las aplicaciones y los datos](remoteapp-secure.md) en la colección de Azure RemoteApp.

## Julio de 2015

Julio prepara el terreno para los cambios que llegan en agosto, así que por ahora no hay mucho de lo que hablar, se trata principalmente de actualizaciones de documentos. Estos son los cambios más recientes:

- Se agregó una pestaña **Soporte técnico** al portal para que pueda acceder más fácilmente a los recursos de soporte técnico, como los foros.
- Se modificó la información de solución de problemas para crear una colección híbrida. Consulte las sugerencias [más recientes y mejores](remoteapp-hybridtrouble.md) para la solución de problemas, por ejemplo, cómo identificar los puertos correctos que se deben configurar para la red virtual.
- Se documentó cómo se crean y se guardan los [datos de usuario](remoteapp-upd.md) en Azure RemoteApp.
- Se documentó cómo [bloquear las aplicaciones](remoteapp-secure.md).
- Se publicaron los [cmdlets de Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- Y, por último, iniciamos una conversación con algunos usuarios de Azure RemoteApp acerca de la terminología. Busque cambios en la forma en que nos referimos a las distintas opciones de colección.

## Junio de 2015

¡Hay muchos cambios! El equipo ha estado muy ocupado en junio:

- Se ha rediseñado la [página de aterrizaje](https://www.remoteapp.windowsazure.com/) de RemoteApp de Azure, compruébelo.
- Se ha actualizado el software en todas las imágenes disponibles como parte de la suscripción.
- Se han realizado mejoras en las colecciones híbridas, incluida la compatibilidad con la tunelización forzada y la comprobación del tamaño de la subred IP antes de intentar crear la colección.
- Se ha detectado que el carácter comodín * no funciona con las cámaras web. Es necesario especificar el identificador de instancia o el GUID. Actualizaremos la información de redirección para reflejar este hecho.
- Esto permite agregar software antivirus personalizado a la imagen al crear una imagen de plantilla desde la galería de Azure.

En julio habrá nuevos cambios, por lo que pronto publicaremos otra actualización.

## Mayo de 2015

Han tenido lugar varias adiciones y han pasado varios meses desde que creamos este tema, por lo que esta lista en realidad corresponde al período entre principios de marzo y mayo. Revise las nuevas características:

- Automatización completa: ahora RemoteApp de Azure incluye [cmdlets en el módulo de Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
- [Creación de una imagen de RemoteApp de Azure basada en una máquina virtual de Azure](remoteapp-image-on-azurevm.md). Consigue que la carga de la imagen personalizada en Azure se realice mucho más rápido.
- Uso de una red virtual de Azure en lugar de una red virtual de RemoteApp para conectar los recursos de la red corporativa a Azure. Hemos actualizado las [instrucciones de la colección híbrida](remoteapp-create-hybrid-deployment.md) para guiarle en el proceso de creación de una red virtual de Azure (el paso 1).
- Y hablando de redes virtuales, consulte [la nueva guía](remoteapp-vnetsizing.md) sobre los límites de tamaño y las limitaciones de la red virtual.
- Y ya que hablamos de límites, ¿cuáles son los [límites de servicio y los valores predeterminados](../azure-subscription-service-limits.md)?

¿Desea obtener más información acerca de RemoteApp de Azure? El equipo de RemoteApp estuvo en Ignite hace unas semanas. Vea el vídeo de Eric, [The Fundamentals of Microsoft Azure RemoteApp Management and Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868) (Aspectos básicos de la gestión y administración de RemoteApp de Microsoft Azure).

¿Necesita ver RemoteApp de Azure en el mundo real? Vea el tutorial [Ejecución de cualquier aplicación en cualquier dispositivo con RemoteApp](remoteapp-anyapp.md): en él se muestra cómo compartir el acceso con los usuarios, incluido el uso compartido de los archivos de base de datos. También disponemos de un tutorial para [conseguir que Office 365](remoteapp-tutorial-o365anywhere.md) se ejecute del mismo modo en cualquier dispositivo.

Gracias por seguir con nosotros, volveremos el próximo mes con más novedades.


### Permítanos ayudarle
¿Sabía que, además de clasificar este artículo y realizar comentarios abajo, puede realizar cambios en el artículo? ¿Falta algo? ¿Algo no es correcto? ¿Algo de lo que he escrito es simplemente confuso? Desplácese hacia arriba y haga clic en **Editar en GitHub** para realizar cambios que nos llegarán para su revisión y, luego, una vez que los aprobemos, verá los cambios y mejoras aquí.

<!---HONumber=AcomDC_0211_2016-->