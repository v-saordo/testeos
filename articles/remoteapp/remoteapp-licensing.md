<properties
    pageTitle="Licencias de Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo funcionan las licencias en RemoteApp de Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/25/2016"
    ms.author="elizapo" />


# ¿Cómo funciona la concesión de licencias de RemoteApp de Azure?


Ha configurado el servicio de RemoteApp de Azure, creado las plantillas y está listo para publicar aplicaciones para los usuarios. Pero hay una última cosa que debe averiguar: cómo funciona la concesión de licencias. ¿Cómo funcionan exactamente las licencias para RemoteApp y para las aplicaciones que se comparten a través de RemoteApp?

RemoteApp no requiere licencias de Windows ni licencias de acceso de cliente (CAL) de Escritorio remoto. La suscripción se encarga de lo relativo a RemoteApp. (Consulte los detalles de los [planes de precios](../../../pricing/details/remoteapp/)).

Si utiliza una de las imágenes incluidas en su suscripción, puede compartir cualquiera de las aplicaciones instaladas en esa imagen sin necesidad de una licencia aparte. Por ejemplo, si usa la imagen de plantilla de Windows Server 2012 R2 para generar una colección, puede compartir System Center Endpoint Protection con los usuarios. Las únicas excepciones a esta regla son Office 365 ProPlus, que requiere una suscripción independiente, y Office 2013, que no se puede compartir en una colección de producción.

Si quiere usar la imagen de plantilla de Office 365 que viene con RemoteApp de Azure, debe tener un plan de Office 365 ProPlus *existente*. Lo mismo ocurre con cualquier aplicación de Office 365 que se publique mediante una plantilla personalizada. Debe activar las aplicaciones con su propia suscripción. Lo mismo ocurre con las suscripciones de prueba y pagadas. Si quiere usar la imagen de plantilla de Office 365 durante el período de prueba, *y ya no tiene una suscripción*, vaya a la página de Office 365 para [suscribirse](https://go.microsoft.com/fwlink/p/?LinkID=403802) y obtener una suscripción de prueba. Consulte [¿Cómo funcionan conjuntamente RemoteApp y Office?](remoteapp-o365.md) para obtener más información.

Si, durante el período de prueba, no quiere obtener una suscripción de prueba de Office 365, use la imagen de plantilla de Office 2013 Professional Plus que viene con RemoteApp. Esta imagen de plantilla solo puede usarse durante 30 días y no se puede convertir en una colección de pago.

Para otras aplicaciones, deberá asegurarse de que dispone de la licencia necesaria para compartir la aplicación.

Esto tiene sentido, ¿no? Puede publicar cualquier aplicación que pueda compartir legalmente. Además, deberá asegurarse de que realmente tiene derecho a compartir sus programas.

Tenga en cuenta que no puede usar un contrato de licencia por volumen ni una licencia CAL en una colección en la nube. *Puede* usar un contrato de licencia por volumen para activar aplicaciones en su colección híbrida (excepto para Office). Necesitará instalarlas en la imagen de plantilla desde los soportes de licencias por volumen. Siga la información del proveedor de la aplicación para instalar licencias en un entorno de Escritorio remoto.

<!---HONumber=AcomDC_0302_2016-->