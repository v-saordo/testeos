### Creación de la aplicación de consola y obtención del ensamblado

Para crear una nueva aplicación de consola en Visual Studio e instalar el paquete NuGet que contiene la Biblioteca de cliente de Almacenamiento de Azure:

1. En Visual Studio, elija **Archivo > Proyecto nuevo** y, a continuación, elija **Windows > Aplicación de consola** en la lista de plantillas de Visual C#.
2. Asigne un nombre a la aplicación de consola y, a continuación, haga clic en **Aceptar**.
3. Una vez creado el proyecto, haga clic con el botón derecho en el Explorador de soluciones y elija **Administrar paquetes de NuGet**. Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar tanto el paquete Biblioteca de cliente de Almacenamiento de Azure para .NET como sus dependencias.

Los ejemplos de código de este artículo también utilizan la [biblioteca del Administrador de configuración de Microsoft Azure](https://msdn.microsoft.com/library/azure/mt634646.aspx) para recuperar la cadena de conexión de almacenamiento de un archivo app.config de la aplicación de consola. Con el Administrador de configuración de Azure, se puede recuperar la cadena de conexión en tiempo de ejecución, independientemente de que la aplicación se ejecute en Microsoft Azure o desde una aplicación web, móvil o de escritorio.

Para el paquete del , haga clic con el botón derecho en el proyecto en el Explorador de soluciones y elija **Administrar paquetes de NuGet**. Busque "ConfigurationManager" en línea y haga clic en **Instalar** para instalar el paquete.

El uso del Administrador de configuración Azure es opcional. También se puede utilizar una API como la [clase ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

<!---HONumber=AcomDC_0309_2016-->