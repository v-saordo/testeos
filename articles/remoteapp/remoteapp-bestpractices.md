<properties
    pageTitle="Procedimientos recomendados de Azure RemoteApp | Microsoft Azure"
    description="Prácticas recomendadas para configurar y usar RemoteApp de Azure"
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
    ms.date="12/05/2015"
    ms.author="elizapo" />

# Prácticas recomendadas para configurar y usar RemoteApp de Azure

La siguiente información puede ayudarle a configurar y usar RemoteApp de Azure de manera productiva.

## Conectividad


- Utilice siempre la versión más reciente del cliente. El uso de clientes anteriores podría producir problemas de conectividad y otras experiencias degradadas. La habilitación de las actualizaciones automáticas de aplicaciones para su dispositivo garantizará que siempre esté instalado el cliente más reciente.
- Utilice siempre la conexión a Internet más estable y confiable disponible.  
- Use solo las conexiones proxy admitidas para obtener un rendimiento óptimo de conectividad. No se admite el proxy SOCKS.

## Aplicaciones


- Guarde y cierre las aplicaciones de RemoteApp cuando haya terminado con la aplicación. Si no se cierra la aplicación se podría producir la pérdida de datos.
- Valide las aplicaciones personalizadas antes de usarlas en RemoteApp de Azure. Esto incluye asegurarse de que funcionan en una plataforma multisesión y de que no consumen recursos innecesarios como memoria y CPU que puedan privar a otro usuario de la misma colección. Para obtener información, descargue y revise las [Prácticas recomendadas de compatibilidad de aplicaciones para servicios de Escritorio remoto](http://www.microsoft.com/download/details.aspx?id=18704).

## Configuración y administración


- Conserve las imágenes de plantilla actualizadas, instalando actualizaciones de software y otras revisiones críticas según sea necesario. Esto garantiza que, conforme RemoteApp de Azure realiza la autoescala para satisfacer su capacidad, se revisa cada instancia.  
- Asegúrese de que su implementación de Servicios de federación de Active Directory (AD FS) es segura y confiable. En caso contrario, es posible que se produzca un error en las autenticaciones de cliente, con lo que se impedirá que los usuarios accedan a RemoteApp de Azure.
- Configurar imágenes de plantilla con características, roles o aplicaciones instaladas, de manera que no tengan estado. No deben depender de todas las instancias de las máquinas virtuales en un servicio de RemoteApp que están en un estado persistente.
	- Almacene todos los datos de usuario en perfiles de usuario u otras ubicaciones de almacenamiento externos al servicio, como recursos compartidos de archivos locales o OneDrive.
	- Almacene datos compartidos en ubicaciones de almacenamiento externos al servicio, como recursos compartidos de archivos locales o OneDrive.
	- Configure la configuración de todo el sistema en la imagen de plantilla en lugar de en las máquinas virtuales individuales de un servicio.
	- Deshabilite las actualizaciones de software automáticas para las aplicaciones publicadas: en su lugar, aplíquelas manualmente a la imagen de plantilla y pruébelas antes de implementar desde la plantilla.

<!---HONumber=AcomDC_1210_2015-->