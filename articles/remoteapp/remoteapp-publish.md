<properties
    pageTitle="Publicación de una aplicación en Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo publicar aplicaciones y recursos de Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/07/2016"
    ms.author="elizapo" />


# Cómo publicar una aplicación en RemoteApp

Después de crear la colección de RemoteApp, deberá publicar las aplicaciones o los recursos que quiere que estén disponibles para los usuarios. Las imágenes de plantilla proporcionadas con la suscripción solo tienen algunas aplicaciones publicadas de forma predeterminada; para compartir las otras aplicaciones, debe publicarlas.

> [AZURE.NOTE]¿Necesita actualizar una aplicación? Necesitará [actualizar la imagen](remoteapp-update.md) primero.

En la pestaña **Publicación** del portal, haga clic en **Publicar**. Puede agregar una aplicación desde el menú **Inicio** de la imagen de plantilla o proporcionar la ruta de acceso donde está instalada la aplicación en la imagen de plantilla. Si opta por agregar desde el menú **Inicio**, elija en la lista la aplicación que va a publicar. Si elige proporcionar la ruta de acceso a la aplicación, escriba un nombre para la aplicación y la ruta de acceso a la aplicación. Use variables en la ruta de acceso; por ejemplo, "%systemdrive%" en lugar de "c:".

> [AZURE.NOTE]Si quiere agregar la aplicación desde el menú **Inicio**, ha de tener *agregada esa aplicación al menú **Inicio** en la imagen de plantilla.* De lo contrario, RemoteApp solo verá lo que *está* en el menú **Inicio** y usted se confundirá.

>Para asegurarse de que la aplicación se encuentra en el menú **Inicio**, coloque un archivo de acceso directo (**.lnk**) dentro de la carpeta %systemdrive%\\ProgramData\\Microsoft\\Windows\\Menú Inicio\\Programas.

> Si olvidó agregar la aplicación al menú **Inicio** al crear la plantilla, tiene la posibilidad de agregar la ruta de acceso a la aplicación. (O volver a crear la imagen de plantilla, pero supone algo más de trabajo).


 

<!---HONumber=AcomDC_0114_2016-->