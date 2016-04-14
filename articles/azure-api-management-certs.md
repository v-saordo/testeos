<properties 
	pageTitle="Carga de un certificado de API de administración de Microsoft Azure en el Portal" 
	description="Conozca cómo cargar el certificado de API de administración en Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="na" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015"
	ms.author="adegeo"/>


# Carga de un certificado de administración de API de Administración de Azure

Los certificados de administración le permiten autenticar con la API de administración de servicio proporcionada por Azure. Muchos programas y herramientas (como Visual Studio o el SDK de Azure) utilizarán estos certificados para automatizar la configuración y la implementación de varios servicios de Azure. Estos no están relacionados realmente con servicios en la nube.

>[AZURE.WARNING] Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados.

Para obtener [más](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) información acerca de los certificados de Azure (incluida la creación de un certificado autofirmado) si lo necesita.

También puede usar [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) para autenticar el código de cliente para fines de automatización.

## Carga de un certificado de administración

Una vez que tenga un certificado de administración creado, (archivo .cer con solo la clave pública) puede cargarlo en el portal. Cuando el certificado esté disponible en el portal, cualquiera que tenga un certificado que coincida (clave privada) puede conectarse a través de la API de administración y obtener acceso a los recursos de la suscripción asociada.

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).
2. Haga clic en **Configuración** en el lateral izquierdo del portal (es posible que tenga que desplazarse hacia abajo). 
    
    ![Settings](./media/azure-api-management-certs/settings.png)

3. Haga clic en la pestaña **Certificados de administración**.

    ![Settings](./media/azure-api-management-certs/certificates-tab.png)
    
4. Haga clic en el botón **Upload**.

    ![Settings](./media/azure-api-management-certs/upload.png)
    
5. Complete la información del cuadro de diálogo y haga clic en la **marca de verificación** de completado.

    ![Settings](./media/azure-api-management-certs/upload-dialog.png)

## Pasos siguientes

Ahora que tiene un certificado de administración asociado a una suscripción, puede conectarse mediante programación (después de haber instalado localmente el certificado correspondiente) a la [API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/mt420159.aspx) y automatizar los distintos recursos de Azure que también están asociados a esa suscripción.

<!---HONumber=AcomDC_0128_2016-->