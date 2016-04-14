<properties
   pageTitle="Hospedaje de aplicaciones web en Docker | Microsoft Azure"
   description="Aprenda a usar Visual Studio para hospedar una aplicación web en un contenedor de Docker."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/10/2016"
   ms.author="tarcher" />

# Hospedaje de Aplicaciones web en Docker

[Docker](https://www.docker.com/whatisdocker/) es un motor de contenedor ligero, semejante de alguna manera a una máquina virtual, que puede utilizar para hospedar aplicaciones y servicios. Visual Studio admite Docker en Ubuntu, CoreOS y Windows.

En este ejemplo se muestra cómo usar la extensión **Visual Studio 2015 Tools for Docker** para publicar una aplicación de ASP.NET 5 en una máquina virtual de Linux Ubuntu (referida aquí como un host de Docker) en Azure. La misma experiencia se puede utilizar para publicar en un contenedor de Windows.

Después de publicar la aplicación en un host de Docker, puede utilizar herramientas de línea de comandos de Docker para interactuar con el contenedor en el que se ha publicado la aplicación.

## Creación y publicación de un nuevo contenedor de Docker

En la sección siguiente, creará un nuevo proyecto de aplicación web ASP.NET 5, creará un host contenedor y luego generará y ejecutará el proyecto de aplicación web en un contenedor de Docker. Para comenzar, descargue e instale [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS).

### Adición de un proyecto de aplicación web ASP.NET 5

1. En el menú de Visual Studio, seleccione **Archivo > Nuevo > Proyecto**. 

1. En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C#** > **Web**.

1. Seleccione **Aplicación web ASP.NET** y asigne un nombre al proyecto nuevo. (Las capturas de pantalla de este artículo usan el nombre predeterminado de **WebApplication1**).

1. Pulse **Aceptar**.

1. Puesto que la aplicación web se hospedará y ejecutará en Docker, desactive la casilla **Hospedar en la nube** si está activada y luego pulse el botón **Aceptar**.

  ![][0]

  Este es el punto donde normalmente agregaría código a la aplicación web para que haga algo útil. En este ejemplo, vamos no vamos a complicar las cosas para centrarnos en Docker. (Observe que también puede optar por usar una aplicación web ASP.NET 5 existente).

### Publicación del proyecto
Una vez que ha creado el proyecto (o ha abierto un proyecto existente), los siguientes pasos le guiarán a través del proceso de publicación de dicho proyecto en un contenedor de Docker en Azure.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Publicar** en el menú contextual de dicho proyecto.

1. En la sección **Seleccionar un destino de publicación** del cuadro de diálogo **Publicación web**, pulse **Contenedores de Docker**.

    Si no ve la opción Contenedores de Docker, asegúrese de que ha instalado Visual Studio 2015 Tools for Docker y que ha seleccionado una plantilla de sitio web de ASP.NET 5 en la sección anterior.

    ![][1]

    El cuadro de diálogo **Seleccionar máquina virtual de Docker** le permite especificar el host de Docker en el que desea publicar el proyecto. Puede crear un nuevo host de Docker o eligir una VM existente hospedada en Azure o en cualquier otro lugar. Más adelante en este ejemplo, crearemos un nuevo host de Docker de Azure.

1. Si ya ha iniciado sesión en una cuenta de Azure, vaya al paso 5. Si no ha iniciado sesión en una cuenta, pulse **Agregar una cuenta**.

    ![][2]

1. En el cuadro de diálogo **Iniciar sesión en Visual Studio**, escriba la cuenta de correo electrónico de su suscripción de Azure y después pulse **Continuar**.

1. Pulse **Nuevo** para crear una nueva VM de Docker de Azure y después pulse **Aceptar**.

    ![][3]

    Tenga en cuenta que también tiene la opción de utilizar un host de Docker existente. Para ello, selecciónelo en la lista desplegable **Máquinas virtuales de Docker de Azure existentes** en lugar de pulsar **Nuevo**. Tenga en cuenta que la lista no está restringida solo a los hosts de contenedor, sino que se muestran todas las VM del inquilino de Azure.

    Como alternativa, puede publicar en un host de Docker personalizado. Consulte la sección [Proporcionar un host de Docker personalizado](#provide-a-custom-docker-host) más adelante en este tema para obtener más información.

1. Escriba la siguiente información en el cuadro de diálogo **Crear una máquina virtual en Microsoft Azure** . Cuando haya terminado, haga clic en **Aceptar**. Esto crea una máquina virtual de Linux con una extensión de Docker configurada.

    ![][4]

    Tenga en cuenta que ahora también tiene la opción de crear a un HOST de contenedor de Windows mediante Windows Server 2016 Technical Preview 3 (TP3).

    ![][8]

	|Nombre de propiedad|Configuración|
	|---|---|
	|Ubicación|Cambie este valor a la región más cercana a su configuración regional.|
	|Nombre DNS|Escriba un nombre único para la máquina virtual. Si Azure acepta el nombre, aparece un círculo verde con una marca de verificación banca a la derecha. Si no se acepta el nombre, aparece un círculo rojo con una x blanca. En ese caso, escriba un nuevo nombre único.|
	|Imagen|Elija una imagen de sistema operativo para usar en el host de Docker, si existe. Por ejemplo, elija una imagen de Ubuntu Server. (Tenga en cuenta que una imagen de Windows Server está ahora disponible en la lista de imágenes disponibles).|
	|Nombre de usuario|Escriba un nombre de usuario único para la máquina virtual.|
	|Contraseñas|Escriba una contraseña para el usuario y después confírmela.|
	|Directorio de certificados |Especifica el directorio donde se almacenan los certificados de Docker. Aunque puede crear un directorio nuevo o apuntar a un directorio existente, se recomienda usar el directorio de certificados predeterminado (C:\\Usuarios\\[*nombre de usuario*]\\.docker). De lo contrario, las opciones de autenticación no se podrán recuperar automáticamente si reutiliza el mismo host en otro proyecto o sistema.|

1. Pulse los puntos suspensivos (...) junto a la entrada **Directorio de certificados** y después, cree un nuevo directorio para los certificados de Docker o navegue a un directorio de certificados de Docker existente.

    Si los certificados de Docker necesarios para VM no se encuentran, aparece un icono de signo de exclamación junto a la entrada, haciéndole saber que no se encontraron los certificados necesarios y que, si continúa, se eliminarán y después se volverán a crear los certificados existentes.

1. Pulse en **Aceptar** para comenzar a crear la VM. Aparecerá un mensaje que indica que se está creando la máquina virtual en Azure.

    Visual Studio crea un archivo de plantilla de Administrador de recursos de Azure (ARM), el archivo de parámetros y un script de PowerShell de modo que puede ejecutar los comandos de nuevo siempre que lo desee.

    ![][7]

    Visual Studio muestra el progreso de la operación en la ventana **Resultados**. Visual Studio llama a un script de PowerShell para implementar la VM. El script usa cmdlets de Azure PowerShell para implementar un grupo de recursos de Azure. Más adelante, otro script de PowerShell utiliza comandos de Docker para publicar, como lo haría el usuario si estuviera creando el host de Docker manualmente.

    El aprovisionamiento del host de Docker puede tardar bastante tiempo, por lo que debe comprobar el estado en la ventana Resultados de Visual Studio para ver cuándo termina el trabajo.

1. Después de que el host de Docker esté totalmente aprovisionado en Azure, puede comprobar su cuenta en el portal de Azure. Debería de ver la nueva máquina virtual en la categoría **Máquina virtual** en el Portal de Azure.

1. Una vez que el host de Docker esté listo, vuelva atrás y publique el proyecto de aplicación web. En el menú contextual del nodo del proyecto de aplicación web en el **Explorador de soluciones**, pulse **Publicar**. Visual Studio crea un archivo de publicación en función de la VM creada.

1. En la pestaña **Conexión** del cuadro de diálogo **Publicación web**, pulse **Validar conexión** para asegurarse de que el host de Docker está preparado. Si la conexión es correcta, pulse **Publicar** para publicar la aplicación web.

    La primera vez que se publica una aplicación en un host de Docker, tardará algo de tiempo en descargar cualquiera de las imágenes base a las que se hace referencia en el archivo de Docker (como **FROM** *nombre\_de\_imagen*).

    Tenga en cuenta que el archivo de Docker es específico del sistema operativo. Si decide volver a publicar en un sistema operativo distinto, deberá cambiar el nombre del archivo de Docker para que Visual Studio puede crear un nuevo valor predeterminado basado en el sistema operativo de destino. Por ejemplo, si publica primero en un contenedor de Linux y posteriormente decide publicar en Windows, debe cambiar el nombre del archivo de Docker para que sea un nombre significativo (pero único), por ejemplo, DockerLinux. Después, cuando vuelva a publicar en Windows, Visual Studio volverá a crear el archivo de Docker predeterminado para Windows. Más adelante, cuando vuelva a publicar cualquiera, seleccione el archivo de Docker adecuado para el sistema operativo.

## Proporcionar un host de Docker personalizado

En la sección anterior, vio cómo crear una máquina virtual de Docker hospedada en Azure. Sin embargo, si ya dispone de un host de Docker existente en otra parte, puede publicar en ese host y no en Azure.

### Cómo proporcionar un host de Docker personalizado

1. En el cuadro de diálogo **Seleccionar máquina virtual de Docker** cuadro de diálogo, seleccione la casilla **Host de Docker personalizado**.

    ![][5]

1. Pulse **Aceptar**.

1. En el diálogo cuadro **Publicación web**, agregue valores a la configuración en la sección **CustomDockerHost**, como los siguientes: dirección URL del servidor, nombre de la imagen, ubicación del archivo de Docker y números de puerto del contenedor y el host.

    En la sección **Opciones avanzadas de Docker**, puede ver o cambiar las opciones de autenticación y ejecución, así como la línea de comandos de Docker.

    ![][6]

1. Después de escribir todos los valores necesarios, pulse **Validar conexión** para asegurarse de que la conexión con el host de Docker funciona correctamente.

1. Si la conexión funciona correctamente, puede pulsar **Siguiente** para ver una lista de los componentes que se van a publicar, o puede pulsar **Publicar** para publicar inmediatamente el proyecto.

## Prueba del host de Docker

Una vez que el proyecto se ha publicado en un host de Docker en Azure, puede probarlo mediante la comprobación de su configuración. Dado que las herramientas de línea de comandos de Docker se instalan con la extensión de Visual Studio, puede emitir comandos a Docker directamente desde un símbolo del sistema de Windows.

El siguiente procedimiento es para la comunicación con un host de Docker que se ha implementado en Azure.

### Cómo probar el host de Docker

1. Abra un símbolo del sistema de Windows.

1. Asigne el host de Docker y compruebe las variables de entorno. Para ello, escriba los siguientes comandos en el símbolo del sistema: (Sustituya el nombre de su host de Docker para *NameofAzureVM* y la región en la que se creó para *Región*.)

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    Establecer estas variables de entorno evita tener que especificar manualmente esta información para cada comando de Docker que emite.

1. Ahora puede emitir comandos como los siguientes para comprobar si el host de Docker está presente y funciona.

	|Línea de comandos|Descripción|
	|---|---|
	|`docker info`|Permite obtener información de la versión de Docker.|
	|`docker ps`|Permite obtener una lista de contenedores en ejecución.|
	|`docker ps –a`|Permite obtener una lista de contenedores, incluidos los que están detenidos.|
	|`docker logs <Docker container name>`|Permite obtener un registro para el contenedor especificado.|
	|`docker images`|Obtenga una lista de dominios.|

    Para obtener una lista completa de comandos de Docker, simplemente escriba el comando `docker` en el símbolo del sistema y presione la tecla **&lt;Intro>**. Para obtener más información, vea la documentación de la [línea de comandos de Docker](https://docs.docker.com/reference/commandline/cli/).

## Pasos siguientes

Ahora que tiene un host de Docker, puede emitir comandos de Docker en él. Para obtener más información sobre Docker, consulte la [documentación de Docker](https://docs.docker.com/) y el [tutorial en línea de Docker](https://www.docker.com/tryit/).

Para obtener información acerca del uso de la extensión de la máquina virtual de Docker para Linux en Azure, consulte [Extensión de máquina virtual Docker para Linux en Azure](/virtual-machines/virtual-machines-docker-vm-extension.md).

Para obtener información sobre los problemas con el uso de Docker en Visual Studio, consulte [Solución de problemas de Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0211_2016-->