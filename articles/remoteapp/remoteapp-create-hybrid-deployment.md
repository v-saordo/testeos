<properties 
	pageTitle="Creación de una colección híbrida de Azure RemoteApp | Microsoft Azure" 
	description="Obtenga información acerca de cómo crear una implementación de RemoteApp que se conecte a su red interna." 
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
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="elizapo"/>

# Creación de una colección híbrida de Azure RemoteApp

Hay dos tipos de colecciones de Azure RemoteApp:

- Nube: reside completamente en Azure. Puede guardar todos los datos en la nube (colección de solo nube) o conectar la colección a una red virtual y guardar datos en ella.   
- Híbrida: incluye una red virtual para el acceso local (esto requiere el uso de Azure AD y de un entorno de Active Directory local).

¿No sabe qué necesita? Revise [¿Qué tipo de colección necesita para Azure RemoteApp?](remoteapp-collections.md)

En este tutorial se realizará un recorrido por el proceso de creación de una colección híbrida. Existen ocho pasos:

1.	Decida qué [imagen](remoteapp-imageoptions.md) desea usar para la colección. Puede crear una imagen personalizada o seleccionar una de las imágenes de Microsoft que se incluyen con la suscripción.
2. Configurar la red virtual. Consulte la información sobre [planeación de la red virtual](remoteapp-planvnet.md) y [ajuste de tamaño](remoteapp-vnetsizing.md).
2.	Crear una colección.
2.	Una la colección al dominio local.
3.	Agregar una imagen de plantilla a la colección.
4.	Configurar la sincronización de directorios. Azure RemoteApp requiere que realice la integración con Azure Active Directory de una de las siguientes maneras: 1) configurar la sincronización de Azure Active Directory con la opción de sincronización de contraseñas; 2) configurar la sincronización de Azure Active Directory sin la opción de sincronización de contraseñas pero usando un dominio que esté federado con AD FS. Consulte la [información de configuración de Active Directory con RemoteApp](remoteapp-ad.md).
5.	Publicar aplicaciones de RemoteApp.
6.	Configurar el acceso del usuario.

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear la colección:

