<properties 
pageTitle="Tareas de inicio comunes para los servicios en la nube | Microsoft Azure" 
description="Este artículo proporciona algunos ejemplos de tareas de inicio comunes que puede realizar en el rol web o rol de trabajo del servicio en la nube." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="12/07/2015" 
ms.author="adegeo"/>

# Tareas de inicio comunes para los servicios en la nube

Este artículo proporciona algunos ejemplos de tareas comunes de inicio que puede realizar en su servicio en la nube. Puede usar las tareas de inicio para realizar operaciones antes de que se inicie un rol. Estas operaciones incluyen la instalación de un componente, el registro de componentes COM, el establecimiento de las claves del registro o el inicio de un proceso de ejecución largo.

Consulte [este artículo](cloud-services-startup-tasks.md) para entender cómo funcionan las tareas de inicio y de forma específica cómo crear las entradas que definen una tarea de inicio.

Muchas de las tareas aquí usan lo siguiente:

>[AZURE.NOTE]Las tareas de inicio no son aplicables a las máquinas virtuales, solo a los roles web y de trabajo del servicio en la nube.


## Definición de variables de entorno antes de que se inicie un rol

Puede definir variables de entorno para un rol completo, agregue el elemento [Runtime] a la definición del role en el archivo de definición de servicio.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

