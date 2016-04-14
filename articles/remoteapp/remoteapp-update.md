<properties
   pageTitle="Actualización de la colección Azure RemoteApp | Microsoft Azure"
   description="Más información sobre la colección Azure RemoteApp"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="12/05/2015"
   ms.author="elizapo"/>

# Actualización de una colección en Azure RemoteApp

Llegará un momento, inevitablemente, en que tendrá que actualizar las aplicaciones o la imagen de la colección Azure RemoteApp. Si usa una de las imágenes incluidas con la suscripción de Azure RemoteApp, ya sea en una colección híbrida o en la nube, la administración de todas y cada una de las actualizaciones la realiza la propia Azure RemoteApp, por lo que puede estar tranquilo.

Sin embargo, si usa una imagen personalizada (creada desde cero o modificando una de nuestras imágenes), es responsable del mantenimiento de la imagen y las aplicaciones.

Por lo tanto, ¿cómo tiene que proceder para actualizar la colección? Es bastante sencillo:

1. Actualice la imagen que usó en la colección. Aplique las revisiones o actualizaciones necesarias y guárdela con un otro nombre.
2. Realice la [carga](remoteapp-uploadimage.md) o la [importación](remoteapp-image-on-azurevm.md) de la imagen en RemoteApp.
3. Ahora, en la página de la colección, haga clic en **Actualizar**.
4. Elija la nueva imagen de la lista de **imágenes de plantilla**.
4. Aquí llega la parte complicada, ha de decidir cómo tratar a los usuarios que usan actualmente una aplicación de la colección. Tiene las siguientes opciones:
	- **Darle a los usuarios un margen de 60 minutos tras la actualización**. Tan pronto como finalice la actualización, Azure RemoteApp mostrará un mensaje a los usuarios activos en el que se les indica que guarden el trabajo, cierren la sesión y vuelvan a iniciarla. Transcurridos los 60 minutos, se cerrará automáticamente la sesión de todos los usuarios activos que no la cerraron. Los usuarios pueden volver a iniciar sesión inmediatamente.
	- **Cerrar inmediatamente la sesión de los usuarios**. En cuanto finalice la actualización, cierre automáticamente la sesión de todos los usuarios sin avisarles. Si elige esta opción, los usuarios pueden perder datos. Sin embargo, pueden volver a conectarse a la aplicación inmediatamente.

1. Haga clic en la marca de verificación para iniciar la actualización.

<!---HONumber=AcomDC_1210_2015-->