- [Suscribirse](https://azure.microsoft.com/services/remoteapp/) a Azure RemoteApp. 
- Cree una cuenta de usuario en Active Directory para usar la cuenta de servicio de Azure RemoteApp. Restrinja los permisos para esta cuenta de forma que solamente pueda unir máquinas al dominio.
- Recopile información sobre la red local: dirección IP de información y detalles de dispositivos VPN.
- Instale el módulo de [Azure PowerShell](../powershell-install-configure.md).
- Recopile información sobre los usuarios a los que quiera conceder acceso. Necesitará el nombre principal de usuario de Azure Active Directory (por ejemplo, name@contoso.com) de cada usuario. Asegúrese de que el UPN de Azure AD y Active Directory coincidan.
- Elija su imagen de plantilla. Una imagen de plantilla de Azure RemoteApp contiene las aplicaciones y los programas que desea publicar para los usuarios. Consulte [Opciones de imagen de Azure RemoteApp](remoteapp-imageoptions.md) para obtener más información. 
- ¿Desea usar la imagen de Office 365 ProPlus? Consulte la información [aquí](remoteapp-officesubscription.md).
- [Configuración de Active Directory para RemoteApp de Azure](remoteapp-ad.md)



## Paso 1: Configuración de la red virtual
Puede implementar una colección híbrida que use una red virtual de Azure existente, o bien puede crear una nueva red virtual. Una red virtual permite a los usuarios acceder a los datos de la red local a través de recursos remotos de RemoteApp. El uso de una red virtual proporciona a la colección acceso de red directo a otros servicios de Azure y máquinas virtuales implementadas en esa red virtual.

Asegúrese de consultar la información de [planeación de la red virtual](remoteapp-planvnet.md) y [tamaño de la red virtual](remoteapp-vnetsizing.md) antes de crear la red virtual.

### Creación de una red virtual de Azure y su unión a la implementación de Active Directory

Empiece por crear una [red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Esto se realiza en la pestaña **Red** del Portal de administración de Azure. Es necesario conectar la red virtual a la implementación de Active Directory que se sincroniza con el inquilino de Azure Active Directory.

Consulte [Acerca de la configuración de red virtual en el Portal de administración](../virtual-network/virtual-networks-settings.md) para obtener más información.

### Asegúrese de que la red virtual está lista para Azure RemoteApp
Antes de crear la colección, debemos asegurarnos de que la nueva red virtual está lista. Esto se puede validar haciendo lo siguiente:

1. Cree una máquina virtual de Azure dentro de la subred de la red virtual que acaba de crear para RemoteApp.
2. Use el Escritorio remoto para conectarse a la máquina virtual. (Haga clic en **Conectar**).
3. Únala a la misma implementación de Active Directory que quiere usar para RemoteApp.

¿Funcionó este procedimiento? Entonces la red virtual y la subred están preparadas para Azure RemoteApp.

[Aquí](https://msdn.microsoft.com/library/azure/jj156003.aspx) encontrará más información sobre cómo crear máquinas virtuales de Azure y conectarse a ellas con Escritorio remoto.

## Paso 2: Crear una colección de Azure RemoteApp ##



1. En el [Portal de Azure](http://manage.windowsazure.com), vaya a la página Azure RemoteApp.
2. Haga clic en **Nuevo > Crear con VPN**.
3. Escriba un nombre para la colección.
4. Seleccione el plan que quiere usar: Standard o Basic.
5. Elija la red virtual en la lista desplegable y, a continuación, la subred.
6. Únala a su dominio.
5. Haga clic en **Crear colección de RemoteApp**.

Una vez creada la colección de Azure RemoteApp, haga doble clic en el nombre de la colección. Se abrirá la página **Inicio rápido**, donde terminará de configurar la colección.

¿Algo salió mal? Consulte la [información para la solución de problemas de colecciones híbridas](remoteapp-hybridtrouble.md).

## Paso 3: Vinculación de la colección al dominio local ##

 
1. En la página **Inicio rápido**, haga clic en **Unirse al dominio local**.
2. Agregue la cuenta de servicio de Azure RemoteApp al dominio de Active Directory local. Necesitará el nombre de dominio, la unidad organizativa, el nombre del usuario de la cuenta de servicio y la contraseña. 

	Se trata de la información que recopiló si siguió los pasos descritos en [Configuración de Active Directory para RemoteApp de Azure](remoteapp-ad.md).


## Paso 4: Vincular a una imagen de Azure RemoteApp ##

Una imagen de plantilla de Azure RemoteApp contiene los programas que desea compartir con los usuarios. Puede crear una [imagen de plantilla](remoteapp-imageoptions.md) o un vínculo a una imagen existente (una ya importada o cargada en RemoteApp de Azure). También puede crear un vínculo a una de las [imágenes de plantilla](remoteapp-images.md) de Azure RemoteApp incluidas en programas de Office 365 o de Office 2013 (con fines de prueba).

Si está cargando la nueva imagen, puede que necesite escribir el nombre y elegir la ubicación para dicha imagen. En la página siguiente del asistente, verá un conjunto de cmdlets de PowerShell. Copie y ejecute estos cmdlets desde un símbolo de Windows PowerShell con privilegios elevados para cargar la imagen especificada.

Si vincula una imagen de plantilla existente, simplemente especifique el nombre, la ubicación y la suscripción de Azure asociada de la imagen.



## Paso 5: Configuración de la sincronización de directorios de Active Directory ##

Azure RemoteApp requiere que realice la integración con Azure Active Directory de una de las siguientes maneras: 1) configurar la sincronización de Azure Active Directory con la opción de sincronización de contraseñas; 2) configurar la sincronización de Azure Active Directory sin la opción de sincronización de contraseñas pero usando un dominio que esté federado con AD FS.

Consulte [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx): este artículo le ayuda a configurar la integración de directorios en cuatro pasos.

Consulte [Guía de sincronización de directorios](http://msdn.microsoft.com//library/azure/hh967642.aspx) para obtener información sobre planeación y pasos detallados.

## Paso 6: Publicar aplicaciones ##

Una aplicación de Azure RemoteApp es la aplicación o el programa que proporciona a los usuarios. Se encuentra en la imagen de plantilla que cargó para la colección. Cuando un usuario accede a una aplicación, parece que se ejecuta en el entorno local, pero realmente se está ejecutando en Azure.

Antes de que los usuarios puedan acceder a las aplicaciones, es necesario publicarlas; esto permite que los usuarios accedan a ellas a través del cliente de Escritorio remoto.
 
Puede publicar varias aplicaciones en su colección. En la página de publicación, haga clic en **Publicar** para agregar una aplicación. Puede publicar desde el menú **Inicio** de la imagen de plantilla o especificando la ruta de acceso en la imagen de plantilla para la aplicación. Si opta por agregarla desde el menú **Inicio**, elija el programa que va a agregar. Si opta por proporcionar la ruta de acceso a la aplicación, proporcione un nombre para la aplicación y la ruta de acceso en la que se instaló en la imagen de la plantilla.

## Paso 7: Configuración del acceso de usuarios ##

Ahora que creó la colección, necesita agregar a los usuarios que desea que puedan usar los recursos remotos. Es necesario que los usuarios a los que proporciona acceso existan en el inquilino de Active Directory asociado a la suscripción que use para crear esta colección de Azure RemoteApp.

1.	En la página Inicio rápido, haga clic en **Configurar acceso de usuario**. 
2.	Escriba la cuenta de trabajo (desde Active Directory) o la cuenta de Microsoft para la que quiere conceder acceso.

	**Notas:**

	Asegúrese de que usa el formato "usuario@dominio.com".

	Si usa Office 365 ProPlus en su colección, debe usar las identidades de Active Directory para los usuarios. Esto ayuda a validar las licencias.


3.	Cuando se validen los usuarios, haga clic en **Guardar**.


## Pasos siguientes ##
Eso es todo, creó e implementó correctamente su colección híbrida de Azure RemoteApp. El paso siguiente es que los usuarios descarguen e instalen el cliente Escritorio remoto. Puede encontrar la dirección URL del cliente en la página Inicio rápido de Azure RemoteApp. Después, indique a los usuarios que inicien sesión en el cliente y accedan a las aplicaciones publicadas.


 
### Permítanos ayudarle 
¿Sabía que, además de clasificar este artículo y realizar comentarios abajo, puede realizar cambios en el artículo? ¿Falta algo? ¿Algo no es correcto? ¿Algo de lo que he escrito es simplemente confuso? Desplácese hacia arriba y haga clic en **Editar en GitHub** para realizar cambios que nos llegarán para su revisión y, a continuación, una vez que los aprobemos, verá los cambios y mejoras aquí.

<!---HONumber=AcomDC_0211_2016-->