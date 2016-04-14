<properties 
	pageTitle="¿Qué es Azure RemoteApp? | Microsoft Azure" 
	description="Obtenga información acerca de cómo compartir aplicaciones y recursos en cualquier dispositivo mediante Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/19/2016" 
	ms.author="elizapo"/>

# ¿Qué es Azure RemoteApp?

Azure RemoteApp ofrece la funcionalidad del programa RemoteApp Microsoft local, respaldado por Servicios de Escritorio remoto, en Azure. Azure RemoteApp ayuda a proporcionar acceso remoto y seguro a aplicaciones desde diferentes dispositivos de usuario. Azure RemoteApp básicamente hospeda en la nube sesiones de Terminal Server no persistentes que usted usará y compartirá con los usuarios.

Con Azure RemoteApp puede compartir aplicaciones y recursos con usuarios en prácticamente cualquier dispositivo. Hospedamos sus aplicaciones en la nube, lo que significa que nosotros nos encargamos del hardware y la escalación para satisfacer las necesidades del usuario. Todo lo que tiene que hacer es cargar las aplicaciones que desea compartir y, a continuación, hacer que los usuarios utilicen esas aplicaciones. [Los usuarios se encargan de mantener sus propios dispositivos](remoteapp-clients.md), mientras usted administra todo el contenido a través del portal de Azure. Tiene incluso la opción de usar las credenciales corporativas, lo que permite garantizar la seguridad de las aplicaciones y los datos.

Siga leyendo para obtener más información sobre Azure RemoteApp o, si ya está convencido, [pruébela ahora](https://azure.microsoft.com/services/remoteapp/).

¿Tiene preguntas acerca de Azure RemoteApp? Consulte nuestras [preguntas más frecuentes](remoteapp-faq.md).

RemoteApp de Azure es parte de la [Infraestructura de Escritorio virtual de Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**¡Nuevo!** ¿Desea obtener más información acerca de RemoteApp de Azure? O bien, ¿está listo para validar Azure RemoteApp a escala? Únase a nuestro [seminario web Pregunte a los expertos](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website) semanal.

## Colecciones de Azure RemoteApp
Hay dos tipos de [colecciones de Azure RemoteApp](remoteapp-collections.md):


- Una **colección en la nube** se hospeda en la nube de Azure y almacena allí todos los datos de los programas. Los usuarios pueden acceder a las aplicaciones mediante el inicio de sesión con sus cuentas de Microsoft o credenciales corporativas sincronizadas o federadas con Azure Active Directory.

	Elija una colección en la nube cuando la aplicación que desea compartir no requiere una conexión a ningún recurso de la red privada de su empresa (por ejemplo, a través de un dispositivo VPN). Si la aplicación usa recursos en Internet, OneDrive o Azure, una colección en la nube funcionará en su caso. También es la más rápida crear.

- Una **colección híbrida** se hospeda en la nube de Azure y almacena allí los datos, pero también permite a los usuarios acceder a datos y recursos almacenados en la red local. Los usuarios pueden acceder a las aplicaciones mediante el inicio de sesión con sus credenciales corporativas sincronizadas o federadas con Azure Active Directory.

	Elija una colección híbrida si necesita una conexión a recursos de la red privada de su empresa. Por ejemplo, si la aplicación necesita acceso a uno de los siguientes elementos:

	- Servidores de archivos ubicados en la intranet
	- Quicken
	- Bases de datos detrás de un firewall

	Esto generalmente resulta más útil en el caso de grandes organizaciones con muchos recursos en sus redes privadas que no se pueden mover a la nube.

Las diferentes colecciones tienen diferentes opciones, incluidas las redes, así que descubra [qué colección](remoteapp-collections.md) responde mejor a sus necesidades.


### Actualización de la colección
Una de las diferencias clave entre las colecciones híbridas y en la nube es cómo se administran las actualizaciones de software. Con una colección en la nube que usa la imagen de Office 365 ProPlus u Office 2013 preinstalada, no tiene que preocuparse por las actualizaciones. El servicio se mantiene a sí mismo e implementa las actualizaciones de manera continua para aplicaciones y el sistema operativo.

Para colecciones híbridas, así como para las colecciones en la nube que usan una imagen de plantilla personalizada, usted es responsable de mantener la imagen y las aplicaciones. Para imágenes unidas a dominio, puede controlar las actualizaciones mediante herramientas como Windows Update, directiva de grupo o System Center.

Después de actualizar la imagen de plantilla personalizada, cargue la nueva imagen en la nube de Azure y, luego, actualice la colección para usar la nueva imagen. (Puede hacerlo desde la página **Inicio rápido** de Azure RemoteApp o el panel).

Vea [Actualización de la colección](remoteapp-update.md) para obtener más información.

## Clientes compatibles con RemoteApp
Azure RemoteApp es compatible con las aplicaciones de cliente de RemoteApp para Windows y Windows RT, así como con las aplicaciones de Escritorio remoto de Microsoft para Mac, iOS y Android. Los usuarios pueden usar estas aplicaciones en sus dispositivos móviles o de proceso para acceder a los nuevos programas de Azure RemoteApp.

Vea [Acceso a las aplicaciones de Azure RemoteApp](remoteapp-clients.md) para obtener más información sobre los clientes.

## Pasos siguientes
Venga, pruébelo. Estos artículos le ayudarán a comenzar a usar Azure RemoteApp:

- [¿Qué tipo de colección necesita para Azure RemoteApp?](remoteapp-collections.md)
- [Creación de una imagen de Azure RemoteApp](remoteapp-imageoptions.md)
- [Creación de una colección en la nube de Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Creación de una colección híbrida de Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [¿Cómo funciona la concesión de licencias de RemoteApp de Azure?](remoteapp-licensing.md)
- [Prácticas recomendadas para usar RemoteApp de Azure](remoteapp-bestpractices.md)
- [Preguntas más frecuentes sobre RemoteApp de Azure](remoteapp-faq.md)
 

### Permítanos ayudarle 
¿Sabía que, además de clasificar este artículo y realizar comentarios abajo, puede realizar cambios en el artículo? ¿Falta algo? ¿Algo no es correcto? ¿Algo de lo que he escrito es simplemente confuso? Desplácese hacia arriba y haga clic en **Editar en GitHub** para realizar cambios que nos llegarán para su revisión y, a continuación, una vez que los aprobemos, verá los cambios y mejoras aquí.

<!---HONumber=AcomDC_0302_2016-->