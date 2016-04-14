<properties
   pageTitle="Solución de problemas de errores del cliente Docker en Windows con Visual Studio | Microsoft Azure"
   description="Solucione los problemas que encuentre al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows mediante Visual Studio."
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
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Solución de problemas de errores de Docker

Después de configurar todos los valores para el contenedor Docker de su aplicación, debe asegurarse de que la configuración y rutas de acceso son correctas. Visual Studio proporciona el botón Validar en el cuadro de diálogo de publicación para ayudarle a hacerlo.

En este tema se le ayuda a diagnosticar y corregir o solucionar los problemas más comunes que encontrará al hospedar una aplicación de Visual Studio en Docker. Se agregarán más problemas a este tema a medida que se encuentren.

## Recibirá un mensaje de error al intentar validar la conexión a su host Docker en el cuadro de diálogo Publicación web

A continuación presentamos posibles soluciones a este problema.

- En la pestaña **Conexión** del cuadro de diálogo **Publicar**, asegúrese de que la **Dirección URL del servidor** sea correcta y de que el `:<port_number>` al final de la **Dirección URL del servidor** sea el puerto que escucha el demonio de Docker.

- En la pestaña **Conexión** del cuadro de diálogo **Publicar**, expanda la sección **Opciones avanzadas de Docker** y asegúrese de que se especifican las opciones de **autenticación** correctas.
  - Si el demonio de Docker del servidor está configurado para usar seguridad TLS, la interfaz de la línea de comandos de Windows Docker (docker.exe) buscará la clave de cliente (key.pem) y el certificado (cert.pem) de forma predeterminada en la carpeta `<%userprofile%>\.docker`. Si estos elementos no están presentes, deberán generarse mediante el uso de OpenSSL. Para obtener más información acerca de cómo configurar Docker para TLS, consulte el artículo sobre [protección del socket de demonio de Docker con HTTPS](https://docs.docker.com/articles/https/).

	Una manera de garantizar la correcta autenticación por parte de Docker desde el cliente Windows en el servidor Linux es copiar el contenido del cuadro de texto Vista previa en una nueva ventana de comandos y cambiar `<command>` por "info" como se muestra a continuación:

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    Como alternativa a copiar los archivos de certificado y clave de cliente en la carpeta .docker, puede cambiar las opciones de **autenticación** mediante la adición de los parámetros siguientes:

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Asegúrese de que la versión de Docker daemon del equipo host Docker es 1.6 o posterior.

## Error de tiempo de espera al usar sus propios certificados sin certificado de cliente en la carpeta Docker

Si decide usar sus propios certificados al crear el host Docker en Visual Studio (es decir, desactiva la casilla **Generar automáticamente certificados Docker** del cuadro de diálogo **Crear máquina virtual en Microsoft Azure**), deberá copiar los archivos de certificado y clave de cliente (cert.pem y key.pem) en la carpeta Docker (`<%userprofile%>\.docker`). De lo contrario, al publicar el proyecto, obtendrá un error de tiempo de espera en una hora y se producirá un error en la operación de publicación.

## Se requiere la publicación de PowerShell 3.0 en contenedores Docker

Si su sistema operativo es Windows 7 o Windows Server 2008, deberá instalar PowerShell 3.0 antes de poder llevar a cabo su publicación en contenedores Docker. PowerShell 3.0 se incluye en [Windows Management Framework 3.0](https://www.microsoft.com/es-ES/download/details.aspx?id=34595). Será necesario que reinicie su sistema tras la instalación.

Como solución alternativa, puede actualizar a Windows 8.1 o Windows 10, que ya tiene PowerShell 3.0.

## La ventana de PowerShell no se cierra automáticamente

Una vez creada una VM, hay veces en que la ventana de PowerShell no se cierra automáticamente. Al cerrarse este ventana, también se cierra Visual Studio. Dado que la ventana no afecta a las características de las herramientas de Visual Studio o Docker, déjela abierta hasta que finalice su trabajo.

## P+F

P: ¿Cómo se puede crear un nuevo equipo Linux habilitado para Docker en Azure con las herramientas de Visual Studio?

R: Consulte [Hospedaje de aplicaciones web en Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md) para obtener información sobre cómo hacerlo.

P: ¿Qué plantillas del proyecto de Visual Studio son compatibles con la publicación en un contenedor Linux Docker?

R: Visual Studio admite actualmente las plantillas web Aplicación de consola de C# (Paquete) y Vista previa de ASP.NET 5 de C#, incluyendo:

- Vacío

- API Web

- Aplicación web

P: ¿Cómo se puede publicar mi proyecto web o de consola ASP.NET 5 en Docker con MSBUILD desde la línea de comandos?

R: Utilice el siguiente comando MSBuild:

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

P: ¿Cómo se puede publicar mi proyecto web o de consola ASP.NET 5 en Docker con PowerShell desde la línea de comandos?

R: Utilice el siguiente comando PowerShell:

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

P: Tengo mi propio servidor Linux con Docker instalado, ¿cómo se puede especificar esto en el cuadro de diálogo **Publicación web**?

R: Consulte la sección **Ofrecimiento de un host Docker personalizado** en el tema [Hospedaje de aplicaciones web en Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

P: Estoy usando mi propio servidor Linux con Docker instalado. ¿Cómo se pueden generar claves y certificados con el fin de configurar la autenticación con TLS?

R: Una forma es usando OpenSSL en el servidor para generar los certificados y claves necesarios para la CA, el servidor y el cliente. Es entonces cuando puede utilizar software de terceros para establecer una conexión SSH/SFTP y, a continuación, copiar los certificados en el equipo de desarrollo de Windows local. De forma predeterminada, Docker (CLI) intentará usar certificados incluidos en la carpeta `<userprofile>\.docker`.

Otra opción es descargando OpenSSL para Windows y generando los certificados y claves necesarios y, a continuación, cargar la CA, certificados de servidor y claves en el equipo Linux. Para obtener más información acerca de cómo establecer una conexión segura con Docker, consulte el artículo sobre [protección del socket de demonio de Docker con HTTPS](https://docs.docker.com/articles/https/).

<!---HONumber=AcomDC_1223_2015-->