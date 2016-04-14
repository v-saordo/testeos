
<properties
    pageTitle="Uso de Office con Azure RemoteApp | Microsoft Azure" 
    description="Obtenga información sobre cómo funcionan juntos Office y Azure RemoteApp."
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
    ms.date="01/07/2016"
    ms.author="elizapo" />

# Uso de Office con Azure RemoteApp

Tiene dos opciones para hospedar aplicaciones de Office en Azure RemoteApp: Office 365 ProPlus u Office 2013 Professional Plus Trial.

**¿Sabía que tenemos un nuevo artículo mejor que pronto reemplazará a este? Consulte [Cómo usar su suscripción a Office 365 con Azure RemoteApp](remoteapp-officesubscription.md). Abarca toda la información que necesita para poder usar Office 365 + RemoteApp de Azure.**

## Office 365 ProPlus
Puede crear una colección de RemoteApp mediante la imagen de plantilla de Office 365 ProPlus. Esta opción le permite ampliar el servicio de Office 365 a RemoteApp. Debe disponer de un plan de suscripción y los usuarios deben tener una licencia para el servicio Office 365 ProPlus, independiente o a través de planes de servicio de Office 365.

RemoteApp es compatible con la activación en equipos compartidos de Office 365. Al habilitar la activación en equipos compartidos y usar la [Herramienta de implementación de Office ](http://www.microsoft.com/download/details.aspx?id=36778) para la instalación, Office 365 ProPlus se instala sin estar activado. Cuando un usuario se conecta a una colección que contiene Office 365, Office comprueba si el usuario se ha aprovisionado para Office 365 ProPlus. En caso afirmativo, Office activa temporalmente Office 365 ProPlus (esta activación se mantiene hasta que los usuarios se desconectan del servicio.

Para utilizar la activación en equipos compartidos de Office 365, tendrá que crear una [plantilla personalizada](remoteapp-create-custom-image.md) y luego instalar Office 365 ProPlus siguiendo [estas instrucciones](https://technet.microsoft.com/library/dn782858.aspx).

Puede administrar las licencias de Office 365 de los usuarios en el [Portal de administración de Office 365](https://portal.office365.com/). Obtenga más información sobre [Planes de servicios de Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).


## Office 2013 Professional Plus Trial
Durante una prueba de 30 días de RemoteApp, puede usar la imagen de plantilla de Office 2013 Professional Plus (prueba) para crear una colección de RemoteApp. Puede asignar usuarios a esta colección de prueba mediante sus cuentas de trabajo de Azure Active Directory o cuentas de Microsoft. No se requieren suscripciones adicionales.

Esta es una opción excelente para probar y hacerse una buena idea de Office de RemoteApp. Sin embargo, esta opción está pensada solo para su evaluación y prueba. Las colecciones de RemoteApp creadas mediante la imagen de plantilla de Office 2013 Professional Plus (prueba) no se pasan al modo de producción y se deshabilitarán al final del período de prueba.

## Cambio de versión de prueba a producción
Cuando se inicia la versión de prueba gratuita de 30 días, una nota en la sección de RemoteApp del portal le indicará cuánto le queda en la versión de prueba antes de tener que realizar una transición a una cuenta pagada. Puede activar su cuenta y cambiar al modo de producción mediante el vínculo en esta nota.

Al activar su cuenta, esto afectará a todas las colecciones de RemoteApp en su cuenta.

- Las colecciones que se ejecutan con Windows Server 2012 R2 o las imágenes de plantilla de Office 365 ProPlus pasarán a producción sin problemas. Todos los datos de usuario y configuración, incluidas las sesiones en curso, permanecerán intactos.
- Si cargó imágenes de plantilla personalizadas, las colecciones que usen esas imágenes también pasarán sin problemas.
- La imagen de plantilla de Office 2013 Professional Plus (prueba) está pensada para evaluación únicamente. Las colecciones que se ejecutan con esta imagen de plantilla no se pueden pasar a producción. Se pondrán en estado "deshabilitado".


Si no pasa al modo de producción en el momento de expiración de la prueba, las colecciones de RemoteApp se deshabilitarán. No se preocupe, la configuración y datos de los usuarios se guardan durante otros 90 días, por lo que puede activar el servicio y cambiar al modo de producción sin perder datos.

<!---HONumber=AcomDC_0114_2016-->