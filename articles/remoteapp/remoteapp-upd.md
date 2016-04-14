
<properties 
    pageTitle="Cómo usar Azure RemoteApp con cuentas de usuario de Office 365 | Microsoft Azure"
	description="Aprender a usar Azure RemoteApp con las cuentas de usuario de Office 365"
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
    ms.date="12/04/2015" 
    ms.author="elizapo" />

# ¿Cómo guarda Azure RemoteApp la configuración y datos de usuario?

Azure RemoteApp guarda la identidad y las personalizaciones de los usuarios en dispositivos y sesiones. Estos datos de los usuarios se almacenan en un disco para cada colección y cada usuario, conocido como disco de perfil de usuario (UDP). El disco sigue al usuario y garantiza que este tenga una experiencia coherente, independientemente de donde inicie sesión.

Los discos de perfil de usuario son completamente transparentes ante el usuario. Los usuarios guardan documentos en su carpeta de documentos (en lo que parece ser una unidad local) y cambian la configuración de sus aplicaciones como de costumbre). Al mismo tiempo, se conservan todas las configuraciones personales al conectarse a Azure RemoteApp desde cualquier dispositivo. El usuario ve es sus datos en el mismo lugar.

Cada UPD tiene 50 GB de almacenamiento persistente y contiene datos de usuarios y configuraciones de aplicaciones.

Siga leyendo para obtener información específica sobre los datos del perfil de usuario.