Si necesita las variables de entorno definidas para una tarea específica, que no estén compartidas con otras tareas, puede usar el elemento [Environment] dentro del elemento [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

También se pueden usar un [valor válido xPath en Azure](https://msdn.microsoft.com/library/azure/hh404006.aspx) para hacer referencia a algo acerca de la implementación. En lugar de usar el atributo `value` de atributo, defina un elemento secundario [RoleInstanceValue].

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Configuración del inicio IIS con AppCmd.exe

La herramienta de línea de comandos [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) puede usarse para administrar la configuración de IIS en el inicio de Azure. *AppCmd.exe* proporciona acceso a través de la línea de comandos a los ajustes de configuración para su uso en las tareas de inicio en Azure. Con *AppCmd.exe* se pueden agregar modificar o quitar ajustes del sitio web para aplicaciones y sitios.

Sin embargo, hay algunos aspectos que hay que tener en cuenta cuando se use *AppCmd.exe* como una tarea de inicio:

- Las tareas de inicio se pueden ejecutar más de una vez entre reinicios. Esto puede suceder, por ejemplo, si el rol se recicla.
- Algunas acciones *AppCmd.exe* pueden generar errores si se realizan más de una vez. Si intenta agregar una sección a *Web.config* dos veces, podría producirse un error.
- Si las tareas de inicio devuelven un código de salida distinto de cero o un **errorlevel** se producirá un error en las mismas. Esto puede suceder si *AppCmd.exe* genera un error.

Por estas razones, es recomendable comprobar las respuestas **errorlevel** después de llamar a *AppCmd.exe*, esto es sencillo si encapsula la llamada a *AppCmd.exe* con un archivo *.cmd*. Si se detecta una respuesta **errorlevel** conocida, puede ignorarla, de lo contrario devuélvala. Esto se muestra en el ejemplo a continuación.

Los mensajes errorlevel que devuelve *AppCmd.exe* se enumeran en el archivo winerror.h y también se pueden ver en [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### Ejemplo

Este ejemplo agrega una sección de compresión y una entrada de compresión para JSON al archivo *Web.config* con control de errores y registro.

Las secciones relevantes del archivo [ServiceDefinition.csdef] se muestran aquí, e incluyen la configuración del atributo [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) como `elevated` para dar a *AppCmd.exe* permisos suficientes para cambiar la configuración en el archivo *Web.config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

El archivo por lotes Startup.cmd usa AppCmd.exe para agregar una sección de compresión y una entrada de compresión para JSON al archivo *Web.config*. El **errorlevel** esperado de 183 se establece en cero mediante el programa de línea de comandos VERIFY.EXE. Los errorlevels inesperados se registran en StartupErrorLog.txt.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Adición de reglas de firewall

A efectos prácticos, hay dos firewalls en Azure. El primer firewall controla las conexiones entre la máquina virtual y el exterior. Esto se controla mediante el elemento [EndPoints] en el archivo [ServiceDefinition.csdef].

El segundo firewall controla las conexiones entre la máquina virtual y los procesos dentro de esa máquina virtual. Esto se controla mediante la herramienta de línea de comandos `netsh advfirewall firewall`, y es tema principal de este artículo.

Azure crea reglas de firewall para los procesos que se inician en sus roles. Por ejemplo, cuando se inicia un servicio o programa, Azure crea automáticamente las reglas de firewall necesarias para permitir que el servicio se comunique con Internet. Sin embargo, si crea un servicio que se inicia con un proceso fuera de su rol (por ejemplo, un servicio COM+ o un programa que se inicia con el Programador de Windows), tendrá que crear manualmente una regla de firewall para permitir el acceso a ese servicio. Estas reglas de firewall pueden crearse mediante una tarea de inicio.

Una tarea de inicio que crea una regla de firewall tiene que tener para el atributo [executionContext][Task] un valor **elevated**. Agregue la siguiente tarea de inicio al archivo [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Para agregar la regla de firewall, tiene que usar los comandos `netsh advfirewall firewall` adecuados en el archivo por lotes de inicio. En este ejemplo, la tarea de inicio requiere seguridad y cifrado para el puerto TCP 80.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Bloqueo de una dirección IP específica

Puede restringir el acceso a un rol web de Azure a un conjunto de direcciones IP especificadas mediante la modificación del archivo IIS **web.config** y la creación de un archivo de comandos que desbloquee la sección **ipSecurity** del archivo **ApplicationHost.config**.

En primer lugar, cree un archivo de comandos que se ejecute cuando se inicia el rol que desbloquea la sección **ipSecurity** del archivo **ApplicationHost.config**. Cree una nueva carpeta en el nivel raíz del rol web llamada **startpu** y, dentro de esta carpeta, cree un archivo por lotes denominado **startup.cmd**. Establezca las propiedades de este archivo en **Copiar siempre** para asegurarse de que se implementará.

Agregue la siguiente tarea de inicio al archivo [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Agregue este comando al archivo **startup.cmd**:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

Esto hace que el archivo por lotes **startup.cmd** se ejecute cada vez que se inicializa el rol web, asegurándose de que la sección **seguridad IP** necesaria está desbloqueada.

Por último, modifique la [sección system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) del archivo de rol web **web.config** para agregar una lista de direcciones IP a las que se concede acceso, tal como se muestra en el ejemplo siguiente:

Esta configuración de ejemplo **permite** a todas las direcciones IP el acceso al servidor con la excepción de las dos definidas

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta configuración de ejemplo **deniega** a todas las direcciones IP el acceso al servidor con la excepción de las dos definidas

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Creación de una tarea de inicio de PowerShell

No se puede llamar directamente a los scripts de Windows PowerShell desde el archivo [ServiceDefinition.csdef], pero se pueden invocar desde dentro de un archivo por lotes de inicio.

PowerShell, de forma predeterminada, no ejecutará un script sin firmar. A menos que firme los scripts, tendrá que configurar Windows PowerShell para ejecutar scripts sin firmar. Para ejecutar scripts sin firmar, **ExecutionPolicy** tiene que establecerse en **Unrestricted**. El ajuste **ExecutionPolicy** que vaya a usar se basa en la versión de Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Si usa un SO invitado que ejecuta PowerShell 2.0 o 1.0 puede forzar la ejecución de la versión 2 y si no está disponible, use la versión 1.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Creación de archivos de una tarea de inicio en un almacenamiento local

Puede usar un recurso de almacenamiento local para almacenar los archivos creados por una tarea de inicio a los que más adelante accederá una aplicación.

Para crear el recurso de almacenamiento local, agregue una sección [LocalResources] al archivo [ServiceDefinition.csdef] y, a continuación, agregue el elemento secundario [LocalStorage]. Asigne al recurso de almacenamiento local un nombre único y un tamaño adecuado para la tarea de inicio.

Para usar un recurso de almacenamiento local en la tarea de inicio, tiene que crear una variable de entorno para hacer referencia a la ubicación del recurso de almacenamiento local. A continuación, la tarea de inicio y la aplicación podrán leer y escribir archivos en el recurso de almacenamiento local.

Las secciones relevantes del archivo **ServiceDefinition.csdef** se muestran aquí:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Por ejemplo, este archivo por lotes **Startup.cmd** usa la variable de entorno **PathToStartupStorage** para crear el archivo **MyTest.txt** en la ubicación de almacenamiento local.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

Puede tener acceso a almacenamiento local desde el SDK de Azure con el método [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Las operaciones de lectura y escritura de archivos estándar funcionarán para leer y escribir el contenido del recurso de almacenamiento local.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Diferenciación entre la ejecución en el emulador y en la nube

Puede hacer que la tarea de inicio emprenda acciones diferentes cuando se ejecuta en la nube en comparación con cuando lo hace en el emulador de proceso. Por ejemplo, puede que quiera usar una copia nueva de los datos de SQL solo cuando se ejecuta en el emulador. O puede que desee hacer a algún tipo de optimizaciones de rendimiento para la nube que no necesita cuando la ejecución se realiza en el emulador.

Esta capacidad para realizar distintas acciones en el emulador de proceso y en la nube puede conseguirse mediante la creación de una variable de entorno en el archivo [ServiceDefinition.csdef],y realizando una prueba de la variable de entorno en la tarea de inicio.

Para crear la variable de entorno, agregue el elemento [Variable]/[RoleInstanceValue] y cree un valor de XPath de `/RoleEnvironment/Deployment/@emulated`. El valor de la variable de entorno **% ComputeEmulatorRunning %** será `"true"` cuando se ejecute en el emulador de proceso y `"false"` cuando se ejecute en la nube.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

Cualquier ejecución de tarea puede usar ahora la variable de entorno **% ComputeEmulatorRunning %** para realizar distintas acciones en función de si se ejecuta el rol en la nube o en el emulador. Aquí tiene un script de shell de .cmd que busca esa variable de entorno.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## Detección de si la tarea ya se ha ejecutado

El rol puede reciclar sin tener que reiniciar con lo que las tareas de inicio se vuelven a ejecutar. Hay una marca para indicar que una tarea ya se ha ejecutado en la máquina virtual de hospedaje. Puede que en algunas tareas no importe si se ejecutan varias veces. Sin embargo, puede haber situaciones en la que es necesario evitar que una tarea se ejecute más de una vez.

La manera más sencilla de detectar si una tarea se ha ejecutado ya es crear un archivo en la carpeta **% TEMP %** cuando la tarea se realiza correctamente y buscarlo al inicio de la tarea. Este es un ejemplo script de shell de cmd que lo hace.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## Prácticas recomendadas para las tareas
A continuación presentamos algunas prácticas recomendadas que debería seguir al configurar una tarea para el rol web o de trabajo.

### Registre siempre las actividades de inicio

Visual Studio no proporciona un depurador para repasar los archivos por lotes, por eso es conveniente tener tantos datos sobre el funcionamiento de archivos por lotes como sea posible. El registro de la salida de archivos por lotes, **stdout** y **stderr**, puede proporcionar información importante a la hora de depurar y corregir los archivos por lotes. Para registrar **stdout** y **stderr** en el archivo StartupLog.txt en el directorio señalado por la variable de entorno **% TEMP %**, agregue el texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` al final de líneas específicas que desee registrar. Por ejemplo, para ejecutar setup.exe en el directorio **% PathToApp1Install %**:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Si desea registrar todas las salidas de la tarea de inicio sin agregar `>> "%TEMP%\StartupLog.txt" 2>&1`al final de cada línea, necesita dos archivos por lotes de inicio. El primer archivo por lotes llamará al segundo archivo por lotes con una redirección para registrar todas las actividades del segundo archivo por lotes. Esto es necesario para que se produzca la redirección de forma correcta.

A continuación se muestra cómo redirigir todas las salidas de un archivo por lotes de inicio. En este ejemplo, el archivo ServerDefinition.csdef crea una tarea de inicio que llama a Startup1.cmd. Startup1.cmd llama a Startup2.cmd, redirigiendo todas las salidas a % TEMP%\\StartupLog.txt.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Establezca executionContext correctamente para las tareas de inicio

Establezca correctamente los privilegios para la tarea de inicio. A veces las tareas de inicio tienen que ejecutarse con privilegios elevados, incluso si el rol se ejecuta con privilegios normales.

El atributo [executionContext][Task] establece el nivel de privilegio de la tarea de inicio. Si usa `executionContext="limited"` la tarea de inicio tendrá el mismo nivel de privilegio que el rol. Si usa `executionContext="elevated"` la tarea de inicio tendrá privilegios de administrador, lo que permite que la tarea de inicio realice tareas de administrador sin otorgar privilegios de administrador a su rol.

Un ejemplo de tarea de inicio que requiere privilegios de nivel "elevated" sería una tarea de inicio que use **AppCmd.exe** para configurar IIS. **AppCmd.exe** requiere `executionContext="elevated"`.

### Use el valor de taskType apropiado

El atributo [taskType][Task] determina la forma en la que se ejecutará la tarea de inicio. Hay tres valores: **simple**, **background**, y **foreground**. Las tareas con valor background y foreground se inician de forma asincrónica, mientras que las tareas con valor simple se ejecutan sincrónicamente una después de otra.

Con las tareas de inicio **simple**, puede establecer el orden en que se ejecutan las tareas a través del orden de las tareas en el archivo ServiceDefinition.csdef. Si una tarea **simple** finaliza con un código de salida distinto de cero, el procedimiento de inicio se detendrá y el rol no se iniciará.

La diferencia entre las tareas de inicio con valor **background** y aquellas con valor **foreground** es que las tareas **foreground** mantienen el rol en ejecución hasta que la tarea **foreground** finaliza. Esto también significa que si la tarea **foreground** se bloquea o falla, el rol no se reciclará hasta que se fuerce el cierre de la tarea **foreground**. Por este motivo, las tareas **background** se recomiendan para las tareas de inicio asincrónicas a menos que necesite esa característica de la tarea **foreground**.

### Finalice lo archivos por lotes con EXIT /B 0

El rol solo se iniciará si **errorlevel** de cada una de las tareas de inicio de valor simple es cero. No todos los programas establecen el **errorlevel** (código de salida) correctamente, por lo que el archivo por lotes debe terminar con `EXIT /B 0` si todo se ejecutó correctamente.

La falta de `EXIT /B 0` al final de un archivo por lotes de inicio es una causa común de que un rol no se inicie.

### Espere que las tareas de inicio se ejecuten más de una vez

No todos los reciclajes de rol incluyen un reinicio, pero todos incluyen la ejecución de todas las tareas de inicio. Esto significa que las tareas de inicio tienen que poder ejecutarse varias veces entre reinicios sin problemas. Esto se explica [más arriba](#detect-that-your-task-has-already-run).

### Use el almacenamiento local para almacenar los archivos que tienen que accederse en el rol

Si desea copiar o crear un archivo durante la tarea de inicio que en ese momento es accesible para el rol, ese archivo tiene que colocarse en el almacenamiento local. Consulte la [sección](#create-files-in-local-storage-from-a-startup-task) anterior.

## Pasos siguientes

Revisar el [modelo de servicio y paquete](cloud-services-model-and-package.md) en la nube

Obtener más información acerca de cómo funcionan las [tareas](cloud-services-startup-tasks.md).

[Crear e implementar](cloud-services-how-to-create-deploy-portal.md) el paquete de servicio en la nube


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_1210_2015-->