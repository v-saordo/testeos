<properties
   pageTitle="Instalación de .NET en un rol de servicio en la nube"
   description="En este artículo se describe cómo instalar manualmente .NET Framework en el rol web y el rol de trabajo del servicio en la nube"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/11/2015"
   ms.author="saurabh"/>

# Instalación de .NET en un rol de servicio en la nube 

En este artículo se describe cómo instalar .NET Framework en el rol web y el rol de trabajo de servicio en la nube. Puede utilizar estos pasos para instalar .NET Framework 4.5.2 o .NET 4.6 en la familia de SO invitado de Azure 4. Para obtener la información más reciente sobre las versiones de sistema operativo invitado, consulte [Versiones de SO invitado de Azure y matriz de compatibilidad de SDK](cloud-services-guestos-update-matrix.md).

El proceso de instalación de .NET en el rol web y el rol de trabajo implica incluir el paquete del instalador de .NET como parte del proyecto en la nube e iniciar el instalador como parte de las tareas de inicio del rol.

## Agregar el instalador de .NET al proyecto
1. Descargue el instalador web del .NET Framework que desea instalar.
	- Instalador web de [.NET 4.5.2](http://go.microsoft.com/fwlink/p/?LinkId=397703)
	- Instalador web de [.NET 4.6](http://go.microsoft.com/fwlink/?LinkId=528259)
	- [Instalador web de .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
2. Para un rol web
  1. En el **Explorador de soluciones**, en **Roles** del proyecto de servicio en la nube, haga clic con el botón secundario en su rol y, a continuación, seleccione **Agregar > Nueva carpeta**. Cree una carpeta llamada *bin*.
  2. Haga clic con el botón secundario en la carpeta **bin** y seleccione **Agregar > Elemento existente**. Seleccione el instalador de .NET y agréguelo a la carpeta bin.
3. Para un rol de trabajo
  1. Haga clic con el botón secundario en el rol y seleccione **Agregar > Elemento existente**. Seleccione el instalador de .NET y agréguelo al rol. 

Los archivos que se agregan de esta manera a la carpeta de contenido del rol se agregarán automáticamente al paquete de servicios en la nube y se implementarán en una ubicación coherente en la máquina virtual. Repita este proceso para todos los roles web y roles de trabajo en el servicio en la nube, de manera que todos los roles tengan una copia del instalador.

![Contenidos de rol con archivos de instalador][1]

## Definir las tareas de inicio para los roles
Las tareas de inicio le permiten realizar operaciones antes de que se inicie un rol. Instalar .NET Framework como parte de la tarea de inicio garantizará que el marco se instale antes de que se ejecute cualquier código de la aplicación. Para obtener más información acerca de las tareas de inicio, consulte: [Ejecutar tareas de inicio en Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. Agregue lo siguiente al archivo *ServiceDefinition.csdef* en el nodo **WebRole** o el nodo **WorkerRole** para todos los roles:
	
	```xml
	 <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
	```

	La configuración anterior ejecutará el comando *install.cmd* de la consola con privilegios de administrador, para así poder instalar .NET Framework. La configuración también crea un LocalStorage con el nombre *NETFXInstall*. El script de inicio establecerá la carpeta temp para que use este recurso de almacenamiento local para que se descargue el instalador de .NET Framework y se instale desde este recurso. Es importante establecer el tamaño de este recurso en al menos 1024 MB para asegurarse de que el marco se instalará correctamente. Para obtener más información, consulte: [Usar el almacenamiento local para almacenar archivos durante el inicio](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. Cree un archivo **install.cmd** y agréguelo a todos los roles haciendo clic con el botón secundario en el rol y seleccionando **Agregar>Elemento existente...**. De este modo, ahora todos los roles deben tener el archivo de instalador de .NET, además del archivo install.cmd.
	
	![Contenidos de rol con todos los archivos][2]

	> [AZURE.NOTE]Para crear este archivo, utilice un editor de texto sencillo, como el Bloc de notas. Si usa Visual Studio para crear un archivo de texto y, a continuación, le cambia el nombre a ".cmd", es posible que el archivo todavía contenga una marca BOM UTF-8 y ejecutar la primera línea del script generará un error. Si desea usar Visual Studio para crear el archivo, agregue una instrucción REM (nota) en la primera línea del archivo, para que se omita al ejecutarlo.

3. Agregue el script siguiente al archivo **install.cmd**:

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP46"
		
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
		
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set netfxinstallfile="NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
		
	:NDP46
	set netfxinstallfile="NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
		
	:NDP461
	set netfxinstallfile="NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
		
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto end
		
	REM ***** Installing .NET *****
	echo Installing .NET: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1
		
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

	```
	
	> [AZURE.IMPORTANT]Actualice el valor de la variable *netfx* en el script para que coincida con la versión de Framework que quiere instalar. Para instalar .NET 4.5.2, la variable *netfx* se debe establecer en *"NDP452"*, para instalar .NET 4.6, la variable *netfx* se debe establecer en *"NDP46"* y para instalar .NET 4.6.1, la variable *netfx* debe establecerse en *"NDP461"*.
		
	El script de instalación consulta el registro para comprobar si la versión de .NET Framework especificada ya está instalada en la máquina. Si la versión de .NET no está instalada, se inicia el instalador web de .NET. El script registrará toda la actividad en un archivo llamado *startuptasklog-(fechayhoractual).txt* almacenado en el almacenamiento local *InstallLogs* para ayudar a solucionar cualquier problema que pueda surgir.
 
      

## Configurar los diagnósticos para transferir los registros de las tareas de inicio al almacenamiento de blobs (opcional)
De manera opcional, puede configurar Diagnósticos de Azure para transferir todos los archivos de registro generados por el script de inicio o el instalador de .NET al almacenamiento de blobs. Con este enfoque, es posible ver los registros simplemente al descargar los archivos de registro desde el almacenamiento de blobs, en lugar de tener que realizar una conexión de Escritorio remoto con el rol.

Para configurar los diagnósticos, abra *diagnostics.wadcfgx* y agregue lo siguiente en el nodo **Directorios**:

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Esto configurará los diagnósticos de Azure para transferir todos los archivos del directorio *log* en el recurso *NETFXInstall* a la cuenta de almacenamiento de diagnóstico en el contenedor de blobs *netfx-install*.

## Implementación del servicio 
Cuando implemente el servicio, se ejecutarán las tareas de inicio e instalarán .NET Framework si todavía no está instalado. Los roles tendrán un estado de ocupado mientras se esté instalando el marco e, incluso, podrían reiniciarse si la instalación del marco así lo requiere.


## Recursos adicionales

- [Instalar .NET Framework][]
- [Determinación de las versiones instaladas de .NET Framework][]
- [Solución de problemas en instalaciones de .NET Framework][]

[Determinación de las versiones instaladas de .NET Framework]: https://msdn.microsoft.com/library/hh925568.aspx
[Instalar .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Solución de problemas en instalaciones de .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_1217_2015-->