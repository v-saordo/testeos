<properties
   pageTitle="Modificación de una aplicación web de ASP.NET 5 activa que se ejecuta en un contenedor de Docker local mediante Editar y actualizar | Microsoft Azure"
   description="Aprenda a modificar una aplicación que se ejecuta en un contenedor de Docker local y a actualizar el contenedor mediante Editar y actualizar"
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
   ms.date="02/17/2016"
   ms.author="tarcher" />

# Modificación de una aplicación web de ASP.NET 5 activa que se ejecuta en un contenedor de Docker local mediante Editar y actualizar

## Información general
Visual Studio Tools para Docker ofrece una práctica forma de desarrollar y probar la aplicación localmente en un contenedor de Docker sin tener que reiniciar el contenedor cada vez que se realiza un cambio de código. En este artículo se explica cómo utilizar la característica "Editar y actualizar" para iniciar una aplicación web de ASP.NET 5 en un contenedor de Docker local, realizar los cambios necesarios y luego actualizar el explorador para ver esos cambios.

## Requisitos previos
Es necesario instalar las siguientes herramientas.

- [Visual Studio 2015 Update 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET y Herramientas web 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools para Docker](https://aka.ms/DockerToolsForVS)

> [AZURE.NOTE] Si tiene instalada una versión anterior de Visual Studio 2015 Tools para Docker, deberá desinstalarla desde el Panel de control antes de instalar la versión más reciente.

## Configuración y prueba del cliente de Docker 
Esta sección le ayudará a asegurarse de que la instancia predeterminada de máquina de Docker se ha configurado y se está ejecutando.

1. Cree una instancia predeterminada del host de Docker; para ello, emita el siguiente comando en un símbolo del sistema.

		docker-machine create --driver virtualbox default
 
1. Compruebe que la instancia predeterminada está configurada y en ejecución mediante la emisión del siguiente comando en el símbolo del sistema. (Verá que se ejecuta una instancia denominada 'predeterminada'.

		docker-machine ls 
		
	![][0]
 
1. Establezca el valor predeterminado como el host actual mediante la emisión del siguiente comando en el símbolo del sistema.

		docker-machine env default

1. Emita el comando siguiente para configurar el shell.

		FOR /f "tokens=*" %i IN ('docker-machine env default') DO %i

1. El siguiente comando debe mostrar una respuesta vacía de contenedores activos en ejecución.

		docker ps

	![][1]
 
> [AZURE.NOTE] Cada vez que reinicie el equipo de desarrollo, deberá reiniciar el host de Docker local. Para ello, emita el siguiente comando en un símbolo del sistema: `docker-machine start default`.

## Edición de una aplicación que se ejecuta en un contenedor de Docker local
Visual Studio 2015 Tools para Docker permite a los desarrolladores de aplicaciones web de ASP .NET 5 probar y ejecutar sus aplicaciones en un contenedor de Docker, realizar cambios en la aplicación en Visual Studio y actualizar el explorador para ver los cambios aplicados a la aplicación que se ejecuta dentro del contenedor.

1. En el menú de Visual Studio, seleccione **Archivo > Nuevo > Proyecto**. 

1. En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C# > Web**.

1. Seleccione **Aplicación web ASP.NET**.

1. Asigne un nombre a la nueva aplicación (o utilice el valor predeterminado).

1. Pulse **Aceptar**.

1. En **Plantillas de ASP.NET 5**, seleccione **Aplicación web ASP.NET**.

1. Pulse **Aceptar**.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Agregar > Compatibilidad con Docker**.

	![][2]
 
1. En el nodo del proyecto se crean los siguientes archivos:

	![][3]

1. Establezca la configuración de la solución en `Debug` y presione **& lt; F5 >** para iniciar la prueba de la aplicación localmente.

1. Una vez que se ha creado la imagen del contenedor y se está ejecutando en un contenedor de Docker, una consola de PowerShell intentará iniciar la aplicación web en el explorador predeterminado. Si utiliza el Explorador de Microsoft Edge, consulte la sección [Solución de problemas](#troubleshooting).

1. Vuelva a Visual Studio y abra `Views\Home\Index.cshtml`.

1. Anexe el siguiente contenido HTML al final del archivo y guarde los cambios.

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	Vuelva al explorador y actualícelo.

1.	Desplácese hasta el final de la página principal y verá que se han aplicado los cambios. Tenga en cuenta que el sitio puede tardar uno segundos en volverse a compilar, así que si no ve que los cambios se reflejan inmediatamente, actualice de nuevo su explorador.

##Solución de problemas 

- **La ejecución de la aplicación hace que PowerShell se abra, muestre un error y luego se cierre. La página del explorador no se abre.**

	Podría tratarse de un error durante `docker-compose-up`. Para ver el error, realice los pasos siguientes:

	1. Abra el archivo `Properties\launchSettings.json`.
	
	1. Busque la entrada Docker.
	
	1. Busque la línea que comienza de la manera siguiente:

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. Agregue el parámetro `-noexit` para que la línea se parezca ahora a la siguiente. De esta forma PowerShell se mantendrá abierto y podrá ver el error.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **Compilación: no se ha podido compilar la imagen. Error al comprobar la conexión TLS: el host no se está ejecutando**

	Compruebe que se ejecuta el host de Docker predeterminado. Consulte la sección [Configuración del cliente de Docker](#configuring-the-docker-client).

- **No se puede encontrar la asignación de volumen**

	De forma predeterminada, VirtualBox se comparte `C:\Users` como `c:/Users`. Si el proyecto no está en `c:\Users`, agréguelo manualmente a las [carpetas compartidas](https://www.virtualbox.org/manual/ch04.html#sharedfolders) de VirtualBox.

- **Uso de Microsoft Edge como explorador predeterminado**

	Si utiliza el Explorador de Microsoft Edge, puede que el sitio no se abra ya que Edge considera que la dirección IP no es segura. Para solucionar esto, realice los siguientes pasos: 1. En el cuadro Ejecutar de Windows, escriba `Internet Options`. 2. Pulse **Opciones de Internet** cuando aparezca. 2. Pulse la pestaña **Seguridad**. 3. Seleccione la zona **Intranet local**. 4. Pulse **Sitios**. 5. Agregue la dirección IP de la máquina virtual (en este caso, el host de Docker) en la lista. 6. Actualice la página en Edge; debería ver que el sitio está en funcionamiento. 7. Para más información sobre este problema, vea la entrada de blog de Scott Hanselman [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) (Microsoft Edge no puede ver ni abrir los sitios web locales hospedados en VirtualBox).

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-ps.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0218_2016-->