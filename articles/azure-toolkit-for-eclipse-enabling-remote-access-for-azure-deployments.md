<properties
    pageTitle="Habilitación del acceso remoto para implementaciones de Azure en Eclipse"
    description="Obtenga información sobre cómo habilitar el acceso remoto para las implementaciones de Azure mediante el kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# Habilitación del acceso remoto para implementaciones de Azure en Eclipse #

Para facilitar la solución de problemas de las implementaciones, puede habilitar y usar el acceso remoto para conectarse a la máquina virtual que hospeda la implementación. La funcionalidad de acceso remoto se basa en el protocolo de Escritorio remoto (RDP). Puede configurar el acceso remoto para la implementación después de publicarlo en Azure, o bien, si usa Eclipse con un sistema operativo Windows, puede configurar el acceso remoto antes de publicar en Azure. Tenga en cuenta que necesitará a un cliente de Escritorio remoto que sea compatible con el sistema operativo para conectarse a la máquina virtual de su implementación en Azure.

## Habilitación del acceso remoto antes de implementar en Azure ##

>[AZURE.NOTE] Para habilitar el acceso remoto antes de implementar la aplicación en Azure, tiene que ejecutarse Eclipse en Windows.

La imagen siguiente muestra el cuadro de diálogo de propiedades **Remote Access** (Acceso remoto) que se usa para habilitar el acceso remoto.

![][ic719494]

Hay dos maneras de mostrar el cuadro de diálogo de propiedades **Remote Access**:

* Haga clic en el vínculo **Advanced** (Propiedades avanzadas) en la sección **Remote Access** del cuadro de diálogo **Publish to Azure** (Publicar en Azure).
* Abra el cuadro de diálogo **Properties** (Propiedades) del proyecto de Azure.

Cuando cree un proyecto de implementación de Azure, el proyecto no tendrá el acceso remoto habilitado de forma predeterminada. Pero, puede habilitar fácilmente el acceso remoto especificando el nombre de usuario y la contraseña en el cuadro de diálogo **Publish to Azure**. La contraseña de acceso remoto se cifra mediante certificados X.509. Si no especifica su propio certificado, el cifrado se basa en un certificado autofirmado que se suministra con el complemento de Azure para Eclipse. Este certificado autofirmado se encuentra en la carpeta **cert** del proyecto de Azure, almacenado como archivo de certificado público (SampleRemoteAccessPublic.cer) y como archivo de certificado de intercambio de información personal (PFX) (SampleRemoteAccessPrivate.pfx). El segundo contiene la clave privada para el certificado y tiene una contraseña de forma predeterminada, **Password1**. Pero, puesto que esta contraseña es de dominio público, el certificado predeterminado solo debe usarse con fines de aprendizaje, no para una implementación de producción. Así pues, si sus fines no son de aprendizaje, cuando quiera habilitar sesiones remotas para sus implementaciones, debe hacer clic en el vínculo **Advanced** del cuadro de diálogo **Publish to Azure** para especificar su propio certificado. Tenga en cuenta que tendrá que cargar la versión PFX del certificado en el servicio hospedado dentro del Portal de administración de Azure, para que Azure pueda descifrar la contraseña del usuario.

El resto del tutorial muestra cómo habilitar el acceso remoto para un proyecto de implementación de Azure que se creó inicialmente con el acceso remoto deshabilitado. Para los fines de este tutorial, vamos a crear un certificado autofirmado y su archivo .pfx tendrá una contraseña de su elección. También tiene la opción de usar un certificado emitido por una entidad de certificación.

## Habilitación del acceso remoto después de haber implementado en Azure ##

Para habilitar el acceso remoto después de haber implementado en Azure, siga estos pasos:

1. Inicie sesión en el Portal de administración de Azure con su cuenta de Azure.
1. En la lista de **Servicios en la nube**, seleccione el servicio en la nube que se implementó
1. En la página web del servicio en la nube, haga clic en el vínculo **Configurar**.
1. En la parte inferior de la página de configuración, haga clic en el vínculo **Remoto**.
1. Cuando aparezca el cuadro de diálogo emergente:
    1. Especifique el rol para el que desea habilitar el acceso remoto
    1. Haga clic en la casilla **Habilitar Escritorio remoto** para seleccionarla.
    1. Especifique un nombre de usuario y la contraseña que quiere usar apara el acceso remoto.
    1. Seleccione el certificado que va a usar.
1. Haga clic en **Aceptar**. 

Verá un mensaje que indica que el cambio de configuración está en curso, lo cual puede tardar unos minutos en completarse. Cuando el cambio en la configuración se haya completado, siga los pasos de la sección **Para iniciar sesión de manera remota** más adelante en este artículo.
	
