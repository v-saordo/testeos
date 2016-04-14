
<properties
    pageTitle="Requisitos de las aplicaciones para RemoteApp de Azure | Microsoft Azure"
    description="Conozca los requisitos de las aplicaciones que desea usar en Azure RemoteApp"
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



# Requisitos de aplicaciones
Azure RemoteApp admite el streaming de aplicaciones Windows de 32 o 64 bits desde una imagen de Windows Server 2012 R2. La mayoría de las aplicaciones Windows de 32 o 64 bits se ejecutan "tal cual" en el entorno de Azure RemoteApp (Servicios de Escritorio remoto antes conocido como Terminal Services). Sin embargo, hay una diferencia entre ejecutar y ejecutar bien; algunas aplicaciones funcionan correctamente y su rendimiento es adecuado, mientras que otras no. La siguiente información proporciona instrucciones para el desarrollo de aplicaciones en un entorno de Servicios de Escritorio remoto y su prueba para garantizar la compatibilidad.

Sugerencia: estamos trabajando en la creación de algunos ejemplos prácticos de aplicaciones para usted. Verá algunos temas en los que se analizará el uso de Microsoft Access, QuickBooks y App-V en RemoteApp.

## Requisitos
Estos tres requisitos, si se siguen, ayudarán a sus aplicaciones a ejecutarse bien en RemoteApp:

1.	Las aplicaciones que cumplan todos los [Requisitos de certificación para aplicaciones de escritorio de Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) y que se ajusten a las [Instrucciones de programación de Servicios de Escritorio remoto](https://msdn.microsoft.com/library/aa383490.aspx) tendrán completa compatibilidad con RemoteApp.
2.	Las aplicaciones nunca deben almacenar los datos localmente en la imagen o en instancias de RemoteApp que se puedan perder. Después de crear una colección de RemoteApp, las instancias se clonan y están sin estado y solo deben contener aplicaciones. Almacene los datos en un origen externo o en el perfil del usuario.
3.	Las imágenes personalizadas nunca deben contener datos que se puedan perder.  

## Prueba de las aplicaciones
Use estos pasos para probar las aplicaciones:

1.	Instale Windows Server 2012 R2 y su aplicación
2.	Habilite Escritorio remoto
3.	Cree dos cuentas de usuario, UsuarioA y UsuarioB, y agregue ambas cuentas de usuario al grupo de seguridad de Escritorio remoto.
4.	Compruebe la compatibilidad multisesión mediante el establecimiento de dos sesiones de RDS simultáneas en el equipo mientras se inicia la aplicación.
5.	Valide el comportamiento de la aplicación

## Instrucciones para el desarrollo de aplicaciones
Use las siguientes instrucciones para desarrollar aplicaciones para RemoteApp.

### Varios usuarios

- La instalación de una [aplicación para un único usuario](https://msdn.microsoft.com/library/aa380661.aspx) puede crear problemas en un entorno de varios usuarios.
- Las aplicaciones deben [almacenar información específica del usuario](https://msdn.microsoft.com/library/aa383452.aspx) en ubicaciones específicas del usuario, aparte de la información global que se aplica a todos los usuarios.
- RemoteApp usa varios [espacios de nombres para objetos de kernel](https://msdn.microsoft.com/library/aa382954.aspx); un espacio de nombres global lo usan principalmente los servicios en aplicaciones cliente/servidor.
- No es seguro suponer que el nombre del equipo o la [dirección IP](https://msdn.microsoft.com/library/aa382942.aspx) que se asignen al equipo se asocian a un único usuario, dado que varios usuarios pueden iniciar sesión a la vez en un servidor host de sesión de Escritorio remoto.

### Rendimiento
- Deshabilite los [efectos gráficos](https://msdn.microsoft.com/library/aa380822.aspx) antes de agregar la aplicación a RemoteApp.
- Para maximizar la disponibilidad de la CPU para todos los usuarios, deshabilite las [tareas en segundo plano](https://msdn.microsoft.com/library/aa380665.aspx) o cree tareas en segundo plano eficientes que no consuman muchos recursos.
- Debe ajustar y equilibrar el [uso de subprocesos](https://msdn.microsoft.com/library/aa383520.aspx) de la aplicación en un entorno de varios usuarios y procesadores.
- Para optimizar el rendimiento, resulta conveniente [detectar](https://msdn.microsoft.com/library/aa380798.aspx) si las aplicaciones se ejecutan en una sesión de cliente.

<!---HONumber=AcomDC_1210_2015-->