>[AZURE.NOTE]¿Necesidad de deshabilitar el UPD? Puede hacerlo ahora: compruebe la entrada de blog de Pavithra, [Deshabilitar discos de perfil de usuario (UPD) en Azure RemoteApp](http://blogs.msdn.com/b/rds/archive/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp.aspx), para más información.


## ¿Cómo puede un administrador acceder a los datos?

Si necesita obtener acceso a los datos de uno de los usuarios (para efectuar la recuperación ante desastres o si el usuario abandona la empresa), póngase en contacto con [Azure RemoteApp](mailto:remoteappforum@microsoft.com) y ofrezca la información de suscripción de la recopilación y la identidad del usuario. El equipo de Azure RemoteApp le proporcionará una dirección URL para el disco duro virtual. Descargue ese VHD y recupere los documentos o archivos que necesite. Tenga en cuenta que el disco duro virtual es de 50 GB, por lo que tardará un poco en descargarlo.


## ¿Se efectúa una copia de seguridad de los datos?

Sí, se guarda una copia de seguridad de los datos del usuario por ubicación geográfica. Los datos son de solo lectura y se puede tener acceso a ellos de la misma manera que a los datos normales (póngase en contacto con Azure RemoteApp para conocer la forma de hacerlo) si el centro de datos principal está inactivo. Los datos se copian en tiempo real a la ubicación de copia de seguridad, y no conservamos copias de las distintas versiones. Por lo tanto, en caso de daño en los datos, no podremos restaurarlos a una versión buena conocida anteriormente; sin embargo, si el centro de datos principal está inactivo, podrá obtener los datos del usuario desde la otra ubicación.

## ¿Cómo ven los usuarios el UPD en el servidor?

Cada usuario tendrá su propio directorio en el servidor que se asigna a su UPD: c:\\Users\\username.

## ¿Cuál es la mejor manera de usar Outlook y UPD?

Azure RemoteApp guarda el estado de Outlook (buzones, PST) entre sesiones. Para ello, necesitamos que el PST se almacene en los datos del perfil de usuario (c:\\users<username>). Se trata de la ubicación predeterminada de los datos, por lo que siempre y cuando no cambie la ubicación, se conservarán los datos entre sesiones.

También se recomienda usar el modo "en caché" en Outlook y usar el modo "servidor/online" para buscar.

Consulte [este artículo](remoteapp-outlook.md) para más información sobre el uso de Outlook y Azure RemoteApp.

## ¿Podemos usar soluciones de datos compartidos?
Sí, Azure RemoteApp admite el uso de soluciones de datos compartidos (especialmente OneDrive para la Empresa y Dropbox). Sin embargo, tenga en cuenta que no se admiten OneDrive Consumer (la versión personal) ni Box.

## Información acerca del redireccionamiento
Puede configurar Azure RemoteApp para permitir a los usuarios obtener acceso a dispositivos locales configurando el [redireccionamiento](remoteapp-redirection.md). A continuación, los dispositivos locales podrán tener acceso a los datos en el UPD.

## ¿Puedo usar mi UPD como un recurso compartido de red?
No. Los UPD no pueden utilizarse como un recurso compartido de red. Los UPD solo están disponibles si el usuario está conectado activamente a Azure RemoteApp.

## Si se elimina un usuario de una colección, ¿se elimina su UPD?

No, cuando se elimina un usuario, no se elimina automáticamente el UPD. En su lugar, almacenamos los datos hasta que se elimina la colección. 90 días después de eliminar la colección, eliminamos todos los UPD.

Si necesita eliminar un UPD de una colección, póngase en contacto con Azure RemoteApp (podemos eliminar UPD desde nuestra ubicación).

## ¿Puedo acceder a los UPD de mis usuarios (de los usuarios actuales o eliminados)?

Sí, si se pone en contacto con [Azure RemoteApp](mailto:remoteappforum@microsoft.com), podemos configurarlo con una dirección URL para tener acceso a los datos. Tendrá aproximadamente 10 horas para descargar los datos o los archivos del UPD antes de que caduque el acceso.

## ¿Están los UPD disponibles sin conexión?

En estos momentos no proporcionamos acceso sin conexión a UPD, aparte de las 10 horas de acceso descritas anteriormente. Esto significa que actualmente no tenemos manera de proporcionarle acceso durante el tiempo suficiente para completar las tareas más complicadas, como ejecutar software antivirus en los UPD o tener acceso a los datos para efectuar una auditoría.

## ¿La configuración de la clave del Registro se conserva?
Sí, todo lo escrito en HKEY\_Current\_User forma parte de los UPD.

## ¿Puedo deshabilitar los UPD para una colección?

Sí, puede pedir a Azure RemoteApp que deshabilite los UPD para una suscripción, pero no puede hacerlo usted. Esto significa que los UPDs se deshabilitarán para todas las colecciones de la suscripción.

Es posible que quiera deshabilitar UPD en cualquiera de las siguientes situaciones:

- Debe completar el acceso y el control de los datos de usuario (para fines de auditoría y revisión como instituciones financieras).
- Dispone de soluciones de administración de perfiles de usuario de terceros locales y quiere seguir usándolas en su implementación de Azure RemoteApp unida al dominio. Esto requeriría que se cargue el agente de perfil en la imagen dorada. 
- No necesita ningún almacenamiento de datos local o tiene todos los datos en la nube (como OneDrive para la Empresa) o un recurso compartido de archivos y quiere controlar el almacenamiento local de datos con Azure RemoteApp.

Vea [Deshabilitar discos de perfil de usuario (UPD) en Azure RemoteApp](http://blogs.msdn.com/b/rds/archive/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp.aspx) para más información.

## ¿Puedo impedir que los usuarios guardar los datos en la unidad del sistema?

Sí, pero deberá configurarla en la imagen de la plantilla antes de crear la colección. Lleve a cabo los pasos siguientes para bloquear el acceso a la unidad del sistema:

1. Ejecute **gpedit.msc** en la imagen de la plantilla.
2. Vaya a **Configuración de usuario > Plantillas administrativas > Componentes de Windows > Explorador**.
3. Seleccione las opciones siguientes:
	- **Ocultar estas unidades especificadas en Mi PC**
	- **Impedir el acceso a las unidades desde Mi PC**

## ¿Se pueden propagar los UPD? Deseo colocar algunos datos en el UPD que está disponible la primera vez que el usuario inicia sesión.

Sí, al crear la imagen de plantilla, puede agregar información al perfil predeterminado. A continuación, esa información se agrega al UPD.

## ¿Puedo cambiar el tamaño del UPD en función de la cantidad de datos que deseo almacenar?

No, todos los UPD tienen 50 GB de almacenamiento. Si desea almacenar distintas cantidades de datos, intente lo siguiente:

1. Deshabilite los UPD para la colección.
2. Configure un recurso compartido de archivos para que accedan los usuarios.
3. Cargue el recurso compartido de archivos mediante un script de inicio. Consulte a continuación para obtener detalles sobre los scripts de inicio en Azure RemoteApp.
4. Indique a los usuarios que guarden todos los datos en el recurso compartido de archivos.

También puede usar aplicaciones de sincronización de datos como OneDrive para la Empresa.

## ¿Cómo se ejecuta un script de inicio en Azure RemoteApp?

Si desea ejecutar un script de inicio, comience por crear una tarea programada en la imagen de plantilla que se va a usar para la colección. (Haga esto *antes* de ejecutar sysprep).

![Crear una tarea del sistema](./media/remoteapp-upd/upd1.png)

![Crear una tarea del sistema que se ejecute cuando un usuario inicia sesión](./media/remoteapp-upd/upd2.png)

La tarea programada iniciará el script de inicio usando las credenciales del usuario. Programe la tarea para ejecutarse cada una vez que un usuario inicie sesión.

![Establezca el desencadenador para la tarea como "Al iniciar la sesión"](./media/remoteapp-upd/upd3.png)

También puede usar [Scripts de inicio basados en directivas de grupo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx).

## ¿Qué sucede si se coloca un script de inicio en el menú Inicio? ¿Funcionaría?

En otras palabras, ¿puedo crear un archivo .bat que ejecute un script de ventana de configuración y guardarlo en la carpeta c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp y, a continuación, que ese script se ejecute cuando un usuario inicie una sesión de RemoteApp?

No, eso no es compatible con Azure RemoteApp, que usa RDSH, que a su vez tampoco admite scripts de inicio en el menú Inicio.

## ¿Puedo usar mstsc.exe (el programa de escritorio remoto) para configurar scripts de inicio de sesión?

No, no es compatible con Azure RemoteApp.

<!---HONumber=AcomDC_1217_2015-->