## Habilitación del acceso remoto en el paquete ##

* En el panel del explorador de proyectos de Eclipse, haga clic con el botón derecho en el proyecto y haga clic en **Properties** (Propiedades).
* En el cuadro de diálogo **Properties**, expanda **Azure** en el panel izquierdo y haga clic en **Remote Access**.
* En el cuadro de diálogo **Remote Access**, asegúrese de que la opción **Enable all roles to accept Remote Desktop Connections with these login credentials** (Habilitar todos los roles para aceptar las conexiones a Escritorio remoto con estas credenciales de inicio de sesión) está activada.
* Especifique un nombre de usuario para la conexión a Escritorio remoto.
* Especifique y confirme la contraseña del usuario. Los valores de nombre de usuario y contraseña de usuario establecidos en este cuadro de diálogo se utilizarán al realizar una conexión a Escritorio remoto. (Tenga en cuenta que se trata de una contraseña independiente de su contraseña PFX).
* Especifique la fecha de caducidad para la cuenta de usuario.
* Haga clic en **New** (Nuevo) para crear un certificado autofirmado (Como alternativa, puede seleccionar un certificado desde el área de trabajo o el sistema de archivos mediante los botones **Workspace** (Área de trabajo) o **FileSystem** (Sistema de archivos), respectivamente, pero para fines de este tutorial vamos a crear un certificado).
* En el cuadro de diálogo **New Certificate** (Nuevo certificado), especifique y confirme la contraseña que utilizará para el archivo PFX.
* Acepte el valor proporcionado para **Name (CN)** (Nombre (CN)) o use un nombre personalizado.
* Especifique la ruta de acceso y el nombre de archivo donde se guardará el nuevo certificado en formato .cer. Para este paso y el siguiente, puede usar la carpeta **cert** de su proyecto de Azure, pero tiene la posibilidad de elegir otra ubicación. Para los fines de este tutorial, usaremos **c:\\mycert\\mycert.cer**. (Cree la carpeta **c:\\mycert** antes de continuar, o utilice una carpeta existente si lo desea).
* Especifique la ruta de acceso y el nombre de archivo donde se guardará el nuevo certificado y su clave privada en formato .pfx. Para los fines de este tutorial, usaremos **c:\\mycert\\mycert.pfx**. El cuadro de diálogo **New Certificate** (Nuevo certificado) debe ser similar al siguiente (actualice las rutas de acceso de la carpeta si no utilizó **c:\\mycert**):
    ![][ic712275]
* Haga clic en **OK** (Aceptar) para cerrar el cuadro de diálogo **New Certificate**.
* El cuadro de diálogo **Remote Access** debería ser similar al siguiente:</p>
    ![][ic719495]
* Haga clic en **OK** para cerrar el cuadro de diálogo **Remote Access**.
	
Recompile la aplicación, con la compilación establecida para su implementación en la nube.

## Para iniciar sesión de forma remota ##

Cuando la instancia de rol esté lista, podrá iniciar sesión de manera remota en la máquina virtual que hospeda la aplicación.

* Si usa Eclipse en Windows y ha seleccionado la opción **Start remote desktop on deploy** (Iniciar escritorio remoto al implementar) durante la implementación en Azure, se abrirá una pantalla de inicio de sesión de conexión a Escritorio remoto cuando se inicie la implementación. Cuando se le pida el nombre de usuario y la contraseña, escriba los valores que especificó para el usuario remoto y podrá iniciar sesión.
* Otra manera de iniciar sesión de manera remota es a través del <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portal de administración de Azure</a>:
    * Dentro de la vista **Servicios en la nube** del Portal de administración de Azure, haga clic en el servicio en la nube, en **Instancias**, en una instancia específica y luego en el botón**Conectar**. El botón **Conectar** aparece del modo siguiente en la barra de comandos: 
    ![][ic659273]  
    >[AZURE.NOTE] Si se encuentra en un sistema operativo distinto de Windows, deberá usar un cliente de Escritorio remoto que sea compatible con el sistema operativo y seguir los pasos para configurar ese cliente con los valores del archivo RDP que descargó.
    * Tras hacer clic en el botón **Conectar**, se le pedirá que abra un archivo RDP. Abra el archivo y siga las indicaciones. (También puede guardar este archivo en el equipo local y luego ejecutarlo haciendo doble clic en él para iniciar sesión de manera remota en la máquina virtual sin necesidad de ir primero al Portal de administración).
    * Cuando se le pida el nombre de usuario y la contraseña, escriba los valores que especificó para el usuario remoto y podrá iniciar sesión.

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hello World para Azure en Eclipse][]

[Instalación del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hello World para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!---HONumber=AcomDC_0302_2016-->